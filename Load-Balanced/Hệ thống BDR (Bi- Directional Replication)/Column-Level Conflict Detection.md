### Column-Level Conflict Detection - Phát hiện xung đột cấp cột

Theo mặc định, xung đột được giải quyết ở cấp hàng. Tức là, khi các thay đổi từ hai nút xung đột, chúng tôi chọn bộ điều chỉnh cục bộ hoặc bộ từ xa và loại bỏ bộ khác. Ví dụ: chúng tôi có thể so sánh dấu thời gian cam kết cho hai thay đổi xung đột và giữ lại dấu thời gian mới hơn. Điều này đảm bảo rằng tất cả các nút hội tụ đến cùng một kết quả và thiết lập ngữ nghĩa giống thứ tự cam kết trên toàn bộ cụm.

Tuy nhiên, trong một số trường hợp, có thể thích hợp để giải quyết xung đột ở cấp cột hơn là cấp hàng.

Hãy xem xét một ví dụ đơn giản, trong đó chúng ta có một bảng "t" với hai cột số nguyên "a" và "b", và một hàng duy nhất (1,1). Giả sử rằng trên một nút, chúng tôi thực thi:

```
UPDATE t SET a = 100
```

... trong khi trên một nút khác, chúng tôi đồng thời (trước khi nhận được UPDATE trước đó) thực thi:

```
UPDATE t SET b = 100
```

Điều này dẫn đến xung đột UPDATE-UPDATE. Với giải pháp xung đột update_if_newer, chúng tôi so sánh dấu thời gian cam kết và giữ phiên bản hàng mới. Giả sử nút thứ hai được cam kết cuối cùng, chúng tôi kết thúc bằng (1.100), hiệu quả loại bỏ thay đổi đối với cột "a".

Đối với nhiều trường hợp sử dụng, đây là hành vi mong muốn và được mong đợi, nhưng đối với một số người thì đây có thể là một vấn đề - ví dụ: hãy xem xét một cụm nhiều nút trong đó mỗi phần của ứng dụng được kết nối với một nút khác, cập nhật một tập hợp con dành riêng của các cột trong một bàn chung. Trong trường hợp đó, các thành phần khác nhau có thể giẫm lên nhau, ghi đè lên các thay đổi của chúng.

Đối với các trường hợp sử dụng như vậy, có thể thích hợp hơn để giải quyết xung đột trên một bảng nhất định ở cấp cột. Để đạt được điều đó, BDR sẽ theo dõi dấu thời gian của thay đổi cuối cùng cho từng cột riêng biệt và sử dụng dấu thời gian đó để chọn giá trị gần đây nhất (về cơ bản là update_if_newer).

Áp dụng cho ví dụ trước, chúng ta sẽ kết thúc với (100,100) trên cả hai nút, mặc dù không có nút nào nhìn thấy một hàng như vậy.

Khi nghĩ về giải quyết xung đột cấp cột, có thể hữu ích khi xem các bảng được phân vùng theo chiều dọc, để mỗi bản cập nhật chỉ ảnh hưởng đến dữ liệu trong một phần. Điều này giúp loại bỏ xung đột giữa các thay đổi đối với các tập hợp con khác nhau của các cột. Trên thực tế, phân vùng theo chiều dọc thậm chí có thể là một giải pháp thay thế thiết thực cho việc giải quyết xung đột cấp cột.

Giải quyết xung đột cấp cột yêu cầu bảng phải có REPLICA IDENTITY REPLICA. Hàm bdr.alter_table_conflict_detection kiểm tra điều đó và nếu không sẽ bị lỗi.

**Bật và tắt giải quyết xung đột cấp cột**

Giải pháp xung đột cấp cột được quản lý bởi hàm bdr.alter_table_conflict_detection ().

_Ví dụ:_

Để minh họa cách bdr.alter_table_conflict_detection () được sử dụng, hãy xem xét ví dụ này tạo một bảng tầm thường test_table và sau đó bật giải quyết xung đột cấp cột trên đó:

```
db=# CREATE TABLE my_app.test_table (id SERIAL PRIMARY KEY, val INT);
CREATE TABLE

db=# ALTER TABLE my_app.test_table REPLICA IDENTITY FULL;
ALTER TABLE

db=# SELECT bdr.alter_table_conflict_detection(
db(# 'my_app.test_table'::regclass, 'column_modify_timestamp', 'cts');
 alter_table_conflict_detection 
--------------------------------
 t

db=# \d my_app.test_table
```

