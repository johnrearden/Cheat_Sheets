# Self-Hosted Postgres Playbook

A playbook for running one Postgres server that hosts several light-traffic projects, and for handling a project that outgrows it.

Assumes Ubuntu 24.04 on a Hetzner/Linode VPS, Django apps, and an existing Prometheus/Grafana/Loki stack. Replace `<ver>` with your Postgres major version and `myapp` with the project name.

---

## 1. Architecture

**One Postgres instance, strict separation inside it.**

- Each project gets **its own database** and **its own login role** that owns it.
- Apps connect as their own role, never as `postgres`.
- Postgres is reachable **only over a private network** (Hetzner private network or Tailscale), never the public internet.

Why: one instance means one set of upgrades, backups and monitoring, and one shared memory cache. Per-project separation means any database can later be moved out on its own (see §9).

---

## 2. Install

Use the official PGDG repository, not Ubuntu's bundled version.

```bash
sudo apt install -y postgresql-common
sudo /usr/share/postgresql-common/pgdg/apt.postgresql.org.sh
sudo apt install -y postgresql
```

Orientation:

```bash
pg_lsclusters                        # list clusters, versions, ports, status
sudo systemctl status postgresql
ls /etc/postgresql/<ver>/main/       # postgresql.conf, pg_hba.conf
sudo -u postgres psql                # superuser shell
```

The two files you'll edit most:

| File | Controls |
|---|---|
| `postgresql.conf` | Behaviour: memory, logging, WAL, listening address |
| `pg_hba.conf` | Who may connect, to which DB, from where, and how |

---

## 3. Lock it down

### 3.1 Listen only on private interfaces

`postgresql.conf`:

```ini
listen_addresses = 'localhost,10.0.0.2'   # add the private / Tailscale IP
password_encryption = scram-sha-256
```

### 3.2 Firewall

```bash
sudo ufw allow from 10.0.0.0/24 to any port 5432 proto tcp   # private subnet only
sudo ufw deny 5432
```

### 3.3 Onboard a new project

```sql
CREATE ROLE myapp LOGIN PASSWORD 'long-random-password';
CREATE DATABASE myapp_db OWNER myapp;
REVOKE CONNECT ON DATABASE myapp_db FROM PUBLIC;
GRANT CONNECT ON DATABASE myapp_db TO myapp;
```

`pg_hba.conf` needs one explicit line per project and app server:

```
# TYPE  DATABASE   USER    ADDRESS         METHOD
host    myapp_db   myapp   10.0.0.3/32     scram-sha-256
```

Reload with `sudo systemctl reload postgresql`.

### 3.4 Onboarding checklist

- [ ] Role + database created, CONNECT revoked from PUBLIC
- [ ] `pg_hba.conf` line added and reloaded
- [ ] Credentials in the app's env/secrets, not in git
- [ ] Database added to the backup job (§5)
- [ ] `CREATE EXTENSION pg_stat_statements;` run in the new DB (§6)

---

## 4. Tuning (the 80/20)

The defaults assume a tiny machine. For a box that's mostly Postgres, set these in `postgresql.conf`:

| Setting | Starting point | Notes |
|---|---|---|
| `shared_buffers` | ~25% of RAM | Postgres's own page cache. Needs a restart. |
| `effective_cache_size` | 50–75% of RAM | A planner hint only; nothing is allocated. |
| `work_mem` | 16–64MB | **Per sort/hash operation, per connection.** Keep it modest. |
| `maintenance_work_mem` | 256MB–1GB | Speeds up VACUUM and index builds. |
| `random_page_cost` | 1.1 | For SSD/NVMe. Makes the planner favour index scans. |
| `max_connections` | 100 | Pool connections rather than raising this (§9.2). |

Tools like pgtune can suggest values for your RAM and CPU. Treat those as a starting point, then adjust based on what monitoring shows.

**Django side:** set `CONN_MAX_AGE` (e.g. 60) to reuse connections instead of opening one per request.

---

## 5. Backups (the non-negotiable part)

### 5.1 Minimum: nightly logical dumps, off-server

```bash
pg_dump -Fc -d myapp_db -f /backups/myapp_db_$(date +%F).dump
# then ship to object storage and rotate (e.g. keep 14 days)
```

Restore:

```bash
createdb -O myapp myapp_db_restored
pg_restore -d myapp_db_restored /backups/myapp_db_2026-09-26.dump
```

### 5.2 Better: continuous WAL archiving with point-in-time recovery

Use **pgBackRest** or **WAL-G**, writing to S3-compatible object storage:

- Take periodic **base backups** (e.g. a weekly full plus daily differentials).
- **Archive WAL segments** continuously.
- Result: you can restore to any moment, e.g. "14:32, just before the bad migration." This is WAL replay used deliberately.

### 5.3 Backup rules

- [ ] Backups live **off the database server**
- [ ] **Restore is tested** regularly, on a scratch server
- [ ] Backup failures raise an alert (Prometheus/GlitchTip)
- [ ] Retention is defined and enforced

---

## 6. Monitoring

### 6.1 Metrics

Deploy **postgres_exporter** into Prometheus. On the Grafana dashboard, watch:

