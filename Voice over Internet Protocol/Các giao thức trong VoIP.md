### Các giao thức trong VoIP

### Mục lục

[Giao Thức H.323](#1)

[]

### <a name="1"> Giao Thức H.323 </a>

**Tổng quan về giao thức H.323**

H.323 là giao thức được phát triển bởi ITU-T. H.323 ban đầu được sử dụng cho mục đích truyền các cuộc hội thoại đa phương tiện trên các mạng LAN, nhưng sau đó H.323 đã phát triển thành 1 giao thức truyền tải VoIP trên thế giới. H.323 là một tập giao thức, gồm các giao thức chính:
 + H.225: là giao thức báo hiệu thiết lập và giải tỏa cuộc gọi.
 + H.245: là giao thức điều khiển cho phép các đầu cuối thỏa hiệp kênh
và trao đổi khả năng của chúng.
 + H.235: công cụ bảo mật hỗ trợ cho H.323.

**Các thành phần chính trong mạng H.323**

Tiêu chuẩn H.323 đề nghị một cấu trúc mà bao gồm 4 thành phần: đầu cuối, Gateway, Gatekeeper, và đơn vị điều khiển đa điểm MCU (Multipoint Control Unit). Cấu trúc này được mô tả như trong hình sau:

_Cấu trúc của H.323_

![image](https://user-images.githubusercontent.com/69178270/137652963-c5e41a1d-24a7-4f3a-8e1d-6b8160b31592.png)

**Đầu cuối (terminal)**

Đây là một điểm cuối khác của LAN cung cấp thông tin thời gian thực,
hai chiều. Tất cả các đầu cuối H.323 đều yêu cầu hỗ trợ H.245, H.225, Q.931,
trạng thái công nhận đăng kí RAS (Registration Admission Status) và các
giao thức truyền thời gian thực RTP (real-time transport protocol). H.245
được dùng để điều khiển việc sử dụng kênh, trong khi H.225 hoặc Q.931
được dùng cho báo hiệu cuộc gọi, thiết lập và xóa cuộc gọi.

RTP được dùng như là một giao thức truyền dẫn mang thông tin lưu
thoại. RAS được sử dụng bởi điểm cuối để tương tác với gatekeeper. Một đầu
cuối H.323 có thể truyền thông với một đầu cuối H.323 khác, một gateway
H.323 hoặc một MCU.
