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

 - bdr.ddl_locking - Được mô tả ở trên.
 - bdr.global_lock_max_locks - Số lượng khóa toàn cục tối đa có thể được giữ trên một nút (mặc định là 1000). Chỉ có thể được đặt khi khởi động máy chủ Postgres.
 - bdr.global_lock_timeout - Đặt thời lượng tối đa cho phép của bất kỳ thời gian chờ khóa toàn cầu nào (mặc định là 1 phút). Giá trị 0 sẽ vô hiệu hóa thời gian chờ này.
 - bdr.global_lock_statement_timeout - Đặt thời lượng tối đa cho phép của bất kỳ câu lệnh nào giữ khóa toàn cục (mặc định là 10 phút). Giá trị 0 sẽ vô hiệu hóa thời gian chờ này.
 - bdr.global_lock_idle_timeout - Đặt khoảng thời gian không hoạt động tối đa được phép trong giao dịch giữ khóa toàn cầu (mặc định là 10 phút). Giá trị 0 sẽ vô hiệu hóa thời gian chờ này.

**Node Management** - Quản lý nút
 
 - bdr.replay_progress_frequency - Khoảng thời gian để gửi thông tin vị trí sao chép đến phần còn lại của cụm (mặc định 1 phút).

**Generic Replication** - Nhân rộng chung

 - bdr.xact_replication - Sao chép giao dịch hiện tại (mặc định là "bật").

Việc tắt tính năng này sẽ chỉ làm cho toàn bộ giao dịch chỉ cục bộ, có nghĩa là giao dịch sẽ không hiển thị với giải mã logic bởi BDR và tất cả các mục tiêu hạ nguồn khác của giải mã logic. Dữ liệu sẽ không được chuyển sang bất kỳ nút nào khác, kể cả các nút dự phòng logic.

Tham số này chỉ có thể được đặt bởi bdr_superuser hoặc các vai trò superuser.

Tham số này chỉ có thể được đặt bên trong giao dịch hiện tại bằng lệnh SET LOCAL trừ khi bdr.permit_unsafe_commands = on.

```
Ghi chú:
Ngay cả khi tính năng sao chép giao dịch bị vô hiệu hóa, WAL sẽ được tạo nhưng những thay đổi đó sẽ bị lọc đi trên nguồn gốc.
```

```
Cảnh báo:
Việc tắt bdr.xact_replication sẽ dẫn đến sự không nhất quán về dữ liệu giữa các nút và chỉ nên được sử dụng để khôi phục từ sự phân kỳ dữ liệu giữa các nút hoặc trong các tình huống sao chép mà các thay đổi trên các nút đơn lẻ được yêu cầu để tiếp tục sao chép. Sử dụng có nguy cơ của riêng bạn.
```

 - bdr.permit_unsafe_commands - Tùy chọn để ghi đè kiểm tra an toàn đối với các lệnh được coi là không an toàn cho mục đích sử dụng chung.

Yêu cầu bdr_superuser hoặc PostgreSQL superuser.

```
Cảnh báo:
Các lệnh thường không được coi là an toàn có thể tạo ra kết quả không nhất quán hoặc phá vỡ hoàn toàn quá trình sao chép. Sử dụng có nguy cơ của riêng bạn.
```

 - bdr.batch_inserts - Số lần chèn liên tiếp vào một bảng trong một giao dịch đơn lẻ sẽ bật xử lý hàng loạt các lần chèn cho bảng đó.

Tùy chọn này cho phép sao chép các tải dữ liệu lớn dưới dạng SAO CHÉP bên trong, thay vì tập hợp các phần chèn. Nó cũng là cách dữ liệu ban đầu trong quá trình tham gia nút được sao chép.

 - bdr.maximum_clock_skew

Điều này chỉ định những gì nên được coi là chênh lệch tối đa giữa dấu thời gian cam kết giao dịch đến và thời gian hiện tại trên người đăng ký trước khi kích hoạt bdr.maximum_clock_skew_action.

Điều này kiểm tra xem dấu thời gian của giao dịch hiện được phát lại có nằm trong tương lai so với thời điểm hiện tại trên người đăng ký hay không; và nếu đúng như vậy và sự khác biệt lớn hơn bdr.maximum_clock_skew, nó sẽ thực hiện hành động được chỉ định bởi cài đặt bdr.maximum_clock_skew_action.

Giá trị mặc định là -1, có nghĩa là: bỏ qua độ lệch đồng hồ (kiểm tra bị tắt). Việc đặt 0 là hợp lệ khi đồng hồ trên tất cả các máy chủ được đồng bộ hóa, thực tế là chúng tôi đang phát lại giao dịch có nghĩa là nó đã được cam kết trong quá khứ.

 - bdr.maximum_clock_skew_action

Điều này chỉ định hành động cần thực hiện nếu phát hiện độ lệch xung nhịp cao hơn bdr.maximum_clock_skew.

