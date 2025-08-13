# Youstable Monitoring Setup

This guide details the steps to set up Prometheus for monitoring, including system updates, user setup, binary installation, configuration, and running Prometheus as a systemd service.

---

## 1. Update Packages and BIOS

Update your system packages and firmware:

```bash
sudo apt update && sudo apt upgrade
sudo fwupdmgr get-updates
sudo fwupdmgr update
```

---

## 2. Setup and Installations

### a. Create Prometheus User

Create a system user for Prometheus with no home directory and no shell access:

```bash
sudo useradd --no-create-home --shell /bin/false prometheus
```

### b. Download and Install Prometheus

Download Prometheus binaries (replace the version as needed):

```bash
wget https://github.com/prometheus/prometheus/releases/download/v3.5.0/prometheus-3.5.0.linux-amd64.tar.gz
tar xvf prometheus-3.5.0.linux-amd64.tar.gz
cd prometheus-3.5.0.linux-amd64
sudo mv prometheus /usr/local/bin/
sudo mv promtool /usr/local/bin/
```

### c. Set Ownership

Ensure correct permissions for configuration and data directories:

```bash
sudo chown -R prometheus:prometheus /etc/prometheus /var/lib/prometheus
sudo chown prometheus:prometheus /usr/local/bin/prometheus /usr/local/bin/promtool
```

---

## 3. Configuration

### a. Copy Configuration Files

Copy your `prometheus.yml` configuration file to the Prometheus config directory (usually `/etc/prometheus/`).

### b. Create systemd Service

Create a systemd service file for Prometheus:

```bash
sudo nano /etc/systemd/system/prometheus.service
```

Paste the following content:

```ini
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
  --storage.tsdb.path=/var/lib/prometheus

[Install]
WantedBy=multi-user.target
```

Save and close the file.

---

## 4. Enable and Start Prometheus

Reload systemd, enable Prometheus to start on boot, and start the service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable prometheus
sudo systemctl start prometheus
```

---

## 5. Test Prometheus

Open your browser and visit:

```
http://<MONITORING_SERVER_IP>:9090
```

Replace `<MONITORING_SERVER_IP>` with your server’s IP address. You should see the Prometheus web UI if everything is running correctly.

---

---

## 1. Create node_exporter User

```bash
sudo useradd --no-create-home --shell /bin/false node_exporter
```

---

## 2. Download & Install Node Exporter

```bash
cd /tmp
curl -LO https://github.com/prometheus/node_exporter/releases/latest/download/node_exporter-1.8.2.linux-amd64.tar.gz
tar xvf node_exporter-*.tar.gz
sudo mv node_exporter-*/node_exporter /usr/local/bin/
sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter
```

---

## 3. Create a Systemd Service

Create a new service file:

```bash
sudo nano /etc/systemd/system/node_exporter.service
```

Paste the following content:

```ini
[Unit]
Description=Node Exporter
After=network.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
```

---

## 4. Enable & Start Node Exporter

```bash
sudo systemctl daemon-reload
sudo systemctl enable node_exporter
sudo systemctl start node_exporter
```

---

## 5. Test Node Exporter

Open a browser and visit:

```
http://<YOUR_SERVER_IP>:9100/metrics
```

You should see a list of metrics being exported.

---

## Notes

- Make sure port `9100` is open in your firewall.
- Repeat these instructions on each server you want to monitor.

# Grafana Installation Guide

This guide explains how to install and start Grafana on a Debian/Ubuntu-based server, and configure data sources.

---

## 1. Install Prerequisites

```bash
sudo apt install -y apt-transport-https software-properties-common
```

---

## 2. Add Grafana Repository & Key

```bash
sudo mkdir -p /etc/apt/keyrings/
wget -q -O - https://packages.grafana.com/gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/grafana.gpg
echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://packages.grafana.com/oss/deb stable main" | sudo tee /etc/apt/sources.list.d/grafana.list
```

---

## 3. Install Grafana

```bash
sudo apt update
sudo apt install grafana -y
```

---

## 4. Enable and Start Grafana

```bash
sudo systemctl enable grafana-server
sudo systemctl start grafana-server
```

---

## 5. Access Grafana

Open your browser and go to:

```
http://<MONITORING_SERVER_IP>:3000
```

- **Default Login:**  
  - Username: `admin`  
  - Password: `admin`

---

## 6. Add Data Sources

After logging into Grafana, add the following data sources:

- **Prometheus:**  
  URL: `http://localhost:9090`

## References

- [Prometheus Documentation](https://prometheus.io/docs/)
