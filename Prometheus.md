# **PROMETHEUS**
# *Prometheus là gì*
Trang chủ: https://prometheus.io/
> Prometheus, một dự án Cloud Native Computing Foundation , là một hệ thống giám sát dịch vụ và hệ thống. Nó thu thập các chỉ số từ các mục tiêu đã định cấu hình trong các khoảng thời gian nhất định, đánh giá các biểu thức quy tắc, hiển thị kết quả và có thể kích hoạt cảnh báo khi quan sát thấy các điều kiện cụ thể.
### Một số tính năng của Prometheus:
* Mô hình dữ liệu đa chiều – time series được xác định bởi tên của số liệu (metric) và các cặp khóa – giá trị (key/value).
* Ngôn ngữ truy vấn linh hoạt.
* Hỗ trợ nhiều chế độ biểu đồ.
* Nhiều chương trình tích hợp và hỗ trợ bởi bên thứ 3.
* Hoạt động cảnh báo vấn đề linh động dễ cấu hình.
* Chỉ cần 1 máy chủ là có thể hoạt động được.
* Hỗ trợ Push các time series thông qua một gateway trung gian.
* Các máy chủ/thiết bị giám sát có thể được phát hiện thông qua service discovery hoặc cấu hình tĩnh.
# *Tổng quan kiến trúc*
![Kiến trúc](./Overview.PNG)
Prometheus thực hiện quá trình lấy các thông số/số liệu (metric) từ các job được chỉ định qua kênh trực tiếp hoặc thông qua dịch vụ Pushgateway trung gian. Sau đấy Prometheus sẽ lưu trữ các dữ liệu thu thập được ở local máy chủ. Tiếp đến sẽ chạy các rule để xử lý các dữ liệu theo nhu cầu cũng như kiểm tra thực hiện các cảnh báo mà bạn mong muốn.
# *Cài đặt*
Có 2 cách để cài đặt Prometheus:
#### Cách 1: Cài đặt Prometheus bằng Docker
#### Cách 2: Cài đặt Prometheus bằng Packge
# *Cài đặt Prometheus bằng Packge trên Linux*
- **Chạy lệnh update:**

```sh 
yum update -y
yum install wget
yum install netstat
```

Tắt SELINUX

```sh
vim /etc/sysconfig/selinux
```

> *Change “SELINUX=enforng” to “SELINUX=disabled”.*

Save và exit file. Sau đó reboot lại server.

- **Tạo người dùng và tải Prometheus:**

Tạo người dùng mới **"Prometheus"** bằng lệnh:

```sh
useradd -m -s /bin/bash prometheus
```

Đăng nhập vào người dùng vừa tạo và tải prometheus bằng lệnh wget

> Download here: https://prometheus.io/download/

```sh
su - prometheus
wget https://github.com/prometheus/prometheus/releases/download/v2.24.0/prometheus-2.24.0.linux-amd64.tar.gz
```

Giải nén tệp và đổi tên thư mục thành "prometheus"

```sh
tar -xzvf prometheus-2.24.0.linux-amd64.tar.gz

mv prometheus-2.24.0.linux-amd64/ prometheus/
```

> Prometheus được tải xuống máy chủ và tất cả các tệp prometheus đều nằm trên thư mục '/ home / prometheus / prometheus'.

- **Cấu hình Prometheus:**

Đi tới thư mục '/etc/systemd/system/' và tạo tệp dịch vụ mới 'prometheus.service'

```sh
cd /etc/systemd/system/

vi prometheus.service
```
Nội dung file:

```sh
[Unit]
Description=Prometheus 
Wants=network-online.target 
After=network-online.target 

[Service]
User=root
Group=root
Type=simple

ExecStart=/usr/local/prometheus/prometheus \
--config.file /usr/local/prometheus/prometheus.yml \
--storage.tsdb.path /usr/local/prometheus/ \
--web.console.templates=/usr/local/prometheus/consoles \
--web.console.libraries=/usr/local/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```
Lưu và thoát ra.

Khởi động dịch vụ prometheus và cho phép nó khởi chạy mọi lúc khi khởi động hệ thống.

```sh
systemctl daemon-reload

systemctl start prometheus

systemctl enable prometheus
```

Kiểm tra trạng thái Prometheus bằng lệnh dưới.

```sh
systemctl status prometheus
```

![](./status%20prometheus.PNG)

Đảm bảo rằng đã được **Active**

Hoặc kiểm tra nó bằng lệnh netstat

```sh
netstat -plntu
```

![](./netstat%20prome.PNG)

Prometheus đang chạy theo mặc định dưới cổng '9090'.

Mở cổng 9090 vào cổng danh sách Firewalld

```sh
firewall-cmd --add-port=9090/tcp --permanent

firewall-cmd --reload
```

Mở trình duyệt web và nhập địa chỉ IP của máy chủ với cổng 9090

http://IP:9090/graph

![](./9090.PNG)

# *Cài đặt Grafana*

> Download here: https://grafana.com/grafana/download
```sh
wget https://dl.grafana.com/oss/release/grafana-7.3.7-1.x86_64.rpm
sudo yum localinstall grafana-7.3.7-1.x86_64.rpm
sudo service grafana-server start
sudo /sbin/chkconfig --add grafana-server
systemctl daemon-reload
systemctl start grafana-server
systemctl status grafana-server
sudo systemctl enable grafana-server.service
```
![](./status%20grafana.PNG)
Truy cập vào Grafana: http://IP:3000
![](./grafana%20login.PNG)
