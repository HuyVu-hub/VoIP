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

**Gateway**

Là cầu nối giữa mạng H.323 với các mạng khác như SIP, PSTN,…
Gateway đóng vai trò chuyển đổi các giao thức trong việc thiết lập và kết thúc
các cuộc gọi, chuyển đổi các định dạng dữ liệu giữa các mạng khác nhau.
Chức năng phần mềm của gateway được chia làm 4 module như hình dưới:

_Kiến trúc phần mềm trong GK_

![image](https://user-images.githubusercontent.com/69178270/137653093-cc22e4fa-4d12-4d61-9f7c-68e821e677c4.png)

- Đóng gói thoại(voice packet module): thực hiện chức năng nhận ra tín
hiệu điện của thoại, loại bỏ tiếng vọng, loại bỏ jitter, nén thoại, đồng bộ đồng
hồ và đóng gói thoại.
- Báo hiệu điện thoại(telephony signaling module): giao tiếp với điện
thoại, chuyển các chỉ thị báo hiệu thành các thay đổi trạng thái mà giao thức
mạng có thể hiểu được.
- Giao thức mạng(network protocol module): chuyển giao thức báo
hiệu trong mạng điện thoại thành các giao thức báo hiệu trong mạng gói.
- Quản lý mạng(network management module): quản lý mạng bằng
SNMP (Simple Network Management Protocol).

**Gatekeeper**

Đây là một thành phần quan trọng trong cấu trúc của H.323 và có chức
năng quản lý. Nó là điểm chung tâm cho tất cả các cuộc gọi trong vùng của nó
và cung cấp các dịch vụ tới các điểm cuối. Một vùng là sự tập hợp của
gatekeeper và các điểm cuối. Nếu mạng tồn tại nhiều GK thì sẽ được thiết lập
thành nhiều vùng và mỗi vùng sẽ do một GK quản lý. Việc thông tin giữa các
GK sẽ được thực hiện thông qua các bản tin giao tiếp xác định vị trí đầu cuối
trong quá trình thiết lập cuộc gọi. Tuy nhiên GK là một thành phần tùy chọn
trong cấu trúc của H.323

Cấu trúc vùng được quản lý bởi gatekeeper được trình bày trong hình
sau:

_Vùng gatekeeper_

![image](https://user-images.githubusercontent.com/69178270/137653175-e11c7119-92ed-4d26-8053-0b2b0bfb321c.png)

Nếu gatekeeper có mặt trong hệ thống H.323 thì nó thực hiện các
nhiệm vụ sau:

Dịch địa chỉ: Cho phép dịch các quy ước, các ký hiệu, các địa chỉ
“email” thành địa chỉ IP để thiết lập liên lạc IP.

Điều chỉnh công nhận (AC): sự truy cập của các đầu cuối có thể được
chấp nhận hoặc từ chối dựa vào việc xác nhận địa chỉ nguồn hoặc địa chỉ đích
thời gian hoặc bất kỳ biến số nào mà gatekeeper quản lý.

Quản lý cuộc gọi: Gatekeeper hoạt động như một điểm liên lạc ban đầu
cho người gọi, cho hai Gateway hoặc cho hai điểm cuối báo hiệu trực tiếp cho
nhau.

Quản lý băng thông: Gatekeeper có thể yêu cầu các đầu cuối và
Gateway thay đổi các thông số truyền thông cuộc gọi để quản lý sử dụng băng
thông.

Quản lý vùng: Gatekeeper có thể yêu cầu không quá một số lượng cuộc
gọi nào đó qua kết nối có dải tần thấp để tránh giảm sút về chất lượng.

**Đơn vị điều khiển đa điểm MCU**

MCU là thiết bị hỗ trợ việc hội thoại đa điểm cho ba hoặc nhiều hơn ba
đầu cuối trong mạng H.323. Một MCU gồm 2 phần: MC (Multipoint
Controller) là thành phần bắt buộc và MP (Multipoint Processor) là thành
phần tùy chọn.

Chức năng của MC là quyết định dung lượng chung của các kết cuối,
có thể định vị đầu cuối, Gateway hoặc Gatekeeper.

MP nhận các luồng dữ liệu audio, video và phân phối chúng tới các
điểm cuối tham dự vào kết nối đa điểm. MP có thể không cần đến nhưng sự
vắng mặt của nó là một gánh nặng trên đầu cuối.

**H.225**

H.225 bao gồm các bản tin RAS và Q.931. Các bản tin RAS liên quan
đến việc quản lý user, còn Q.931 mang phần báo hiệu cuộc gọi. Cả hai giao
thức dùng kênh kết nối riêng là kênh RAS và kênh báo hiệu cuộc gọi.
