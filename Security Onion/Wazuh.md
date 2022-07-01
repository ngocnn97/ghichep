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
# docker exec -u 0 -it so-wazuh /var/ossec/bin/agent_control -l

Wazuh agent_control. List of available agents:
   ID: 000, Name: securityonion-wazuh-manager (server), IP: 127.0.0.1, Active/Local
   ID: 001, Name: securityonion, IP: 192.168.189.128, Active
   ID: 002, Name: user, IP: 192.168.189.150, Active

List of agentless devices:

```
User với ID:002 đã chuyển trạng thái từ Nerver connected --> Active

## Tuning Rules
Để tuning rule của Wazuh có 2 phần:

**Decoder**
Bộ giải mã của wazuh trích xuất thông tin các dòng log thành các block để chuẩn bị cho các bước tiếp theo:
Tham khảo: [Wazuh_decoder_syntax](https://documentation.wazuh.com/current/user-manual/ruleset/ruleset-xml-syntax/decoders.html#decoder)

**Rule**
Bộ quy tắc Wazuh sử dụng các block đã được decoder trích xuất ra và áp dụng thêm các tham số như tần suất, đối chiếu,... để đưa ra cảnh báo thích hợp:
Tham khảo [Wazuh_rules_syntax](https://documentation.wazuh.com/current/user-manual/ruleset/ruleset-xml-syntax/rules.html#rule)

> Để thử nghiệm các decoder hoặc rules tự tạo trên Security Onion chạy lệnh: ***sudo docker exec -it so-wazuh /var/ossec/bin/ossec-logtest*** và paste dòng log cần phân tích.

**Thêm Rules trên Security Onion**
Thêm các Rules tự tạo trên Security Onion tại đường dẫn :
```
   /opt/so/rules/hids/local_rules.xml
```
1. Thêm 1 Rule mới dựa trên rule đã có:
   - Tìm rule đã có trong thư mục: 
   ```
      /opt/so/rules/hids/ruleset/rules/
   ```
   - Tạo Rule mới và đặt trong block \<group>  \</group> 
   - Đổi ID và thêm các tham số cần thiết 
   - Cuối cùng khởi động lại Wazuh :
   ```sh
      # sudo  so-wazuh-restart
   ```
   *Ví dụ:*
   ```sh
      <group name="local,syslog,sshd,">
        <rule id="66656" level="10" frequency="5" timeframe="120">
          <if_matched_sid>5710</if_matched_sid>
          <description>sshd: Mutil f*cking atempt to login using a non-existent user</description>
        </rule>
      </group>

   ```
   *Giải thích:*
   - Thêm luật mới với id=66656 dựa trên luật có sẵn với id=5710 với tần suất 5 lần trong 120s thì sẽ đặt cảnh báo ở mức level=10 và xuất cảnh báo: Mutil f*cking atempt to login using a non-existent user
   ![Canhbao1](/images/canhbao1.png)
2. Thêm 1 Rule mới ghi đè lên rule đã có:
   - Tìm rule đã có trong thư mục: 
   ```
      /opt/so/rules/hids/ruleset/rules/
   ```
   - Copy Rule cần ghi đè vào file:
   ```
      /opt/so/rules/hids/local_rules.xml
   ```
   - Đặt Rule vào trong block \<group>  \</group> 
   - Cập nhật lại Rule theo mong muốn và thêm tag **overwrite="yes"** trong \<rule>
   - Cuối cùng khởi động lại Wazuh :
   ```sh
      # sudo  so-wazuh-restart
   ```
   *Ví dụ:*
   ```sh
      <group name="local,syslog,sshd,">
        <rule id="5710" level="7" overwrite="yes">
          <if_matched_sid>5710</if_matched_sid>
          <match>Admin</match>
          <description>sshd: Attack to user Admin from $(srcip)</description>
        </rule>
      </group>
   ```
   *Giải thích:*
   - Lấy ví dụ user Admin chỉ 1 người duy nhất được phép đăng nhập, tất cả các đăng nhập khác vào server bằng tài khoản Admin đều được coi là tấn công. 
   - Wazuh đã có luật id=5710 để phân tích các hành động đăng nhập sai password nhưng với tất cả mọi người dùng, ta sẽ tiến hành ghi đè thêm một số thông tin vào luật 5710 của wazuh để chỉ bắt sự kiện tài khoản admin của server bị đăng nhập sai và cảnh báo IP đang cố đăng nhập.
![Canhbao2](/images/canhbao2.png)

## Gửi mail cảnh báo
Để gửi mail cảnh bác cáo sự kiện Wazuh, chỉnh sửa file: ***/opt/so/conf/wazuh/ossec.conf***:
```
<global>
   <email_notification>yes</email_notification>
   <email_to>{địa_chỉ_email_đích}</email_to>
   <smtp_server>{địa_chỉ_máy_chủ_SMTP}</smtp_server>
   <email_from>{địa_chỉ_email_nguồn}</email_from>
   <email_maxperhour>{số_email_tối_đa_trong_1_giờ}</email_maxperhour>
</global>
<alerts>
    <log_alert_level>1</log_alert_level>
    <email_alert_level>{cấp_độ_rule}</email_alert_level>
  </alerts>

```
- Cấp độ Rule được cấu hình khi tạo Rules ở bước trên và được quy định tại [Rules Classification](https://www.ossec.net/docs/manual/rules-decoders/rule-levels.html)
- Wazuh không xử lý xác thực SMTP. Nếu dịch vụ email sử dụng điều này, cần phải định cấu hình chuyển tiếp máy chủ, tham khảo: [   SMTP server with authentication](https://documentation.wazuh.com/current/user-manual/manager/manual-email-report/smtp-authentication.html#smtp-authentication)

Ví dụ 1 mail gửi khi gặp sự kiện có level=10: ![Mail](/images/mail.png)