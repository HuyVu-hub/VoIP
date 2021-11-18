### Application Usage (Sử dụng ứng dụng)

Chương này xem xét BDR từ góc độ ứng dụng hoặc người dùng.

Việc thiết lập các nút sẽ được thảo luận trong chương sau, cũng như sao chép DDL, và các tùy chọn khác nhau để kiểm soát sao chép bằng cách sử dụng các tập hợp sao chép.

**Application Behavior** - Hành vi ứng dụng

BDR hỗ trợ sao chép các thay đổi được thực hiện trên một nút sang các nút khác.

Theo mặc định, BDR sẽ sao chép tất cả các thay đổi từ CHÈN, CẬP NHẬT, XÓA và TRUNCATE từ nút nguồn sang các nút khác. Chỉ những thay đổi cuối cùng sẽ được gửi sau khi tất cả các trình kích hoạt và quy tắc đã được xử lý. Ví dụ, INSERT ... ON CONFLICT UPDATE sẽ gửi một INSERT hoặc UPDATE tùy thuộc vào những gì đã xảy ra trên nguồn gốc. Nếu CẬP NHẬT hoặc XÓA ảnh hưởng đến không hàng, thì sẽ không có thay đổi nào được gửi.

INSERT có thể được sao chép mà không có bất kỳ điều kiện trước nào.

Để CẬP NHẬT và XÓA được sao chép trên các nút khác, chúng ta phải có khả năng xác định các hàng duy nhất bị ảnh hưởng. BDR yêu cầu một bảng phải có một KHÓA CHÍNH được xác định, một ràng buộc DUY NHẤT hoặc có một NHẬN DẠNG REPLICA rõ ràng được xác định trên (các) cột cụ thể. Nếu một trong những điều đó không được xác định, một CẢNH BÁO sẽ được tạo và các CẬP NHẬT hoặc XÓA sau này sẽ bị chặn rõ ràng. Nếu REPLICA IDENTITY FULL được xác định cho một bảng, thì một chỉ mục duy nhất là không bắt buộc; trong trường hợp đó, CẬP NHẬT và XÓA được cho phép và sẽ sử dụng chỉ mục không phải là duy nhất đầu tiên đang hoạt động, hợp lệ, không bị trì hoãn và không có biểu thức hoặc mệnh đề WHERE, nếu không sẽ sử dụng quá trình quét tuần tự.

TRUNCATE có thể được sử dụng ngay cả khi không có danh tính sao chép xác định. Việc sao chép các lệnh TRUNCATE được hỗ trợ, nhưng cần phải cẩn thận khi cắt bớt các nhóm bảng được kết nối bằng các khóa ngoại. Khi sao chép một hành động cắt ngắn, người đăng ký sẽ cắt ngắn cùng một nhóm bảng đã được cắt ngắn trên nguồn gốc, được chỉ định rõ ràng hoặc được thu thập ngầm thông qua CASCADE, ngoại trừ các trường hợp tập hợp nhân bản được xác định, hãy xem chương Bộ nhân bản để biết thêm chi tiết và ví dụ. Điều này sẽ hoạt động chính xác nếu tất cả các bảng bị ảnh hưởng là một phần của cùng một gói đăng ký. Nhưng nếu một số bảng được cắt ngắn trên người đăng ký có các liên kết khóa ngoại đến các bảng không thuộc cùng một bộ sao chép (hoặc bất kỳ) nào, thì việc áp dụng hành động cắt ngắn trên người đăng ký sẽ không thành công.

Các khóa cấp hàng được thực hiện ngầm bởi các lệnh INSERT, UPDATE và DELETE sẽ được sao chép khi các thay đổi được thực hiện. Các khóa cấp bảng được thực hiện ngầm bởi các lệnh INSERT, UPDATE, DELETE và TRUNCATE cũng sẽ được sao chép. Khóa cấp hàng rõ ràng (CHỌN ... ĐỂ CẬP NHẬT / ĐỂ CHIA SẺ) theo phiên của người dùng không được sao chép, cũng không phải là khóa tư vấn. Thông tin được lưu trữ bởi các giao dịch đang chạy ở chế độ SERIALIZABLE không được sao chép sang các nút khác; mức độ cô lập giao dịch của SERIALIAZABLE được hỗ trợ nhưng các giao dịch sẽ không được tuần tự hóa trên các nút, với sự hiện diện của các giao dịch đồng thời trên nhiều nút.

Nếu DML được thực thi đồng thời trên nhiều nút thì xung đột tiềm ẩn có thể xảy ra nếu thực thi với bản sao không đồng bộ và chúng phải được xử lý hoặc tránh. Có thể có nhiều cơ chế tránh khác nhau, được thảo luận trong chương về Xung đột .

Trình tự cần xử lý đặc biệt, được mô tả trong chương Trình tự .

Dữ liệu nhị phân trong các cột BYTEA được sao chép bình thường, cho phép các "đốm màu" dữ liệu có kích thước lên đến 1GB. Việc sử dụng cơ sở "Đối tượng lớn" PostgreSQL không được hỗ trợ trong BDR.

