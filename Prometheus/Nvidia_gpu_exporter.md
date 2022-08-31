# Cài đặt NVIDIA_GPU_EXPORTER

## Client

### Cài đặt nvidia_gpu_exporter

- Download source từ github

```sh
wget https://github.com/utkuozdemir/nvidia_gpu_exporter/releases/download/v0.3.0/nvidia_gpu_exporter_0.3.0_linux_x86_64.tar.gz
```

- Giải nén 

` tar -xvzf nvidia_gpu_exporter_${VERSION}_linux_x86_64.tar.gz  `

- Move file vào thư mục local/bin

` mv nvidia_gpu_exporter /usr/local/bin  `

- Tạo người dùng mới để chạy dịch vụ:

` sudo useradd -rs /bin/false nvidia_gpu_exporter   `

- Tạo service cho nvidia_gpu_exporter

```sh
 [Unit]
Description=Nvidia GPU Exporter
After=network-online.target

[Service]
Type=simple

User=nvidia_gpu_exporter
Group=nvidia_gpu_exporter

ExecStart=/usr/local/bin/nvidia_gpu_exporter

SyslogIdentifier=nvidia_gpu_exporter

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
`sudo systemctl start nvidia_gpu_exporter`

- Bật dịch vụ tự động chạy cùng hệ thống

`sudo systemctl enable nvidia_gpu_exporter`

### Mở firewall
	
	- Iptables:

	`echo -e '-A INPUT  -p tcp -m tcp --dport 9835 -j ACCEPT' >> /etc/sysconfig/iptables`

	`systemctl reload iptables`

	- Firewalld:

	`echo -e '<port protocol="tcp" port="9835"/>' >>/etc/firewalld/zones/vmg.xml`

	`firewall-cmd --reload`


### Kiểm tra hoạt động

`curl http://localhost:9835/metrics`

## Server

### Add new scape job

Thêm cấu hình job mới vào file `/etc/prometheus/prometheus.yml`

```sh
scrape_configs:
  # ...
  - job_name: “nvidia_gpu_exporter”
    scrape_interval: 10s
    static_configs:
     - targets: [‘IP-client-linux:9835]
```

|Tham số |Ý nghĩa |
|job_name|Tên của job|
|scrape_interval|Thời gian nghỉ giữa 2 lần scrape|
|targets|Thông tin ip client : cổng|

### Khởi động lại prometheus service 

`systemctl restart prometheus`

### Query metric trên Prometheus Dashboard

Truy cập tại đỉa chỉ: [http://Server_ip:9090/graph]