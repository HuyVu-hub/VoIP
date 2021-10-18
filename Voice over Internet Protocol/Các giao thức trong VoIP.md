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

**1. Đầu cuối (terminal)**

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

**2. Gateway**

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

**3. Gatekeeper**

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

**4. Đơn vị điều khiển đa điểm MCU**

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

**1. Bản tin RAS(Registration, Admission, Status)**

Chức năng chính của các bản tin RAS:
- EP(endpoint) phát hiện ra GK mà chúng sẽ phải đăng ký.
- EP đăng ký với GK của nó.
- EP phải yêu cầu sự cho phép của GK khi khởi tạo một cuộc gọi.
- EP yêu cầu giải phóng cuộc gọi.
- Trước khi ngắt kết nối với GK, EP phải ngắt đăng ký.
Bản tin RAS được gửi đi bằng giao thức vận chuyển UDP. EP và GK
trao đổi thông tin trên kênh RAS theo dạng client-server. 

_Các bản tin RAS_

![image](https://user-images.githubusercontent.com/69178270/137660041-e7b2eeac-9dab-472c-85e8-69a0ff56284b.png)

**2. Q.931**

Q.931 là khuyến nghị của ITU-T cho báo hiệu cuộc gọi, làm chức năng
thiết lập, duy trì và kết thúc cuộc gọi. Bản tin Q.931 được vận chuyển bằng
giao thức TCP. EP sẽ thương lượng lắng nghe trên port nào. Quá trình thỏa
thuận này được thực hiện bằng các bản tin RAS (trong call Admission), port
1720 thường được chọn.

_Các loại bản tin Q.931_

![image](https://user-images.githubusercontent.com/69178270/137660137-9dcbc160-7dfc-40d0-af1b-3ef8b7b1b7f2.png)

**3. H.245**

H.245 là giao thức điều khiển báo hiệu cuộc gọi giữa các EP bao gồm
năng lực trao đổi, xác định master-slave, quản lý kênh luận lý. Giao thức này
được vận chuyển bằng TCP.

Xác định Master-slave: để tránh xung đột khi cả hai bên đều khởi tạo
cùng một cuộc gọi. Đầu cuối thỏa thuận vai trò này bằng cách áp dụng theo
một cách nào đó. Vai trò này sẽ giữ nguyên trong suốt cuộc gọi.
Trao đổi năng lực: mỗi đầu cuối phải biết được khả năng của nhau bao
gồm khả năng truyền và nhận, nếu không nó có thể không chấp nhận cuộc
gọi.

Quản lý kênh luận lý: đảm bảo cho đầu cuối có khả năng nhận và đọc
được dữ liệu khi kênh luận lý mở. Bản tin OpenLogicalChannel sẽ mô tả loại
dữ liệu sẽ truyền.

**Các thủ tục báo hiệu trong mạng H.323**

Người ta chia một cuộc gọi làm 5 giai đoạn gồm :
Giai đoạn 1: Thiết lập cuộc gọi
Giai đoạn 2: Thiết lập kênh điều khiển
Giai đoạn 3: Thiết lập kênh gọi ảo
Giai đoạn 4: Dịch vụ
Giai đoan 5: Kết thúc cuộc gọi

**1. Thiết lập cuộc gọi**

Việc thiết lập cuộc gọi sử dụng các bản tin được định nghĩa trong
khuyến nghị H.225.0. Ta sẽ xem xét thủ tục thiết lập cuộc gọi trong 6 trường
hợp sau:
- Cả hai thiết bị đầu cuối đều không đăng ký.
- Cả hai thuê bao đều đăng ký tới một GK.
- Chỉ có thuê bao chủ gọi có đăng ký với GK.
- Chỉ có thuê bao bị gọi có đăng ký với GK.
- Hai thuê bao đăng ký với hai GK khác nhau.
- Thiết lập cuộc gọi qua Gateway.

**2. Thiết lập kênh điều khiển**

Khi kết thúc giai đoạn 1 tức là cả chủ gọi lẫn bị gọi đă hoàn thành việc
trao đổi các bản tin thiết lập cuộc gọi, thì các đầu cuối sẽ thiết lập kênh điều
khiển H.245:

Bản tin đầu tiên được trao đổi giữa các đầu cuối là terminal
CapabilitySet để các bên thông báo cho nhau khả năng làm việc của mình
(chế độ mã hoá, truyền, nhận và giải mã các tín hiệu đa dịch vụ).

Kênh điều khiển này có thể do thuê bao bị gọi thiết lập sau khi nó nhận
được bản tin Set-up hoặc do thuê bao chủ gọi thiết lập khi nó nhận được bản
tin Alerting hoặc Call Proceeding. Trong trường hợp không nhận được bản
tin Connect hoặc một đầu cuối gởi Release Complete, thì kênh điều khiển
H.245 sẽ được giải phóng.

**3. Thiết lập kênh truyền thông**

Sau khi trao đổi khả năng (tốc độ nhận tối đa, phương thức mã hoá…)
và xác định quan hệ master-slave trong giao tiếp ở giai đoạn 2, thủ tục điều
khiển kênh H.245 sẽ thực hiện việc mở kênh logic để truyền dữ liệu. Các
kênh này là kênh H.225.

Sau khi mở kênh logic để truyền tín hiệu là âm thanh và hình ảnh thì
mỗi đầu cuối truyền tín hiệu sẽ truyền đi một bản tin h2250 MaximumSkew
Indication để xác định thông số truyền.

**4. Dịch vụ cuộc gọi**

Có một số dịch vụ cuộc gọi được thực hiện trên mạng H.323 như: thay
đổi độ rộng băng tần, giám sát trạng thái hoạt động, hội nghị đặc biệt, các
dịch vụ bổ sung. Dưới đây là hai loại dịch vụ điển hình: hay đổi độ rộng băng
tần và giám sát trạng thái hoạt động.

**5. Kết thúc cuộc gọi**

Một thiết bị đầu cuối có thể kết thúc cuộc gọi theo các bước của thủ tục
sau:
+ Dừng truyền luồng tín hiệu video khi kết thúc truyền hình ảnh, sau
đó giải phóng tất cả các kênh logic phục vụ truyền video.
+ Dừng truyền dữ liệu và đóng tất cả các kênh logic dùng để truyền dữ
liệu.
+ Dừng truyền audio sau đó đóng tất cả các kênh logic dùng để truyền
audio.

Truyền bản tin H.245 end Session Command trên kênh điều khiển
H.245 để báo cho thuê báo đầu kia biết nó muốn kết thúc cuộc gọi. Sau đó nó
dừng truyền các bản tin H.245 và đóng kênh điều khiển H.245. Nó sẽ chờ
nhận bản tin end Session Command từ thuê bao đầu kia và sẽ đóng kênh điều
khiển H.245. Nếu kênh báo hiệu cuộc gọi đang mở, thì nó sẽ truyền đi bản tin
ReleaseComplete sau đó đóng kênh báo hiệu.

Nó có thể kết thúc cuộc gọi theo các thủ tục sau đây: Một đầu cuối
nhận bản tin end Session Command mà trước đó nó không truyền đi bản tin
này, thì nó sẽ lần lượt thực hiện các bước từ 1 đến 6 ở trên chỉ bỏ qua bước 5.

_Chú ý:_ Kết thúc một cuộc gọi không có nghĩa là kết thúc một hội nghị
(cuộc gọi có nhiều đầu cuối tham gia). Một hội nghị sẽ chắc chắn kết thúc khi
sử dụng bản tin H.245 drop Conference. Khi đó các đầu cuối sẽ chờ MC kết
thúc cuộc gọi theo thủ tục trên.

_Kết thúc cuộc gọi có sự tham gia của GK_

![image](https://user-images.githubusercontent.com/69178270/137660441-fce36c7a-4ab5-4030-a571-99746a548eb8.png)

_Thiết bị đầu cuối kết thúc cuộc gọi có sự tham gia của GK._

Trong một cuộc gọi không có sự tham gia của GK thì chỉ cần thực hiện
các bước 1 đến 6. Trong cuộc gọi có sự tham gia của GK thì cần có hoạt động
giải phóng băng tần. Vì vậy, sau khi thực hiện các bước từ 1 đến 6, mỗi đầu
cuối sẽ truyền đi bản tin DRQ(3) tới GK. Sau đó, GK sẽ trả lời bằng bản tin
DCF(4). Sau khi gởi DRQ, đầu cuối sẽ không gởi bản tin IRR tới GK nữa và
khi đó cuộc gọi kết thúc. 

_Thủ tục kết thúc cuộc gọi do GK thực hiện._

Đầu tiên, GK gởi bản tin DRQ tới đầu cuối. Khi nhận được bản tin này,
đầu cuối sẽ lần lượt thực hiện các bước từ 1 đến 6, sau đó trả lời GK bằng bản
tin DCF. Thuê bao đầu kia khi nhận được bản tin endSessionCommand sẽ
thực hiện thủ tục giải phóng cuộc gọi giống trường hợp đầu cuối chủ động kết
thúc cuộc gọi. Nếu cuộc gọi là một hội nghị thì GK sẽ gởi DRQ tới tất cả các
đầu cuối tham gia hội nghị.

_Kết thúc cuộc gọi bắt đầu từ GK_

![image](https://user-images.githubusercontent.com/69178270/137660522-a340daa1-fbc3-443c-b25f-1e8730fff412.png)

**Giao thức SIP**

**1. Tổng Quan**

Giao thức SIP (Session Initiation Protocol) là một giao thức điều khiển
và được tiêu chuẩn hóa bởi IETF. Nhiệm vụ của nó là thiết lập, hiệu chỉnh và
xóa các phiên làm việc giữa các người dùng. Các phiên làm việc cũng có thể
là hội nghị đa phương tiện, cuộc gọi điện thoại điểm-điểm SIP được sử dụng
kết hợp với các chuẩn giao thức IETF khác như SAP, SDP và MGCP để cung
cấp một lĩnh vực rộng hơn cho các dịch vụ VoIP. Cấu trúc của SIP cũng
tương tự như cấu trúc của HTTP (giao thức client-server). Nó bao gồm các
yêu cầu được gửi đến từ người sử dụng SIP client đến SIP server. Server sử lý
các yêu cầu và đáp ứng đến các client. Một thông điệp yêu cầu cùng với thông
điệp đáp ứng tạo nên sự thực thi SIP.

SIP là một công cụ hỗ trợ hấp dẫn đối với điện thoại IP với các lí do
sau:
+ Nó có thể hoạt động vô trạng thái hoặc có trạng thái. Vì vậy sự hoạt
động vô trạng thái cung cấp sự mở rộng tốt do các server không phải duy trì
thông tin về trạng thái cuộc gọi một khi sự thực hiện đã được xử lý.
+ Nó có thể sử dụng nhiều dạng hoặc cú pháp giao thức chuyển siêu
văn bản HTTP. Vì vậy, nó cung cấp một cách thuận lợi để hoạt động trên các
trình duyệt.
+ Bản tin SIP thì không rõ ràng, nó có thể là bất cứ cú pháp nào. Vì
vậy, nó có thể được mô tả theo nhiều cách. Chẳng hạn, nó có thể được mô tả
với sự mở rộng thư internet đa mục đính MIME (Multipurpose Internet Mail
Extension) hoặc ngôn ngữ đánh dấu mở rộng XML (Extensible Markup
Language).
+ Nó nhận dạng một người dùng với bộ định vị tài nguyên đồng nhất
URL(Uniform Resource Locator), vì vậy nó cung cấp cho người dùng khả
năng khởi tạo cuộc gọi bằng cách nhập vào một liên kết trên trang web.
Nói chung, SIP hỗ trợ các hoạt động chính sau:
  * Định vị trí của người dùng.
  * Định media cho phiên làm việc.
  * Định sự sẵn sàng của người dùng để tham gia vào một phiên làm việc.
  * Thiết lập cuộc gọi, chuyển cuộc gọi và kết thúc.

