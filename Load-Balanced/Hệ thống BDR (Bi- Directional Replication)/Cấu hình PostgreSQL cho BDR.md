### PostgreSQL Configuration for BDR (Cấu hình PostgreSQL cho BDR)

Có một số tham số cấu hình PostgreSQL ảnh hưởng đến các nút BDR. Lưu ý rằng các tham số này có thể được đặt khác nhau trên mỗi nút, mặc dù nói chung điều đó không được khuyến nghị.

**Cài đặt PostgreSQL cho BDR**

 BDR yêu cầu các cài đặt PostgreSQL này chạy chính xác:

 - wal_level - Phải được đặt thành logic, vì BDR dựa trên giải mã logic.
 - shared_preload_libraries - Điều này phải chứa pglogical, bdr (theo thứ tự đó), mặc dù cũng có thể chứa các mục nhập khác trước hoặc sau đó, nếu cần.
 - track_commit_timestamp - Phải được đặt thành 'bật' để giải quyết xung đột để truy xuất dấu thời gian cho mỗi hàng xung đột.

BDR yêu cầu các cài đặt PostgreSQL này phải được đặt thành các giá trị thích hợp, các giá trị này thay đổi theo kích thước và quy mô của cụm.

 - logic_decoding_work_mem - kích thước bộ đệm bộ nhớ được sử dụng bởi giải mã logic. Các giao dịch lớn hơn mức này sẽ làm tràn bộ đệm và được lưu trữ tạm thời trên đĩa cục bộ. Mặc định 64MB, nhưng có thể được đặt cao hơn nhiều.
 - max_worker_processes - BDR sử dụng background worker cho các tác vụ nhân bản và bảo trì, vì vậy cần có đủ vùng worker để nó hoạt động chính xác. Công thức cho số lượng nhân công tối thiểu chính xác là: một nhân viên cho mỗi phiên bản PostgreSQL + một cho mỗi cơ sở dữ liệu trên phiên bản đó + bốn cho mỗi cơ sở dữ liệu hỗ trợ BDR + một cho mỗi nút ngang hàng trong nhóm BDR + một cho mỗi người viết được kích hoạt trên mỗi nút ngang hàng trong BDR nhóm, cho mỗi cơ sở dữ liệu. Các quy trình nhân viên bổ sung có thể cần tạm thời khi nút đang bị xóa khỏi nhóm BDR.
 - max_wal_senders - Cần hai nút cho mỗi nút ngang hàng.
 - max_replication_slots - Giống như max_wal_senders.
 - wal_sender_timeout và wal_receiver_timeout - Xác định mức độ nhanh chóng mà một nguồn cung cấp xem đối tác CAMO của nó là bị ngắt kết nối hoặc được kết nối lại; xem Các trường hợp lỗi CAMO để biết thêm chi tiết.

Lưu ý rằng trong quá trình chạy bình thường cho một nhóm có N nút ngang hàng, BDR sẽ yêu cầu N vị trí và người gửi WAL. Trong quá trình đồng bộ hóa, BDR sẽ tạm thời sử dụng các khe cắm N-1 khác và bộ gửi WAL, vì vậy hãy cẩn thận đặt các thông số trên đủ cao để đáp ứng nhu cầu cao điểm không thường xuyên này.

Khi bật tính năng áp dụng song song, số lượng vị trí cần được tăng lên N vị trí từ người viết công thức ở trên *. Điều này là do max_replication_slots cũng đặt số lượng gốc sao chép tối đa và một số chức năng của áp dụng song song sử dụng thêm nguồn gốc cho mỗi người viết.

Khi Trình làm việc giải mã được bật, quá trình này sẽ yêu cầu thêm một vị trí sao chép cho mỗi nhóm BDR.

Giá trị được đề xuất an toàn chung trên Nhóm BDR 4 nút với một cơ sở dữ liệu duy nhất là đặt max_replication_slots và max_worker_processes thành 50 và max_wal_senders thành ít nhất 10.

