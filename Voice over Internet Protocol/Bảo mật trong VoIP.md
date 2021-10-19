### BẢO MẬT TRONG VoIP

### Mục lục

[Vấn đề bảo mật trong VoIP](#1)

[Nhu cầu bảo mật](#2)

[Một số cách tấn công chặn cuộc gọi](#3)

[Các công nghệ bảo mật](#4)

[Bảo vệ các thiết bị VoIP](#5)

### <a name="1"> Vấn đề bảo mật trong VoIP </a>

Chính vì VoIP dựa trên kết nối internet nên có thể có những điểm yếu
đối với bất kì mối đe doạ và các vấn đề gì mà máy tính của bạn có thể đối
mặt. Công nghệ này cũng là một công nghệ mới nên cũng có nhiều tranh cãi
về những tấn công có thể xảy ra, VoIP cũng có thể bị tấn công bởi virut và mã
nguy hiểm khác, các kể tấn công có thể chặn việc truyền thông, nghe trộm và
thực hiện các tấn công giả mạo bằng việc thao túng ID và làm hỏng dịch vụ
của bạn. Các hành động tiêu tốn lượng lớn các tài nguyên mạng như tải file,
chơi trò chơi trực tuyến…cũng ảnh hưởng đến dịch vụ VoIP.

VoIP cũng chịu chung với các vấn đề bảo mật vốn có của mạng data.
Những bộ giao thức mới dành riêng của VoIP ra đời cũng mang theo nhiều
vấn đề khác về tính bảo mật.

Nghe nén cuộc gọi: nghe nén qua công nghệ VoIP càng có nguy cơ cao
do có nhiều node chung gian trên đường truyền giữa hai người nghe và người
nhận. Kẻ tấn công có thể nghe nén được cuộc gọi bằng cách tóm lấy các gói
IP đang lưu thông qua các node trung gian. Có khá nhiều công cụ miễn phí và
có phí kết hợp với các card mạng hỗ trợ chế độ pha tạp giúp thực hiện được
các điều này.

Truy cập trái phép(unauthorized access attack): kẻ tấn công có thể xâm
phạm các tài nguyên trên mạng do nguyên nhân chủ quan của các admin. Nếu
các mật khẩu mặc định của các gateway và switch không được đổi thì kẻ tấn
công có thể lợi dụng để xâm nhập. Các switch cũ vẫn còn dùng telnet để truy
cập từ xa, và clear-text protocol có thể bị khai thác một khi kẻ tấn công có thể
sniff được các gói tin. Với các gateway hay switch sử dụng giao diện web
server cho việc điều khiển từ xa thì kẻ tấn công có thể tóm các dụng cụ kỹ
thuật ARP để tóm lấy các gói tin đang lưu chuyển trong một mạng nội bộ.

Caller ID spoofing: caller ID là một dịch vụ cho phép uer có thể biết
được số của người gọi đến. Caller ID spoofing là kỹ thuật mạo danh cho phép
thay đổi số ID của người gọi bằng những con số do uer đặt ra. So với mạng
điện thoại truyền thông, thì việc giả mạo số điện thoại VoIP dễ hơn nhiều, bởi
có khá nhiều công cụ và website cho phép thực hiện điều này.

_Mô tả các cấp độ mà cấu trúc VoIP có thể bị tấn công_

![image](https://user-images.githubusercontent.com/69178270/137698890-73114bcf-6149-4d76-a107-61c247715534.png)

Ngoài những vấn đề trên, VoIP còn kế thừa những vấn đề chính trong
việc định tuyến trên kết nối băng thông rộng. Không giống như các hệ thống
điện thoại truyền thông bạn có thể gọi cả khi mất điện. Trong hệ thống VoIP,
nếu mất nguồn điện thì VoIP cũng không thể thực hiện được cuộc gọi . Ở đây
cũng có vài vấn đề liên quan đó là các hệ thống bảo mật ở nhà hoặc số khẩn
cấp có thể không làm việc theo như mong muốn.

### <a name="2"> Nhu cầu bảo mật </a>

Trước khi đi vào chi tiết về những công nghệ khác nhau để bảo vệ cho
mạng VoIP. Bạn cần phải hiểu những vấn đề và tập hợp những nhu cầu mà
bạn đã được thấy. Phần này sẽ phác thảo những nhu cầu bảo mật tiêu biểu.
Không phải là một danh sách toàn diện. Những dịch vụ VoIP đặc biệt có thể
cần những nhu cầu phụ:

**Tính toàn vẹn :** Người nhận nên nhận những gói dữ liệu của người khởi
tạo gửi với nội dung không có sự thay đổi. Một bên thứ ba cần phải không có
khả năng chỉnh sửa gói trong quá trình vận chuyển. Định nghĩa này được áp
dụng một cách chính xác trong trường hợp của tín hiệu VoIP. Tuy nhiên, trong trường hợp của phương tiện truyền thông, sự mất mát gói thông thường
có thể tha thứ được.

**Tính bí mật:** Một hãng thứ ba không nên có khả năng để đọc dữ liệu mà
được dự định cho người nhận.

**Tính xác thực:** Bên gửi và bên nhận tín hiệu VoIP hay thông điệp
truyền thông nên chắc chắn rằng chúng đang liên lạc ngang hàng nhau.

**Tính sẵn sàng:** Sự bảo vệ từ việc tấn công DoS(từ chối dịch vụ) đối với
thiết bị VoIP nên sẵn có đối với những người sử dụng liên tục. Những người
sử dụng/những thiết bị có ác tâm hoặc có cư xử không đúng đắn không được
cấp quyền để phá vỡ dịch vụ. Để làm dịu các cuộc tấn công DoS đòi hỏi cách
xử lý lây nhiễm để bảo vệ tài nguyên VoIP và bảo vệ mạng IP bên dưới.

### <a name="3"> Một số cách tấn công chặn cuộc gọi </a>

**1. Tấn công Replay**

Tấn công replay là tấn công chủ động hướng về nghi thức. Đặc trưng
của người tấn công này giành được gói dữ liệu gởi hoặc nhận đến host. Anh ta
sửa đổi chúng và sử dụng lại để truy cập vào một số dịch vụ nào đó. Một ví
dụ tương ứng với loại thoại IP là người tấn công đạt được trong tay các gói dữ
liệu gởi từ một user có quyền để thiết lập cuộc gọi và gởi lại chúng sau khi đã
sửa đổi địa chỉ nguồn và IP. Nó có thể bị ngăn chặn bằng cách thực thi hai
dịch vụ bảo mật nhận thực thực thể ngang hàng (peer entity authencation) và
tính toàn vẹn dữ liệu (data intergrity).

**2. Tấn công tràn bộ đệm**

Đây là phương thức tấn công phổ biến. Đây là kết quả chính của việc
phát triển phần mềm không đúng lúc. Kỹ thuật này lợi dụng trên thực tế là có
một vài lệnh không kiểm tra đầu vào dữ liệu. Chúng được ứng dụng đặc biệt
để xâu chuỗi xử lý các lệnh. Quá trình gia nhập với nhiều đầu vào, các lệnh
hay là các chương trình có khả năng làm cho bộ nhớ hệ thống bị viết đè lên.
Nội dung của bộ nhớ này có thể bắt đầu hoặc quay trở lại địa chỉ của các
chương trình subroutine. Trường hợp xấu nhất người tấn công có thể thêm
vào đoạn code hiểm để cung cấp cho anh ta các quyền quản lí của hệ thống.
Biện pháp đối phó là huỷ tất cả các code “yếu”, chính các lỗ hổng nhận thức
được chứa trong các hệ thống hoạt động và các chương trình ngôn ngữ.

**3. Tấn công man in the middle**

Trong tấn công man in the middle người tấn công quản lý để cắt đứt kết
nối giữa hai bên gọi. Cả hai bên tham gia kết nối này đều nghĩ rằng chúng
truyền thông với nhau. Thực tế, tất cả các dữ liệu được định tuyến qua người
tấn công. Hacker đã hoàn thành việc truy cập để thay thế các dữ liệu bên
trong. Hacker có thể đọc chúng, thay đổi chúng hoặc và gửi chúng như là dữ
liệu của anh ta. Thực tế hacker được xác định ở vị trí ở giữa của hai bên
truyền thông mang lại cho người tấn công tên của hai bên truyền thông. Một
ví dụ cho tấn công này là thiết lập của việc bảo đảm kết nối được sử dụng bởi
bảo mật lớp dữ liệu. Điểm yếu của TLS là nguyên nhân của việc thiết lập
phiên này. Ở đây hai bên truyền thông có thể trao đổi hai khóa. Khóa này
được đổi có khả năng làm cho người tấn công có thể ở giữa hai bên truyền
thông.

**4. Chặn và đánh cắp cuộc gọi**

Nghe trộm và đánh chặn cuộc gọi là vấn đề liên quan đến mạng VoIP,
định nghĩa nghe lén có nghĩa là một người tấn công có thể giám sát toàn bộ
báo hiệu hoặc dòng dữ liệu giữa hai hoặc nhiều đầu cuối VoIP, nhưng không
thể biến đổi dữ liệu. Đánh cắp cuộc gọi thành công tương tự như việc nghe
trộm trên dây nối, cuộc gọi của hai bên có thể bị đánh cắp, ghi lại, và nghe lại
mà hai bên không hề biết. Rõ ràng người tấn công mà có thể đánh chặn và
chứa dữ liệu này có thể sử dụng dữ liệu này cho mục đích khác phục vụ cho
mục đích của anh ta.

**5. Đầu độc DNS**

Một hồ sơ DNS (Domain Name System) A được sử dụng cho việc chứa
các domain hay hostname ánh xạ thành địa chỉ IP. SIP tạo ra việc sử dụng
rộng rãi hồ sơ SRV để xác định các dịch vụ SIP như là SIP uỷ quyền và đăng
nhập. Các hồ sơ SRV thường bắt đầu với gạch dưới (_sip.tcpserver.udp.domain.com) và chứa thông tin về miêu tả dịch vụ, vận
chuyển, host, và thông tin khác. Các hồ sơ SRV cho phép người quản lý sử
dụng một vài user cho một domain, để di chuyển dịch vụ từ host đến host với
một ít quan trọng hoá, và để bổ nhiệm một vài host như là các server chính
cho các dịch vụ.

Một người có mục đích tấn công, sẽ cố gắng đầu độc DNS hay tấn công
giả mạo, sẽ thay thế giá trị lưu trữ hồ sơ DNS A, SSRV, hay NS với các bản
tin mà chỉ đến các server của người tấn công. Điều này có thể được hoàn
thành bằng cách bắt đầu bằng cách dời vùng từ DNS server của người tấn
công đến DNS server nạn nhân, bằng cách yêu cầu server DNS nạn nhân phân
tích thiết bị mạng trong domain của người tấn công. Server DNS nạn nhân
không những chấp nhận yêu cầu hồ sơ mà còn chấp nhận và chứa các hồ sơ
mà server tấn công có.

Vì vậy việc thêm vào hồ sơ A cho www.Attacker.com, server DNS nạn
nhân có thể nhận được hồ sơ giả là www.yourbank.com. Nạn nhận vô tội sẽ bị
hướng đến chuyển hướng lại đến attacker.com. Trang web mà bất mà bất kỳ
thời điểm nào muốn truy cập là yourbank.com. Trang web mà hồ sơ giả được
lưu trữ. SIP URL thay thế cho địa chỉ website, và vấn đề tương tự cũng gặp
phải trong môi trường VoIP.

Các loại đe doạ này dựa vào sự vắng mặt của bảo đảm nhận thực của
người tạo ra yêu cầu. Các tấn công trong loại này cố gắng tìm kiếm để phá
hoại tính toàn vẹn của dữ liệu đàm thoại.Các thảm hoạ này chỉ ra rằng việc
cần thiết phải bảo mật dịch vụ để có khả năng nhận thực thể tạo ra yêu cầu và
để kiểm tra nội dung của thông điệp và điều khiển các luồng không bị biến
đổi khi phát.

**6. Đánh lừa (ARP Spoofing)**

ARP là giao thức cơ sở Ethernet. Có lẽ do nguyên nhân này, thao tác
vào các gói ARP là kỹ thuật tấn công thường thấy trong mạng VoIP. Một vài
kỹ thuật hay công cụ hiện tại cho phép bất kỳ user nào có thể tìm ra lưu lượng
mạng trên mạng bởi vì ARP không có điều khoản cho câu hỏi nhận thực và
câu hỏi trả lời. Thêm vào đó, bởi vì ARP là một giao thức stateless, hầu hết
các hệ thống hoạt động cập nhật cache của nó khi mà nhận một lời đáp ARP,
bất chấp nó được gởi đi từ một yêu cầu thực tế hay không.

Trong số những tấn công này, chuyển hướng ARP, đánh lừa ARP, đánh
cắp ARP và đầu độc cache ARP là các phương pháp để phá hoại quá trình
ARP bình thường. Các dạng này thường xuyên được xen kẽ hoặc xáo trộn
nhau. Dành cho mục đích của chương này, có thể xem đầu độc cache ARP và
đánh lừa ARP như là cùng một quá trình. Sử dụng các công cụ tuỳ thích có 
thể như là ettercap, Cain, và dsnif, và các thiết bị IP có hại có thể đánh lừa
thiết bị IP thông thường bằng cách gởi một đáp ứng ARP không yêu cầu đến
host mục tiêu. Một đáp ứng ARP giả chứa địa chỉ phần cứng của thiết bị bình
thường và địa chỉ IP của thiết bị có ý đồ xấu. Ned là máy tính tấn công. Khi
SAM broadcast một câu hỏi ARP cho địa chỉ IP của Sally, NED, người tấn
công, đáp ứng câu hỏi để chỉ ra rằng địa chỉ IP (10.1.1.2) liên quan đến địa
chỉ MAC của Ned, BA:AD:BA:AD. Các gói giả sử gửi từ SAM đến Sally sẽ
được thay thế gởi đến Ned. Sam sẽ hiểu lầm rằng địa chỉ MAC của Ned tương
ứng với địa chỉ IP của Sally. Thực tế, Ned có thể đầu độc cache ARP của Sam
mà không cần đợi một yêu cầu ARP từ hệ thống Windows (9x/NT/2k), các
mục ARP tĩnh được viết đè lên khi một trả lời câu hỏi được nhận bất chấp có
hay không câu hỏi được phát. Mục này sẽ được giữ cho đến khi chúng hết hạn
hoặc mục mới thay thế.

Chuyển hướng ARP có thể hoạt động hai chiều và thiết bị đánh lừa có
thể đưa vào ở giữa của cuộc đàm thoại giữa hai thiết bị IP trên mạng chuyển
mạch. Bằng cách định tuyến các gói trên các thiết bị được nhận các gói, việc
gài vào này (được biết như là Man/Monkey/Moron trong việc tấn công ở giữa
) có thể vẫn không được nhận ra cho một vài lần. Người tấn công có thể định
tuyến các gói như mong muốn, dẫn đến như tấn công DoS.

Vì tất cả lưu lượng IP giữa người gởi thực và người nhận thực bây giờ
đều đi qua thiết bị của người tấn công, thật bình thường để cho người tấn
công tìm ra lưu lượng sử dụng các công cụ tuỳ thích như là Ethereal hay
tcpdump. Bất kỳ thông tin nào không được mã hoá (bao gồm email, username
và password, và lưu lưọng web) có thể bị chặn đứng và bị xem.

Sự chặn đứng này có khả năng tác động mạnh đến lưu lượng VoIP. Các
công cụ miễn phí như là vomit hay rtpsnif, cũng như là các công cụ công cộng
như là VoIPCrack, cho phép chặn đứng và mã hoá lưu lượng VoIP. Các nội
dụng chiếm được có thể bao gồm thoại, báo hiệu và thông tin tính cước, đa
phương tiện, số PIN. Đàm thoại qua nội mạng IP có thể bị chặn và ghi âm lại
sử dụng kỹ thuật này.

Ở đây cũng có một số biến thể của kỹ thuật kể trên. Thay cho việc
phỏng theo các host, người tấn công có thể phỏng theo gateway. Điều này làm
cho người tấn công có thể chặn đứng nhiều luồng gói. Tuy nhiên, hầu hết kỹ
thuật chuyển hướng dựa vào việc lén lút. Người tấn công trong các trường
hợp này đều hy vọng việc không nhận ra của các user mà chúng mạo nhận.
Mạo nhận gateway có thể có kết quả trong các user đề phòng sự có mặt của
người tấn công xâm phạm bất ngờ trong mạng.

Trong các thủ tục giới hạn lỗi do thao tác ARP, người quản lí phải thực
thi các công cụ phần mềm để giám sát việc ánh xạ địa chỉ IP thành địa chỉ
MAC. Ở lớp mạng, ánh xạ địa chỉ MAC/IP có thể được mật mã tĩnh trên
switch, tuy nhiên nó thường xuyên không được quản lý tốt.

Các rủi ro của việc mã hoá lưu lượng VoIP có thể được giới hạn bởi
thực thi mật mã. Sử dụng việc mật mã hoá media, các cuộc đàm thoại giữa hai
đầu cuối IP phải được sử dụng cùng một dạng mật mã hoá. Trong môi trường
bảo mật cao thì các tổ chức cần phải đảm bảo cùng một phương thức mật mã
trong bộ codec IP.

Tiếp theo là một vài ví dụ thêm vào của các đánh chặn hay ăn cắp cuộc
gọi hay tín hiệu. Các đe doạ của lớp này khó thực hiện hoàn thành hơn là
DoS, kết quả của nó có thể là dữ liệu bị mất hay bị thay đổi. Các tấn công
DoS, là do nguyên nhân của các phương pháp hoạt động hay sơ xuất, nó làm
ảnh hưởng đến chất lượng dịch vụ và thường gây sự không hài lòng đối với
user và người quản trị mạng. Các tấn công đánh chặn và ăn cắp, thường là các
tấn công chủ động với việc đánh cắp dịch vụ, thông tin, hoặc tiền như là mục
tiêu tấn công. Cần chú ý rằng danh sách này không khái quát hết khía cạnh
nhưng cũng bao gồm một vài tấn công cốt lõi.

**7.  Tấn công đánh lừa đầu cuối VoIP (Roque VoIP Endpoint Attack)**

Giả mạo đầu cuối EP giao tiếp với các dịch vụ VoIP bằng cách dựa trên
các đánh cắp hay ước đoán các nhận dạng, các uỷ nhiệm hoặc các truy cập
mạng. Ví dụ, một đánh lừa đầu cuối EP có thể sử dụng các jack không được
bảo vệ hay tự động đăng ký thoại VoIP để có thể vào mạng. Ước chừng mật
mã có thể được sử dụng để giả dạng như là một đầu cuối hợp pháp. Việc quản
lí các tài khoản không chặt chẽ có thể gia tăng nguy cơ của việc lợi dụng này

**8. Cướp đăng ký (Registration Hijacking)**

Cướp đăng ký xảy ra khi một người tấn công mạo nhận là một UA có
giá trị để giữ và thay thế đăng ký với địa chỉ của mình. Các tấn công này là
nguyên nhân của việc tất cả các cuộc gọi đến được gởi đến người tấn công.

**9. Giả mạo ủy nhiệm**

Giả mạo uỷ nhiệm xảy ra khi một người tấn công đánh lừa một uỷ
nhiệm (proxy) trong việc truyền thông với một proxy giả.. Nếu một người tấn
công thành công trong việc giả mạo uỷ nhiệm, anh ta có thể truy cập vào tất
cả các thông điệp SIP.

**10. Lừa tính phí**

Giả mạo đầu cuối VoIP sử dụng server VoIP để đặt việc tính phí bất
hợp pháp của cuộc gọi qua PSTN. Ví dụ, các điều khiển truy cập không đầy
đủ có thể cho phép các thiết bị giả đặt phí của các cuộc gọi bằng cách gởi yêu
cầu VoIP đến các ứng dụng tiến hành cuộc gọi. Các server VoIP có thể bị
hack trong các thủ tục để tiến hành cuộc gọi miễn phí đến đích bên ngoài.

**11. Xáo trộn thông điệp**

Bắt giữ, sửa đổi, và sắp đặt để không xác thực các gói VoIP đến đầu
cuối. Các tấn công này có thể xảy ra qua việc đánh cắp đăng nhập, giả mạo uỷ
nhiệm, hay tấn công trên bất kỳ một thành phần VoIP thực nào mà tiến hành
các thông điệp SIP hay H.323, như là server proxy, registration, media
gateway, hay các bức tường lửa.

### <a name="4"> Các công nghệ bảo mật </a>

Khi đưa ra những nhu cầu bảo mật cho những thiết bị VoIP, phần này
mô tả một vài công nghệ có sẵn để đảm bảo tính toàn vẹn,tính bí mật, và tính
chứng thực. Các công nghệ này không phải là những giải pháp tối ưu nhưng
nó góp phần giải quyết những vấn đề trong mạng VoIP:

**1. VLAN**

Sự tích hợp thoại, dữ liệu và video trên cùng một mạng làm cho sự bảo
mật của hệ thống VoIP cũng bị ảnh hưởng bởi các dịch vụ khác. Để có thể
giải quyết được vấn đề này ta tách biệt về luận lý giữa các dịch vụ bằng
VLAN

_VLAN_

![image](https://user-images.githubusercontent.com/69178270/137830652-424d457f-ad4c-4835-a5ec-f39d04948e99.png)

Lợi ích của VLAN:
- Giảm lưu lượng broadcast và multicast vì chỉ có các máy trong cùng
một VLAN mới có thể thông tin được với nhau. VLAN được cấu hình trên
switch.
- VLAN dễ dàng quản lý, giúp quản lý thiết bị một cách tập trung.
VLAN có thể sắp xếp và quản lý các PC hay softphone dựa vào chức năng,
lớp dịch vụ, tốc độ kết nối hoặc những tiêu chuẩn khác.
- Giảm delay và jitter, do đó cải thiện QoS.
Hệ thống VoIP có thể bị ảnh hưởng bởi sự thiếu bảo mật của các dịch
vụ khác của mạng dữ liệu.

_VLAN phân theo chức năng_

![image](https://user-images.githubusercontent.com/69178270/137830704-6b312a82-f562-428d-835d-3dbc5519b712.png)

VLAN góp phần trong bảo mật hệ thống VoIP. Lưu lượng giữa các
VLAN được đảm bảo (trừ khi sử dụng router). Nó làm giảm các broadcast lưu
lượng trên mạng mà điện thoại phải nhận.

Quản lý lưu lượng bằng VLAN giúp cho lưu lượng SNMP và syslog
không bị nhiễu với dữ liệu, dễ dàng hơn trong việc quản lý mạng.

VLAN còn làm giảm nguy cơ DoS. Do muốn liên lạc giữa các VLAN
thì phải đi qua lớp mạng, các lưu lượng này sẽ bị lọc bởi các ACL trên lớp
mạng.

Để bảo đảm an toàn cho lưu lượng tại lớp 2 thì cần hạn chế quyền truy
cập bằng cổng console của Switch bằng cách sử dụng những phương pháp
chứng thực mạng như RADIUS hay AAA.

**2. VPN**

Công nghệ VPN cung cấp một phưong thức giao tiếp an toàn giữa các
mạng riêng dựa trên hạ tầng mạng công cộng (Internet). VPN thường được
dùng để kết nối các văn phòng, chi nhánh với nhau, các người dùng từ xa về
văn phòng chính. Công nghệ này có thể triển khai dùng các giải pháp sau:
Frame Relay, ATM hay Leased line.

Các giao thức và thuật toán được dùng trong VPN bao gồm DES (Data
Encryption Standard), Triple Des (3DES), IP Security (IPSec) và Internet key
Exchange (IKE).

Có hai loại kết nốit VPN:
+ Client – to – LAN
+ LAN – to – LAN

 _Client-to-LAN VPN_
 
 ![image](https://user-images.githubusercontent.com/69178270/137830863-b063d369-683c-4ced-a459-2a848991cc1a.png)

Công nghệ VPN dựa trên kỹ thuật đường hầm (tunneling). Kỹ thuật này
bao gồm đóng gói, truyền đi, giải mã, định tuyến. VPN có ba loại: Point – to –
Point Tunneling Protocol (PPTP), Layer 2 Tunneling Protocol (L2TP), IPsec.

**2.1 Point – to – Point Tunneling Protocol**

Đây là một giao thức phát triển bởi Microsoft, làm việc ở lớp 2 trong
mô hình OSI. PPTP đóng gói frame PPP vào gói IP bằng cách sử dụng GRE
(General Routing Encapsulation).Các hình thức đảm bảo sự bảo mật gồm:
chứng thực, mã hóa dữ liệu, lọc gói PPTP.

PPTP dùng các giao thức chứng thực PPP gồm: EAP, MS-CHAP (ver 1
và ver 2), PAP, trong đó MS-CHAP ver2 và EAP-TLS được xem là bảo mật
nhất vì cả VPN server và VPN client đều chứng thực lẫn nhau. Tải trong PPP
frame được mã hóa bằng RSA (Rivest, Shamir and Adleman), RC4 (Rivest
Cipher 4).

Trong MS-CHAP ver1 giá trị băm của LAN và của Windows NT được
sinh ra dựa trên cùng một password và được gửi song song từ client đến
server. Vì giá trị LAN manager hash được bảo mật kém nên các chương trình
bẻ password có thể tấn công được, khi đã biết được giá trị băm của LAN, có
thể dùng nó để tìm ra giá trị của Windows NT. MS-CHAP ver 2 khắc phục
được lỗi trên nhờ dùng cơ chế mã hóa.

RSA và RC4 cũng có các điểm yếu do khóa mã hóa dựa trên password
của user và cả client và server đều dùng chung khóa mã hóa.

**2.2  Layer 2 Tunneling Protocol**

L2TP là giao thức chuẩn của IETF (RFC 2661). Khác với PPTP, L2TP
có thể chạy trên nhiều chuyển mạch khác nhau như X.25, Frame Relay, ATM,
nhưng thường thì L2TP đóng gói PPP frame trong L2TP frame và dùng UDP
để truyền đi (không dùng GRE). Dùng UDP tốt hơn cho các dịch vụ thời gian
thực.

Bản thân L2TP không đảm bảo bảo mật, nó cần các giao thức vận
chuyển bên dưới làm điều này. Điều này được thực hiện qua việc bảo mật
trong PPP hoặc dùng IPsec.

_Cấu trúc L2PT_

![image](https://user-images.githubusercontent.com/69178270/137831164-e7f49aaf-169d-49c7-9e6b-d4a9b8e6fa5d.png)

**2.3 IP Security**

Với đặc điểm là dễ bị bắt gói trong mạng IP nên yêu cầu mã hóa là cần
thiết cho hệ thống VoIP. IPsec có thể bảo mật thông tin của EP và luồng dữ
liệu. IPsec là tập giao thức phát triển bởi IETF, bảo mật ở lớp IP.

IPSec bao gồm 4 thành phần: thành phần mã hóa (Encryption), trao đổi
khóa (Security Association), đảm bảo toàn vẹn dữ liệu (Data Integrity) và
kiểm tra nguồn gốc dữ liệu (Origin Authentication). 

IPsec gồm hai giao thức: Authenticaion Header (AH) và Encapsulating
Security Payload (ESP).
- AH: chứng thực data và chống replay, dùng giao thức IP số 51
- ESP: dùng giao thức IP số 50

ESP chỉ mã hóa và chứng thực trên gói ban đầu (không có header), còn
AH thì chứng thực toàn bộ gói (có header) và không mã hóa.

_Chứng thực và mã hóa của AH và ASP_

![image](https://user-images.githubusercontent.com/69178270/137831272-f03101e1-43fa-4d72-85ee-650778681658.png)

![image](https://user-images.githubusercontent.com/69178270/137831298-e241758f-c0c6-474a-9d2a-b9f4622f009c.png)

- IPsec gồm 2 mode:
     + Tunnel mode: tạo thêm một IP header mới gồm một địa chỉ nguồn và
một địa chỉ đích (có thể khác với địa chỉ nguồn và địa chỉ đích trong gói IP).
ESP chứng thực và mã hóa trên gói IP, còn AH chứng thực thêm một phần
của header mới.
     + Transport mode: ESP mã hóa và chứng thực gói IP (không có phần
header), AH thì có chứng thực thêm một phần header mới.

 _Cấu trúc gói IPsec ở transport mode_
 
 ![image](https://user-images.githubusercontent.com/69178270/137831435-bb7c4b69-7448-43c8-9843-c3e40dba42ca.png)

_Cấu trúc gói IPsec ở tunnel mode_

![image](https://user-images.githubusercontent.com/69178270/137831469-1ba66862-c95d-45fb-a909-0fb7bde2575c.png)

Trong quá trình thiết lập kết nối, VPN client và VPN server sẽ thương
lượng thuật toán mã hóa được sử dụng trong số các thuật toán sau: DES,
MD5, SHA, DH

Security Association (SA) thường được quản lý bời IKE. SA thường có
thể dùng pre-shared key, mã hóa RSA hoặc chữ ký số. IPsec chứng thực bằng
shared secret và certificate, bảo mật hơn so với PPTP chứng thực bằng
password của user.

**3. Firewalls**

Đóng vai trò rất quan trọng trong việc bảo mật mạng dữ liệu khỏi
những tấn công từ bên ngoài. Một số loại firewall cơ bản sau có thể bảo vệ dữ
liệu ở các lớp khác nhau trong mô hình OSI:

Packet filtering firewall

Circiut level gateway firewall

Personal firewall

Chức năng cơ bản của firewall được thiết kế không phải dành cho các
ứng dụng thời gian thực như VoIP nên việc thiết lập firewall cho hệ thống
VoIP sẽ làm cho hệ thống phức tạp hơn ở một số quá trình: port động
trunking, thủ tục thiết lập cuộc gọi.

Ngoài ra, firewall còn có nhiệm vụ điều khiển luồng thoại và dữ liệu.
Nếu không cài đặt firewall thì tất cả các lưu lượng đến và đi từ IP phone đều
phải được cho phép vì RTP dùng port UDP động, và như vậy thì tất cả các
port UDP đều phải mở, thiếu bảo mật. Vì vậy, IP phone thường đặt sau
firewall để tất cả các lưu lượng đều được kiểm soát mà không cần phải mở tất
cả các port UDP  firewall được sử dụng để cách ly về mặt luận lý giữa thoại
và dữ liệu.

**4. NAT (Network Address Translation)**

Là kỹ thuật mà địa chỉ nguồn hay địa chỉ đích thay đổi khi đi qua thiết
bị có chức năng NAT, cho phép nhiều host trong mạng nội bộ dùng chung
một địa chỉ IP để đi ra mạng bên ngoài.

Ngoài one-to-one mapping thì còn có many-to-one mapping hay còn
gọi là NAPT (Network Address Port Translation).

 _Quá trình thay đổi địa chỉ trong NAT_
 
 ![image](https://user-images.githubusercontent.com/69178270/137831636-2b3e4bfb-94b8-4cee-b54b-1ee3a80a7118.png)

**NAT có 4 chính sách:**
- **Full:** tất cả các yêu cầu từ cùng các host bên trong (địa chỉ IP và port)
được ánh xạ tới cùng một IP hay port đại diện bên ngoài, vì vậy bất kỳ một
host bên ngoài có thể gửi gói tới 1 host bên trong nếu biết địa chỉ được ánh xạ
đó.
- **Restricted:** chỉ cho phép 1 host bên ngoài với IP X gửi gói cho host
mạng bên trong nếu host của mạng bên trong đã gửi tới IP X một gói trước
đó.
- **Port restricted:** Giống Restricted one nhưng có thêm port. Chính sách
này được sử dụng để có thể dùng chung một địa chỉ IP đại diện bên ngoài.
- **Symmetric:** tất cả các request từ cùng 1 IP hay port đến 1 đích nào đó
được ánh xạ đi bằng 1 IP đại diện, nếu đi tới 1 đích khác thì nó sẽ đi bằng IP
đại diện khác  Chỉ có những host bên ngoài nhận được gói thì mới gửi gói
ngược trở lại các host bên trong được.

**Lợi ích của NAT:**
Giảm bớt số IP cần dùng bằng cách sử dụng chung 1 IP đại diện để đi
ra bên ngoài. Với việc sử dụng chung 1 IP đại diện để đi ra bên ngoài như vậy
thì mọi lưu lượng muốn truy nhập vào mạng bên trong thì phải qua NAT, bảo
mật hơn.

**5. Một số chú ý khi sử dụng NAT và firewall trong hệ thống VoIP.**

Ảnh hưởng đến QoS:

Việc thiết lập firewall và NAT gây ra trễvà jitter, làm giảm QoS. Về
bản chất, muốn cải thiện QoS thì quá trình xử lý gói khi qua firewall phải
nhanh, mà khả năng xử lý gói của firewall lại phụ thuộc vào năng lực của
CPU. CPU xử lý gói chậm là do: header của gói thoại phức tạp hơn gói IP
bình thường nên thời gian xử lý lâu hơn; số lượng gói RTP quá lớn có thể làm
firewall CPU bị qua tải

Cuộc gọi tới:

Khi một có một cuộc gọi tới thì các lưu lượng báo hiệu tới đi qua
firewall, cần phải mở một số port, điều này có thể gây nguy hiểm.

Với NAT điều này càng khó khăn vì NAT dùng port động, mà một host
bên ngoài chỉ có thể gọi cho 1 host nằm sau NAT nếu biết chính xác địa chỉ
IP và port của nó.

Voice Stream:

RTP dùng port động (1024-65534), còn RTPC quản lý luồng thoại
bằng một port ngẫu nhiên, khó mà đồng bộ port của RTP và RTPC. Nếu cả
hai host đều nằm sau NAT thì càng khó khăn.

NAT chỉ ánh xạ địa chỉ bên trong và địa chỉ đại diện đi ra bên ngoài
trong 1 khoảng thời gian t(s). Nếu kết nối bị đứt hay không có lưu lượng đi
qua NAT trong t(s) thì ánh xạ này sẽ biến mất.

Nếu dùng TCP thì khi kết nối TCP kết thúc thì cuộc gọi cũng kết thúc.

Nếu dùng UDP thì không nhận biết được vì UDP là phi kết nối. Nếu sử dụng
VAD thì có khả năng thông tin kết nối bị xóa trước khi cuộc gọi thật sự kết
thúc.

Mã hóa:

Việc mã hóa giúp đảm bảo tính toàn vẹn dữ liệu nhưng ta cũng gặp một
số vấn đề với nó khi sử dụng NAT và firewall:
+ Firewall sẽ chặn các gói có header được mã hóa.
+ NAT dấu đi IP bên trong với mạng bên ngoài nên phương pháp
chứng thực ESP và AH của Ipsec là không hợp lệ.

**6. Share-key (khoá dùng chung)**

Những cách tiếp cận Chìa khóa- Dùng chung:

Một cách tiếp cận tới sự chứng thực là một hệ thống mà trong đó người
gửi và người nhận chia sẻ một mật khẩu bí mật ( đôi khi tham chiếu tới như
một chìa khóa- dùng chung) mà không được biết đối với một bên thứ ba.

Người gửi tính toán một hash nội dung thông điệp và nối vào giá trị
hash đó với một thông điệp. Bên phía nhận được thông điệp, người nhận cũng
tính toán hash thông điệp với một mật khẩu dùng chung. Sau đó nó so sánh
hash đã được tính toán với giá trị hash mà được bổ sung vào thông điệp. Nếu
chúng phù hợp, sự toàn vẹn của thông điệp được bảo đảm như là tính xác thực
của người gửi.

Bạn có thể sử dụng mật khẩu dùng chung để mã hóa nội dung thông
điệp và truyền dữ liệu đã mã hóa tới người nhận. Trong trường hợp này, yêu
cầu riêng tư được đề cập không vì bên thứ ba có thể đánh hơi dữ liệu đang
vận chuyển và có thể nhìn nội dung thông báo của văn bản gốc. Người nhận
chạy giải thuật giải mã (sự mở khóa) với mật khẩu dùng chung như một trong
những đầu vào và tạo ra lại thông báo văn bản gốc.

Một hệ thống mà có nhiều nguồn dữ liệu có thể gặp phải yêu cầu xác
thực bằng việc bảo đảm rằng mỗi người gửi sử dụng một chìa khóa duy nhất
cho dữ liệu được gửi.

Trong một cách tiếp cận chìa khóa- dùng chung, người quản trị phải có
sự chuẩn bị đối với mật khẩu bí mật dùng chung. Trong một hệ thống mà có
nhiều cặp người gửi/ nhận, việc đương đầu với sự chuẩn bị có thể rất cao.

Ngoài ra, nếu một chìa khóa- dùng chung được thỏa hiệp ( stolen/ lost),
Mọi thiết bị sử dụng chìa khóa dùng chung cần được chuẩn bị với chìa khóa
dùng chung mới.

**7. Public-Key Cryptography (Mật mã chìa khoá-công cộng):**

Để làm giảm bớt sự đau đầu cho người quản trị với những cách tiếp cận
chìa khóa- dùng chung, bạn có thể sử dụng mật mã chìa khóa- công cộng.
Những khái niệm cơ bản trong mật mã chìa khóa chung là những chìa khóa và
những chữ ký số hóa không cân đối, được mô tả trong những mục sau đây:

Những chìa khóa không cân đối:

Những cặp chìa khóa không cân đối từng cặp là những chìa khóa
(thông thường của độ dài cố định) được tham chiếu tới như chìa khóa công
cộng và chìa khóa riêng tư mà có liên quan toán học đến lẫn nhau. Chúng
thông thường được đại diện trong hệ mười sáu và có những đặc trưng sau đây:
- Chỉ có chìa khoá công cộng tương ứng mới có thể giải mã giữ liệu mà
được mã hoá với một chìa khoá riêng tư.
- Chỉ có cặp chìa khoá riêng tư tương ứng mới có thể giải mã dữ liệu
mà được mã hoá với một chìa khoá công cộng
- Có mối quan hệ một-một giữa những chìa khoá.
- Chìa khoá riêng tư được giữ bí mật, còn chìa khoá công cộng thì được
chia sẻ với mọi người.

Đối với sự chứng thực, một người gửi có thể sử dụng chìa khóa riêng tư
của riêng mình để mã hóa thông điệp. Thông điệp chỉ có thể được giải mã với
chìa khóa công cộng tương ứng. Người nhận có thể giải mã thông điệp miễn
là anh ta có sự truy nhập tới chìa khóa công cộng của người gửi. Vì chỉ có
người gửi mới biết chìa khóa riêng tư nên anh ta buộc phải mã hóa thông
điệp.

Đối với truyền thông an toàn, một người gửi có thể mã hóa nội dung
thông báo bằng cách sử dụng kỹ thuật mật mã chìa khóa- công cộng. Anh ta
làm điều này bằng cách sử dụng chìa khóa công cộng của người nhận. Người
nhận sau đó có thể giải mã thông điệp với chìa khóa riêng tư tương ứng. Bởi
vì người nhận đã dự định có chìa khóa riêng tư nên anh ta có thể giải mã hông
điệp. Không có bên thứ ba nào khác có thể giải mã thông báo này, bởi vì
không ai khác biết chìa khóa riêng tư của người nhận.

Chú ý rằng người gửi phải sử dụng chìa khóa riêng tư để mã hóa thông
điệp cho những mục đích chứng thực, trong khi mà người nhận phải sử dụng
chìa khóa công cộng để mã hóa thông điệp cho sự truyền thông an toàn.

Trong thế giới thực, pha chứng thực đến đầu tiên. Sau khi người gửi và
người nhận xác nhận lẫn nhau thì họ chuyển tới pha truyền thông an toàn.

Sự mã hóa sử dụng những chìa khóa không cân đối là một tiến trình
cường độ cao của CPU. Bởi vậy, khi mà bao gồm rất nhiều dữ liệu, những
người quản lý nói chung sử dụng mật mã chìa khóa công cộng để đàm phán
một bí mật dùng chung duy nhất trên phiên họp. Họ dùng những ký số chìa
khóa cân đối bằng cách sử dụng bí mật dùng chung này cho phần còn lại của
phiên họp.

**8. IDS (Intrusion Detection)**

IDS là hệ thống giám sát tất cả các lưu lượng trong mạng. IDS là thiết
bị thụ động, lưu lượng không đi qua nó, mà nó chỉ lấy tất cả các gói trên mạng
để phân tích. Nếu có lưu lượng không bình thường bản thân nó sẽ phát cảnh
báo cho người quản trị mạng biết.

_Vị trí của IDS trong hệ thống_

![image](https://user-images.githubusercontent.com/69178270/137832113-02168dfe-8810-4a3e-a92c-94a15a8430f5.png)


Hoạt động của IDS:
- IDS theo dõi tất cả những trạng thái bình thường của hệ thống và do
đó phát hiện ra những tấn công bất thường vào hệ thống. Kiến trúc của nó
gồm Call State Fact Base, chứa các trạng thái điều khiển và các biến trạng
thái, cho phép theo dõi tiến trình của cuộc gọi. Thông tin trạng thái được cập
nhật từ Event Distributor. Attack Scenarino chứa những kiểu tấn công đã biết.
- IDS quản lý sự thay đổi trạng thái của các gói được phân tích bằng
chức năng Call basis. Tất cả gói của một cuộc gọi được phân thành một
nhóm, rồi lại chia thành các nhóm nhỏ dựa trên loại giao thức, rồi đưa vào các
bộ máy phân tích khác nhau, các bộ máy này được đồng bộ bằng các tham số
chung và các sự kiện nội bộ. Event Destributor cũng phân loại các gói nhận
được cho Attack Scenarino.

Các gói từ Event Destributor và thông tin trạng thái từ Attack
Scenarino/ Call State Fact Base được đưa đến Analysis Engine. Khi có sự bất
thường nào về giao thức hay trùng với một kiểu tấn công biết trước thì IDS sẽ
bật cờ cảnh báo cho người quản trị phân tích thêm.

_Cấu trúc bên trong của thiết bị IDS_

![image](https://user-images.githubusercontent.com/69178270/137832183-b8238fab-b2df-43aa-b739-f15f347ddd98.png)

### <a name="5"> Bảo vệ các thiết bị VoIP </a>


