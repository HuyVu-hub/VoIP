# BDR (Bi-Directional Replication)

BDR (viết tắt của Bi-Directional Replication) là một phần mở rộng của PostgreSQL cung cấp giải pháp xây dựng các cụm đa tổng thể với cấu trúc liên kết lưới. 
Điều này có nghĩa là bạn có thể ghi vào bất kỳ máy chủ nào và các thay đổi sẽ được gửi từng hàng một đến tất cả các máy chủ khác thuộc cùng một nhóm BDR.

BDR phiên bản 3 ("BDR3") được xây dựng trên phần mở rộng pglogical3 . 

Ở đây chúng tachỉ đề cập đến BDR3, không đề cập đến các kiến trúc trước đó, được gọi là BDR1 và BDR2. 

BDR3 hỗ trợ các phiên bản và biến thể khác nhau của PostgreSQL, như được hiển thị bên dưới.

![image](https://user-images.githubusercontent.com/55483458/143796431-a9ea7d41-6f22-423b-af27-5172d6ed4474.png)

EDB Postgres Extended trước đây được gọi là 2ndQ Postgres.

Một số tính năng chỉ có sẵn trên các phiên bản cụ thể của máy chủ Postgres:

- Các loại dữ liệu được sao chép không có xung đột - các loại dữ liệu bổ sung cung cấp tính nhất quán đã được đảm bảo về mặt toán học 
trong các tình huống cập nhật đa tổng thể không đồng bộ (EDB Postgres Advanced, EDB Postgres Extended)

- Giải quyết xung đột ở cấp độ cột - khả năng sử dụng mỗi cột trong bản cập nhật mới nhất sẽ có quyền điều khiển độ phân giải để UPDATE 
trên các trường khác nhau có thể được "merged" mà không làm mất một trong hai (EDB Postgres Advanced, EDB Postgres Extended)

- Trình kích hoạt chuyển đổi - trình kích hoạt được thực thi trên luồng dữ liệu được gửi tới, cung cấp khả năng sửa đổi hoặc thực hiện lọc theo chương trình nâng cao 
(EDB Postgres Advanced, EDB Postgres Extended)

- Trình kích hoạt xung đột - trình kích hoạt được gọi khi xung đột được phát hiện, cung cấp cách sử dụng các kỹ thuật giải quyết xung đột tùy chỉnh (EDB Postgres Advanced, EDB Postgres Extended)

- Hỗ trợ DDL bổ sung (CREATE TABLE AS) (EDB Postgres Nâng cao, EDB Postgres Mở rộng)

- Xử lý DDL nâng cao cho các ràng buộc NOT VALID và ALTER TABLE (EDB Postgres Advanced, EDB Postgres Extended)

- Đồng bộ hóa bổ sung cho các máy chủ Logic / Vật lý ở chế độ chờ để xây dựng nhanh hơn các standby có thể sửa chữa được (EDB Postgres Advanced, EDB Postgres Extended)

- Áp dụng song song - cho phép nhiều người viết áp dụng các thay đổi sắp tới (EDB Postgres Advanced, EDB Postgres Extended)

- Eager Replication - đồng bộ hóa giữa các node của cluster trước khi thực hiện trao đổi thông tin để cung cấp sao chép không có xung đột (EDB Postgres Extended)

- Cam kết nhiều nhất một lần - một tính năng nhất quán giúp ứng dụng chỉ cam kết mỗi giao dịch một lần, ngay cả khi có lỗi nút (EDB Postgres Extended)

- Timestamp - based Snapshots - cung cấp các lần đọc nhất quán trên nhiều node để truy xuất dữ liệu khi chúng xuất hiện hoặc sẽ xuất hiện tại một thời điểm nhất định (EDB Postgres Extended)

- Ước tính thời gian bắt đầu sao chép (EDB Postgres Extended)

- Giữ đóng băng để hỗ trợ giải quyết xung đột UPDATE / DELETE (EDB Postgres Extended)

- Công cụ giải mã (EDB Postgres Extended 13 trở lên)

- Các tính năng hiện chỉ khả dụng với EDB Postgres Extended dự kiến sẽ có sẵn với EDB Postgres Advanced 14.




