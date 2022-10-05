# Error 
***Note:*** 
Các lỗi xảy ra trong quá trình cài đặt và vận hành tham khảo [Github](https://github.com/Security-Onion-Solutions/securityonion/discussions?discussions_q=wazuh+mail)
## Security onion
Các lỗi có thể xảy ra trong quá trình cài đặt và vận hành security onion
## Wazuh
Các lỗi có thể xảy ra trong quá trình cài đặt và vận hành wazuh trên nền tảng security onion

### Lỗi kết nối wazuh agent
- ***Mô tả:***
Lỗi gặp phải khi nhập key và connect wazuh agent với wazuh manager cổng 

- ***Nguyên nhân:***
Chưa mở luật trên firewall, chưa so-allow, phiên bản không tương thích

- ***Khắc phục:***
	- Mở luật firewall
	- So-allow
	- Phiên bản Wazuh_agent <= Wazuh_manager

## Postfix
Các lỗi có thể xảy ra trong quá trình cài đặt và vận hành postfix
### Lỗi network is unreachable
- ***Mô tả:***
Lỗi gặp phải khi gửi mail 

- ***Nguyên nhân:***
Server không có IPv6, cấu hình mynetworks sai

- ***Khắc phục:***
Cấu hình lại 2 dòng trong file `/etc/postfix/main.cf`
```sh
mynetworks = all
inet_protocols = ipv4
```
### Lỗi SASL authentication failed
- ***Mô tả:***
Lỗi không thể xác thực với server smtp

- ***Nguyên nhân:***
Sai tài khoản, mật khẩu
Nếu sử dụng với tài khoản gmail thì cần bật ứng dụng kém an toàn, hoặc sử dụng mật khẩu ứng dụng

- ***Khắc phục:***
Từ ngày 30/5/2022 Gmail đã vô hiệu hóa tùy chọn ứng dụng kém an toàn 
Sử dụng mật khẩu ứng dụng để đăng nhập tham khảo: [Google app pass](https://support.google.com/accounts/answer/185833)

### Lỗi khi gửi mail với reply mail server
- ***Mô tả:***
Lỗi khi Wazuh tiến hành gửi mail thông qua postfix
Xem log gửi mail cũng như toàn bộ log ossec tại : `/nsm/wazuh/logs/ossec.log`

- ***Nguyên nhân:***
Cấu hình sai thông tin gửi mail

- ***Khắc phục:***
Cấu hình chuẩn IP, username,.. trong file `/opt/so/conf/wazuh/ossec.conf`

### Lỗi khi gửi mail với reply mail server (Postfix error: Host or domain name not found)

- ***Mô tả:***

Đã có kết nối internet nhưng Postfix vẫn thông báo là không thể phân giải được domain

- ***Nguyên nhân:***

Là 1 bug của postfix khi sao chép /etc/resolv.copf quá sớm dẫn tới postfix bị lỗi khi phân giải địa domain

- ***Khắc phục:***

Sau khi khởi động lại tiến hành copy lại file /etc/resolv.copf vào phần cấu hình của postfix:

```sh
sudo cp /etc/resolv.conf /var/spool/postfix/
```

```sh
service postfix restart
```