Có hai giá trị có thể có cho tùy chọn này:

   - WARN - Ghi cảnh báo về sự thật này. Các cảnh báo được ghi lại một lần mỗi phút (mặc định) ở mức tối đa để tránh làm ngập nhật ký máy chủ.
   - WAIT - Chờ cho đến khi dấu thời gian cục bộ hiện tại không còn cũ hơn dấu thời gian cam kết từ xa trừ bdr.maximum_clock_skew.

**CRDTs**

 - bdr.crdt_raw_value - Đặt định dạng đầu ra của Kiểu dữ liệu CRDT. Đầu ra mặc định (khi cài đặt này bị tắt) chỉ trả về giá trị hiện tại của loại CRDT cơ sở (ví dụ: bigint cho crdt_pncounter). Khi được đặt thành bật, giá trị trả về đại diện cho toàn bộ giá trị CRDT, ví dụ có thể bao gồm trạng thái từ nhiều nút.

**Max Prepared Transactions** - Giao dịch chuẩn bị tối đa

 - max_prepared_transactions - Cần đặt đủ cao để đối phó với số lượng tối đa các giao dịch được chuẩn bị đồng thời trên toàn bộ cụm do các cam kết hai giai đoạn rõ ràng, các giao dịch CAMO hoặc Eager. Vượt quá giới hạn ngăn một nút chạy một giao dịch CAMO hoặc cam kết hai giai đoạn cục bộ và sẽ ngăn tất cả các giao dịch Eager trên cụm. Chỉ có thể được đặt khi khởi động máy chủ Postgres. (EDB Postgres mở rộng)

**Eager Replication** - Háo hức sao chép

 - bdr.commit_scope - Đặt phạm vi cam kết thành toàn cầu cho phép nhân rộng tất cả các nút (cục bộ mặc định) một cách háo hức.

 - bdr.global_commit_timeout - Thời gian chờ cho cả hai giai đoạn của cam kết hai giai đoạn toàn cầu (mặc định 60s) cũng như đối với các giao dịch được CAMO bảo vệ trong giai đoạn cam kết của chúng, là giới hạn thời gian chờ đối tác CAMO.

```
Ghi chú:
Điều này chỉ có sẵn trên EDB Postgres Extended.
```

**Commit at Most Once** - Cam kết nhiều nhất một lần

 - bdr.enable_camo - Được sử dụng để bật và điều khiển tính năng CAMO. Mặc định là tắt. CAMO có thể được bật cho mỗi giao dịch bằng cách đặt cài đặt này thành remote_write, remote_commit_async hoặc remote_commit_flush. Để tương thích ngược, các giá trị on, true và 1 đặt chế độ remote_commit_flush an toàn nhất. Trong khi false hoặc 0 cũng vô hiệu hóa CAMO.
 - bdr.camo_partner_of - Cho phép chỉ định đối tác CAMO trên mỗi cơ sở dữ liệu. Mong đợi các cặp tên cơ sở dữ liệu và tên nút được nối bằng dấu hai chấm. Nhiều cặp có thể được chỉ định, nhưng chỉ tính đến lần xuất hiện đầu tiên trên mỗi cơ sở dữ liệu. Ví dụ: 'db1: node_4 test_db: test_node_3'. Chỉ có thể được đặt khi khởi động máy chủ Postgres.
 - bdr.camo_origin_for - Tên nút trên mỗi cơ sở dữ liệu về nguồn gốc của các giao dịch trong ghép nối CAMO; đối với mỗi cơ sở dữ liệu, điều này cần phải khớp với cài đặt       bdr.camo_partner_of trên nút gốc tương ứng. Chỉ có thể được đặt khi khởi động máy chủ Postgres.
 - bdr.standby_dsn - Cho phép ghi đè thủ công chuỗi kết nối (DSN) để đến đối tác CAMO, trong trường hợp nó đã thay đổi kể từ sự cố của nút cục bộ. Thường nên không được đặt. Chỉ có thể được đặt khi khởi động máy chủ Postgres.
 - bdr.camo_local_mode_delay - Độ trễ cam kết áp dụng trong chế độ Local của CAMO để mô phỏng chi phí thường xảy ra với đối tác CAMO phải xác nhận giao dịch. Mặc định là 5 ms. Đặt thành 0 sẽ tắt tính năng này.
 - bdr.camo_enable_client_warnings - Đưa ra cảnh báo nếu một hoạt động được thực hiện trong cơ sở dữ liệu mà các thuộc tính CAMO không thể được đảm bảo. Điều này được kích hoạt theo mặc định. Người dùng thông thạo có thể chọn tắt tính năng này để giảm lượng cảnh báo đi vào nhật ký của họ.

