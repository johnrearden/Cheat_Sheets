# PostgreSQL Lore

## DBeaver

#### NB
In order for a local client to run successfully, its postgres version must equal or exceed that of the remote server.

## Terminal

### Restore remote db:

<pre>
# Connect to your server and create a test database
psql -h rojosample.net -p 5432 -U postgres -c "CREATE DATABASE forum_test;"

# Restore to the test database
pg_restore -h rojosample.net -p 5432 -U postgres -d forum_test /home/john/dump-forum-202509061639.sql

# Verify the restore worked
psql -h rojosample.net -p 5432 -U postgres -d forum_test -c "\dt"  # List tables
psql -h rojosample.net -p 5432 -U postgres -d forum_test -c "SELECT count(*) FROM your_main_table;"  # Check data
</pre>

### Automatic backups

###### backup_{DB_NAME}_db.sh

<pre>
#!/bin/bash
set -e

# Configuration
DB_HOST="rojosample.net"
DB_PORT="5432"
DB_NAME="forum"
DB_USER="postgres"
BACKUP_DIR="/var/backups/postgresql"
RETENTION_DAYS=7  # Fixed: removed quotes, just the number
LOG_FILE="/var/log/postgresql_backup.log"
TIMESTAMP=$(date +"%Y%m%d_%H%M%S")
BACKUP_FILE="$BACKUP_DIR/${DB_NAME}_backup_$TIMESTAMP.sql"

# Function to log messages
log_message() {
    echo "$(date '+%Y-%m-%d %H:%M:%S'): $1"
    echo "$(date '+%Y-%m-%d %H:%M:%S'): $1" >> "$LOG_FILE" 2>/dev/null || echo "Warning: Could not write to log file"

# Create backup directory if it doesn't exist
mkdir -p "$BACKUP_DIR" 2>/dev/null || {
    log_message "ERROR: Cannot create backup directory $BACKUP_DIR"
    exit 1
}

# Test connection first
log_message "Starting backup of database $DB_NAME"

if pg_isready -h "$DB_HOST" -p "$DB_PORT" -U "$DB_USER"; then
    echo "PostgreSQL is ready"
    
    # Perform backup - only proceed if this succeeds
    if pg_dump -h "$DB_HOST" -p "$DB_PORT" -U "$DB_USER" -d "$DB_NAME" > "$BACKUP_FILE"; then
        log_message "pg_dump completed successfully"

        # Compress backup - only proceed if pg_dump succeeded
        if gzip "$BACKUP_FILE"; then
            log_message "Backup completed successfully: ${BACKUP_FILE}.gz"

            # Clean up old backups - only if compression succeeded
            if find "$BACKUP_DIR" -name "${DB_NAME}_backup_*.sql.gz" -mtime +${RETENTION_DAYS} -delete 2>/dev/null; then
                log_message "Cleaned up backups older than $RETENTION_DAYS days"
            else
                log_message "Warning: Could not clean up old backups (or none found to clean)"
            fi
        else
            log_message "ERROR: Failed to compress backup file"
            # Remove the uncompressed file if compression failed
            rm -f "$BACKUP_FILE"
            exit 1
        fi
    else
        log_message "ERROR: pg_dump failed!"
        # Remove any partial backup file
        rm -f "$BACKUP_FILE"
        exit 1
    fi
else
    log_message "ERROR: Cannot connect to PostgreSQL server"
    exit 1
fi
</pre>

- Note : requires ~/.pgpass file with password for remote server




