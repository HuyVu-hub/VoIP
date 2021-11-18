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
