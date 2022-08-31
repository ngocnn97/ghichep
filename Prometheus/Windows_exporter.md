# Cài đặt WINDOWS_EXPORTER

## Client

### Tải windows_exporter

Tải file cài đặt tại địa chỉ [Windows_exporter_github](https://github.com/prometheus-community/windows_exporter/releases)

*Đối với PC chọn đuôi .exe*
*Đối với Server chọn đuôi .msi*

### Cài đặt windows_exporter

- Mở **Command Pormpt**

- Cài đặt bằng dòng lệnh

	- Tổng hợp mặc định:

	```sh
	msiexec /i  C:\Users\User\Downloads\windows_exporter-0.16.0-amd64.msi
	```

	- Tổng hợp các tham số nhất định:

	```sh
	msiexec /i  C:\Users\User\Downloads\windows_exporter-0.16.0-amd64.msi   ENABLED_COLLECTORS=cpu,iis,ad,cs,logical_disk,net,os,service,system,textfile,thermalzone,ad,cache,process,tcp,time,logon,memory,cpu_info,vmware,dfsr,dns,dhcp,mssql,msmq 
	```

- Kiểm tra 

Windows_exporter mặc định chạy trên cổng 9182

`curl http://IP:9182/metrics`

## Server

### Add new scape job

Thêm cấu hình job mới vào file `/etc/prometheus/prometheus.yml`

```sh
scrape_configs:
  # ...
  - job_name: 'window01'
    scrape_interval: 10s
    static_configs:
     - targets: [IP:9182]
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