### Tối ưu hóa Cụm FusionPBX Cân bằng tải

Như đã nêu trước đây, FusionPBX Cluster cân bằng tải sẽ cho phép bạn đăng ký một số điểm cuối trong các máy chủ khác nhau trong khi bạn có thể giao tiếp giữa các phần mở rộng trong cùng một ngữ cảnh. Mặc dù điều này thực sự tuyệt vời, nhưng nó có thể dẫn đến chi phí CPU. Một cụm không được tối ưu hóa như thế này sẽ buộc FreeSWITCH ủy quyền luồng RTP, theo tôi, nó vô dụng và lãng phí tài nguyên CPU quý giá.

Hình ảnh sau đây cho thấy luồng SIP / RTP.

![image](https://user-images.githubusercontent.com/69178270/141259720-bb803ac7-9362-40cf-9a34-ad7f34dea842.png)

Như bạn thấy, bất kể PBX có đủ thông minh để xác định xem người dùng có được đăng ký cục bộ hay người dùng đó được đăng ký trong một mạng ngang hàng hay không, thì luồng RTP sẽ chảy giữa PBX`1 và PBX 2. Bây giờ, để tránh trường hợp xấu nhất, chúng ta hãy suy nghĩ chúng tôi có chuyển mã. Tôi không cần phải giải thích ưu điểm và nhược điểm của điều này, nhưng chỉ cần nhắc bạn rằng chuyển mã là CPU đói.

**Giảm tải Cụm FusionPBX Cân bằng tải của bạn**