Cũng lưu ý rằng việc thay đổi các tham số này yêu cầu khởi động lại nút cục bộ: max_worker_processes, max_wal_senders, max_replication_slots.

Các ứng dụng cũng có thể muốn thiết lập các thông số này. Vui lòng xem chương về [Tùy chọn độ bền & hiệu suất] để thảo luận thêm.

 - sync_commit - ảnh hưởng đến độ bền và hiệu suất của sao chép BDR theo cách tương tự như sao chép vật lý.
 - sync_standby_names - tương tự như trên

**2ndQPostgres/EDB Postgres Extended Settings for BDR** - Cài đặt mở rộng 2ndQPostgres / EDB Postgres cho BDR

Các cài đặt Postgres sau đây cần được xem xét để cam kết nhiều nhất một lần (CAMO), một tính năng chỉ khả dụng cho BDR kết hợp với 2ndQPostgres. Một số trong số này chỉ có sẵn trong 2ndQPostgres; những người khác đã tồn tại trong phiên bản cộng đồng, nhưng chỉ trở nên phù hợp với BDR kết hợp với CAMO.

 - sync_replication_available - Có thể tùy chọn không đồng bộ để tăng tính khả dụng bằng cách cho phép một nguồn gốc tiếp tục và cam kết sau khi đối tác CAMO của nó bị ngắt kết nối. Theo giá trị mặc định của thời gian chờ, điểm gốc sẽ chờ vô thời hạn và chỉ tiến hành cam kết sau khi đối tác CAMO kết nối lại và gửi xác nhận.
 - snapshot_timestamp - Bật việc sử dụng ảnh chụp nhanh dựa trên dấu thời gian và đặt dấu thời gian để sử dụng.

**pglogical Settings for BDR** - Cài đặt pglogical cho BDR

BDR cũng bị ảnh hưởng bởi một số cài đặt pglogical vì nó sử dụng pglogical bên trong để thực hiện sao chép cơ bản.

 - pglogical.track_subscription_apply - Theo dõi số liệu thống kê áp dụng cho mỗi đăng ký.
 - pglogical.track_relation_apply - Thống kê áp dụng theo dõi cho từng mối quan hệ.
 - pglogical.track_apply_lock_timing - Theo dõi thời gian khóa khi theo dõi thống kê cho các mối quan hệ.
 - pglogical.standby_slot_names - Khi sử dụng các nút Chờ vật lý dành cho mục đích chuyển đổi dự phòng, nên được đặt thành (các) vị trí nhân bản cho mỗi Chế độ chờ dự định.
 - pglogical.writers_per_subscription - Số lượng tác giả mặc định trên mỗi đăng ký (trong BDR, bdr.alter_node_group_config cho một nhóm).
 - pglogical.max_writers_per_subscription - Số lượng người viết tối đa trên mỗi đăng ký (đặt giới hạn trên cho cài đặt ở trên).

**BDR Specific Settings** - Cài đặt cụ thể BDR

Ngoài ra còn có các cài đặt cấu hình cụ thể BDR có thể được đặt. Trừ khi có ghi chú khác, các giá trị có thể được đặt bởi bất kỳ người dùng nào bất kỳ lúc nào.

_Conflict Handling (Xử lý xung đột)_

 - bdr.default_conflict_detection - Đặt phương pháp phát hiện xung đột mặc định cho các bảng mới được tạo; chấp nhận các giá trị giống như bdr.alter_table_conflict_detection ()

_Global Sequence Parameters (Tham số trình tự toàn cầu)_ 

 - bdr.default_sequence_kind - Đặt loại trình tự mặc định.
 
_DDL Handling (Xử lý DDL)_

 - bdr.default_replica_identity- Đặt giá trị mặc định cho REPLICA IDENTITY các bảng mới tạo. Xác REPLICA IDENTITYđịnh thông tin nào được ghi vào nhật ký ghi trước để xác định các hàng được cập nhật hoặc xóa.

