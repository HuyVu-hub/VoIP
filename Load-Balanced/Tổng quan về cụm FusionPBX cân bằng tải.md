### Tổng quan về cụm FusionPBX High Availability

Điều đầu tiên tôi cần làm rõ rằng một cụm cân bằng tải không phải là một cụm có tính khả dụng cao. Mặc dù cả hai loại phương pháp tiếp cận không loại trừ lẫn nhau (bạn có thể kết hợp chúng), nhưng ưu và nhược điểm của chúng là khác nhau và cả cách chúng hoạt động. 

**Tổng quan về load-balanced cluster**

Việc triển khai cụm High Availability cần ít nhất 5 máy chủ. Hình ảnh sau đây cho thấy một triển khai cơ bản.

![image](https://user-images.githubusercontent.com/69178270/141229831-b89d0033-3e8f-48fa-a18e-03de5d234b83.png)

Các yếu tố là:

 - hai máy chủ PBX chịu trách nhiệm xử lý luồng SIP và RTP. Các máy chủ này đã cài đặt FusionPBX, FreeSWITCH, Memcached, các tập lệnh hỗ trợ Lua và nhiều thứ khác.

 - hai máy chủ Cơ sở dữ liệu là những máy chủ lưu trữ tất cả thông tin mà FreeSWITCH và FusionPBX sử dụng.

 - Một máy chủ Arbitrator - Trọng tài - mà vai trò duy nhất của nó là tránh vấn đề phân tách não khi giao tiếp giữa hai nút cơ sở dữ liệu bị hỏng (Có thể có hoặc không tùy vào yêu cầu của hệ thống, chỉ cần đảm bảo các Data Centre luôn được đồng bộ với nhau).

**Ưu, nhược điểm và tác dụng phụ của một cụm FusionPBX cân bằng tải**

Bất kỳ chủ sở hữu cụm nào cũng cần nhớ rằng cụm này còn lâu mới trở thành một máy chủ độc lập. Có sự khác biệt bên trong; các luồng thông tin là khác nhau và do đó, cách thức hoạt động của nó cũng không giống nhau. Đầu tiên, hãy liệt kê những ưu điểm:

 - Khả năng chịu lỗi: khi một nút gặp sự cố, nút kia sẽ chịu tải

 - Nhiều dung lượng hơn: vì cả hai nút đều hoạt động, bạn có thể chứa nhiều tiện ích mở rộng hơn trong cụm của mình

 - Có thể lây lan: các nút trong cụm không cần phải ở trong cùng một trung tâm dữ liệu.

 - Kết nối máy chủ chéo: ví dụ: máy chủ mở rộng 100 @ miềnA được đăng ký trong PBX 1 và máy chủ mở rộng 200 @ miềnB trong PBX2. Khi máy lẻ 100 gọi 200, nó sẽ kết nối với nhau. Các PBX nhận thức được các đồng nghiệp của họ và họ sẽ định tuyến cuộc gọi đúng cách.

Bây giờ là khuyết điểm:

 - Trong trường hợp xảy ra sự cố, một số điểm cuối có thể cần khởi động lại để có thể kết nối lại. Điều này là do một số hiệu ứng DNS, có thể do DNS cục bộ hoặc cách hoạt động của phần sụn của điện thoại IP.

 - Kết nối máy chủ chéo nghiêm ngặt: một số hội nghị, hàng đợi hoặc nhóm vòng phức tạp có thể không hoạt động nếu các điểm cuối của một đối tượng thuê nhất định được kết nối với các nút khác nhau. Cách giải quyết rõ ràng là đảm bảo tất cả các điểm cuối của một đối tượng thuê nhất định được đăng ký trong cùng một PBX.

Một số tác dụng phụ không tốt cũng không xấu, nhưng chúng khác với một máy chủ độc lập:

 - Xóa Memcached: trong trường hợp xấu nhất, bạn không biết máy chủ mình đang kết nối để thực hiện sửa đổi gói quay số, bạn không thể chắc chắn nơi các điểm cuối đang đăng ký. Ví dụ: trường hợp xấu nhất bạn đang chỉnh sửa kế hoạch quay số trên PBX 1 nhưng phần mở rộng được đăng ký trong PBX 2. Bạn sẽ cần xóa Memcached trong PBX 2 theo cách thủ công. Tùy thuộc vào tính chất của PBX, bạn có thể chọn đặt crontabs để thực hiện thường xuyên hoặc chỉ thực hiện theo yêu cầu. Điều đó phụ thuộc vào bạn; Bạn đã được cảnh báo.

 - Đồng bộ hóa tệp: như hành vi Memcached, điều này cũng giống như vậy, bạn không biết cuộc gọi đang thực hiện ở đâu. Mặc dù cụm PBX có cơ chế đồng bộ hóa (bất kể bạn đã chọn cái nào), nhưng điều quan trọng ở đây là đặt một chính sách đồng bộ hóa. Việc đồng bộ hóa cứ năm giây là rất tốn kém cho CPU, bạn sẽ lãng phí tài nguyên CPU có giá trị, điều này sẽ ảnh hưởng đến chất lượng dịch vụ của bạn. Bạn có thể chọn chính sách đồng bộ hóa năm phút hoặc chính sách nửa đêm. Dù đó là gì, đừng quên điều này, rất phổ biến phàn nàn về việc IVR không phát bản ghi thích hợp và nguyên nhân là do tệp chưa được đồng bộ hóa.

 - Chính sách DNS: Tôi đã viết một bài báo về mối quan hệ giữa DNS với VoIP. Nếu bạn không chọn giải pháp DNS thông minh, bạn chỉ cần cẩn thận. Ví dụ: nếu khách hàng A có 90% điểm cuối của họ gần với PBX 1, thì bản ghi SRV và A của bạn phải trỏ đến IP PBX 1 trước tiên.

 - Khởi động lại máy chủ cơ sở dữ liệu: bất kỳ lý do gì khiến bạn khởi động lại một nút cơ sở dữ liệu, đừng bao giờ khởi động lại tất cả chúng cùng một lúc. Khởi động lại một, đợi nó phục hồi, sau đó khởi động lại lần tiếp theo, v.v.

**Luồng thông tin**

Luồng thông tin của một cụm hơi khác một chút so với một máy chủ FusionPBX độc lập. Trong triển khai độc lập, tất cả các luồng gần như là cục bộ, sau đó chỉ có luồng bên ngoài là luồng liên quan đến các điểm cuối hoặc bắc cầu một cuộc gọi đến PSTN. Một cụm có một số luồng bổ sung đi qua giữa các máy chủ.

![image](https://user-images.githubusercontent.com/69178270/141232441-b92b433f-b2de-4973-9c3a-b78e72af9e1d.png)

Các điểm cuối sẽ quyết định nút nào sẽ kết nối bằng cách thực hiện các yêu cầu DNS. Khi một máy chủ kết nối, PBX sẽ ghi lại một số thông tin trong cơ sở dữ liệu. Cơ sở dữ liệu sẽ sao chép thông tin với nút khác.

Khi một cuộc gọi xảy ra, PBX nhận sẽ giữ tất cả các xử lý cục bộ càng nhiều càng tốt; có một điểm trong kế hoạch quay số mà nó sẽ cần phải được mở rộng. Sau đó, PBX sẽ tham khảo cơ sở dữ liệu để biết nơi đăng ký điểm cuối mong muốn. Trường hợp tốt nhất, phần mở rộng được đăng ký trong cùng một tổng đài nhận và luồng sẽ được giữ cục bộ; Trong trường hợp xấu nhất, tiện ích mở rộng được tìm thấy đang hoạt động trong một máy chủ khác, khi đó PBX sẽ kết nối với PBX khác. PBX khác sau đó sẽ kết nối với điểm cuối cục bộ hiện tại của nó và cuộc gọi sẽ được thực hiện.

**Khả năng chịu lỗi trong một cụm FusionPBX High Availability**

Khi một sự kiện chịu lỗi xảy ra, các người dùng cuối là những người sẽ quyết định phải làm gì. Nhờ thông tin từ DNS, các tiện ích mở rộng sẽ biết ai là lựa chọn tốt thứ hai của họ và họ sẽ kết nối với PBX đang hoạt động. Tùy thuộc vào thương hiệu điểm cuối, một số Điện thoại IP có thể cần khởi động lại; đôi khi bộ nhớ đệm DNS cục bộ không đáp ứng được DNS TTL và có thể cần khởi động lại bộ định tuyến.

Hình ảnh sau đây cho thấy sự kiện chịu lỗi.

![image](https://user-images.githubusercontent.com/69178270/141232533-5af069b9-043f-427b-9949-e80a38282f98.png)

Điện thoại IP đã được kết nối với PBX 1 bây giờ nó kết nối với PBX 2. PBX 1 là tùy chọn tốt nhất cho tiện ích mở rộng đó, nhưng vì nó không khả dụng nên nó sẽ kết nối với tùy chọn tốt nhất thứ hai; trong trường hợp này là PBX 2.


Link bài viết về tối ưu hóa RTP / SDP cho mô hình trên:

https://inside-out.xyz/technology/optimize-your-load-balanced-fusionpbx-cluster.html