Bạn sẽ thấy rằng hàm thêm một cột cts mới (như được chỉ định trong lệnh gọi hàm), nhưng nó cũng tạo ra hai trình kích hoạt (CHÈN TRƯỚC và CẬP NHẬT TRƯỚC) chịu trách nhiệm duy trì dấu thời gian trong cột mới trước mỗi thay đổi.

Cũng đáng nói là cột mới chỉ định NOT NULL với giá trị mặc định, có nghĩa là ALTER TABLE ... ADD COLUMN không thực hiện ghi lại bảng.

Lưu ý: Chúng tôi không khuyến khích sử dụng các cột có kiểu dữ liệu bdr.column_timestamps cho các mục đích khác vì nó có thể có nhiều tác động tiêu cực khác nhau (nó chuyển bảng sang giải quyết xung đột cấp cột, điều này sẽ không hoạt động chính xác nếu không có trình kích hoạt, v.v.).

**Bảng danh sách có giải quyết xung đột cấp cột**

Các bảng đã bật giải quyết xung đột cấp cột có thể được liệt kê với truy vấn sau, truy vấn này phát hiện sự hiện diện của một cột kiểu bdr.column_timestamp:

```
SELECT nc.nspname, c.relname
FROM pg_attribute a
JOIN (pg_class c JOIN pg_namespace nc ON c.relnamespace = nc.oid)
  ON a.attrelid = c.oid
JOIN (pg_type t  JOIN pg_namespace nt ON t.typnamespace = nt.oid)
  ON a.atttypid = t.oid
WHERE NOT pg_is_other_temp_schema(nc.oid)
  AND nt.nspname = 'bdr'
  AND t.typname = 'column_timestamps'
  AND NOT a.attisdropped
  AND c.relkind IN ('r', 'v', 'f', 'p');
```

_**bdr.column_timestamps_create**_

Chức năng này tạo ra giải quyết xung đột cấp cột. Điều này được gọi trong column_timestamp_enable.

_Tóm tắc_

```
bdr.column_timestamps_create(p_source cstring, p_timestamp timestampstz)
```

_Thông số_

 - p_source - Hai tùy chọn là 'hiện tại' hoặc 'cam kết'.
 - p_timestamp - Dấu thời gian phụ thuộc vào nguồn được chọn: if 'commit', then TIMESTAMP_SOURCE_COMMIT; nếu 'hiện tại', thì TIMESTAMP_SOURCE_CURRENT.

**Khóa DDL**

Khi bật hoặc tắt dấu thời gian cột trên bảng, mã sử dụng khóa DDL để đảm bảo rằng không có thay đổi nào đang chờ xử lý từ trước khi chuyển đổi, để đảm bảo chúng ta chỉ thấy xung đột với dấu thời gian trong cả hai bộ hoặc cả hai bộ. Nếu không, mã có thể bất ngờ nhìn thấy dấu thời gian trong bộ mã cục bộ và NULL trong bộ điều khiển từ xa. Nó cũng đảm bảo rằng các thay đổi được giải quyết theo cùng một cách (cấp cột hoặc cấp hàng) trên tất cả các nút.

**Current vs Commit Timestamp** - Dấu thời gian hiện tại so với cam kết

Một câu hỏi quan trọng là dấu thời gian nào để gán cho các cột đã sửa đổi.

Theo mặc định, dấu thời gian được gán cho các cột đã sửa đổi là dấu thời gian hiện tại, như thể được lấy từ clock_timestamp. Điều này đơn giản và trong nhiều trường hợp, nó hoàn toàn đúng (ví dụ: khi các hàng xung đột sửa đổi các tập con không chồng chéo của cột).

Tuy nhiên, nó có thể có nhiều tác dụng bất ngờ khác nhau:

 - Dấu thời gian thay đổi trong quá trình thực thi câu lệnh, vì vậy nếu một UPDATE ảnh hưởng đến nhiều hàng, mỗi hàng sẽ nhận được một dấu thời gian hơi khác. Điều này có nghĩa là các tác động của các thay đổi đồng thời có thể bị "trộn lẫn" theo nhiều cách khác nhau (tùy thuộc vào cách chính xác các thay đổi được thực hiện trên xen kẽ các nút khác nhau).

 - Dấu thời gian không liên quan đến dấu thời gian cam kết và việc sử dụng nó để giải quyết xung đột có nghĩa là kết quả không tương đương với thứ tự cam kết, có nghĩa là nó không thể tuần tự hóa được.

