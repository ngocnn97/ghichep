
# High Availability
Cấu hình HA trên Firewall Fortigate 

## Yêu cầu
- Tất cả Firewall tham gia vao cụm HA phải có cùng: Model phần cứng, firmware, cùng lisense, có heartbeat interface và monitor interface,...

## Mô hình
![mohinh](/images/mohinhhafortigate.png)

## Chế độ
1.	Active-Active (A-A)
	- Cân bẳng tải và dự phòng phần cứng
	- Tất cả các node trong cụm đều chạy ở chế độ active
	- Dùng cho mô hình mạng lớn
2.	Active-Passive (A-A)
	-	Dự phòng phần cứng
	-	1 node chạy chính và 1 node dự phòng
	-	Dùng cho mô hình mạng vừa và nhỏ

## Override
Override quy định cách cụm Fortigate HA bầu chọn Primary node. Override có 2 tùy chọn là disable và enable.
Mặc định khi sau khi cấu hình HA thì tham số override sẽ bị disable

## Cấu hình
Cấu hình trọng số của Primary phải cao hơn trọng số của Backup,
Port Heartbeat là port dùng để đồng bộ cấu hình
1. Primary node:
	![](/images/ha-master-node.png)
2. Backup node:
	![](/images/ha-slave-node.png)

## Khắc phục lỗi
### Out-of-sync, Not sync,...
1.	Mô tả:
	Lỗi các server trong cụm không thể đồng bộ
2.	Nguyên nhân:
	- Các server trong cụm đang có 1 tham số khác nhau
	- Lỗi kết nối heatbeat interface
3.	Khắc phục:
	- Kiểm tra lại kết nối card heatbeat interface
	- Kiểm tra checksum của các server phải trùng nhau: `diag sys ha checksum cluster`
		+ Nếu không trùng nhau check từng vdom bằng lệnh : `diag sys ha checksum show <VDOM_NAME>` Ví dụ: `diag sys ha checksum show global`
		+ Tìm kiếm sự khác nhau giữa các server và tiến hành khắc phục.
		