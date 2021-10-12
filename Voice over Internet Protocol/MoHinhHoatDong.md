### Các mô hình và cách hoạt động hệ thống điện thoại voip

### Mục Lục

[Mô hình hệ thống điện thoại voip](#1)

[Mô hình hệ thống điện thoại IP được tích hợp với hệ thống điện thoại truyền thống analog](#2)

Hệ thống điện thoại voip là giải pháp viễn thông đang được nhiều doanh nghiệp lựa chọn và áp dụng vào hoạt động kinh doanh. 
Với nhiều tính năng nâng cao lưu trữ dữ liệu lớn, truyền tải thông tin nhanh chóng, dễ dàng mở rộng.

![image](https://user-images.githubusercontent.com/69178270/136925042-abd9cfb1-a95a-40f6-bb8b-9a176b2e66fc.png)

### <a name="1"> Mô hình hệ thống điện thoại voip </a>

Hệ thống điện thoại IP là tập hợp bao gồm tổng đài IP và thiết bị điện thoại hoạt động dựa trên công nghệ IP chạy trên nền tảng internet. Tổng đài IP sử dụng chung hạ tầng mạng của chính doanh nghiệp như LAN/WAN, hoạt động bằng cách mã hoá giọng thành các gói dữ liệu và truyền đi cũng với các dữ liệu khác trong mạng nội bộ.

Có những doanh nghiệp có nhiều chi nhánh, hệ thống cửa hàng, các bộ phận, phòng ban làm việc ở những địa điểm, khu vực khác nhau và khi ứng dụng hệ thống điện thoại IP doanh nghiệp sẽ nhận được những lợi ích sau:

 - Thống kê, báo cáo số liệu dịch vụ
 - Quản lý danh bạ, cuộc gọi
 - Ghi âm cuộc gọi
 - Khi thiết lập giao thức SIP trên điện thoại di động, thì thiết bị di động có thể sử dụng đầu số điện thoại bàn để gọi ra hoặc gọi miễn phí vào số nội bộ của công ty
 - Giữ hoặc chuyển cuộc gọi đường dài, liên tỉnh
 - Các chi nhánh có thể thực hiện cuộc gọi miễn phí
 - Chức năng lời chào hoặc dẫn máy khi khác hàng gọi vào công ty\
 - Thực hiện được nhiều cuộc gọi cùng lúc.

<a> **Mô hình giải pháp tổng đài** </a>

![image](https://user-images.githubusercontent.com/69178270/136926195-f3236c14-1104-4e9f-b873-0d68ab4058c2.png)

 - Tổng đài và router sẽ được kết nối giúp định tuyến cuộc gọi
 - Điện thoại IP sẽ được kết nối với gateway, sw để kết nối đến router và máy chủ (server)
 - Bên cạnh đó, có thể cung cấp software được cài trên máy tính để nhân viên sử dụng như điện thoại bàn thông thường.
 - Các chi nhánh khi thực hiện cuộc gọi chỉ cần connect qua địa chỉ IP mà không cần kết nối qua máy chủ.

<a> **Thành phần hệ thống** </a>

 - Thiết bị voice gateway: được hiểu là thiết bị trung gian giao tiếp giữa tín hiệu PSTN và server của tổng đài, có khả năng giao tiếp giữa tổng đài voip và thiết bị analog. Bên cạnh đó, không giới hạn số lượng voip gateway với máy chủ tổng đài thông qua kết nối IP. 

 - Thiết bị đầu cuối: tuỳ vào nhu cầu sử dụng của doanh nghiệp mà lựa chọn các thiết bị khác nhau. Có thể là điện thoại analog sẵn có, điện thoại ip, phần mềm software được sử dụng trên máy tính, điện thoại bàn không dây sử dụng tai nghe và micro.

 - Sever tổng đài VOIP: máy chủ chạy trên hệ điều hành window hoặc linux được tích hợp các ứng dụng giám sát, quản lý và thiết lập kết nối với các thiết bị đầu cuối. Được xem là 
trung tâm quản lý các cuộc gọi ra vào của hệ thống, lưu lượng, tình trạng hệ thống và xuất ra báo cáo.

<a> **Cách thức hoạt động** </a>

 - Tại trụ sở chính của doanh nghiệp

      * Máy chủ voip sẽ được đặt ngay tại trung của doanh nghiệp hoặc nơi được gọi là datacenter, có vai trò quản lý, giám sát tình trạng, cuộc gọi nội bộ giữa các chi nhánh. Bên cạnh đó, máy chủ sẽ cung cấp các tài khoản SIP cho tất cả FXS gateway hoặc điện thoại voip tại các chi nhánh và kết nối từ xa qua đường mạng internet cáp quang

      * Quản lý hệ thống thông qua giao diện web, bảo trì các thiết bị một cách nhanh chóng.

### <a name="2"> Mô hình hệ thống điện thoại IP được tích hợp với hệ thống điện thoại truyền thống analog </a>