Các quy tắc chỉ thực thi trên nút gốc, do đó, không được thực thi trong quá trình áp dụng, ngay cả khi chúng được kích hoạt cho các bản sao.

Chỉ có thể sao chép từ bảng cơ sở sang bảng cơ sở. Có nghĩa là, các bảng trên nguồn và đích ở phía đăng ký phải là bảng, không phải là dạng xem, dạng xem vật thể hóa hoặc bảng ngoại. Cố gắng sao chép các bảng khác với bảng cơ sở sẽ dẫn đến lỗi. Các thay đổi DML được thực hiện thông qua các khung nhìn có thể cập nhật được giải quyết thông qua các bảng cơ sở trên nguồn gốc và sau đó được áp dụng cho cùng một tên bảng cơ sở trên đích.

BDR hỗ trợ các bảng được phân vùng một cách minh bạch, có nghĩa là một bảng được phân vùng có thể được thêm vào một tập hợp sao chép và các thay đổi liên quan đến bất kỳ phân vùng nào sẽ được sao chép xuống phía dưới.

Theo mặc định, trình kích hoạt chỉ thực thi trên nút gốc. Ví dụ, một kích hoạt INSERT thực thi trên nút gốc và bị bỏ qua khi chúng ta áp dụng thay đổi trên nút đích. Bạn có thể chỉ định rằng trình kích hoạt sẽ thực thi trên cả nút gốc tại thời điểm thực thi và trên đích khi nó được sao chép ("thời gian áp dụng") bằng cách sử dụng ALTER TABLE ... ENABLE ALWAYS TRIGGERhoặc sử dụng REPLICAtùy chọn chỉ thực thi tại thời điểm áp dụng ALTER TABLE ... ENABLE REPLICA TRIGGER,.

Một số loại trình kích hoạt không được thực thi khi áp dụng, ngay cả khi chúng tồn tại trên bảng và hiện đang được bật. Các loại trình kích hoạt không được thực thi là

 - Trình kích hoạt cấp câu lệnh (CHO TỪNG BÁO CÁO)
 - Trình kích hoạt UPDATE trên mỗi cột (UPDATE OF column_name [, ...] )

Áp dụng sao chép BDR sử dụng đường dẫn tìm kiếm mặc định cấp hệ thống. Trình kích hoạt bản sao, trình kích hoạt luồng và các hàm biểu thức chỉ mục có thể giả định các cài đặt đường dẫn tìm kiếm khác sau đó sẽ không thành công khi chúng thực thi được áp dụng. Để đảm bảo điều này không xảy ra, hãy giải quyết rõ ràng các tham chiếu đối tượng bằng cách chỉ sử dụng search_path mặc định, luôn sử dụng các tham chiếu đủ điều kiện cho các đối tượng, ví dụ: schema.objectname hoặc đặt đường dẫn tìm kiếm cho một hàm sử dụng ALTER FUNCTION ... SET search_path = ...cho các hàm bị ảnh hưởng.

Lưu ý rằng BDR giả định rằng không có vấn đề nào liên quan đến văn bản hoặc các kiểu dữ liệu đối chiếu khác, tức là tất cả các đối chiếu đang sử dụng đều có sẵn trên tất cả các nút và đối chiếu mặc định là giống nhau trên tất cả các nút. Việc sao chép các thay đổi sử dụng các tìm kiếm bình đẳng để xác định các giá trị Nhận dạng Bản sao, vì vậy điều này sẽ không có bất kỳ ảnh hưởng nào ngoại trừ trường hợp các chỉ mục duy nhất được xác định rõ ràng với các định tính đối chiếu không phù hợp. Bộ lọc hàng có thể bị ảnh hưởng bởi sự khác biệt trong đối chiếu nếu sử dụng biểu thức đối chiếu.

BDR xử lý dữ liệu "nướng" rất dài trong PostgreSQL là minh bạch đối với người dùng. Lưu ý rằng các giá trị TOAST "chunkid" có thể sẽ khác nhau giữa cùng một hàng trên các nút khác nhau, nhưng điều đó không gây ra bất kỳ vấn đề nào.

BDR không thể hoạt động chính xác nếu các cột Nhận dạng Bản sao được đánh dấu là "bên ngoài".

PostgreSQL cho phép các ràng buộc CHECK () chứa các hàm dễ bay hơi. Vì BDR thực thi lại các ràng buộc CHECK () được áp dụng, bất kỳ quá trình thực thi lại nào sau đó không trả lại kết quả giống như trước đó sẽ gây ra phân kỳ dữ liệu.

BDR không hạn chế việc sử dụng Khóa ngoại; FK xếp tầng được cho phép.

BDR hiện không hỗ trợ việc sử dụng lược đồ hoặc tên quan hệ không phải ASCII. Các phiên bản sau sẽ loại bỏ hạn chế này.

**Non-replicated statements** - Câu lệnh không sao chép

