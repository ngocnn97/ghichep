# Wazuh
## Cấu hình Wazuh agent 
### Cài đặt Wazuh Agent trên hệ điều hành Debian/Ubuntu

 - Cài đặt các gói bổ trợ :
```sh
apt-get install curl apt-transport-https lsb-release -y
```

 - Thêm Wazuh repo  :
```sh
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | apt-key add -
```

 - Tìm tên của OS và thêm vào repo
```sh
echo "deb https://packages.wazuh.com/3.x/apt/ stable main" | tee /etc/apt/sources.list.d/wazuh.list
```

 - Update thông tin package
```sh
apt-get update -y
```

 - Install Wazuh agent
```sh
apt-get install wazuh-agent -y
```
### Cài đặt Wazuh Agent trên hệ điều hành Windows
- Dowload package từ source : https://documentation.wazuh.com/current/installation-guide/packages-list/index.html
  
 - **Cách 1**: Sử dụng cmd để cài đặt :
```sh
wazuh-agent-3.6.1-1.msi /q
```

 - **Cách 2** : Sử dụng GUI : Double-click vào file dowload và cài đặt với mặc định. Một khi cài đặt xong, agent sẽ có giao diện đồ họa để cấu hình, mở log file hoặc start/stop service :

![Screenshot](/images/wazuh-agent-windows.png)
 
Mặc định tất cả file agent được đặt tại : `C:\Program Files(x86)\ossec-agent`
### Cài đặt Wazuh Agent trên hệ điều hành MacOS
