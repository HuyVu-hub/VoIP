### Security and Roles - ảo mật và vai trò

Phần mở rộng BDR3 chỉ có thể được tạo bởi superuser, mặc dù nếu muốn, có thể thiết lập phần mở rộng pgextwlist và định cấu hình nó để cho phép BDR3 được tạo bởi một người không phải superuser.

Việc định cấu hình và quản lý BDR3 không yêu cầu quyền truy cập siêu người dùng, điều đó cũng không được khuyến nghị. Các đặc quyền được yêu cầu bởi BDR3 được chia thành các vai trò mặc định / xác định trước sau, được đặt tên tương tự như các vai trò mặc định / xác định trước của PostgreSQL:

 - bdr_superuser - vai trò có đặc quyền cao nhất, có quyền truy cập vào tất cả các bảng và chức năng BDR.
 - bdr_read_all_stats - vai trò có quyền truy cập chỉ đọc vào các bảng, dạng xem và chức năng, đủ để hiểu trạng thái của BDR.
 - bdr_monitor - hiện tại giống với bdr_read_all_stats, sẽ được mở rộng sau này.
 - bdr_application - các đặc quyền tối thiểu mà các ứng dụng chạy BDR yêu cầu.
 - bdr_read_all_conflicts - có thể xem tất cả các xung đột trong bdr.conflict_log.
 - Các vai trò BDR này được tạo khi phần mở rộng BDR3 được cài đặt. Xem [Vai trò mặc định của BDR] bên dưới để biết thêm chi tiết.

Quản lý BDR không yêu cầu quản trị viên phải có quyền truy cập vào dữ liệu người dùng.

Các sắp xếp để đảm bảo xung đột được thảo luận ở đây Ghi nhật ký Xung đột vào Bảng.

Xung đột có thể được theo dõi bằng cách sử dụng chế độ xem BDR.conflict_history_summary.

**Bảng danh mục**

Danh mục hệ thống và các bảng Lược đồ thông tin luôn bị BDR loại trừ khỏi việc sao chép.

Ngoài ra, các bảng thuộc sở hữu của phần mở rộng bị loại trừ khỏi việc nhân rộng.

_1. Các chức năng & Nhà điều hành BDR_

Tất cả các hàm BDR được hiển thị trong lược đồ bdr. Bất kỳ lệnh gọi nào đến các hàm này phải đủ điều kiện giản đồ, thay vì đặt bdr trong search_path.

Tất cả các toán tử BDR đều có sẵn thông qua lược đồ pg_catalog để cho phép người dùng loại trừ lược đồ công khai khỏi đường dẫn tìm kiếm mà không gặp sự cố.

_2. Cấp đặc quyền cho các đối tượng danh mục_

Quản trị viên không nên cấp các đặc quyền rõ ràng trên các đối tượng danh mục như bảng, dạng xem và chức năng; quản lý quyền truy cập vào các đối tượng đó bằng cách cấp một trong các vai trò được ghi trong [Vai trò mặc định của BDR].

Yêu cầu này là hệ quả của tính linh hoạt cho phép tham gia một nhóm nút ngay cả khi các nút ở hai bên của liên kết không có cùng phiên bản BDR (và do đó của danh mục BDR).

Chính xác hơn, nếu các đặc quyền trên các đối tượng danh mục riêng lẻ đã được cấp rõ ràng, thì thủ tục bdr.join_node_group () có thể không thành công vì các câu lệnh GRANT tương ứng được trích xuất từ nút đang tham gia có thể không áp dụng cho nút đang tham gia.

_3. Quản lý vai trò_

Người dùng là các đối tượng toàn cục trong một phiên bản PostgreSQL. Các lệnh CREATE USER và CREATE ROLE được sao chép tự động nếu chúng được thực thi trong cơ sở dữ liệu nơi BDR đang chạy và bdr.role_replication được bật. Tuy nhiên, nếu các lệnh này được thực thi trong các cơ sở dữ liệu khác trong cùng một phiên bản PostgreSQL thì chúng sẽ không được sao chép, ngay cả khi những người dùng đó có quyền trên cơ sở dữ liệu BDR.

Khi một nút BDR mới tham gia nhóm BDR, người dùng hiện tại không được sao chép tự động trừ khi nút được thêm bằng bdr_init_physical. Đây là chủ ý và là một tính năng bảo mật quan trọng. PostgreSQL cho phép người dùng truy cập nhiều cơ sở dữ liệu, với mặc định là truy cập bất kỳ cơ sở dữ liệu nào. BDR không biết người dùng nào truy cập cơ sở dữ liệu nào và do đó không thể quyết định một cách an toàn người dùng nào sẽ sao chép sang nút mới.

PostgreSQL cho phép bạn kết xuất tất cả người dùng bằng lệnh:

```
pg_dumpall --roles-only > roles.sql
```