Không có lệnh người dùng nào sau đây được sao chép bởi BDR, vì vậy các tác động của chúng chỉ xảy ra trên nút cục bộ / nguồn gốc:

 - Các hoạt động con trỏ (DECLARE, CLOSE, FETCH)
 - Các lệnh thực thi (DO, CALL, PREPARE, EXECUTE, EXPLAIN)
 - Quản lý phiên (DEALLOCATE, DISCARD, LOAD)
 - Các lệnh tham số (SET, SHOW)
 - Thao tác ràng buộc (THIẾT LẬP CONSTRAINTS)
 - Khóa lệnh (LOCK)
 - Lệnh bảo trì bảng (VACUUM, ANALYZE, CLUSTER, REINDEX)
 - Hoạt động không đồng bộ (NOTIFY, LISTEN, UNLISTEN)

Lưu ý rằng vì NOTIFYlệnh SQL và các pg_notify()chức năng không được sao chép, các thông báo không đáng tin cậy trong trường hợp chuyển đổi dự phòng. Điều này có nghĩa là các thông báo có thể dễ dàng bị mất khi chuyển đổi dự phòng nếu một giao dịch được thực hiện ngay tại thời điểm máy chủ gặp sự cố. Các ứng dụng đang chạy LISTENcó thể bỏ lỡ thông báo trong trường hợp chuyển đổi dự phòng. Đáng tiếc, điều này đúng trong bản sao PostgreSQL tiêu chuẩn và BDR vẫn chưa cải thiện điều này. Các tùy chọn sao chép CAMO và Eager không cho phép NOTIFYlệnh SQL hoặc pg_notify()hàm.

**DML and DDL Replication** - Nhân bản DML và DDL

Lưu ý rằng BDR không sao chép câu lệnh DML, nó sao chép những thay đổi do câu lệnh DML gây ra. Vì vậy, ví dụ: một CẬP NHẬT đã thay đổi hai hàng sẽ sao chép hai thay đổi, trong khi một XÓA không loại bỏ bất kỳ hàng nào sẽ không sao chép bất kỳ thứ gì. Điều này có nghĩa là kết quả của việc thực hiện các câu lệnh thay đổi được sao chép, đảm bảo không có sự khác biệt giữa các nút như có thể xảy ra với sao chép dựa trên câu lệnh.

Bản sao DDL hoạt động khác với DML. Đối với DDL, BDR sao chép câu lệnh, câu lệnh này sau đó được thực thi trên tất cả các nút. Vì vậy, một BẢNG DROP NẾU TỒN TẠI có thể không sao chép bất kỳ thứ gì trên nút cục bộ, nhưng câu lệnh vẫn được gửi đến các nút khác để thực thi nếu tính năng sao chép DDL được bật. Chi tiết đầy đủ được đề cập trong chương riêng của chúng: [Nhân rộng DDL] .

BDR có độ dài lớn để đảm bảo rằng các câu lệnh DML và DDL trộn lẫn hoạt động chính xác, ngay cả trong cùng một giao dịch.

**Replicating between different release levels** - Nhân rộng giữa các mức phát hành khác nhau

BDR được thiết kế để sao chép giữa các nút có các phiên bản PostgreSQL chính khác nhau. Đây là một tính năng được thiết kế để cho phép nâng cấp phiên bản lớn mà không cần thời gian chết.

BDR cũng được thiết kế để sao chép giữa các nút có các phiên bản phần mềm BDR khác nhau. Đây là một tính năng được thiết kế để cho phép nâng cấp phiên bản và bảo trì mà không cần thời gian chết.

Tuy nhiên, mặc dù có thể tham gia một nút có phiên bản chính trong một cụm, nhưng bạn không thể thêm nút có phiên bản phụ nếu cụm sử dụng phiên bản giao thức mới hơn, điều này sẽ trả về lỗi.

Cả hai tính năng trên có thể bị ảnh hưởng bởi các hạn chế cụ thể; mọi điểm không tương thích đã biết sẽ được mô tả trong ghi chú phát hành.

**Replicating between nodes with differences** - Nhân rộng giữa các nút với sự khác biệt
Theo mặc định, DDL sẽ tự động được gửi đến tất cả các nút. Điều này có thể được kiểm soát theo cách thủ công, như được mô tả trong DDL Replication , có thể được sử dụng để tạo ra sự khác biệt giữa các lược đồ cơ sở dữ liệu giữa các nút. BDR được thiết kế để cho phép sao chép tiếp tục ngay cả khi có sự khác biệt nhỏ giữa các nút. Các tính năng này được thiết kế để cho phép di chuyển lược đồ ứng dụng mà không có thời gian chết hoặc cho phép các nút chờ logic để báo cáo hoặc thử nghiệm.

Hiện tại, việc nhân rộng yêu cầu tên bảng giống nhau trên tất cả các nút. Một tính năng trong tương lai có thể cho phép ánh xạ giữa các tên bảng khác nhau.

Có thể sao chép giữa các bảng với các định nghĩa phân vùng khác nhau, chẳng hạn như nguồn là một bảng bình thường sao chép sang một bảng được phân vùng, bao gồm hỗ trợ cho các bản cập nhật thay đổi phân vùng trên đích. Có thể nhanh hơn nếu định nghĩa phân vùng giống nhau trên nguồn và đích vì định tuyến phân vùng động không cần thực hiện tại thời điểm áp dụng. Thông tin chi tiết có sẵn trong chương về Bộ sao chép.

