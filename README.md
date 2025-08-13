## Youstable_Monitoring_Setups

### Update Packages and BIOS

```bash
sudo apt update && sudo apt upgrade && sudo fwupdmgr get-updates && sudo fwupdmgr update
```
------------------------

### Setup and installations
```bash
sudo useradd --no-create-home --shell /bin/false prometheus
```
```bash
https://github.com/prometheus/prometheus/releases/download/v3.5.0/prometheus-3.5.0.linux-amd64.tar.gz
```
```bash
tar xvf prometheus-3.5.0.linux-amd64.tar.gz
cd prometheus-3.5.0.linux-amd64
mv prometheus /usr/local/bin
mv promtool /usr/local/bin
```
```bash
sudo chown -R prometheus:prometheus /etc/prometheus /var/lib/prometheus
sudo chown prometheus:prometheus /usr/local/bin/prometheus /usr/local/bin/promtool
```

------------------------
## Setup Steps

1. **Copy the Configuration**

   Copy `prometheus.yml` to your Prometheus directory.




------------------------