- active connections vs `max_connections`
- cache hit ratio (should stay above ~99% for OLTP workloads)
- database sizes and growth
- dead tuples and autovacuum activity
- transaction ID age (wraparound safety)
- replication lag (once you have replicas)

### 6.2 Query statistics

`postgresql.conf`:

```ini
shared_preload_libraries = 'pg_stat_statements'   # restart required
```

In each database, run `CREATE EXTENSION pg_stat_statements;`. Then to see the top queries by total time:

```sql
SELECT round(total_exec_time) AS total_ms, calls,
       round(mean_exec_time::numeric, 2) AS mean_ms, query
FROM pg_stat_statements
ORDER BY total_exec_time DESC
LIMIT 20;
```

### 6.3 Slow query logging (into Loki)

```ini
log_min_duration_statement = 500ms
log_line_prefix = '%m [%p] %u@%d '
```

### 6.4 Unused indexes

This query finds indexes that have never been scanned since the statistics were last reset:

```sql
SELECT schemaname, relname, indexrelname, idx_scan,
       pg_size_pretty(pg_relation_size(indexrelid)) AS size
FROM pg_stat_user_indexes
WHERE idx_scan = 0
ORDER BY pg_relation_size(indexrelid) DESC;
```

Before trusting a zero, check how long the stats have been accumulating.

---

## 7. Routine maintenance

- **Autovacuum:** leave it on. If a hot table accumulates dead tuples faster than autovacuum clears them, tune that table specifically rather than globally.
- **Disk:** alert when disk usage passes 80%. WAL and bloat can grow fast.
- **Review monthly:** top pg_stat_statements queries, unused indexes, database growth.

---

## 8. Upgrades

- **Minor versions** (e.g. x.4 → x.5): apply promptly with `apt upgrade` and a restart. These are bug and security fixes.
- **Major versions:** use `pg_upgrade` in place, or better, build a fresh server and migrate by dump/restore or logical replication (§9.5).
  - Always take a verified backup first.
  - Test the app against the new version on staging.
- Each major version is supported for roughly five years, so there's no need to chase every release.

---

## 9. When a project gets busy: the escalation ladder

Work top to bottom and stop when the problem goes away.

### 9.1 Fix the queries (free, and usually enough)

- Rank queries by total time with pg_stat_statements (§6.2).
- Run `EXPLAIN (ANALYZE, BUFFERS)` on the worst offenders.
- Add missing indexes on filter and join columns.
- Kill N+1 queries in Django with `select_related` / `prefetch_related`.

### 9.2 Pool connections

- Put **PgBouncer** in **transaction mode** in front of Postgres.
- This matters most once Gunicorn workers, Daphne and Celery all hold connections.
- In transaction mode, avoid session-level features (session advisory locks, `SET` without `LOCAL`).

### 9.3 Cache hot reads

- Cache expensive, rarely-changing reads in **Redis** (Django's cache framework).

### 9.4 Scale up

- Resize the VPS (more RAM and CPU), then revisit §4.
- A single well-tuned box handles far more than most side projects need.

### 9.5 Move the noisy neighbour to its own server

**Option A: dump/restore (simple, with a short maintenance window)**

1. Put the app in maintenance mode.
2. `pg_dump -Fc` the database, then `pg_restore` it on the new server.
3. Point Django's `DATABASES` at the new host, deploy, and lift maintenance mode.

**Option B: logical replication (near-zero downtime)**

1. Source: set `wal_level = logical` (restart required).
2. Copy the schema: `pg_dump --schema-only myapp_db | psql -h new-host myapp_db`.
3. Source: `CREATE PUBLICATION myapp_pub FOR ALL TABLES;`
4. Target: `CREATE SUBSCRIPTION myapp_sub CONNECTION 'host=old-host dbname=myapp_db user=replicator password=...' PUBLICATION myapp_pub;`
5. Wait for the initial copy and catch-up (watch `pg_stat_subscription`).
6. Cutover: briefly stop writes and **sync sequence values** (check whether your version replicates sequences). Then switch Django to the new host and drop the subscription.

### 9.6 Add a read replica

1. Use streaming replication, which ships WAL to a standby:
   ```bash
   pg_basebackup -h primary -U replicator -D /var/lib/postgresql/<ver>/main -R -X stream
   ```
2. Route read-only queries to the replica with a **Django database router**.
3. Monitor replication lag. Reads that must see just-written data stay on the primary.

### 9.7 High availability (only when downtime costs real money)

- Automatic failover (e.g. Patroni) is a big jump in complexity.
- At this point, seriously weigh a **managed Postgres** service against becoming a part-time DBA.

---

## 10. Quick reference

| Situation | Action |
|---|---|
| New project | §3.3 onboarding + checklist |
| Slow page | pg_stat_statements → EXPLAIN ANALYZE → index |
| "Too many connections" | PgBouncer / `CONN_MAX_AGE`, not a higher `max_connections` |
| One project hogging the box | Scale up (§9.4), then move it out (§9.5) |
| Read-heavy growth | Redis cache (§9.3), then a replica (§9.6) |
| Bad migration / data loss | Point-in-time restore via pgBackRest/WAL-G (§5.2) |
| Major version upgrade | Fresh server + migrate (§8) |
