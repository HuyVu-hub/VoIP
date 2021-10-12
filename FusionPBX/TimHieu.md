### Tổng quan về tổng đài FusionPBX 

FusionPBX là một gói phần mềm tổng đài cung cấp cho người dùng là cá nhân hoặc doanh nghiệp. Hỗ trợ rất tốt cho doanh nghiệp cung cấp tổng đài ảo. FusionPBX cho phép quản lý nhiều nhóm người dùng một cách độc lập với nhau. Cho phép định tuyến cuộc gọi thủ công, hoặc có sẵn. Hổ trợ rất nhiều các tính năng như: call center, fax, voicemail, conference, đa luồng, giao tiếp đa nền tảng. Cung cấp không giới hạn số lượng extension, voicemail, ghi âm, nhạc chờ,… tùy vào khả năng của server của bạn đang có.

Giới thiệu về giao diện và tính năng cơ bản của tổng đài FusionPBX.

### Mục lục

[Giao diện đăng nhập](#1)

[Dashboard](#2)

[Kết nối SIP trên tổng đài FusionPBX](#3)

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
