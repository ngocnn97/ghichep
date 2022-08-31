# Prometheus

![Logo](/images/prometheus-logo.png)

[Github-Prometheus](https://github.com/prometheus/prometheus/)

## Tổng quan về Prometheus

**Prometheus** là một bộ công cụ giám sát và cảnh báo hệ thống mã nguồn mở được phát triển bởi **SoundCloud**.

**Prometheus** có khả năng thu thập các thông số/số liệu (**metric**) từ các mục tiêu được cấu hình theo các khoảng thời gian nhất định, đánh giá các biểu thức quy tắc, hiển thị và kích hoạt các cảnh báo nếu một số điều kiện thỏa mãn yêu cầu. 

### Một số tính năng của Prometheus
- Mô hình dữ liệu  chiều
- Ngôn ngữ truy vấn linh hoạt
- Hỗ trợ nhiều chế độ biểu đồ
- Nhiều chương trình tích hợp và hỗ trợ bên thứ 3
- Hoạt động cảnh báo linh hoạt, dễ dàng cấu hình
- Mô hình đơn giản 

### Thành phần 
- [Prometheus server](https://github.com/prometheus/prometheus) phục vụ cho việc tổng hợp (scraped) và lưu trữ dữ liệu time series.
- [Client libraties](https://prometheus.io/docs/instrumenting/clientlibs/) phục vụ tạo các đoạn mã ứng dụng tính toán cho dịch vụ monitor.
- [Push Gateway](https://github.com/prometheus/pushgateway) tạo các metrics từ *short-lived job* cho Prometheus, được coi như là *metric cache*. 
- [GUI-based dashboard](https://prometheus.io/docs/visualization/promdash/) được code bằng Rails và lấy dữ liệu từ SQL.
- [Exporters](https://prometheus.io/docs/instrumenting/exporters/) giúp cho việc tạo ra các metric từ hệ thống bên thứ 3 như Database, Storage, Hardware...
- [Alertmanager](https://github.com/prometheus/alertmanager) đưa ra các cảnh báo như mail...
- Command-line query tool.
- Các công cụ hỗ trợ khác...

### Kiến trúc

![Kientruc_Prometheus](/images/kientruc-prometheus.png)

Cách hoạt động:
- Các jobs được phân chia thành short-lived và long-lived jobs/Exporter.

Short-lived là những job sẽ tồn tại trong thời gian ngắn và prometheus-server sẽ không kịp scrapes metrics của các jobs này. Do đó, những short-lived jobs sẽ push (đẩy) các metrics đến một nơi gọi là Pushgateway. Pushgateway sẽ là nơi sẽ phơi bày metrics ra và prometheus-server sẽ lấy được metrics của short-lived thông qua Pushgateway.

Long-lived jobs/Exporter: Là những job sẽ tồn tại lâu dài. Các Exporter sẽ được chạy như dưới dạng 1 service. Exporter sẽ có nhiệm vụ thu thập metrics và phơi bày metrics đấy ra. Prometheus-server sẽ scrapes được metrics thông qua hành động pull (kéo).

- Prometheus-server **scrapes** metrics từ các jobs. Sau đó, nó sẽ lưu trữ các metrics này vào Database. (Lưu trữ trong thư mục data). Prometheus sử dụng kiểu time-series database (arrays of numbers indexed by time). Dựa vào các rules mà ta quy định, (ví dụ như khi cpu xử lý hơn 80%) thì prometheus-server sẽ push (đẩy) cảnh báo đến thành phần Alertmanager.

- PromDash, Grafana,.. dùng câu lệnh querying (PromQL - Prometheus Query Language) để lấy được thông tin metrics lưu trữ ở Prometheus-server và trình diễn.

- Alertmanager sẽ được cấu hình các thông tin cần thiết để có thể gửi cảnh bảo đến email, slack,.... Sau khi prometheus-server push alerts đến alertmanager, alertmanager sẽ gửi cảnh báo đến người dùng.

### Exporter

- Các phần mềm thứ 3 mà không hỗ trợ `Prometheus metrics natively` thì có thể được monitor bằng exporters.
- Exporter có thể thu thập số liệu thống kê và số liệu hiện có, và chuyển đổi chúng sang các số liệu Prometheus.
- Exporter giống như một dịch vụ instrumented, phơi bày những số liệu thông qua một thiết bị đầu cuối, và có thể được `scaped` bởi Prometheus
- Một số Exporter do Prometheus hỗ trợ như:
  - MySQL
  - Haproxy

### Pushgateway

- Pushgateway được sử dụng trong trường hợp mà Promethes server không thể scrape metrics một cách trực tiếp. Có thể là các job chỉ tồn tại trontg thời gian ngắn mà Promethes server chưa kịp scrape metrics.

- Để giải quyết vấn đề này, thì Pushgateway được ra đời. Pushgateway sẽ đóng vai trò trung gian giữa promethes server và targets
cần monitor. Lúc này, metrics sẽ được phơi bay ở pushgateway, chứ không pải là ở targets nữa.

- Trên targets, short-live job sẽ được cấu hình để push metrics đến Pushgateway (có thể sử dụng bằng lệnh curl để push metrics). Rồi từ đó, Prometheus server sẽ scrape (pull) metrics ở Pushgateway về và lưu trữ trên server.
- Pushgateway sẽ không lưu trữ metrics lâu dài. Nó chỉ lưu trữ tạm thời mà thôi. Khi metrics có values mới, nó sẽ thay thế values cũ.


#Tham khảo: 

[1]- https://github.com/hocchudong

[2]- https://prometheus.io/docs/introduction/overview/