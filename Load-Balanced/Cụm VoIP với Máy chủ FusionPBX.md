### Cụm VoIP với Máy chủ FusionPBX

Kể từ phiên bản 3.6 của FusionPBX, có thể triển khai giải pháp thoại qua IP trên đám mây đáp ứng các đặc điểm tiếp theo:

 - Cân bằng: Các máy chủ phân chia tải giữa chúng tùy thuộc vào các điều kiện mạng khác nhau;

 - Cluster: Máy chủ biết đồng nghiệp của chúng và chúng gửi thông tin đồng bộ cho nhau;

 - Tính sẵn sàng cao: Máy chủ phát hiện khi quả lê bị hỏng và chúng chia tải giữa chúng.

Giải pháp này hoạt động mà không cần Kamailio.

Các dịch vụ của OKay có thể giúp bạn thực hiện loại triển khai này. Chúng tôi giải thích chi tiết.

Trong một cảnh như sau:

 - Một máy chủ FusionPBX ở Buffalo, EUA
 - Một máy chủ FusionPBX ở Luân Đôn, Vương quốc Anh
 - One FusionPBX tại Singapore, Singapore
 - Một máy chủ DNS ở Los Ángeles, EUA
 - Một máy chủ DNS ở Jacksonville, EUA

Các máy chủ chia sẻ cùng một cơ sở dữ liệu và chúng đồng bộ hóa hệ thống tệp của mình một cách thường xuyên.

![image](https://user-images.githubusercontent.com/69178270/141260662-fe805f0d-bd02-4924-833f-8d6ef3d3667e.png)

**Cân bằng tải giữa các nút**

Bằng các thuật toán giám sát, các máy khách được chuyển tiếp đến máy chủ có độ trễ thấp nhất. Giám sát là thụ động và không xâm phạm đến mạng khách hàng.

Theo ví dụ, người dùng từ Vancouver (Canada), Mexico City (Mexico) và Buenos Aires (Argentina) sẽ được chuyển tiếp đến máy chủ ở Búfalo; người dùng từ Roma (Ý) được kết nối với máy chủ ở London (Anh); người dùng ở Sidney (Úc) và Tokio (Nhật Bản) được chuyển tiếp đến máy chủ ở Singapour (Singapour).

Cách tốt nhất để thực hiện việc này là sử dụng Tiện ích bổ sung PowerDNS có độ trễ thấp (Low Latency PowerDNS Add-on). Phần mềm này sẽ sử dụng số liệu thống kê từ việc giám sát để chọn ra máy chủ tốt nhất.

**Clustering và Interconnecting Peers**

Khi một người dùng gọi một người dùng khác được kết nối với cùng một máy chủ, cuộc gọi sẽ xảy ra như một máy chủ triển khai duy nhất. Khi người dùng gọi một người dùng từ xa (đã đăng nhập vào một máy chủ khác), FusionPBX sẽ phát hiện một người dùng không phải là người cục bộ và tạo kết nối với máy chủ đích.

![image](https://user-images.githubusercontent.com/69178270/141261021-3cf9c149-0b9a-40f6-b3fb-ac098ff377b5.png)

Trong ví dụ này, một người dùng ở Vancouver gọi một người dùng trong Tokio. PBX ở Buffalo kết nối với máy chủ ở Singapore.

**Các điểm cuối chịu lỗi và chuyển hướng**

Khả năng chịu lỗi trong kiểu triển khai này xảy ra khi máy chủ FusionPBX bị lỗi; người dùng được chuyển tiếp trong vài phút tới máy chủ tốt thứ hai.

![image](https://user-images.githubusercontent.com/69178270/141261252-72b835ca-b0ef-4ca1-9c61-26529f9ab752.png)

Trong trường hợp này, khi máy chủ ở London bị lỗi, người dùng ở Roma sẽ được chuyển tiếp đến máy chủ ở Buffalo.

Hiệu ứng này được lưu trữ nhờ theo dõi và tích hợp DNS thông minh.

![image](https://user-images.githubusercontent.com/69178270/141261292-e5a16d7e-4384-4281-b699-945dc3f8b57e.png)

Tính khả dụng của máy chủ thoại luôn được giám sát. Khi có sự cố, các máy chủ DNS được cập nhật.
