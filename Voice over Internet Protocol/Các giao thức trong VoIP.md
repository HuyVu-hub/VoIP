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

**2. Cấu trúc của giao thức SIP**

Một khía cạch khác biệt của SIP đối với các giao thức xử lý cuộc gọi IP
khác là nó không sử dụng bộ điều khiển Gateway. Nó không dùng khái niệm
Gatway/bộ điều khiển Gateway nhưng nó dựa vào mô hình khách
chủ(client/server).
 
 _Kiến trúc báo hiệu SIP và thủ tục báo hiệu_
 
![image](https://user-images.githubusercontent.com/69178270/137661029-faa8ee0f-6c61-4dff-8c28-ba0023c4838c.png)

Server: Là một chương trình ứng dụng chấp nhận các bản tin yêu cầu
để phục vụ các yêu cầu này và gửi trả các đáp ứng cho các yêu cầu đó. Server
là Proxy, Redirect, UA hoặc Registrar.

Proxy server: là một chương trình trung gian, hoạt động như là một
server và một client cho mục đính tạo các yêu cầu thay mặt cho các client
khác. Các yêu cầu được phục vụ bên trong hoặc truyền chúng đến server
khác. Một proxy có thể dịch và nếu cần thiết, có thể tạo lại bản tin yêu cầu
SIP trước khi chuyển chúng đến server khác hoặc một UA

Redirect server: là một server chấp nhận một yêu cầu SIP, ánh xạ địa
chỉ trong yêu cầu thành một địa chỉ mới và trả lại địa chỉ này về client. Không
giống như proxy server, nó không khởi tạo một yêu cầu SIP và không chuyển
các yêu cầu đến các server khác. Không giống như server đại diện người dùng
USA, nó không chấp nhận cuộc gọi.

Registrar: là một server chấp nhận yêu cầu register. Một Registrar được
xếp đặt với một Proxy hoặc một server gửi lại và có thể đưa ra các dịch vụ
định vị. Registrar được dùng đằng kí các đối tượng SIP trong miền SIP và cập
nhật vị trí hiện tại của chúng. Một miền SIP thì tương tự với một vùng H.323.
UA (User Agent): là một ứng dụng chứa cả UAC (user agent client) và
UAS (user agent server).
- UAC: là phần người sử dụng được dùng để khởi tạo một yêu cầu SIP
tới Server SIP hoặc tới UAS.
- UAS: là một ứng dụng server gio tiếp với người dùng khi yêu cầu SIP
được nhận và trả lại một đáp ứng đại diện cho người dùng.

Server SIP có hai loại: Proxy server và Redirect server. Proxy server
nhận một yêu cầu từ client và quyết định server kế tiếp mà yêu cầu sẽ đi đến.
Proxy này có thể gửi yêu cầu đến một server khác một Redirect hoặc UAS.
Đáp ứng sẽ được truyền cùng đường với yêu cầu nhưng theo chiều ngược lại.
Proxy server hoạt động như là một client và server. Redirect sẽ không chuyển
yêu cầu nhưng sẽ chỉ định client tiếp xúc trực tiếp với server kế tiếp, đáp ứng
gửi lại client chứa chỉ định của server kế tiếp. Nó không hoạt động được như
là một client, nó không chấp nhận cuộc gọi.

_Redirect Server_

![image](https://user-images.githubusercontent.com/69178270/137661109-5a331c11-4fc3-4b57-95c8-f3ce0ec24625.png)

**3. SDP (Session Description Protocol)**

Là giao thức cho phép client chia sẻ thông tin về phiên kết nối cho các
client khác. Nó đóng một vai trò quan trọng trong VoIP.

_Mô tả SDP:_

SDP không phải là một giao thức lớp vận chuyển, nó không thực sự
vận chuyển dữ liệu giữa các client mà nó chỉ thiết lập cấu trúc thông tin về
các thuộc tính của luồng dữ liệu, dữ liệu thực sự được truyền đi bởi các giao
thức SIP, RTSP hay HTTP.
Thông tin trong gói SDP ở dạng ASCII gồm nhiều dòng, mỗi dòng là 1
trường. Ví dụ bản tin SDP:

v=0

o=bsmith 2208988800 2208988800 IN IP4 68.33.152.147

s=

e=bsmith@foo.com

c=IN IP4 20.1.25.50

t=0 0

a=recvonly

m=audio 0 RTP/AVP 0 1 101

a=rtpmap:0 PCMU/8000

_Ý nghĩa của các trường_

![image](https://user-images.githubusercontent.com/69178270/137661250-a6655c60-0f09-47fc-86dc-dae367aba4d5.png)

_Hoạt động của SDP:_

Client gửi SIP request, thiết bị sẽ tạo một gói SDP gửi trả lại. Gói SDP
này mang thông tin về phiên kết nối. Sau đây là một ví dụ:

v=0

o=alice 2890844526 2890844526 IN IP4

host.atlanta.example.com

s=

c=IN IP4 host.atlanta.example.com

t=0 0

m=audio 49170 RTP/AVP 0 8 97

a=rtpmap:0 PCMU/8000

a=rtpmap:8 PCMA/8000

a=rtpmap:97 iLBC/8000

m=video 51372 RTP/AVP 31 32

a=rtpmap:31 H261/90000

a=rtpmap:32 MPV/90000

Trong ví dụ trên, người gửi là Alice, lắng nghe kết nối từ host. atlanta.
Example .com. Gói được gửi tới bất kỳ ai muốn tham gia phiên kết nối. Kết
nối của Alice hỗ trợ ba loại kết nối cho audio là PCMU, PCMIA và iLBC, hai
loại kết nối video H.261 và MPV. Nếu Bob muốn tham gia kết nối thì gửi lại
bản tin SDP:

v=0

o=bob 2808844564 2808844564 IN IP4 host.biloxi.example.com

s=

c=IN IP4 host.biloxi.example.com

t=0 0

m=audio 49174 RTP/AVP 0

a=rtpmap:0 PCMU/8000

m=video 49170 RTP/AVP 32

a=rtpmap:32 MPV/90000

_Bảo mật cho SDP:_

Bản tin SDP mang thông tin về phiên kết nối như nhận dạng phiên kết
nối, IP người gửi, người nhận,… Nếu kẻ tấn công bắt được những gói SDP
này nó có thể thay đổi giá trị trong các trường rồi gửi đi. Nhưng điều này
hoàn toàn có thể khắc phục bằng phương pháp chứng thực user của SIP.

**4. Các bản tin của SIP**

Có hai loại bản tin SIP: bản tin yêu cầu được khởi tạo từ client và bản
tin đáp ứng được trả lại từ server. Mỗi bản tin chứa một tiêu đề mô tả chi tiết
về sự truyền thông.

Một bản tin cơ bản gồm: dòng bắt đầu (start-line), một hoặc nhiều
trường tiêu đề, một dòng trống (CRLF) dùng để kết thúc các trường tiêu đề và
một nội dung bản tin tùy chọn.

![image](https://user-images.githubusercontent.com/69178270/137661409-7ec838f2-e57f-495a-a188-9c4bb5f91498.png)

**4.1 Tiêu đề bản tin**

Dùng để chỉ ra người gọi, người bi gọi, đường định tuyến và loại bản
tin của cuộc gọi. Có bốn nhóm bản tin như sau:

Tiều đề chung: áp dụng cho các yêu cầu và các đáp ứng.

Tiêu đề thực thể: định nghĩa thông tin về loại bản tin và chiều dài.

Tiêu đề yêu cầu: cho phép client thêm vào các thông tin yêu cầu.

Tiêu đề đáp ứng: cho phép server thêm vào các thông tin đáp ứng.

Các tiêu đề này được liệt kê trong bảng dưới đây:

_Tiêu đề của SIP_

![image](https://user-images.githubusercontent.com/69178270/137661473-231943a0-6a81-4f1c-893b-127cca1cf598.png)

Giải thích một số tiêu đề chính của SIP theo bảng dưới:

_Giải thích một số tiêu đề chính của SIP._

![image](https://user-images.githubusercontent.com/69178270/137661524-79031b33-d76e-4169-b603-e632d4bdad51.png)

**4.2 Bản tin yêu cầu.**

Các yêu cầu cũng có thể được xem như các phương pháp (method) cho
phép User agent và server mạng định vị, mời và quản lí các cuộc gọi. Bản tin
yêu cầu SIP có dạng sau:

![image](https://user-images.githubusercontent.com/69178270/137661596-49f875f9-7e92-4ee5-b1bf-b8260306c178.png)

Dòng yêu cầu bắt đầu bằng mã phương pháp, bộ nhận dạng tài nguyên
đồng nhất yêu cầu, phiên bản giao thức SIP và kết thúc với CRLF. Các thành
phần được phân các bởi ký tự SP.

Có 6 loại bản tin yêu cầu SIP: INVITE, ACK, OPTIONS, BYE,
CANCEL và REGISTER.

INVITE: Bản tin INVITE chỉ ra người dùng hoặc dịch vụ đang được
mời tham dự một phiên làm việc. Nội dung bản tin chứa sự mô tả phiên mà
người bị gọi được mời. Đối với cuộc gọi hai người, người gọi chỉ ra loại
media mà nó có thể nhận. Một đáp ứng thành công phải chứa trong nội dung
bản tin của nó loại media nào mà người bị gọi mong muốn nhận. Với bản tin
này, người dùng có thể nhận biết được khả năng của người dùng khác và mở
ra một phiên hội thoại với số bản tin giới hạn.

ACK: Bản tin ACK xác nhận client đã nhận được đáp ứng sau cùng đối
với bản tin INVITE (ACK chỉ được sử dụng với bản tin INVITE).

Nội dung bản tin ACK chứa sự mô tả phiên sau cùng được sử dụng bởi
người bị gọi. Nếu nội dung bản tin ACK bị rỗng thì người bị gọi sử dụng sự
mô tả phiên trong bản tin INVITE.

OPTIONS: Bản tin này cho phép truy vấn và thu thập User Agent và
các khả năng của Server mạng. Tuy nhiên, bản tin này không được sử dụng để
thiết lập phiên

BYE: User Agent Client sử dụng bản tin BYE báo cho Server biết nó
muốn giải phóng cuộc gọi. Bản tin BYE được chuyển giống như là bản tin
INVITE và có thể được phát đi từ người gọi hoặc người bị gọi. Khi một đối
tác nhận bản tin BYE thì nó phải ngừng việc truyền các luồng dữ liệu về
hướng đối tác phát đi bản tin BYE.

CANCEL: Bản tin CANCEL cho phép User Agent và server mạng hủy
bỏ bất cứ yêu cầu nào đang trong quá trình xử lý, nó không ảnh hưởng đến
các yêu cầu đã hoàn thành mà các đáp ứng sau cùng đã nhận được.

RIGISTER: Bản tin này được sử dụng bởi client để đăng ký thông tin
vị trí của nó với server SIP

**4.3 Đáp ứng bản tin**

Các bản tin đáp ứng có dạng như sau:

![image](https://user-images.githubusercontent.com/69178270/137661707-4e71f3ba-4b6b-4ac6-8ece-195f22770aba.png)

Dòng trạng thái bao gồm phiên bản của giao thức, mã trạng thái (số), lý
do và CRLF. Các thành phần được cách nhau bằng hai ký tự SP.

Dòng trạng thái = SIP-version SP status-code SP Reason-Phrase CRLF
Mã trạng thái có 3 chứ số chỉ ra kết quả của việc đáp ứng yêu cầu. Lý
do là sự mô tả ngắn gọn về mã trạng thái.

Chữ số đầu tiên của mã trạng thái định nghĩa lớp đáp ứng. SIP phiên
bản 2.0 định nghĩa 6 giá trị cho lớp đáp ứng.

1xx: thông tin-các yêu cầu được nhận, xử lý các yêu cầu

2xx: thành công-hoạt động được nhận thành công và được chấp nhận.

3xx: đổi hướng (redirection) cần thêm một số hoạt động để hoàn thành
yêu cầu.

4xx: lỗi client – yêu cầu bị sai lỗi cú pháp hoặc không thỏa mãn ở
server.

5xx: lỗi server – server không thỏa mãn một yêu cầu đúng.

6xx: lỗi toàn cầu – yêu cầu không thể thỏa mãn ở bất kì server nào.

Mã số mã trạng thái được định nghĩa trong SIP phiên bản 2.0 được định
nghĩa trong bảng dưới đây:

_Các đáp ứng của SIP_

![image](https://user-images.githubusercontent.com/69178270/137662057-f58ed8ca-1e46-4684-8cb3-46bf2e6c615e.png)

![image](https://user-images.githubusercontent.com/69178270/137662079-20072be3-5566-44e1-a3ec-84a95d858ea0.png)

**5. Các giao thức vận chuyển trong SIP.**

SIP có thể sử dụng UDP và TCP. Khi được gửi trên UDP hoặc TCP,
nhiều sự giao dịch SIP có thể được mang trên một kết nối TCP đơn lẻ hoặc
gói dữ liệu UDP. Gói dữ liệu UDP (bao gồm tất cả các tiêu đề) thì không vượt
quá đơn vị truyền dẫn lớn nhất MTU (Maximum Transmission Unit) nếu MTU được định nghĩa hoặc không vượt quá 1500 byte nếu MTU không được
định nghĩa.

**5.1 UDP**

UDP là giao thức tầng vận chuyển không có điều khiển tắc nghẽn. Nó
được dùng để vận chuyển bản tin SIP vì đơn giản và thích hợp với các ứng
dụng thời gian thực. Các bản tin SIP thường có kích thước nhỏ hơn MTU
(Message Transport Unit). Nếu bản tin lớn thì phải dùng TCP, vì lý do này mà
SIP không có chức năng chia nhỏ gói.

_Trao đổi bản tin SIP bằng UDP_

![image](https://user-images.githubusercontent.com/69178270/137662352-53f4ff1a-6883-49ab-aec3-e077877a71f3.png)

**5.2  TCP**

TCP là giao thức ở tầng vận chuyển đáng tin cậy do có điều khiển tắc
nghẽn, hơn nữa nó có thể vận chuyển gói tin có kích thước bất kỳ. Nhược
điểm của nó là tăng độ trễ.

_Vận chuyển bản tin SIP bằng TCP_

![image](https://user-images.githubusercontent.com/69178270/137662431-3a8ae551-7873-4e88-80aa-e31679a33cb8.png)

Để tăng cường tính bảo mật thì còn có những giao thức bổ sung để vận
chuyển bản tin SIP như TLS, SRTP.

**So sánh H.323 và SIP**

SIP và H.323 được phát triển với những mục đích khác nhau bởi các tổ
chức khác nhau. H.323 được phát triển bởi ITU-T từ theo PSTN, dùng mã hóa
nhị phân và dùng lại một phần báo hiệu ISDN. SIP được IETF phát triển dựa
trên mạng Internet, dùng một số giao thức và chức năng của mạng Internet.

Hệ thống mã hóa: SIP là giao thức text-based (text dạng ASCII) giống
như HTTP trong khi đó H.323 dùng các bản tin mã hóa nhị phân. Mã hóa nhị
phân giúp giảm kích thước bản tin nhưng nó phức tạp hơn dạng text bình
thường. Ngược lại các bản tin text dễ dàng tạo ra, lưu lại, kiểm tra và không
cần bất cứ một tool nào để biên dịch nó, điều này làm cho SIP thân thiện với
môi trường Internet và các nhà phát triển web. Bản tin SIP có cấu trúc ABNF,
(Augmented Backus-Naur Form) còn bản tin H.323 ASN.1 không có cấu trúc.

H.323 chỉ có chức năng báo hiệu, SIP có thêm khả năng thông tin về
trạng thái của user (presense and Instant message) vì SIP sử dụng địa chỉ URI.
Điều này là thế mạnh của SIP và hầu hết các dịch vụ ngày nay dùng SIP nhiều
hơn so với H.323. SIP được hỗ trợ bởi thiết bị của các nhà cung cấp dich vụ
và đang dần thay thế H.323. SIP cũng được các hãng di động sử dụng như
giao thức báo hiệu cuộc gọi. 

Tính cước: SIP muốn có thông tin tính cước phải ở trong quá trình báo
hiệu cuộc gọi để phát hiện ra thời điểm kết thúc cuộc gọi. Còn với H.323, tại
thời điểm khởi tạo và kết thúc cuộc gọi, các thông tin tính cước nằm trong các
bản tin ARQ/DRQ. Với trường hợp cuộc gọi báo hiệu trực tiếp, EP thông báo
cho GK thời điểm bắt đầu và kết thúc cuộc gọi bằng bản tin RAS.

Về mức độ bảo mật: SIP có nhiều hỗ trợ bảo mật đảm bảo mã hóa,
chứng thực dùng certificate, toàn vẹn bản tin end-to-end. Bản thân SIP không
phát triển những hỗ trợ này mà nó thừa hưởng từ các giao thức hỗ trợ bảo mật
của Internet như TLS và S/MIME. Còn H.323 thì xây dựng H.235 cho chứng
thực và mã hóa.

Các thiết bị SIP còn hạn chế về việc trao đổi khả năng. Còn các thiết bị
trong mạng H.323 có khả năng trao đổi khả năng và thương lượng mở kênh
nào (audio, thoại, video hay dữ liệu).

H.323 và SIP cùng tồn tại và có chức năng tương tự như nhau. SIP
được hỗ trợ DNS và URL ngay từ đầu còn H.323 thì không. Tương tự như
vậy H.323 hỗ trợ hội nghị truyền hình với khái niệm MCU ngay từ đầu thì với
SIP tính năng đó được phát triển sau gọi là “focus”.

SIP ban đầu dùng UDP, sau đó dùng TCP. Còn với H.323 thì ban đầu
không dùng UDP nhưng bây giờ đã có hỗ trợ thêm UDP.

_Ưu điểm của từng giao thức:_

H.323 dùng thay thế một phần trong hệ thống PSTN và chiếm lĩnh thị
trường hội nghị truyền hình. Đối với những bộ phận chỉ dùng tính năng báo
hiệu (thiết lập và kết thúc) cuộc gọi, không dùng hết những ưu điểm nổi trội
của SIP thì không cần thay thế H.323 bằng SIP.

SIP hiện tại vẫn chưa hỗ trợ hội nghị truyền hình. Điểm mạnh của nó
hiện tại vẫn là một giao thức đơn giản, dựa trên kiến trúc Internet.

**Giao thức vận chuyển trong VoIP**

