### Các thành phần

Nền tảng Wazuh cung cấp các tính năng để bảo vệ khối lượng công việc đám mây, vùng chứa và máy chủ của bạn. Chúng bao gồm phân tích dữ liệu nhật ký, phát hiện xâm nhập và phần mềm độc hại, giám sát tính toàn vẹn của tệp, đánh giá cấu hình, phát hiện lỗ hổng bảo mật và hỗ trợ tuân thủ quy định. Giải pháp Wazuh dựa trên ba thành phần sau:

 - Tác nhân Wazuh : Được cài đặt trên các thiết bị đầu cuối như máy tính xách tay, máy tính để bàn, máy chủ, phiên bản đám mây hoặc máy ảo, nó cung cấp khả năng phòng ngừa, phát hiện và phản ứng. Nó hỗ trợ các nền tảng Windows, Linux, MacOS, HP-UX, Solaris và AIX.

 - Máy chủ Wazuh : Nó phân tích dữ liệu nhận được từ các tác nhân, xử lý nó thông qua bộ giải mã và quy tắc, đồng thời sử dụng thông tin tình báo về mối đe dọa để tìm kiếm các chỉ số thỏa hiệp (IOC) nổi tiếng. Một máy chủ duy nhất có thể phân tích dữ liệu từ hàng trăm hoặc hàng nghìn tác nhân và mở rộng quy mô theo chiều ngang khi được thiết lập dưới dạng một cụm. Máy chủ cũng được sử dụng để quản lý các tác nhân, cấu hình và nâng cấp chúng từ xa khi cần thiết.

 - Elastic Stack : Nó lập chỉ mục và lưu trữ các cảnh báo do máy chủ Wazuh tạo ra. Bên cạnh đó, sự tích hợp giữa Wazuh và Kibana cung cấp một giao diện người dùng mạnh mẽ để trực quan hóa và phân tích dữ liệu. Giao diện này cũng được sử dụng để quản lý cấu hình Wazuh và theo dõi trạng thái của nó.

Ngoài khả năng giám sát dựa trên tác nhân, nền tảng Wazuh có thể giám sát các thiết bị không có tác nhân như tường lửa, thiết bị chuyển mạch, bộ định tuyến hoặc IDS mạng, trong số những thiết bị khác. Ví dụ: dữ liệu nhật ký hệ thống có thể được thu thập thông qua Syslog và cấu hình của nó có thể được giám sát thông qua việc thăm dò dữ liệu định kỳ (ví dụ: thông qua SSH hoặc thông qua API).

Sơ đồ dưới đây đại diện cho các thành phần Wazuh và luồng dữ liệu. Nó cho thấy ba thành phần chính của giải pháp, đó là những tác nhân Wazuh , các máy chủ Wazuh , và đàn hồi ngăn xếp.

