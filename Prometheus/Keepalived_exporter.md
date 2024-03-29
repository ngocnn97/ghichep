# Cài đặt KEEPALIVED_EXPORTER

## Client

### Cài đặt keepalived_exporter

- Download source từ github

```sh
git clone --depth 1 https://github.com/cafebazaar/keepalived-exporter.git
```
- Cài đặt

`cd keepalived-exporter`

`make build`

`sudo mv keepalived-exporter /usr/local/bin/`

` sudo useradd -rs /bin/false keepalived_exporter `

- Tạo service cho keepalived_exporter

```sh
 [Unit]
Description=Keepalived Exporter
After=network-online.target

[Service]
Type=simple

User=keepalived-exporter
Group=keepalived-exporter

ExecStart=/usr/local/bin/keepalived-exporter

SyslogIdentifier=keepalived-exporter

Restart=always
RestartSec=1

NoNewPrivileges=yes

ProtectHome=yes
ProtectSystem=strict
ProtectControlGroups=true
ProtectKernelModules=true
ProtectKernelTunables=yes
ProtectHostname=yes
ProtectKernelLogs=yes
#ProtectProc=yes

[Install]
WantedBy=multi-user.target

```

### Khởi chạy service

`sudo systemctl daemon-reload`
`sudo systemctl start keepalived_exporter`

- Bật dịch vụ tự động chạy cùng hệ thống

`sudo systemctl enable keepalived_exporter`

### Mở firewall
	
- Iptables:

	`echo -e '-A INPUT  -p tcp -m tcp --dport 9165 -j ACCEPT' >> /etc/sysconfig/iptables`

	`systemctl reload iptables`

- Firewalld:
	
	Thêm dòng `<port port="9165" protocol="tcp"/>` vào file tương ứng tại `/etc/firewalld/zones/`


	`firewall-cmd --reload`


### Kiểm tra hoạt động

`curl http://localhost:9165/metrics`

## Server

### Add new scape job

Thêm cấu hình job mới vào file `/etc/prometheus/prometheus.yml`

```sh
scrape_configs:
  # ...
  - job_name: keepalived_exporter
    scrape_interval: 10s
    static_configs:
     - targets: [IP-client-linux:9165]
```

|Tham số |Ý nghĩa |
|--------|--------|
|job_name|Tên của job|
|scrape_interval|Thời gian nghỉ giữa 2 lần scrape|
|targets|Thông tin ip client : cổng|

### Khởi động lại prometheus service 

`systemctl restart prometheus`

### Query metric trên Prometheus Dashboard

Truy cập tại đỉa chỉ: [http://Server_ip:9090/graph]
