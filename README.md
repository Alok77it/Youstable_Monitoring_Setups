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

## References

- [Prometheus Documentation](https://prometheus.io/docs/)
