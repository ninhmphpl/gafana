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
node_exporter
```

- Make file service
```angular2html
nano /etc/systemd/system/node_exporter.service
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
systemctl start node_exporter
systemctl status node_exporter
```

- Enable service
```angular2html
systemctl enable node_exporter
```

# Mysql Exporter Install

```angular2html
curl -s https://api.github.com/repos/prometheus/mysqld_exporter/releases/latest | grep browser_download_url   | grep linux-amd64 | cut -d '"' -f 4   | wget -qi -
tar xvf mysqld_exporter*.tar.gz
sudo mv  mysqld_exporter-*.linux-amd64/mysqld_exporter /usr/local/bin/
sudo chmod +x /usr/local/bin/mysqld_exporter

CREATE USER exporter IDENTIFIED BY 'PASSWD';
GRANT PROCESS, REPLICATION CLIENT, SELECT ON . TO 'exporter';
FLUSH PRIVILEGES;
EXIT

Configure DB Credential
sudo nano /etc/.mysqld_exporter.cnf

[client]
user=exporter
password=PASSWD
host=localhost


Create Systemd File
sudo nano /etc/systemd/system/mysql_exporter.service

[Unit]
Description=Prometheus MySQL Exporter
After=network.target

[Service]
ser=root
roup=root
Type=simple
Restart=always
ExecStart=/usr/local/bin/mysqld_exporter \
--config.my-cnf /etc/.mysqld_exporter.cnf \
--collect.global_status \
--collect.info_schema.innodb_metrics \
--collect.auto_increment.columns \
--collect.info_schema.processlist \
--collect.binlog_size \
--collect.info_schema.tablestats \
--collect.global_variables \
--collect.info_schema.query_response_time \
--collect.info_schema.userstats \
--collect.info_schema.tables \
--collect.perf_schema.tablelocks \
--collect.perf_schema.file_events \
--collect.perf_schema.eventswaits \
--collect.perf_schema.indexiowaits \
--collect.perf_schema.tableiowaits \
--collect.slave_status \
--web.listen-address=0.0.0.0:9104

[Install]
WantedBy=multi-user.target


sudo systemctl daemon-reload
sudo systemctl enable mysql_exporter
sudo systemctl start mysql_exporter
```




# Prometheus Install
```angular2html
wget https://github.com/prometheus/prometheus/releases/download/v2.53.0/prometheus-2.53.0.linux-amd64.tar.gz
tar -xvzf prometheus-2.53.0.linux-amd64.tar.gz
mv prometheus-2.53.0.linux-amd64 /usr/local/prometheus/
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

- Enable service
```angular2html
systemctl enable prometheus
```

- Stop service
```angular2html
systemctl stop prometheus
```

- Restart service
```angular2html
systemctl restart prometheus
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

- Restart service
```angular2html
systemctl restart grafana-server
```

- Config Grafana UI
```angular2html
Dashboards > Add your first data source > Prometheus
Required URL: http://localhost:9090 > Save & Test
Create > Import > ID : 1860 > Load > Import
```

# Config spring boot to prometheus

- Add dependency
```angular2html
runtimeOnly 'io.micrometer:micrometer-registry-prometheus'
implementation 'org.springframework.boot:spring-boot-starter-actuator'
```

- Config application.properties
```angular2html
management.endpoints.web.exposure.include=*
```

- Config prometheus.yml (/usr/local/prometheus/prometheus.yml)
```angular2html
scrape_configs:
  - job_name: 'spring-boot-application'
    metrics_path: '/actuator/prometheus'
    scrape_interval: 15s # This can be adjusted based on our needs
    static_configs:
      - targets: ['localhost:8080']
```




