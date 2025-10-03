# Setup Guide
### Part 1: Install Prometheus + Grafana (Monitor Server)
On Ubuntu, install Prometheus:

<pre>
# Create prometheus user
sudo useradd --no-create-home --shell /bin/false prometheus

# Create directories
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
sudo chown prometheus:prometheus /var/lib/prometheus

# Download Prometheus (check for latest version at prometheus.io)
cd /tmp
wget https://github.com/prometheus/prometheus/releases/download/v2.45.0/prometheus-2.45.0.linux-amd64.tar.gz
tar -xvf prometheus-2.45.0.linux-amd64.tar.gz
cd prometheus-2.45.0.linux-amd64

# Copy binaries
sudo cp prometheus /usr/local/bin/
sudo cp promtool /usr/local/bin/
sudo chown prometheus:prometheus /usr/local/bin/prometheus
sudo chown prometheus:prometheus /usr/local/bin/promtool

# Copy config files
sudo cp -r consoles /etc/prometheus
sudo cp -r console_libraries /etc/prometheus
sudo chown -R prometheus:prometheus /etc/prometheus
</pre>

Create Prometheus configuration /etc/prometheus/prometheus.yml:

<pre>
global:
  scrape_interval: 15s  # How often to scrape targets
  evaluation_interval: 15s  # How often to evaluate rules

# Alertmanager configuration (optional for now)
alerting:
  alertmanagers:
    - static_configs:
        - targets: []

# Where to find targets to monitor
scrape_configs:
  # Monitor Prometheus itself
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  # Monitor your 8 servers
  - job_name: 'node_exporter'
    static_configs:
      - targets:
          - 'server1.example.com:9100'
          - 'server2.example.com:9100'
          - 'server3.example.com:9100'
          - 'server4.example.com:9100'
          - 'server5.example.com:9100'
          - 'server6.example.com:9100'
          - 'server7.example.com:9100'
          - 'server8.example.com:9100'
        labels:
          environment: 'production'
</pre>

Create systemd service /etc/systemd/system/prometheus.service:

<pre>
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/var/lib/prometheus/ \
  --web.console.templates=/etc/prometheus/consoles \
  --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
</pre>

Start Prometheus:

<pre>
sudo systemctl daemon-reload
sudo systemctl start prometheus
sudo systemctl enable prometheus
sudo systemctl status prometheus
</pre>

Prometheus is now running on http://your-monitor-server:9090


### Install Grafana:

<pre>
# Add Grafana repository
sudo apt-get install -y software-properties-common
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -

# Install Grafana
sudo apt-get update
sudo apt-get install grafana

# Start Grafana
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
sudo systemctl status grafana-server
</pre>

Grafana is now running on http://your-monitor-server:3000

Default login: admin/admin (you'll be prompted to change)

# Part 2: Install Node Exporter (Each Target Server)
### Manual installation on one server:

<pre>
# Create user
sudo useradd --no-create-home --shell /bin/false node_exporter

# Download Node Exporter
cd /tmp
wget https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz
tar -xvf node_exporter-1.6.1.linux-amd64.tar.gz

# Copy binary
sudo cp node_exporter-1.6.1.linux-amd64/node_exporter /usr/local/bin/
sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter
</pre>

### Create systemd service /etc/systemd/system/node_exporter.service:

<pre>
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter \
  --collector.systemd \
  --collector.processes

[Install]
WantedBy=multi-user.target
</pre>

### Start Node Exporter:

<pre>
sudo systemctl daemon-reload
sudo systemctl start node_exporter
sudo systemctl enable node_exporter
sudo systemctl status node_exporter
</pre>

### Node Exporter is now exposing metrics on http://server:9100/metrics

#### Test it:

<pre>
curl http://localhost:9100/metrics
</pre>