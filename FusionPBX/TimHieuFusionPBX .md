### Tổng quan về tổng đài FusionPBX

Tổng đài FusionPBX  là một tổng đài sử dụng nền tảng mới là Freeswitch. So sánh về hiệu năng có thể vượt trội hơn Asterisk, nhưng độ phức tạp thì cũng cao hơn Asterisk.

FusionPBX là một gói phần mềm tổng đài cung cấp cho người dùng là cá nhân hoặc doanh nghiệp. Hỗ trợ rất tốt cho doanh nghiệp cung cấp tổng đài ảo. FusionPBX cho phép quản lý nhiều nhóm người dùng một cách độc lập với nhau. Cho phép định tuyến cuộc gọi thủ công, hoặc có sẵn. Hổ trợ rất nhiều các tính năng như: call center, fax, voicemail, conference, đa luồng, giao tiếp đa nền tảng. Cung cấp không giới hạn số lượng extension, voicemail, ghi âm, nhạc chờ,… tùy vào khả năng của server của bạn đang có.

Giới thiệu về giao diện và tính năng cơ bản của tổng đài FusionPBX.

### Mục lục

[Giao diện đăng nhập](#1)

[Dashboard](#2)

[Kết nối SIP trên tổng đài FusionPBX](#3)

[Multi-tenant](#4)

[Máy nhánh nội bộ](#5)

[Cấu hình thiết bị đầu cuối](#6)

[Chặn cuộc gọi (Call Block) trên tổng đài FusionPBX](#7)

[Chuyển cuộc gọi (Call Flow) trên tổng đài FusionPBX](#8)

[IVR](#9)


### <a name="1"> Giao diện đăng nhập </a>

Giao diện đăng nhập có thể tùy chỉnh màu sắc, logo theo cá nhận người dùng. Ngôn ngữ sử dụng là PHP.

![image](https://user-images.githubusercontent.com/69178270/136929221-4c56e3ee-f525-4591-b24c-f4504d84efd5.png)

### <a name="2"> Dashboard </a>

![image](https://user-images.githubusercontent.com/69178270/136929287-ff4d2874-5c75-4844-b0d6-4fdee66e4589.png)

Màn hình tổng quan về tổng đài FusionPBX, gồm các thông tin về voicemail, các thông tin của tổng đài, thông tin của server.

Thông tin về tổng đài: domains, thiết bị, máy nhánh, gateways, users, các đầu số, queue, IVR,… các số liệu tổng quan.

Thông tin server: phiên bản FusionPBX, phiên bản của hệ thống định tuyến (Switch) , thời gian hoạt động, thời gian hoạt động của server, dung lượng ổ cứng, CPU, databases,…

### <a name="3"> Kết nối SIP trên tổng đài FusionPBX </a>

Trên tổng đài FusionPBX các kết nối về SIP được định nghĩa như sau:

 - Kết nối trung kế (SIP trunk): được gọi là gateway định nghĩa là các cổng kết nối.
 
 - Đầu số hotline nhận cuộc gọi từ bên ngoài: được gọi là destination
 
 - Cấu hình cuộc gọi vào: được gọi là inbound route
 
 - Cấu hình gọi ra: được gọi là outbound route
 
 - Quản lý các dialplan: có phần quản lý dialplan bằng giao diện cụ thể, trực quan.
 
 - Máy nhánh: được gọi là extension.

Ngoài ra các dịch vụ khác được định nghĩa như nhau trên các tổng đài khác nhau.

Kết nối trên tổng đài FusionPBX được chia làm 2 dạng: Internal và External. Internal là các kết nối dùng cho extension kết nối lên tổng đài, mặc định dùng port 5060. External là kết nối trunk với các tổng đài khác, mặc định dùng port 5080. Đây là cấu hình quan trọng cần lưu ý khi sử dụng tổng đài FusionPBX.

![image](https://user-images.githubusercontent.com/69178270/136929528-39a9e768-43d8-40d3-87a0-5c706fbdc764.png)

Gateways được cấu hình theo 2 dạng là trunk peer và trunk account như các tổng đài SIP khác.

Ví dụ: kết nối trunk với tổng đài Mobifone như hình trên.

 - Gateway: tên đặt cho trunk với Mobifone

 - User/pass: chỉ dùng khi trunk account

 - Proxy: IP của server Mobifone, mặc định là port 5060. Nếu dùng port khác thì nhập IP:port

 - Register: chọn True khi dùng trunk account, False với trunk peers

![image](https://user-images.githubusercontent.com/69178270/136929800-d5bc32e4-188a-4a8c-984d-6b0496277350.png)

Hình trên là mô hình kết nối giữa tổng đài FusionPBX với nhà cung cấp đầu số SIP. Khi cung cấp thông tin cho nhà cung cấp nên lưu ý port 5080.

### <a name="4"> Multi-tenant </a>

Đây là một đặc điểm đặc biệt so với các tổng đài IP khác sử dụng nền tảng Asterisk. Với FusionPBX sử dụng nền tảng là Freeswitch, nó hổ trợ nhiều người dùng riêng biệt sử dụng chung một tổng đài mà không làm ảnh hướng đến người khác.

Một doanh nghiệp cho thuê tổng đài nội bộ, họ luôn muốn xây dựng một tổng đài mà có thể phục vụ cho nhiều khách hàng độc lập với nhau. Khách hàng thứ nhất dùng extension từ 101 đến 105, thì khách hàng thứ 2 cũng có thể dùng dãy extension đó cho công ty của họ.

Để đáp ứng được tính năng đó FusionPBX đã quản lý mỗi người dùng (người thuê tổng đài) theo tên miền ảo trên hệ thống FusionPBX. Mọi thông tin, extension, đầu số hotline,… đều sử dụng, quản lý riêng cho từng người dùng.

Mô hình kết nối như sau:

![image](https://user-images.githubusercontent.com/69178270/136930895-76f4c6a4-04c0-45e3-8214-81218f7ba18e.png)

Khách hàng kết nối vào domain ảo trong hệ thống FusionPBX, có một tổng đài riêng với đầy đủ các tính năng, quản lý dữ liệu, quản lý cuộc gọi, kết nối SIP trunk,…

### <a name="5"> Máy nhánh nội bộ </a>

Là số máy nội bộ của mỗi nhân viên khi sử dụng tổng đài nội bộ, được gọi là extension. 

Với cơ chế multi-tenant mỗi khách hàng đều có đầy đủ dãy số extension cho riêng mình mà không sợ bị trùng với các khách hàng khác. Mỗi extension bao gồm đầy đủ các tính năng: 

 - Số hotline dùng để gọi ra cho từng extension

 - Giới hạn số cuộc gọi tối đa.

 - Thời gian đổ chuông.

 - Nhạc chờ riêng biệt cho mỗi extension.

 - Hộp thư thoại (voicemail), cho phép gửi voicemail đến email.

 - Cấu hình chuyển máy: khi bận, không online, không trả lời, chuyển trực tiếp.

![image](https://user-images.githubusercontent.com/69178270/136931151-122b496a-ad68-45ff-997f-d013da504648.png)

Hình trên là cấu hình chuyển máy trong các trường hợp: chuyển trực tiếp đến số đích, khi máy bận, khi không trả lời, khi không extension không online, khi extension không online. 

Đặc biệt extension trên FusionPBX hỗ trợ tính năng multi registration, có thể đăng nhập cùng một extension trên nhiều thiết bị mà vẫn giữ được kết nối với tổng đài.

### <a name="6"> Cấu hình thiết bị đầu cuối </a>

![image](https://user-images.githubusercontent.com/69178270/136931220-8bd453a6-4f62-4db3-a9d4-0cf66cd15a83.png)

FusionPBX hổ trợ việc quản lý thiết bị đầu cuối thông qua việc đồng bộ dữ liệu từ giao diện web của tổng đài. Tại đây người quản trị hệ thống chỉ cần thiết lập các thông số của IP phone, ngay khi IP phone có kết nối internet và thiết lập kết nối đến tổng đài thì tất cả các cấu hình sẽ được thiết lập một cách tự động. Tính năng này rất hữu ích cho việc triển khai số lượng lớn các thiết bị đầu cuối. Ngoài ra còn dễ quản lý, đặt lại cấu hình ngay trên web khi có sự cố mà không cần thiết lập trên IP phone.