Sau đó, tệp role.sql có thể được chỉnh sửa để loại bỏ những người dùng không mong muốn trước khi thực thi lại tệp đó trên nút mới được tạo. Các cơ chế khác có thể thực hiện được, tùy thuộc vào danh tính và giải pháp quản lý truy cập (IAM) của bạn, nhưng không được tự động hóa tại thời điểm này.

_4. Vai trò và nhân rộng _

Các thay đổi DDL do một người dùng thực hiện được áp dụng như chính người dùng đó trên mỗi nút.

Các thay đổi DML đối với bảng được sao chép dưới dạng người dùng sở hữu bảng trên nút đích. Khuyến nghị - nhưng không bắt buộc - một bảng được sở hữu bởi cùng một người dùng trên mỗi nút.

Nếu bảng A thuộc sở hữu của người dùng X trên node1 và thuộc sở hữu của người dùng Y trên node2, thì nếu người dùng Y có đặc quyền cao hơn người dùng X, thì điều này có thể được xem là sự leo thang đặc quyền. Vì một số nút có các trường hợp sử dụng khác nhau, chúng tôi cho phép điều này nhưng cảnh báo chống lại nó để cho phép quản trị viên bảo mật lập kế hoạch và kiểm tra tình huống này.

Trên các bảng đã bật chính sách bảo mật cấp hàng, các thay đổi sẽ được sao chép mà không cần áp dụng lại các chính sách. Điều này tương đương với những thay đổi đang được áp dụng là KHÔNG CÓ BẢO MẬT CẤP ĐỘ CỦA NGOẠI LỰC, ngay cả khi BẢO MẬT CẤP ĐỘ CỦA NGOẠI LỰC được chỉ định. Nếu điều này không mong muốn, hãy chỉ định row_filter để tránh sao chép tất cả các hàng. Khuyến nghị - nhưng không được thực thi - các chính sách bảo mật hàng trên tất cả các nút phải giống hệt nhau hoặc ít nhất là tương thích.

Lưu ý rằng bdr_superuser kiểm soát sao chép đối với BDR và ​​có thể thêm / xóa bất kỳ bảng nào khỏi bất kỳ tập hợp nhân bản nào. bdr_superuser không cần hoặc cũng không nên có bất kỳ đặc quyền nào đối với các bảng riêng lẻ. Nếu có nhu cầu hạn chế quyền truy cập vào các chức năng thiết lập nhân bản, các phiên bản hạn chế của các chức năng này có thể được thực hiện dưới dạng các chức năng ĐỊNH NGHĨA BẢO MẬT và được CẤP cho người dùng thích hợp.

_5. Vai trò kết nối_

Khi cấp phát một nút BDR mới, người dùng đã cung cấp trong DSN cho đối số local_dsn của bdr.create_node và join_target_dsn của bdr.join_node_group được sử dụng thường xuyên để tham chiếu, tạo và quản lý các đối tượng cơ sở dữ liệu. Điều này đặc biệt có liên quan trong quá trình khởi động ban đầu, nơi các tài khoản được chỉ định có thể gọi các hoạt động trên các đối tượng cơ sở dữ liệu trực tiếp hoặc thông qua mô-đun pglogical chứ không phải BDR.

BDR được viết cẩn thận để ngăn chặn các cuộc tấn công leo thang đặc quyền ngay cả khi sử dụng vai trò có quyền SUPERUSER trong các DSN này.

Để giảm thiểu bề mặt tấn công hơn nữa, một người dùng bị hạn chế hơn có thể được chỉ định trong DSN ở trên. Ở mức tối thiểu, người dùng như vậy phải được cấp quyền trên tất cả các nút, sao cho thỏa mãn các quy định sau:

 - người dùng có thuộc tính REPLICATION
 - nó được cấp quyền CREATE trên cơ sở dữ liệu
 - nó kế thừa các vai trò pglogical_superuser và bdr_superuser
 - nó sở hữu tất cả các đối tượng cơ sở dữ liệu để sao chép, trực tiếp hoặc thông qua quyền từ (các) vai trò chủ sở hữu.

Sau khi tất cả các nút được tham gia, các quyền có thể được giảm thêm xuống chỉ như sau để vẫn cho phép sao chép DML và DDL:

 - Người dùng có thuộc tính REPLICATION.
 - Nó kế thừa các vai trò pglogical_superuser và bdr_superuser.

_6. Hạn chế về Đặc quyền_

BDR thực thi các hạn chế bổ sung, ngăn chặn hiệu quả việc sử dụng DDL chỉ dựa vào các đặc quyền TRIGGER hoặc REFERENCES. Các phần phụ sau đây giải thích những điều này.

GRANT ALL sẽ vẫn cấp cả đặc quyền TRIGGER và REFERENCES, vì vậy bạn nên nêu rõ các đặc quyền, ví dụ: GRANT SELECT, INSERT, UPDATE, DELETE, TRUNCATE thay vì ALL.