Các giá trị được chấp nhận là:

   - DEFAULT - ghi lại các giá trị cũ của các cột của khóa chính, nếu có (đây là hành vi PostgreSQL mặc định).
   - FULL - ghi lại giá trị cũ của tất cả các cột trong hàng.
   - NOTHING - không ghi thông tin về hàng cũ.

Xem tài liệu PostgreSQL để biết thêm chi tiết.

BDR không thể sao chép UPDATEcác s và DELETEs trên các bảng mà không có một PRIMARY KEY hoặc UNIQUEràng buộc, trừ khi nhận dạng bản sao cho bảng FULL, bằng cấu hình bảng cụ thể hoặc thông qua bdr.default_replica_identity.

Nếu bdr.default_replica_identity là DEFAULT và có một UNIQUE ràng buộc trên bảng, nó sẽ không được chọn tự động dưới dạng REPLICA IDENTITY. Nó cần được đặt rõ ràng tại thời điểm tạo bảng hoặc sau đó như được mô tả trong tài liệu ở trên.

Đặt nhận dạng bản sao của (các) bảng để FULLtăng khối lượng WAL được ghi và lượng dữ liệu được sao chép trên dây cho bảng.

 - bdr.ddl_replication - Tự động sao chép DDL qua các nút (mặc định "bật").

Tham số này chỉ có thể được đặt bởi bdr_superuser hoặc các vai trò siêu người dùng.

Chạy DDL hoặc gọi các chức năng quản trị BDR với bdr.ddl_replication = offcó thể tạo ra các tình huống mà việc sao chép dừng lại cho đến khi quản trị viên có thể can thiệp. Xem chương nhân bản DDL để biết thêm chi tiết.

LOGThông báo nhật ký A -level được gửi tới nhật ký máy chủ PostgreSQL bất cứ khi nào bdr.ddl_replicationđược đặt thành off. Ngoài ra, một WARNING-level thông báo được viết bất cứ khi nào bản sao các lệnh DDL đã bắt được hoặc các chức năng sao chép BDR bị bỏ qua do cài đặt này.

 - bdr.role_replication- Tự động sao chép các lệnh ROLE qua các nút (mặc định là "bật"). Tham số này chỉ có thể được thiết lập bởi superuser. Cài đặt này chỉ hoạt động nếu bdr.ddl_replicationcũng được bật.

Việc tắt tính năng này mà không sử dụng các phương pháp bên ngoài để đảm bảo các vai trò được đồng bộ trên tất cả các nút có thể khiến DDL được sao chép làm gián đoạn quá trình sao chép cho đến khi quản trị viên can thiệp.

Xem các câu lệnh thao tác vai trò trong chương sao chép DDL để biết thêm chi tiết.

- bdr.ddl_locking - Cấu hình chế độ hoạt động của khóa toàn cục cho DDL.

Tham số này chỉ có thể được đặt bởi bdr_superuser hoặc các vai trò siêu người dùng.

Các tùy chọn có thể là:

 - off - không sử dụng khóa toàn cầu cho các hoạt động DDL
 - on - sử dụng khóa toàn cầu cho tất cả các hoạt động DDL
 - dml - chỉ sử dụng khóa toàn cục cho các hoạt động DDL cần ngăn ghi bằng cách sử dụng khóa DML toàn cục cho một mối quan hệ

LOGThông báo nhật ký A -level được gửi tới nhật ký máy chủ PostgreSQL bất cứ khi nào bdr.ddl_replicationđược đặt thành off. Ngoài ra, một WARNING thông báo được viết bất cứ khi nào bất kỳ bước khóa toàn cầu nào bị bỏ qua do cài đặt này. Thông thường đối với một số câu lệnh có kết quả là hai WARNINGs, một để bỏ qua khóa DML và một để bỏ qua khóa DDL.

 - bdr.truncate_locking - False by default, this configuration option sets the TRUNCATE command's locking behavior. Determines whether (when true) TRUNCATE obeys the bdr.ddl_locking setting.

**Global Locking** - Khóa toàn cầu