Theo mặc định, tất cả các cột đều được sao chép. BDR sao chép các cột dữ liệu dựa trên tên cột. Nếu một cột có cùng tên nhưng khác kiểu dữ liệu, chúng tôi sẽ cố gắng truyền từ kiểu nguồn sang kiểu đích, nếu các phôi đã được xác định cho phép điều đó.

BDR hỗ trợ sao chép giữa các bảng có số lượng cột khác nhau.

Nếu đích bị thiếu (các) cột từ nguồn thì BDR sẽ phát sinh xung đột target_column_missing, mà trình giải quyết xung đột mặc định là ignore_if_null. Điều này sẽ tạo ra một LỖI nếu đến một giá trị không phải NULL. Ngoài ra, một nút cũng có thể được định cấu hình bằng trình giải quyết xung đột là bỏ qua. Cài đặt này sẽ không tạo ra LỖI, chỉ bỏ qua bất kỳ cột bổ sung nào.

Nếu mục tiêu có (các) cột bổ sung không được nhìn thấy trong bản ghi nguồn thì BDR sẽ phát sinh xung đột source_column_missing, mà trình giải quyết xung đột mặc định là use_default_value. Sao chép sẽ tiếp tục nếu các cột bổ sung có mặc định, NULL (nếu null) hoặc một biểu thức mặc định, nhưng sẽ xuất hiện một LỖI và tạm dừng sao chép nếu không.

Trình kích hoạt chuyển đổi cũng có thể được sử dụng trên các bảng để cung cấp các giá trị mặc định hoặc thay đổi dữ liệu đến theo nhiều cách khác nhau trước khi áp dụng.

Nếu nguồn và đích có các ràng buộc khác nhau, thì việc sao chép sẽ được cố gắng thực hiện, nhưng có thể thất bại nếu không thể áp dụng các hàng từ nguồn cho đích. Bộ lọc hàng có thể hữu ích ở đây.

Việc sao chép dữ liệu từ một giản đồ sang một giản đồ khác sẽ không gây ra lỗi. Việc sao chép dữ liệu từ một giản đồ sang một lược đồ hạn chế hơn sẽ là một nguồn dẫn đến các lỗi tiềm ẩn. Cách đúng đắn để giải quyết vấn đề này là đặt một hạn chế ở bên thoải mái hơn, vì vậy dữ liệu xấu sẽ bị ngăn chặn nhập. Bằng cách đó, không có dữ liệu xấu nào đến được qua bản sao, vì vậy nó sẽ không bao giờ thất bại khi chuyển đổi thành lược đồ hạn chế hơn. Ví dụ: nếu một lược đồ có một cột kiểu TEXT và một lược đồ khác xác định cột giống như XML, hãy thêm ràng buộc CHECK vào cột TEXT để bắt buộc văn bản đó là XML.

Một bảng có thể được xác định với các chỉ mục khác nhau trên mỗi nút. Theo mặc định, các định nghĩa chỉ mục sẽ được sao chép. Tham khảo Bản sao DDL để chỉ định cách tạo chỉ mục chỉ trên một tập hợp con các nút hoặc chỉ cục bộ.

Các tham số lưu trữ, chẳng hạn như fillfactor và toast_tuple_target, có thể khác nhau giữa các nút của một bảng mà không gặp vấn đề gì. Một ngoại lệ là giá trị của tham số lưu trữ của bảng user_catalog_tablephải giống nhau trên tất cả các nút.

Một bảng đang được sao chép phải được sở hữu bởi cùng một người dùng / vai trò trên mỗi nút. Tham khảo Bảo mật và Vai trò để thảo luận thêm.

Các vai trò có thể có các mật khẩu khác nhau để kết nối trên mỗi nút, mặc dù các thay đổi theo mặc định đối với các vai trò được sao chép cho mỗi nút. Tham khảo Bản sao DDL để chỉ định cách thay đổi mật khẩu vai trò chỉ trên một tập hợp con các nút hoặc chỉ cục bộ.

**Comparison between nodes with differences** - So sánh giữa các nút với sự khác biệt

Livecompare là một công cụ được sử dụng để so sánh dữ liệu trên cơ sở dữ liệu, chống lại các nút BDR và ​​không phải BDR. Nó cần một số lượng tối thiểu hai kết nối để so sánh và đi đến kết quả cuối cùng.

Từ Livecompare 1.3, bạn có thể cấu hình với all_bdr_nodesset. Điều này sẽ giúp bạn không phải làm rõ tất cả các DSN có liên quan cho từng nút riêng biệt trong cụm. Một cụm BDR có N số lượng nút với thông tin kết nối, nhưng kết nối đầu ra và ban đầu duy nhất của nó mà nó có trực tiếp 1.3+ cần để hoàn thành công việc của nó. Cài đặt logical_replication_modesẽ cho biết tất cả các nút đang giao tiếp như thế nào.

Tất cả các cấu hình được thực hiện trong một tệp .ini, có tên bdrLC.ini chẳng hạn. Các mẫu cho tệp cấu hình này có thể được nhìn thấy trong /etc/2ndq-livecompare/vị trí, nơi chúng được đặt sau khi cài đặt gói.

