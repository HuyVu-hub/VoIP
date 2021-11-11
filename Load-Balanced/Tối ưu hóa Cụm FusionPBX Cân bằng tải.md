### Tối ưu hóa Cụm FusionPBX Cân bằng tải

Như đã nêu trước đây, FusionPBX Cluster cân bằng tải sẽ cho phép bạn đăng ký một số điểm cuối trong các máy chủ khác nhau trong khi bạn có thể giao tiếp giữa các phần mở rộng trong cùng một ngữ cảnh. Mặc dù điều này thực sự tuyệt vời, nhưng nó có thể dẫn đến chi phí CPU. Một cụm không được tối ưu hóa như thế này sẽ buộc FreeSWITCH ủy quyền luồng RTP, theo tôi, nó vô dụng và lãng phí tài nguyên CPU quý giá.

Hình ảnh sau đây cho thấy luồng SIP / RTP.

![image](https://user-images.githubusercontent.com/69178270/141259720-bb803ac7-9362-40cf-9a34-ad7f34dea842.png)

Như bạn thấy, bất kể PBX có đủ thông minh để xác định xem người dùng có được đăng ký cục bộ hay người dùng đó được đăng ký trong một mạng ngang hàng hay không, thì luồng RTP sẽ chảy giữa PBX`1 và PBX 2. Bây giờ, để tránh trường hợp xấu nhất, chúng ta hãy suy nghĩ chúng tôi có chuyển mã. Tôi không cần phải giải thích ưu điểm và nhược điểm của điều này, nhưng chỉ cần nhắc bạn rằng chuyển mã là CPU đói.

**Giảm tải Cụm FusionPBX Cân bằng tải của bạn**

Khái niệm này thực sự đơn giản. Vì bạn không cần PBX đầu tiên để ủy quyền RTP. Nó là thuận tiện để đưa nó ra khỏi con đường. Điều này có thể thực hiện được vì giao thức SIP có thể cho các điểm cuối và các PBX khác biết rằng luồng RTP có thể được quản lý bởi một thiết bị khác với thiết bị đang xử lý SIP. Nếu bạn tò mò, bạn có thể muốn đọc về tải trọng SDP, đặc biệt là tập trung vào các tiêu đề a, c và o.

![image](https://user-images.githubusercontent.com/69178270/141260107-30442fd8-ff9e-4eff-aafe-4874ccd36f36.png)

Khi người dùng1 ở miền A cố gắng gọi người dùng2 ở miền A, PBX 1 phát hiện đó không phải là một tiện ích mở rộng cục bộ mà nó thực sự đang hoạt động trong một PBX ngang hàng. Khi PBX 1 biết người dùng 2 được đăng ký ở đâu, nó cho biết trong câu trả lời của INVITE (từ người dùng 1) rằng RTP sẽ được xử lý bởi PBX 2. PBX 1 gửi lời mời đến PBX 2 nói rằng RTP sẽ được xử lý bởi điểm cuối (người dùng1 tại miền A). Do đó, PBX 1 nằm ngoài dòng RTP và do đó, chúng tôi không làm quá tải CPU của PBX 1 mà không có lý do.

Hãy cẩn thận với các bộ định tuyến không thân thiện mặc dù điều này rất phổ biến hiện nay, bạn vẫn có thể tìm thấy một số bộ định tuyến SIP không thân thiện. Cũng xin lưu ý rằng PBX 1 và PBX 2 phải có một IP công cộng được đính kèm.