```
Ghi chú:
Điều này chỉ có sẵn trên EDB Postgres Extended.
```
**Timestamp-based Snapshots** - Ảnh chụp nhanh dựa trên dấu thời gian

 - bdr.timestamp_snapshot_keep - Trong bao lâu để giữ các ảnh chụp nhanh hợp lệ cho việc sử dụng ảnh chụp nhanh dựa trên dấu thời gian (mặc định là 0, nghĩa là không giữ các ảnh chụp nhanh trong quá khứ). Cũng xem snapshot_timestamp ở trên. (EDB Postgres mở rộng)

**Monitoring and Logging** - Giám sát và Ghi nhật ký

 - bdr.debug_level - Xác định mức nhật ký mà BDR sử dụng để viết các thông báo gỡ lỗi của nó. Giá trị mặc định là debug2. Nếu bạn muốn xem chi tiết đầu ra gỡ lỗi BDR, hãy đặt bdr.debug_level = 'log'.

 - bdr.trace_level - Tương tự như trên, điều này xác định mức nhật ký để sử dụng cho các thông báo theo dõi BDR. Bật theo dõi trên tất cả các nút của một cụm BDR có thể giúp Hỗ trợ 2ndQuadrant chẩn đoán sự cố. Chỉ có thể được đặt khi khởi động máy chủ Postgres.

```
Cảnh báo:
Đặt bdr.debug_level hoặc bdr.trace_level thành một giá trị> = log_min_messages có thể tạo ra một khối lượng rất lớn đầu ra nhật ký, vì vậy nó không nên được bật lâu dài trong sản xuất trừ khi có kế hoạch lọc nhật ký, lưu trữ và xoay để ngăn dung lượng đĩa kiệt quệ.
```

**Internals** - Nội bộ

 - bdr.raft_keep_min_entries - Số mục nhập tối thiểu cần giữ trong nhật ký Raft khi thực hiện nén nhật ký (mặc định là 100). Giá trị 0 sẽ vô hiệu hóa tính năng nén nhật ký. CẢNH BÁO: Nếu tính năng nén nhật ký bị tắt, nhật ký sẽ tăng kích thước mãi mãi. Chỉ có thể được đặt khi khởi động máy chủ Postgres.
 - bdr.raft_response_timeout - Để giải quyết các lỗi mạng, giao thức đồng thuận Raft được triển khai sẽ hết thời gian yêu cầu sau một khoảng thời gian nhất định. Thời gian chờ này mặc định là 30 giây.
 - bdr.raft_log_min_apply_duration - Để di chuyển máy trạng thái về phía trước, Raft gắn các mục nhập vào nhật ký nội bộ của nó. Trong quá trình hoạt động bình thường, việc bổ sung chỉ mất vài mili giây. Điều này đặt ra một ngưỡng cao hơn về thời lượng của hành động nối thêm đó, trên đó thông báo INFO được ghi lại. Điều này có thể chỉ ra một vấn đề thực tế. Giá trị mặc định của tham số này là 3000 ms.
 - bdr.raft_log_min_message_duration - Khi nào cần ghi nhật ký một yêu cầu đồng thuận. Đo thời gian khứ hồi của một yêu cầu đồng thuận bdr và ghi lại thông báo INFO nếu thời gian vượt quá thông số này. Giá trị mặc định của tham số này là 5000 ms.
 - bdr.raft_group_max_connections - Số lượng kết nối tối đa trên tất cả các nhóm BDR cho một máy chủ Postgres. Các kết nối này mang các yêu cầu đồng thuận bdr giữa các nút của nhóm. Giá trị mặc định của tham số này là 100 kết nối. Chỉ có thể được đặt khi khởi động máy chủ Postgres.
 - bdr.backwards_compatibility - Chỉ định phiên bản tương thích ngược với, ở định dạng số giống như được sử dụng bởi bdr.bdr_version_num, ví dụ: 30618. Cho phép hoạt động chính xác của phiên bản BDR cũ, ngay cả khi điều này nói chung có tác dụng không mong muốn. Mặc định là phiên bản BDR hiện tại. Vì điều này thay đổi từ bản phát hành sang bản phát hành, chúng tôi khuyên bạn không nên sử dụng rõ ràng trong tệp cấu hình trừ khi giá trị khác với phiên bản hiện tại.
 - bdr.track_replication_estimate - Theo dõi ước tính sao chép về tỷ lệ áp dụng và khoảng thời gian bắt kịp cho các nút ngang hàng. Thông tin này có thể được sử dụng bởi các giao thức như CAMO để ước tính mức độ sẵn sàng của một nút ngang hàng. Tham số này được bật theo mặc định. (EDB Postgres mở rộng)
 - bdr.lag_tracker_apply_rate_weight - Chúng tôi theo dõi mức độ tụt hậu của các nút ngang hàng về việc áp dụng WAL từ nút cục bộ và tính toán trung bình động của các tỷ lệ áp dụng cho theo dõi độ trễ. Tham số này chỉ định mức độ đóng góp của các giá trị được tính toán mới hơn trong phép tính trung bình động này. Giá trị mặc định là 0,1. (EDB Postgres mở rộng)