Trong quá trình thực thi LiveCompare, bạn sẽ thấy N + 1 thanh tiến trình, N là số tiến trình. Sau khi tất cả các bảng được lấy nguồn, một thời gian sẽ hiển thị, vì các giao dịch trên giây (tps) đã được đo lường. Thao tác này sẽ tiếp tục đếm thời gian, cung cấp cho bạn ước tính, sau đó là tổng thời gian thực hiện khi kết thúc.

Công cụ này có rất nhiều tùy chỉnh và bộ lọc. Chẳng hạn như bảng, lược đồ và replication_sets. LiveCompare có thể sử dụng stop-start mà không làm mất thông tin ngữ cảnh, vì vậy nó có thể được chạy vào những thời điểm thuận tiện. Sau khi so sánh, một bản tóm tắt và một tập lệnh DML được tạo để người dùng có thể xem lại. Vui lòng áp dụng DML để sửa chữa những khác biệt được tìm thấy, nếu có.

**Quy tắc chung cho các ứng dụng**

Như đã thảo luận ở trên, BDR sử dụng các giá trị nhận dạng bản sao để xác định các hàng cần thay đổi. Các ứng dụng có thể gây ra khó khăn nếu chúng chèn, xóa và sau đó sử dụng lại cùng một số nhận dạng duy nhất. Đây được gọi là Bài toán ABA. BDR không thể biết liệu các hàng là hàng hiện tại, hàng cuối cùng hay các hàng cũ hơn nhiều. Xem https://en.wikipedia.org/wiki/ABA_problem .

Tương tự như vậy, vì BDR sử dụng tên bảng để xác định bảng dựa vào đó những thay đổi nào sẽ được phát lại, một vấn đề ABA tương tự tồn tại với các ứng dụng TẠO, sau đó DROP và sau đó sử dụng lại cùng tên đối tượng.

Những vấn đề này dẫn đến một số quy tắc đơn giản để ứng dụng tuân theo:

1. Sử dụng số nhận dạng duy nhất cho các hàng (CHÈN)
2. Tránh sửa đổi số nhận dạng duy nhất (CẬP NHẬT)
3. Tránh sử dụng lại các số nhận dạng duy nhất đã xóa
4. Tránh sử dụng lại các tên đối tượng đã bỏ

Trong trường hợp chung, việc phá vỡ các quy tắc đó có thể dẫn đến dị thường và phân kỳ dữ liệu. Các ứng dụng có thể phá vỡ các quy tắc đó miễn là đáp ứng các điều kiện nhất định, nhưng hãy thận trọng: mặc dù có thể khó xảy ra hiện tượng bất thường, nhưng không phải là không thể. Ví dụ: một giá trị hàng có thể được sử dụng lại miễn là DELETE đã được phát lại trên tất cả các nút, bao gồm cả các nút xuống. Điều này thường có thể xảy ra trong vòng chưa đầy một giây, nhưng có thể mất vài ngày nếu sự cố nghiêm trọng xảy ra trên một nút khiến nút không thể khởi động lại chính xác.

**Timing Considerations and Synchronous Replication** - Cân nhắc về thời gian và sao chép đồng bộ

Không đồng bộ theo mặc định, các nút ngang hàng có thể bị tụt hậu khiến máy khách có thể được kết nối với nhiều nút BDR hoặc chuyển đổi giữa chúng để đọc dữ liệu cũ.

Một chức năng chờ hàng đợi được cung cấp cho máy khách hoặc proxy để ngăn chặn những lần đọc cũ như vậy.

Các tính năng sao chép đồng bộ của Postgres cũng có sẵn cho BDR. Ngoài ra, BDR cung cấp nhiều biến thể để nhân rộng đồng bộ hơn. Vui lòng tham khảo chương Tùy chọn độ bền & hiệu suất để có cái nhìn tổng quan và so sánh về tất cả các biến thể có sẵn và các chế độ khác nhau của nó.

**Kiểm tra ứng dụng**

Các ứng dụng BDR có thể được kiểm tra bằng các chương trình sau, ngoài các kỹ thuật khác.

 - [TPAexec]
 - [pgbench với tùy chọn CAMO / Chuyển đổi dự phòng]
 - [bộ kiểm tra cách ly có quyền truy cập nhiều nút]

**TPAexec**

TPAexec là hệ thống được EDB sử dụng để triển khai các kiến ​​trúc TPA tham chiếu, bao gồm cả những kiến ​​trúc dựa trên Postgres-BDR.

TPAexec bao gồm các bộ thử nghiệm cho mỗi kiến ​​trúc tham chiếu; nó cũng đơn giản hóa việc tạo và quản lý một tập hợp các bài kiểm tra cục bộ để chạy trên một cụm TPA, sử dụng cú pháp như trong ví dụ sau:

```
tpaexec kiểm tra mycluster mytest
```

Chúng tôi đặc biệt khuyên các nhà phát triển nên viết bộ kiểm tra TPAexec đa nút của riêng họ để xác minh các thuộc tính mong đợi chính của ứng dụng.

**pgbench với tùy chọn CAMO / Chuyển đổi dự phòng**

pgbench đã được mở rộng để cho phép người dùng chạy các bài kiểm tra chuyển đổi dự phòng trong khi sử dụng triển khai CAMO hoặc BDR thông thường. Các tùy chọn mới sau đây đã được thêm vào:

