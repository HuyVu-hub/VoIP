# Nhắc lại một chút:

## SIP là gì?

SIP là từ viết tắt của Session Initiation Protocol có nghĩa là giao thức khởi tạo phiên. Đây là một giao thức báo hiệu để liên kết giữa điện thoại với tổng đài IP, hoặc giữa tổng đài IP với các nhà cung cấp dịch vụ sip trunking.

## SIP Server là gì?

SIP Server, còn được gọi là SIP Proxy đảm nhiệm xử lý tất cả việc quản lý các cuộc gọi SIP trong mạng và chịu trách nhiệm nhận các yêu cầu từ các user agent (tác nhân người dùng)
để thực hiện và kết thúc cuộc gọi. Máy chủ SIP thường được bao gồm bên trong các tổng đài IP hỗ trợ SIP ( SIP-enabled IP-PBXs ). 

## Hệ thống PBX là gì?

PBX là tên viết tắt của cụm từ tiếng Anh Private Branch Exchange (Tổng đài Nhánh Riêng), là một mạng điện thoại riêng được sử dụng trong phạm vi một công ty. Những người sử dụng hệ thống điện thoại PBX dùng chung một số đường điện thoại ngoài để thực hiện các cuộc gọi ra bên ngoài.

![image](https://user-images.githubusercontent.com/55483458/142436395-3d8e242d-ec05-4593-865c-101a99480b29.png)

Hệ thống PBX kết nối các điện thoại nội bộ trong một doanh nghiệp đồng thời cũng kết nối chúng vào mạng điện thoại chuyển mạch công cộng (PSTN). Một trong những khuynh hướng mới nhất trong sự phát triển của hệ thống điện thoại PBX là VoIP PBX, hay còn được gọi là IP PBX, sử dụng Giao thức Internet để truyền dẫn các cuộc gọi.

# Một số công nghệ SIP Server:

## Kamailio

Kamailio ® (kế thừa của OpenSER và SER - SIP Express Router - trước đây) là một Máy chủ SIP nguồn mở, có thể xử lý hàng nghìn thiết lập cuộc gọi mỗi giây. 
Kamailio có thể được sử dụng để xây dựng các nền tảng lớn cho VoIP và truyền thông thời gian thực - hiện diện, WebRTC, nhắn tin tức thời và các ứng dụng khác. 
Hơn nữa, nó có thể dễ dàng được sử dụng để mở rộng các cổng SIP-to-PSTN(Mạng điện thoại chuyển mạch công cộng), hệ thống PBX hoặc máy chủ đa phương tiện như Asterisk ™, FreeSWITCH ™ hoặc SEMS.

![image](https://user-images.githubusercontent.com/55483458/142432883-5178ab6a-5daa-448a-b342-8a2f24a6e9de.png)


## OpenSIPs

OpenSIPS (Open SIP Server) là một hệ thống mã nguồn mở của một SIP server Nó không chỉ là một SIP proxy/router vì OpenSIPS bao gồm những chức năng ở cấp độ ứng dụng, 
OpenSIPS nhƣ là một máy chủ SIP,là thành phần cốt lõi của bất kỳ giải pháp VoIP trên SIP server đa chức năng,đa mụcđích:router,switch,application-server,redirect-server,gateway,
load-balancer.. Với một cơ cấu định tuyến linh hoạt và  tùy  biến  OpenSIPs hợp nhất tiếng nói, video, IM(Instant  Message) và các dịch vụ Presence một cách có hiệu quả cao nhờ thiết kế 
dạng Module có thể mở rộng. Vì vậy,OpenSIPS có hiệu quả cao,đáng  tin  cậy. Không những thế OpenSIPSlà  một trong những SIP Server nhanh nhất,với một thông lượng được xác định
nó như là mộtgiải pháp tối ưu cho doanh nghiệp,các ngành công nghiệp.

![image](https://user-images.githubusercontent.com/55483458/142432815-3cce71f5-05a5-4925-964a-533a26d77a3c.png)

## Asterisk

Asterisk là một dịch vụ chuyển mạch điện thoại mã nguồn mở và trao đổi chi nhánh riêng cho Linux.

![image](https://user-images.githubusercontent.com/55483458/142433655-8fd63ff2-1062-4165-8836-2ffe1db9e013.png)

## FreeSWITCH

FreeSWITCH là một ứng dụng đa phương tiện mã nguồn mở được thiết kế để hỗ trợ các protools phổ biến như SIP và WebRTC, đồng thời cung cấp một nền tảng để phát triển các ứng dụng thoại và video.

![image](https://user-images.githubusercontent.com/55483458/142433693-1fda1f06-7dae-43c5-a522-9c0915baf8d8.png)

## FusionPBX  

FusionPBX là một GUI FreeSWITCH mã nguồn mở.

![image](https://user-images.githubusercontent.com/55483458/142433764-1d4d4622-f434-4976-aa36-53002f0e8152.png)

## FreePBX

FreePBX là một GUI mã nguồn mở dựa trên web kiểm soát và quản lý Asterisk (PBX).

![image](https://user-images.githubusercontent.com/55483458/142433838-2a07bfed-dd71-42c6-8914-6a34ee654db0.png)

## Wazo IP Telephony

Wazo IP Telephony là một nền tảng mã nguồn mở được thiết kế để xây dựng nền tảng viễn thông IP của riêng bạn và cung cấp tính di động, tính di động của viễn thông IP.

![image](https://user-images.githubusercontent.com/55483458/142433900-d9337060-6f04-451b-a422-a02565318945.png)

## 3CX

3CX là một sàn giao dịch chi nhánh riêng (PBX) dựa trên phần mềm dựa trên tiêu chuẩn SIP ( Session Initiation Protocol ). 
Nó cho phép các phần mở rộng thực hiện cuộc gọi qua mạng điện thoại chuyển mạch công cộng (PSTN) hoặc qua các dịch vụ Thoại qua Giao thức Internet (VoIP). 3CX cho Windows , 
Linux , Raspberry Pi [1] và Đám mây là hệ thống điện thoại doanh nghiệp IP hỗ trợ điện thoại cứng / mềm tiêu chuẩn SIP, dịch vụ VoIP và đường dây điện thoại PSTN truyền thống.

![image](https://user-images.githubusercontent.com/55483458/142433934-e0b40d13-56f0-4ae6-9a77-cfe9066d20d7.png)