![image](https://user-images.githubusercontent.com/69178270/143823109-aeee63a5-9d2c-4402-a24a-e93a45619cac.png)

**Wazuh agent** - Đại lý Wazuh

Tác nhân Wazuh chạy trên Linux, Windows, macOS, Solaris, AIX và các hệ điều hành khác. Nó có thể được triển khai cho máy tính xách tay, máy tính để bàn, máy chủ, phiên bản đám mây, vùng chứa hoặc máy ảo. Nó cung cấp khả năng ngăn ngừa, phát hiện và phản ứng các mối đe dọa. Nó cũng được sử dụng để thu thập các loại dữ liệu hệ thống và ứng dụng khác nhau để chuyển tiếp đến máy chủ Wazuh thông qua một kênh được mã hóa và xác thực.


_1. Kiến trúc đại lý_

Tác nhân Wazuh có kiến trúc mô-đun, nơi các thành phần khác nhau đảm nhiệm các nhiệm vụ riêng của chúng: giám sát hệ thống tệp, đọc thông báo nhật ký, thu thập dữ liệu kiểm kê, quét cấu hình hệ thống, tìm kiếm phần mềm độc hại, v.v. Người dùng có thể bật hoặc tắt các mô-đun tác nhân thông qua cấu hình cài đặt, điều chỉnh giải pháp cho các trường hợp sử dụng cụ thể của chúng.

Sơ đồ bên dưới thể hiện kiến trúc tác nhân và các thành phần:

![image](https://user-images.githubusercontent.com/69178270/143823229-535f1031-a15c-4d9e-b9e5-0aa5e157b9dd.png)

_2. Mô-đun đại lý_

Tất cả các mô-đun đại lý có các mục đích và cài đặt khác nhau. Dưới đây là một mô tả ngắn gọn về những gì họ làm:

 - Bộ thu thập nhật ký: Thành phần tác nhân này có thể đọc các tệp nhật ký phẳng và các sự kiện Windows, thu thập thông báo nhật ký hệ điều hành và ứng dụng. Nó hỗ trợ bộ lọc XPath cho các sự kiện Windows và nhận dạng các định dạng nhiều dòng (ví dụ: nhật ký Kiểm toán Linux). Nó cũng có thể làm phong phú thêm các sự kiện JSON với siêu dữ liệu bổ sung.

 - Thực thi lệnh: Đặc vụ có thể chạy các lệnh được ủy quyền theo định kỳ, thu thập kết quả đầu ra của chúng và báo cáo lại cho máy chủ Wazuh để phân tích thêm. Mô-đun này có thể được sử dụng để đáp ứng các mục đích khác nhau (ví dụ: theo dõi dung lượng ổ cứng còn lại, lấy danh sách người dùng đăng nhập lần cuối, v.v.).

 - Giám sát toàn vẹn tệp (FIM): Mô-đun này giám sát hệ thống tệp, báo cáo khi tệp được tạo, xóa hoặc sửa đổi. Nó theo dõi các thuộc tính, quyền, quyền sở hữu và nội dung của tệp. Khi một sự kiện xảy ra, nó nắm bắt thông tin chi tiết về ai , cái gì và khi nào trong thời gian thực. Ngoài ra, mô-đun này xây dựng và duy trì cơ sở dữ liệu với trạng thái của các tệp được giám sát, cho phép chạy các truy vấn từ xa.

 - Đánh giá cấu hình bảo mật (SCA): Thành phần này cung cấp đánh giá cấu hình liên tục, sử dụng các kiểm tra bên ngoài dựa trên điểm chuẩn của Trung tâm Bảo mật Internet (CIS). Người dùng cũng có thể tạo kiểm tra SCA của riêng họ để giám sát và thực thi các chính sách bảo mật của họ.

 - Kiểm kê hệ thống: Mô-đun tác nhân này định kỳ chạy quét, thu thập dữ liệu kiểm kê như phiên bản hệ điều hành, giao diện mạng, quy trình đang chạy, ứng dụng đã cài đặt và danh sách các cổng đang mở. Kết quả quét được lưu trữ vào cơ sở dữ liệu SQLite cục bộ có thể được truy vấn từ xa.

 - Phát hiện phần mềm độc hại: Sử dụng cách tiếp cận không dựa trên chữ ký, thành phần này có khả năng phát hiện sự bất thường và sự hiện diện có thể có của rootkit. Giám sát các cuộc gọi hệ thống, nó tìm kiếm các quy trình ẩn, tệp ẩn và cổng ẩn.

 - Phản hồi tích cực: Mô-đun này chạy các hành động tự động khi các mối đe dọa được phát hiện. Trong số những thứ khác, nó có thể chặn kết nối mạng, dừng quá trình đang chạy hoặc xóa tệp độc hại. Người dùng cũng có thể tạo phản hồi tùy chỉnh khi cần thiết (ví dụ: chạy tệp nhị phân trong hộp cát, nắm bắt lưu lượng kết nối mạng, quét tệp bằng phần mềm chống vi-rút, v.v.).

 - Giám sát bảo mật vùng chứa: Mô-đun tác nhân này được tích hợp với API Docker Engine để theo dõi những thay đổi trong môi trường được chứa trong vùng chứa. Ví dụ: nó phát hiện các thay đổi đối với hình ảnh vùng chứa, cấu hình mạng hoặc khối lượng dữ liệu. Bên cạnh đó, nó cảnh báo về các vùng chứa đang chạy ở chế độ đặc quyền và về việc người dùng thực hiện các lệnh trong một vùng chứa đang chạy.

 - Giám sát bảo mật đám mây: Thành phần này giám sát các nhà cung cấp đám mây như Amazon AWS, Microsoft Azure hoặc Google GCP. Nó tự nhiên giao tiếp với các API của họ. Nó có khả năng phát hiện các thay đổi đối với cơ sở hạ tầng đám mây (ví dụ: người dùng mới được tạo, nhóm bảo mật được sửa đổi, phiên bản đám mây bị dừng, v.v.) và thu thập dữ liệu nhật ký dịch vụ đám mây (ví dụ: AWS Cloudtrail, AWS Macie, AWS GuardDuty , Azure Active Directory, v.v.)

_3. Giao tiếp với máy chủ Wazuh_

Đặc vụ Wazuh giao tiếp với máy chủ Wazuh để gửi dữ liệu đã thu thập và các sự kiện liên quan đến bảo mật. Bên cạnh đó, tác nhân gửi dữ liệu hoạt động, báo cáo cấu hình và trạng thái của nó. Sau khi kết nối, tác nhân có thể được nâng cấp, giám sát và định cấu hình từ xa từ máy chủ Wazuh.

Giao tiếp giữa tác nhân Wazuh với máy chủ diễn ra thông qua một kênh bảo mật (TCP hoặc UDP), cung cấp mã hóa và nén dữ liệu trong thời gian thực. Ngoài ra, nó bao gồm các cơ chế kiểm soát luồng để tránh lũ lụt, các sự kiện xếp hàng khi cần thiết và bảo vệ băng thông mạng.

Việc đăng ký đại lý Wazuh là cần thiết trước khi kết nối nó với máy chủ lần đầu tiên. Quy trình này cung cấp cho đại lý một khóa chia sẻ trước duy nhất được sử dụng để xác thực và mã hóa dữ liệu.

**Máy chủ Wazuh**

Thành phần máy chủ Wazuh phụ trách phân tích dữ liệu nhận được từ các tác nhân , kích hoạt cảnh báo khi phát hiện ra các mối đe dọa hoặc điểm bất thường. Nó cũng được sử dụng để quản lý cấu hình tác nhân từ xa và theo dõi trạng thái của chúng.

Máy chủ Wazuh sử dụng các nguồn thông tin tình báo về mối đe dọa để cải thiện khả năng phát hiện của nó. Nó cũng sử dụng các yêu cầu tuân thủ quy định (ví dụ như PCI DSS, HIPAA, NIST 800-53…) và khung Mitre ATT & CK để làm phong phú thêm dữ liệu cảnh báo, cung cấp ngữ cảnh hữu ích xung quanh chúng.

Ngoài ra, máy chủ Wazuh có thể được tích hợp với phần mềm bên ngoài như hệ thống bán vé (ví dụ: Service Now, Jira, PagerDuty) và các nền tảng nhắn tin tức thời (ví dụ: Slack). Điều này thuận tiện để hợp lý hóa các hoạt động bảo mật.


_1. Kiến trúc máy chủ_

Máy chủ Wazuh chạy công cụ phân tích, API Wazuh RESTful, dịch vụ đăng ký tác nhân, dịch vụ kết nối tác nhân, daemon cụm Wazuh và Filebeat. Sơ đồ dưới đây đại diện cho kiến trúc máy chủ và các thành phần:

![image](https://user-images.githubusercontent.com/69178270/143823608-8b1fd709-1e1a-4d3a-9621-08d0e39df1a0.png)

Máy chủ thường chạy trên một máy vật lý độc lập, máy ảo, bộ chứa docker hoặc phiên bản đám mây. Nó được cài đặt trên hệ điều hành Linux. Dưới đây là danh sách các thành phần máy chủ chính:

 - Dịch vụ đăng ký đại lý: Được sử dụng để đăng ký đại lý mới bằng cách cung cấp và phân phối khóa xác thực chia sẻ trước dành riêng cho từng đại lý. Quá trình này chạy như một dịch vụ mạng và hỗ trợ xác thực thông qua chứng chỉ TLS / SSL hoặc bằng cách cung cấp mật khẩu cố định.

 - Dịch vụ kết nối đại lý: Đây là dịch vụ nhận dữ liệu từ các đại lý. Nó sử dụng các khóa chia sẻ trước để xác thực danh tính của từng tác nhân và mã hóa thông tin liên lạc giữa tác nhân và máy chủ Wazuh. Ngoài ra, dịch vụ này được sử dụng để cung cấp quản lý cấu hình tập trung, có thể đẩy các cài đặt tác nhân mới từ xa.

 - Công cụ phân tích: Đây là quá trình thực hiện phân tích dữ liệu. Nó sử dụng bộ giải mã để xác định loại thông tin đang được xử lý (ví dụ: sự kiện Windows, nhật ký SSHD, nhật ký máy chủ web, v.v.) và trích xuất các phần tử dữ liệu có liên quan từ thông báo nhật ký (ví dụ: địa chỉ IP nguồn, ID sự kiện, tên người dùng, v.v.) . Tiếp theo, bằng cách sử dụng các quy tắc , nó xác định các mẫu cụ thể trong các sự kiện được giải mã có thể kích hoạt cảnh báo và thậm chí có thể gọi các biện pháp đối phó tự động (ví dụ: lệnh cấm IP trên tường lửa).

 - Wazuh RESTful API: Dịch vụ này cung cấp giao diện để tương tác với cơ sở hạ tầng Wazuh. Nó được sử dụng để quản lý các tác nhân và cài đặt cấu hình máy chủ, để theo dõi trạng thái cơ sở hạ tầng và tình trạng tổng thể, để quản lý và chỉnh sửa bộ giải mã và quy tắc Wazuh cũng như truy vấn về trạng thái của các điểm cuối được giám sát. Nó cũng được sử dụng bởi giao diện người dùng web Wazuh, đó là ứng dụng Kibana.

 - Wazuh cluster daemon: Dịch vụ này được sử dụng để mở rộng các máy chủ Wazuh theo chiều ngang, triển khai chúng dưới dạng một cụm. Loại cấu hình này, kết hợp với bộ cân bằng tải mạng, cung cấp khả năng cân bằng tải và tính sẵn sàng cao. Daemon cụm Wazuh là thứ mà các máy chủ Wazuh sử dụng để giao tiếp với nhau và giữ đồng bộ hóa.

 - Filebeat: Nó được sử dụng để gửi các sự kiện và cảnh báo tới Elasticsearch. Nó đọc kết quả của công cụ phân tích Wazuh và gửi các sự kiện trong thời gian thực. Nó cũng cung cấp khả năng cân bằng tải khi được kết nối với một cụm Elasticsearch nhiều nút.

**Elastic Stack** - Ngăn xếp đàn hồi

Elastic Stack là một bộ thống nhất gồm các dự án nguồn mở phổ biến để quản lý nhật ký, bao gồm Elasticsearch, Kibana, Filebeat và các dự án khác. Các dự án đặc biệt liên quan đến giải pháp Wazuh là:

 - Filebeat: Một trình chuyển tiếp nhẹ được sử dụng để truyền tải nhật ký qua mạng, thường là tới Elasticsearch. Nó được sử dụng trên máy chủ Wazuh để gửi các sự kiện và cảnh báo tới Elasticsearch. Nó đọc đầu ra của công cụ phân tích Wazuh và gửi các sự kiện trong thời gian thực thông qua một kênh được mã hóa. Nó cũng cung cấp khả năng cân bằng tải khi được kết nối với một cụm Elasticsearch nhiều nút.

 - Elasticsearch: Công cụ phân tích và tìm kiếm toàn văn bản, có khả năng mở rộng cao. Elasticsearch được phân phối, có nghĩa là các chỉ số dữ liệu được chia thành các phân đoạn và mỗi phân đoạn có thể có không hoặc nhiều bản sao. Wazuh sử dụng các chỉ số khác nhau cho dữ liệu cảnh báo, sự kiện thô và thông tin giám sát trạng thái.

 - Kibana: Một giao diện web linh hoạt và trực quan để khai thác, phân tích và trực quan hóa dữ liệu. Nó chạy trên đầu nội dung được lập chỉ mục trong một cụm Elasticsearch. Giao diện người dùng web Wazuh đã được nhúng hoàn toàn vào Kibana, dưới dạng một plugin. Nó bao gồm bảng điều khiển sẵn có cho các sự kiện bảo mật, tuân thủ quy định (ví dụ: PCI DSS, GDPR, CIS, HIPAA, NIST 800-53), các ứng dụng dễ bị tấn công, dữ liệu giám sát tính toàn vẹn của tệp, kết quả đánh giá cấu hình, sự kiện giám sát cơ sở hạ tầng đám mây , và những người khác.

Wazuh tích hợp với Elastic Stack, cung cấp nguồn cấp dữ liệu các tin nhắn đã được giải mã sẽ được Elasticsearch lập chỉ mục, cũng như bảng điều khiển web thời gian thực để phân tích dữ liệu nhật ký và cảnh báo. Ngoài ra, giao diện người dùng Wazuh, chạy trên Kibana, được sử dụng để quản lý và giám sát cơ sở hạ tầng Wazuh.

Chỉ mục Elasticsearch là một tập hợp các tài liệu có các đặc điểm hơi giống nhau (như các trường chung nhất định và các yêu cầu lưu giữ dữ liệu được chia sẻ). Wazuh sử dụng ba chỉ số khác nhau, được tạo hàng ngày, để lưu trữ các loại sự kiện khác nhau:

 - wazuh-alerts: Chỉ mục cho các cảnh báo được tạo bởi máy chủ Wazuh . Chúng được tạo mỗi khi một sự kiện truy cập một quy tắc có mức độ ưu tiên đủ cao (ngưỡng này có thể định cấu hình).

 - wazuh-events: Lập chỉ mục cho tất cả các sự kiện (dữ liệu lưu trữ) nhận được từ các tác nhân, cho dù chúng có đi theo quy tắc hay không.

 - wazuh-Monitoring: Chỉ mục cho dữ liệu liên quan đến trạng thái của các tác nhân Wazuh theo thời gian. Nó được sử dụng bởi giao diện web để đại diện cho khi đại lý cá nhân đang hoặc đã Active, Disconnectedhoặc .Never connected

Một chỉ mục bao gồm các tài liệu. Đối với các chỉ số ở trên, tài liệu là các cảnh báo riêng lẻ, các sự kiện được lưu trữ hoặc dữ liệu liên quan đến trạng thái của tác nhân Wazuh.

Chỉ mục Elasticsearch được chia thành một hoặc nhiều phân đoạn và mỗi phân đoạn có thể có một hoặc nhiều bản sao tùy ý. Mỗi phân đoạn chính và phân đoạn bản sao là một chỉ số Lucene riêng lẻ. Do đó, một chỉ số Elasticsearch được tạo thành từ nhiều chỉ số Lucene. Khi tìm kiếm được chạy trên chỉ mục Elasticsearch, tìm kiếm được thực hiện trên tất cả các phân đoạn song song và kết quả được hợp nhất. Việc chia chỉ mục Elasticsearch thành nhiều phân đoạn và bản sao được sử dụng trong các cụm Elasticsearch nhiều nút, với mục đích mở rộng tìm kiếm và để có tính khả dụng cao. Các cụm Elasticsearch một nút thường chỉ có một phân đoạn cho mỗi chỉ mục và không có bản sao.