```
-m, --mode = thường | camo | 
chế độ chuyển đổi dự phòng trong đó pgbench sẽ chạy (mặc định: thông thường) 

- 
thử lại các giao dịch trên chuyển đổi dự phòng
```

Ngoài các tùy chọn trên, thông tin kết nối về nút ngang hàng để chuyển đổi dự phòng phải được chỉ định ở dạng DSN .

 - Sử dụng -m camohoặc -m failoverđể chỉ định chế độ cho pgbench. Đặc -m failoverđiểm kỹ thuật có thể được sử dụng để kiểm tra chuyển đổi dự phòng trong các triển khai BDR thông thường.

 - Sử dụng --retryđể chỉ định xem các giao dịch có nên được thử lại khi chuyển đổi dự phòng xảy ra với -m failoverchế độ hay không. Điều này được bật theo mặc định cho -m camochế độ.

Đây là một lệnh gọi ví dụ trong môi trường CAMO:

```
    pgbench -m camo -p $ node1_port -h $ node1_host bdrdemo \ 
        "host = $ node2_host user = postgres port = $ node2_port dbname = bdrdemo"
```

Lệnh trên sẽ chạy trong camochế độ. Nó sẽ kết nối node1và chạy các bài kiểm tra; nếu kết nối với node1kết nối bị mất, thì pgbench sẽ kết nối với node2. Nó sẽ truy vấn node2để có được trạng thái của các giao dịch trên chuyến bay. Các giao dịch đang bay và đang bị hủy bỏ sẽ được thử lại ở camochế độ.

Trong failoverchế độ, nếu --retryđược chỉ định thì các giao dịch trên chuyến bay sẽ được thử lại. Trong trường hợp này, không có cách nào để tìm trạng thái của các giao dịch trên chuyến bay.

**isolationtester with multi-node access** - bộ điều khiển cách ly với quyền truy cập nhiều nút

Trình kiểm tra cách ly đã được mở rộng để cho phép người dùng chạy thử nghiệm trên nhiều phiên và trên nhiều nút. Điều này được sử dụng để kiểm tra BDR nội bộ, mặc dù nó cũng có sẵn để sử dụng với kiểm tra ứng dụng của người dùng.

```
$ isolationtester \ 
     --outputdir =. / iso_output \ 
     --create-role = logic \ 
     --dbname = postgres \ 
     --server 'd1 = dbname = node1' \ 
     --server 'd2 = dbname = node2' \ 
     --server 'd3 = dbname = node3'
```

Các bài kiểm tra cô lập là một tập hợp các bài kiểm tra được chạy để kiểm tra các hành vi đồng thời trong PostgreSQL. Các bài kiểm tra này yêu cầu chạy nhiều giao dịch tương tác, yêu cầu quản lý nhiều kết nối đồng thời và do đó không thể kiểm tra bằng pg_regresschương trình bình thường . Cái tên "cách ly" xuất phát từ thực tế là động cơ ban đầu là để kiểm tra mức độ cách ly có thể nối tiếp hóa; nhưng các bài kiểm tra cho các loại hành vi đồng thời khác cũng đã được thêm vào.

Nó được xây dựng bằng cách sử dụng PGXS như một mô-đun bên ngoài. Khi cài đặt, nó tạo tệp nhị phân phân lập được chạy pg_isolation_regressđể thực hiện kiểm tra hồi quy đồng thời và quan sát kết quả.

pg_isolation_regresslà một công cụ tương tự như pg_regress, nhưng thay vì sử dụng psql để thực hiện kiểm tra, nó sử dụng bộ kiểm tra cách ly. Nó chấp nhận tất cả các đối số dòng lệnh giống như pg_regress. Nó đã được sửa đổi để chấp nhận nhiều máy chủ làm tham số. Sau đó, nó chuyển các conninfo của máy chủ này cùng với tên máy chủ để phân lập nhị phân. Trình kiểm tra cách ly so sánh các tên máy chủ này với các tên được chỉ định trong mỗi phiên trong các tệp thông số kỹ thuật và chạy các thử nghiệm đã cho trên các máy chủ tương ứng.

Để xác định các thử nghiệm với các giao dịch chồng chéo, chúng tôi sử dụng các tệp đặc tả thử nghiệm với cú pháp tùy chỉnh, được mô tả trong phần tiếp theo. Để thêm một bài kiểm tra mới, hãy đặt một tệp đặc tả trong thông số kỹ thuật / thư mục con, thêm đầu ra dự kiến ​​trong thư mục con / dự kiến ​​và thêm tên của bài kiểm tra vào Makefile.

Isolationtester là một chương trình sử dụng libpq để mở nhiều kết nối và thực hiện kiểm tra được chỉ định bởi một tệp đặc tả. Chuỗi kết nối libpq chỉ định máy chủ và cơ sở dữ liệu để kết nối; các giá trị mặc định bắt nguồn từ các biến môi trường được sử dụng theo cách khác.

Đặc điểm kỹ thuật bao gồm năm phần, được kiểm tra theo thứ tự sau:

