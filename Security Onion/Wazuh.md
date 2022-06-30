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
**(todo)*
## Đăng ký Agent
Trong phần này sẽ hướng dẫn cách register để giám sát các agent :

**Trên Manager server** : 

 - Chạy lệnh `manage_agents` :
```sh
# sudo so-wazuh-agent-manage

****************************************
* Wazuh v3.13.1 Agent manager.            *
* The following options are available: *
****************************************
   (A)dd an agent (A).
   (E)xtract key for an agent (E).
   (L)ist already added agents (L).
   (R)emove an agent (R).
   (Q)uit.
Choose your action: A,E,L,R or Q:
```

 - Chọn `A` để thêm 1 agent. Điền `hostname` và `IP` cho agent :
```sh
Choose your action: A,E,L,R or Q: A

- Adding a new agent (use '\q' to return to the main menu).
  Please provide the following:
   * A name for the new agent: user
   * The IP Address of the new agent: 192.168.189.150
   * An ID for the new agent[001]:
Agent information:
   ID:002

Confirm adding it?(y/n): y
Agent added with ID 002.
```

 - Chọn `E` để tạo ra 1 key mới :
```sh
****************************************
* Wazuh v3.13.1 Agent manager.            *
* The following options are available: *
****************************************
Choose your action: A,E,L,R or Q: E

Available agents:
   ID: 001, Name: securityonion, IP: 192.168.189.128
   ID: 002, Name: user, IP: 192.168.189.150
Provide the ID of the agent to extract the key (or '\q' to quit): 002

Agent key information for '002' is:
MDAyIHVzZXIgMTkyLjE2OC4xODkuMTUwIGI4NmJiYzg5MTdiZTVlNjcwMWQ2ZjAxZDhkYTdkMDI5NWM0ZjI3MWFjNjczZDI0NTgzY2FhYmFjMzg0NTFiNmE=
```

 - Exit với `Q`

- Chỉnh sửa IP của server Manager tại file `/var/ossec/etc/ossec.conf` : 
```sh
<client>
      <server-ip>192.168.189.150</server-ip>
</client>
```

 - Khởi động agent :
```sh
#  systemctl enable wazuh-agent
#  systemctl start wazuh-agent
```
- Kiểm tra tiến trình wazuh-agnet
```sh
#  systemctl status wazuh-agent
```

## Kết nối agent

**Trên Manager server** : 
- kiểm tra thông tin kết nối của tất cả các agent:
```sh
# docker exec -u 0 -it so-wazuh /var/ossec/bin/agent_control -l

Wazuh agent_control. List of available agents:
   ID: 000, Name: securityonion-wazuh-manager (server), IP: 127.0.0.1, Active/Local
   ID: 001, Name: securityonion, IP: 192.168.189.128, Active
   ID: 002, Name: user, IP: 192.168.189.150, Never connected

List of agentless devices:
```
User với ID: 002 hiện đang không kết nối do chưa mở luật
- Để mở kết nối cho User tại địa chỉ IP 192.168.189.150:
```sh
# so-allow -w -i 192.168.189.150
Adding 192.168.189.150 to the wazuh_agent role. This can take a few seconds...
```
- Kiểm tra lại kết nối tới User với ID: 002 
```sh
]# docker exec -u 0 -it so-wazuh /var/ossec/bin/agent_control -l

Wazuh agent_control. List of available agents:
   ID: 000, Name: securityonion-wazuh-manager (server), IP: 127.0.0.1, Active/Local
   ID: 001, Name: securityonion, IP: 192.168.189.128, Active
   ID: 002, Name: user, IP: 192.168.189.150, Active

List of agentless devices:

```
User với ID:002 đã chuyển trạng thái từ Nerver connected --> Active