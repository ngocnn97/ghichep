#  Security Onion

Security Onion là một bản phân phối Linux mã nguồn mở, tập hợp các công cụ như Wazuh, Zeek, Suricata, CyberChef... để tìm kiếm mối đe dọa, giám sát an ninh mạng và giám sát nhật ký của một hệ thống mạng.

## Cài đặt 
### WMware Workstation

**Mô hình :**
Triển khai Security Onion ở kiến trúc Standalone để thu thập và phân tích log từ 1 client: Ubuntu 20.04

Yêu cầu phần cứng:
![Screenshot](/images/yeucauphancung.png)

Mô hình triển khai:
![Screenshot](/images/mohinhtrienkhai.png)

### VMware ESXi

**Mô hình :**
Triển khai Security Onion ở kiến trúc Standalone để thu thập và phân tích log từ 2 client: Ubuntu 20.04, Win 10 pro

Yêu cầu phần cứng:
![Screenshot](/images/yeucauphancungESXi.png)

Mô hình triển khai:
![Screenshot](/images/mohinhtrienkhaiESXi.png)

1. Cài đặt Security Onion
***Lưu ý:***
***Trong quá trình cài đặt nếu sử dụng chế độ Standard/Direct thì SO cần phải được kết nối Internet ngay trong khi cài đặt với IP đã cấu hình***
- Tải ISO tại địa chỉ: [https://download.securityonion.net/file/securityonion/securityonion-2.3.130-20220607.iso](https://download.securityonion.net/file/securityonion/securityonion-2.3.130-20220607.iso)
- Tạo máy ảo mới trên hệ thống VMware Workstation/ESXi sử dụng image đã tải ở trên theo các bước trong tài liệu: https://docs.securityonion.net/en/2.3/vmware.html
- Khởi động lại máy ảo và cấu hình theo kiến trúc Standalone với địa chỉ IP theo đúng **yêu cầu phần cứng**: https://docs.securityonion.net/en/2.3/first-time-users.html
- Sau khi cấu hình xong và khởi động lại chay lệnh 
`so-status` để kiểm tra tình trạng khởi động của các thành phần trong Security Onion:
![Screenshot](/images/so-status.png)
- Khi tất cả các container khởi động thành công, tiến hành truy cập giao diện GUI tại địa chỉ : https://192.168.189.128 và đăng nhập với email và mật khẩu đã cấu hình trước đó
2. Cài đặt Ubuntu 20.04 
- Tải ISO Ubuntu 20.04 tại địa chỉ:
https://ubuntu.com/download/desktop
- Tạo máy ảo mới đúng với **yêu cầu phần cứng** trên hệ thống VMware Workstation/ESXi sử dụng image đã tải ở trên