```
server "<name>"
```

Điều này xác định tên của các máy chủ mà các phiên sẽ chạy trên đó. Có thể không có hoặc nhiều <name>thông số kỹ thuật máy chủ . Conninfo tương ứng với các tên được cung cấp thông qua lệnh để chạy bộ kiểm tra cách ly. Điều này được mô tả trong quickstart_isolationtest.md. Phần này là tùy chọn.

```
setup { <SQL> }
```

Khối SQL đã cho được thực thi một lần, chỉ trong một phiên, trước khi chạy thử nghiệm. Tạo bất kỳ bảng kiểm tra hoặc các đối tượng cần thiết khác tại đây. Phần này là tùy chọn. Nhiều khối thiết lập được cho phép nếu cần; mỗi cái được chạy riêng biệt, theo thứ tự nhất định. (Lý do cho phép nhiều khối thiết lập là mỗi khối được chạy như một trình PQexec duy nhất và một số câu lệnh như VACUUM không thể được kết hợp với các câu lệnh khác trong một khối như vậy.)
  
```
  teardown { <SQL> }
```

Khối SQL nhỏ được thực thi một lần sau khi quá trình kiểm tra kết thúc. Sử dụng công cụ này để dọn dẹp nhằm chuẩn bị cho lần hoán vị tiếp theo, ví dụ: loại bỏ bất kỳ bảng kiểm tra nào được tạo bằng cách thiết lập. Phần này là tùy chọn.

```
 session "<name>"
```

Thường có một số phần "phiên" trong một tệp đặc tả. Mỗi phiên được thực thi trong kết nối riêng của nó. Một phần phiên bao gồm ba phần: thiết lập, chia nhỏ và một hoặc nhiều "bước". Các phần thiết lập và chia nhỏ theo từng phiên có cùng cú pháp với thiết lập và chia nhỏ theo từng thử nghiệm được mô tả ở trên, nhưng chúng được thực thi trong mỗi phiên. Phần thiết lập thường chứa lệnh "BEGIN" để bắt đầu giao dịch.

Ngoài ra, một phần phiên cũng bao gồm connect_tođặc điểm kỹ thuật. Điều này trỏ đến tên máy chủ được chỉ định ngay từ đầu cho biết máy chủ mà phiên này chạy trên đó.

```
 connect_to "<name>"
```

Mỗi bước có cú pháp

```
step "<name>" { <SQL> }
```
đâu <name>là tên xác định bước này và SQL là một câu lệnh SQL (hoặc các câu lệnh, được phân tách bằng dấu chấm phẩy) được thực thi trong bước này. Tên bước phải là duy nhất trên toàn bộ tệp thông số kỹ thuật.

```
permutation "<step name>"
```

Một dòng hoán vị chỉ định danh sách các bước được chạy theo thứ tự đó. Có thể xuất hiện bất kỳ số dòng hoán vị nào. Nếu không có dòng hoán vị nào được đưa ra, chương trình kiểm tra sẽ tự động tạo tất cả các chuỗi thứ tự có thể có của các bước từ mỗi phiên (chạy các bước của bất kỳ phiên nào theo thứ tự). Lưu ý rằng danh sách các bước trong dòng "hoán vị" được chỉ định thủ công không thực sự phải là hoán vị của các bước có sẵn; ví dụ, nó có thể lặp lại một số bước nhiều hơn một lần hoặc bỏ qua những bước khác.

Các dòng bắt đầu bằng dấu # được coi là nhận xét.

Đối với mỗi hoán vị của các bước phiên (cho dù chúng được chỉ định theo cách thủ công trong tệp thông số kỹ thuật hay được tạo tự động), trình kiểm tra cách ly chạy phần thiết lập chính, sau đó là các phần thiết lập mỗi phiên, sau đó là các bước phiên đã chọn, sau đó chia nhỏ từng phiên , sau đó là kịch bản giọt nước mắt chính. Mỗi bước đã chọn sẽ được gửi đến kết nối được liên kết với phiên của nó.

Để chạy các bài kiểm tra cách ly trong môi trường BDR3 đã chạy tất cả các lệnh thực hiện điều kiện tiên quyết, hãy làm theo các bước dưới đây,
 
1. Chạy make isolationcheck-installđể cài đặt mô-đun con của bộ điều khiển cách ly

2. Bạn có thể chạy kiểm tra hồi quy cô lập bằng cách sử dụng một trong các lệnh sau từ repo bdr-private

```
make isolationcheck-installcheck make isolationcheck-makecheck
```
 A. Để chạy kiểm tra cách ly-cài đặt, bạn cần có hai hoặc nhiều máy chủ postgresql đang chạy. Chuyển conninfo của máy chủ đến pg_isolation_regress trong Makefile BDR 3.0. Bán tại:pg_isolation_regress --server 'd1=host=myhost dbname=mydb port=5434' --server 'd2=host=myhost1 dbname=mydb port=5432'

Bây giờ, thêm một tệp .spec chứa các bài kiểm tra trong thư mục specs / isolate của bdr-private / repo. Thêm tệp .out vào thư mục mong đợi / cách ly của bdr-private / repo.

Sau đó chạy make isolationcheck-installcheck

