# Node Exporter install

```angular2html
apt update && apt install wget -y
wget https://github.com/prometheus/node_exporter/releases/download/v1.5.0/node_exporter-1.5.0.linux-amd64.tar.gz
tar -xzvf node_exporter-1.5.0.linux-amd64.tar.gz
cd node_exporter-1.5.0.linux-amd64
mv node_exporter /usr/local/bin
cd ..
rm -rf node_exporter-1.5.0.linux-amd64.tar.gz node_exporter-1.5.0.linux-amd64
cd /usr/local/bin
node_exporter --version
ode_exporter
```

- Make file service
```angular2html
nano /etc/systemd/system/node-exporter.service
```

- Add content
```angular2html
[Unit]
Description=Node Exporter
After=network.target

[Service]
User=root
Group=root
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
```

- Start service
```angular2html
systemctl daemon-reload
systemctl start node-exporter
systemctl status node-exporter
```

- Enable service
```angular2html
systemctl enable node-exporter
```



# Prometheus Install
```angular2html
wget https://github.com/prometheus/prometheus/releases/download/v2.33.3/prometheus-2.33.3.linux-amd64.tar.gz
tar -xvzf prometheus-2.33.3.linux-amd64.tar.gz
mv prometheus-2.33.3.linux-amd64 /usr/local/prometheus/
```
- Config prometheus.yml
```angular2html
nano /usr/local/prometheus/prometheus.yml
```

- Add content prometheus.yml
```angular2html
scrape_configs:
    - job_name: "prometheus"
    static_configs:
    - targets: ["localhost:9100"]
```

- Make file service prometheus.service
```angular2html
nano /etc/systemd/system/prometheus.service
```

- Add content prometheus.service
```angular2html
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=root
Group=root
Type=simple
ExecStart=/usr/local/prometheus/prometheus \
--config.file /usr/local/prometheus/prometheus.yml \
--storage.tsdb.path /usr/local/prometheus/ \
--web.console.templates=/usr/local/prometheus/consoles \
--web.console.libraries=/usr/local/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```

- Start service
```angular2html
systemctl daemon-reload
systemctl start prometheus
systemctl status prometheus
```


# Grafana Install
```angular2html
sudo apt-get install -y adduser libfontconfig1 musl
wget https://dl.grafana.com/enterprise/release/grafana-enterprise_11.1.0_amd64.deb
sudo dpkg -i grafana-enterprise_11.1.0_amd64.deb
service grafana-server start
systemctl daemon-reload
systemctl start grafana-server
systemctl status grafana-server
sudo systemctl enable grafana-server.service
```

- Config Grafana UI
```angular2html
Dashboards > Add your first data source > Prometheus
Required URL: http://localhost:9090 > Save & Test
Create > Import > ID : 1860 > Load > Import
```



