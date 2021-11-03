# Mục Lục

&ensp;[1. Zabbix là gì ?](#1)

&ensp;[2. Ưu/nhược điểm của Zabbix](#2)

&ensp;[2.1 Ưu điểm của dịch vụ Zabbix](#2.1)

&ensp;[2.2 Nhược điểm của dịch vụ Zabbix](#2.2)

&ensp;[3. Yêu cầu phần cứng](#3)

&ensp;[4. Các thành phần của hệ thống giám sát Zabbix](#4)

![image](https://user-images.githubusercontent.com/55483458/140005593-def39247-1b70-488b-9bdc-ecea7f8b2ad3.png)

# <a name="1">1. Zabbix là gì ?</a>

Zabbix là một giải pháp giám sát dịch vụ hệ thống mạng phân tán mã nguồn mở nổi tiếng, có nhiều tính năng độc đáo và khả năng tùy biến cao. Zabbix có khả năng phục vụ cho hệ thống mạng tầm trung và lớn của các doanh nghiệp hiện tại với mức chi phí đầu tư vừa phải.

Zabbix được sáng lập bởi Alexei Vladishev và hiện tại được phát triển cũng như hỗ trợ bởi tổ chức Zabbix SIA. Zabbix được viết và phát hành dưới bản quyền General Public License GPL phiên bản 2. Zabbix sử dụng các cơ chế thông báo vấn đề linh hoạt cho quản trị viên như email, sms, OTT App,.. Zabbix cũng cung cấp báo cáo và dữ liệu cực kì chính xác dựa trên cơ sở dữ liệu đã thu thập được từ thiết bị mạng.

Tất cả báo cáo, thống kê cũng như cấu hình thông số của Zabbix có thể dễ dàng truy cập qua giao diện web tinh tế đẹp mắt. Giúp chúng ta theo dõi được tình trạng hệ thống thiết bị server, dịch vụ,..

![image](https://user-images.githubusercontent.com/55483458/140006082-0b4a2287-b5a0-4b5d-8cb4-1e7831d22f67.png)


# <a name="2">2. Ưu/nhược điểm của Zabbix</a>

## <a name="2.1">2.1 Ưu điểm của dịch vụ Zabbix</a>

Zabbix có các ưu điểm sau mà bạn không nên bỏ qua :

- Tự động tìm phát hiện server và hệ thống mạng
- Hỗ trợ server cài đặt trên dòng hệ điều hành Unix/Linux.
- Hỗ trợ máy trạm client nhiều hệ điều hành.
- Giao diện web cực kì tinh tế và đẹp mắt.
- Có thông báo sự cố qua email hoặc OTP App.
- Có báo cáo, biểu đồ qua giao diện web đẹp mắt.
- Kiểm tra theo dõi việc đăng nhập.
- Linh động trong việc phân quyền người sử dụng.
- Mã nguồn mở, chi phí đầu tư thấp.
- Nhiều plugin hỗ trợ cho các dịch vụ hệ thống khác nhau.

![image](https://user-images.githubusercontent.com/55483458/140006104-016ad272-475f-4e35-be7d-39b3793632cc.png)

## <a name="2.2">2.2 Nhược điểm của dịch vụ Zabbix</a>

- Không có giao diện web mobile hỗ trợ.
- Không phù hợp với hệ thống mạng lớn hơn 1000+ node thiết bị client cần giám sát. Lúc này phát sinh vấn đề hiệu suất về PHP và Database.
- hiết kế template/alerting rule đôi khi khá phức tạp.

![image](https://user-images.githubusercontent.com/55483458/140006125-b03ce375-c90b-4910-a2ee-9ae8dd720c4b.png)

# <a name="3">3. Yêu cầu phần cứng</a>
  
Tuỳ theo số lượng máy chủ hoặc thiết bị mạng cần giám sát mà ta sẽ có các mức cấu hình phần cứng phù hợp cho dịch vụ Zabbix Server. Theo cá nhân mình thì phần cứng tối thiểu sẽ gồm :

- CPU : 2 core
- RAM : 1GB
- Disk : 50GB
- zabbix hardware requirement

# <a name="4">4. Các thành phần của hệ thống giám sát Zabbix</a>

Zabbix Server: Đây là ứng dụng chương trình dịch vụ chính của dịch vụ Zabbix. Zabbix Server sẽ chịu trách nhiệm cho các hoạt động kiểm tra dịch vụ mạng từ xa, thu thập thông tin, lưu trữ, hiển thị, cảnh báo,… từ đó các quản trị viên có thể thao tác giám sát hệ thống tốt nhất.

![image](https://user-images.githubusercontent.com/55483458/140006152-f1f22de9-c418-4c47-b982-6fa2310c147c.png)

Zabbix Proxy: là một máy chủ được dùng cho việc quản lý nhiều nhánh hệ thống ở xa, hoặc ở các lớp mạng khác nhau. Từ Zabbix Proxy sẽ thu thập các thông tin thiết bị mạng rồi chuyển tiếp về cho máy chủ dịch vụ chính Zabbix Server.

![image](https://user-images.githubusercontent.com/55483458/140006180-e447bc9e-2a31-4df7-9de7-5d73c8845efe.png)

Zabbix Agent: zabbix agent là chương trình zabbix dùng để cài đặt lên các máy chủ hoặc thiết bị phía client. Từ đó hỗ trợ kết nối từ Zabbix Server để lấy các thông tin cần thiết từ client nhằm kiểm tra các tình trạng hệ thống hoặc theo nhu cầu quản trị viên.

![image](https://user-images.githubusercontent.com/55483458/140006191-310379a1-32b9-4026-b005-0d5892a94739.png)

Giao diện web: cung cấp giao diện web trên nền tảng mã nguồn PHP cùng phong cách metro tinh tế. Hiện tại có thể xem Zabbix là một trong những ứng dụng có giao diện đẹp nhất, thiết kế vị trí tính năng bắt mắt và hợp lý.
zabbix web interface.

![image](https://user-images.githubusercontent.com/55483458/140006219-f44117b5-3a92-464c-9984-15af430ab434.png)







