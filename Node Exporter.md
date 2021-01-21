# **NODE EXPORTER**
# *Prometheus Node Exporter là gì ?*
> Github: https://github.com/prometheus/node_exporter

Prometheus Node Exporter là một chương trình exporter viết bằng ngôn ngữ Golang. Exporter là một chương trình được sử dụng với mục đích thu thập, chuyển đổi các metric không ở dạng kiểu dữ liệu chuẩn Prometheus sang chuẩn dữ liệu Prometheus. Sau đấy exporter sẽ expose web service api chứa thông tin các metrics hoặc đẩy về Prometheus.

Node Exporter này sẽ đi thu thập các thông số về máy chủ Linux như : *ram, load, cpu, disk, network,…*. từ đó tổng hợp và xuất ra kênh truy cập các metrics hệ thống này ở port **TCP 9100** để Prometheus đi lấy dữ liệu metric cho việc giám sát.
![](./node%20exporter%20prometheus.PNG)

# *Cài đặt Prometheus Node Exporter*
Cài đặt node_exporter trên cùng một máy chủ prometheus. Ta sẽ theo dõi và lấy số liệu của máy chủ prometheus.

- Đăng nhập vào người dùng prometheus và tải xuống 'node_exporter'
> Download: https://prometheus.io/download/
```sh
su - prometheus
wget https://github.com/prometheus/node_exporter/releases/download/v1.0.1/node_exporter-1.0.1.linux-amd64.tar.gz
```
- Giải nén node exporter và đổi tên thư mục thành 'node_exporter'
```sh
tar -xzvf node_exporter-1.0.1.linux-amd64.tar.gz
mv node_exporter-1.0.1.linux-amd64 node_exporter
```
- Tiếp theo, ta sẽ tạo tệp dịch vụ mới cho node_exporter. Quay lại người dùng root, truy cập thư mục '/ etc / systemd / system' và tạo tệp dịch vụ node_exporter mới 'node_exporter.service'
```sh
cd /etc/systemd/system/
vi node_exporter.service
```
- Dán cấu hình dịch vụ node_exporter bên dưới
```sh
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
ExecStart=/home/prometheus/node_exporter/node_exporter

[Install]
WantedBy=default.target
```
Lưu và thoát.

- Sau đó reload lại systemd và enable node exporter
```sh
systemctl daemon-reload
systemctl start node_exporter
systemctl enable node_exporter
```
- Dịch vụ node_exporter hiện đang chạy trên máy chủ - Kiểm tra trạng thái
```sh
systemctl status node_exporter
```
![](./status%20node_exporter.PNG)
# *Thêm Node exporter vào Prometheus Server*
- Đăng nhập vào người dùng prometheus
```sh
su - prometheus
```
- Chuyển đến thư mục 'prometheus' và chỉnh sửa tệp cấu hình 'prometheus.yml'
```sh
cd prometheus/
vi prometheus.yml
```
- Trong dòng 'scrape_config', thêm job_name node_exporter mới bằng cách sao chép-dán cấu hình bên dưới
```sh
- job_name: 'node_exporter' 
    static_configs: 
      - target: ['localhost: 9100']
```
![](./prometheus.yml.PNG)
Lưu và thoát.
- Khởi động lại dịch vụ Prometheus
```sh
systemctl restart prometheus
```
- Thêm cổng node_exporter 9100 vào firewalld
```sh
firewall-cmd --add-port=9100/tcp --permanent
firewall-cmd --reload
```
- Mở trình duyệt web của bạn và sau đó nhập địa chỉ IP của máy chủ.
```sh
http://IP:9100/metrics
```
Và ta sẽ nhận được dữ liệu của node_exporter dưới dạng Raw
![](./9100%20metrics.PNG)