B. Isolationcheck-makecheck hiện hỗ trợ chạy các thử nghiệm cách ly trên một phiên bản duy nhất bằng cách thiết lập BDR giữa nhiều cơ sở dữ liệu.

Bạn cần chuyển các tên cơ sở dữ liệu thích hợp, conninfos của các phiên bản bdr vào pg_isolation_regresstrong BDR Makefile như sau: pg_isolation_regress --dbname=db1,db2 --server 'd1=dbname=db1' --server 'd2=dbname=db2'

Sau đó chạy make isolationcheck-makecheck

Mỗi bước có thể chứa các lệnh chặn cho đến khi thực hiện thêm hành động (rất có thể, một số phiên khác chạy một bước mà bỏ chặn nó hoặc gây ra bế tắc). Một bài kiểm tra sử dụng khả năng này phải chỉ định thủ công các hoán vị hợp lệ, tức là những hoán vị không mong đợi một phiên bị chặn thực hiện một lệnh. Nếu một bài kiểm tra không tuân theo quy tắc đó, bộ đo cách ly sẽ hủy nó sau 300 giây. Nếu quá trình hủy không hoạt động, bộ xử lý cách ly sẽ thoát ra không sạch sau tổng thời gian chờ là 375 giây. Nên tránh kiểm tra các hoán vị không hợp lệ vì chúng có thể làm cho các kiểm tra cô lập mất nhiều thời gian để chạy và chúng không phục vụ mục đích kiểm tra hữu ích.
 
 Lưu ý rằng bộ điều khiển cách ly nhận ra rằng một lệnh đã bị chặn bằng cách xem nó có được hiển thị là đang chờ trong pg_lockschế độ xem hay không; do đó, chỉ các khối trên ổ khóa nặng mới được phát hiện.
 
**Performance Testing & Tuning**
 
BDR cho phép bạn đưa ra các giao dịch ghi trên nhiều nút chính. Mang những ghi đó lại với nhau trên mỗi nút có một chi phí hiệu suất mà bạn nên biết.

Đầu tiên, việc phát lại các thay đổi từ một nút khác có chi phí CPU, chi phí I / O và nó sẽ tạo ra các bản ghi WAL. Việc sử dụng tài nguyên thường ít hơn trong giao dịch ban đầu vì chi phí CPU thấp hơn do không cần thực thi lại SQL. Trong trường hợp CẬP NHẬT và XÓA các giao dịch, có thể có chi phí I / O khi phát lại nếu dữ liệu không được lưu vào bộ nhớ đệm.

Thứ hai, các thay đổi phát lại giữ các khóa cấp bảng và cấp hàng có thể tạo ra sự tranh chấp chống lại khối lượng công việc cục bộ. Các tính năng CRDT (Loại dữ liệu được sao chép không có xung đột) và CLCD (Phát hiện xung đột ở cấp độ cột) đảm bảo bạn nhận được câu trả lời chính xác ngay cả đối với các bản cập nhật đồng thời, nhưng chúng không loại bỏ các chi phí khóa thông thường. Nếu bạn bị khóa tranh chấp, hãy cố gắng tránh cập nhật xung đột và / hoặc giữ các giao dịch càng ngắn càng tốt. Một hàng được cập nhật nhiều trong một giao dịch lớn hơn sẽ gây ra tắc nghẽn về hiệu suất cho giao dịch đó. Các ứng dụng phức tạp đòi hỏi một số suy nghĩ để duy trì khả năng mở rộng.

Nếu bạn cho rằng mình đang gặp vấn đề về hiệu suất, bạn nên phát triển các bài kiểm tra hiệu suất bằng cách sử dụng các công cụ đo điểm chuẩn ở trên. pgbench cho phép bạn viết các tập lệnh kiểm tra tùy chỉnh cụ thể cho trường hợp sử dụng của bạn để bạn có thể hiểu chi phí chung của SQL và đo lường tác động của việc thực thi đồng thời.

Vì vậy, nếu "BDR chạy chậm", thì chúng tôi đề xuất như sau:

 1. Viết tập lệnh thử nghiệm tùy chỉnh cho pgbench, càng gần càng tốt với trường hợp sự cố của hệ thống sản xuất.

 2. Chạy tập lệnh trên một nút để cung cấp cho bạn một số liệu cơ bản.

 3. Chạy tập lệnh trên nhiều nút như trong quá trình sản xuất, sử dụng tổng số phiên tương tự như bạn đã làm trên một nút. Điều này sẽ cho bạn thấy hiệu quả của việc di chuyển đến nhiều nút.

 4. Tăng số lượng phiên cho 2 bài kiểm tra trên, để bạn có thể vẽ biểu đồ ảnh hưởng của việc gia tăng tranh chấp đối với ứng dụng của mình.

 5. Đảm bảo rằng các bài kiểm tra của bạn đủ dài để giải thích cho sự chậm trễ sao chép.

 6. Đảm bảo rằng độ trễ sao chép không tăng trong các thử nghiệm của bạn.

 Sử dụng tất cả các tính năng điều chỉnh thông thường của Postgres để cải thiện tốc độ của các phần quan trọng trong ứng dụng của bạn.
 
 
