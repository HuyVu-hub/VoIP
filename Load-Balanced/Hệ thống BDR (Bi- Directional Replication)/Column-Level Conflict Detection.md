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

Lưu ý: Chúng tôi có thể thêm dấu thời gian sao kê và giao dịch trong tương lai, điều này sẽ giải quyết các vấn đề về tác động trộn lẫn giữa các báo cáo hoặc giao dịch đồng thời. Tuy nhiên, cả hai tùy chọn này đều không thể tạo ra kết quả tương đương với thứ tự cam kết.

Cũng có thể sử dụng dấu thời gian cam kết thực tế, mặc dù tính năng này hiện đang được coi là thử nghiệm. Để sử dụng dấu thời gian cam kết, hãy đặt tham số cuối cùng thành true khi bật giải quyết xung đột cấp cột:

```
SELECT bdr.column_timestamps_enable('test_table'::regclass, 'cts', true);
```
Điều này cũng có thể được tắt bằng cách sử dụng bdr.column_timestamps_disable.

Dấu thời gian cam kết hiện có một số hạn chế được giải thích trong phần "Hạn chế".

**Inspecting Column Timestamps** - Kiểm tra Dấu thời gian cột

Dấu thời gian lưu trữ cột cho các cột đã sửa đổi được trình kích hoạt duy trì tự động và không được sửa đổi trực tiếp. Có thể hữu ích khi kiểm tra giá trị dấu thời gian hiện tại, chẳng hạn như trong khi điều tra cách giải quyết một xung đột cụ thể.

Có ba chức năng cho mục đích này:

 - bdr.column_timestamps_to_text(bdr.column_timestamps)

Hàm này trả về một biểu diễn mà con người có thể đọc được của ánh xạ dấu thời gian và được sử dụng khi truyền giá trị sang văn bản:

```
db=# select cts::text from test_table;
                                                 cts
-----------------------------------------------------------------------------------------------------
 {source: current, default: 2018-09-23 19:24:52.118583+02, map: [2 : 2018-09-23 19:25:02.590677+02]}
(1 row)
```

 - bdr.column_timestamps_to_jsonb(bdr.column_timestamps)

Hàm này biến một biểu diễn JSONB của ánh xạ dấu thời gian và được sử dụng khi truyền giá trị tới jsonb:

```
db=# select jsonb_pretty(cts::jsonb) from test_table;
                   jsonb_pretty                    
---------------------------------------------------
 {                                                +
     "map": {                                     +
         "2": "2018-09-23T19:24:52.118583+02:00"  +
     },                                           +
     "source": "current",                         +
     "default": "2018-09-23T19:24:52.118583+02:00"+
 }
(1 row)
```

- bdr.column_timestamps_resolve(bdr.column_timestamps, xid)

Hàm này cập nhật ánh xạ với dấu thời gian cam kết cho các thuộc tính được sửa đổi bởi giao dịch gần đây nhất (nếu nó đã được cam kết). Điều này chỉ quan trọng khi sử dụng dấu thời gian cam kết. Ví dụ: trong trường hợp này, giao dịch cuối cùng đã cập nhật thuộc tính thứ hai (với attnum = 2):

```
test=# select cts::jsonb from test_table;
                                                                  cts
----------------------------------------------------------------------------------------------------------------------------------------
 {"map": {"2": "2018-09-23T19:29:55.581823+02:00"}, "source": "commit", "default": "2018-09-23T19:29:55.581823+02:00", "modified": [2]}
(1 row)

db=# select bdr.column_timestamps_resolve(cts, xmin)::jsonb from test_table;
                                               column_timestamps_resolve
-----------------------------------------------------------------------------------------------------------------------
 {"map": {"2": "2018-09-23T19:29:55.581823+02:00"}, "source": "commit", "default": "2018-09-23T19:29:55.581823+02:00"}
(1 row)
```

**Handling column conflicts using CRDT Data Types** - Xử lý xung đột cột bằng Kiểu dữ liệu CRDT

Theo mặc định, giải quyết xung đột cấp cột chỉ cần chọn giá trị có dấu thời gian cao hơn và loại bỏ giá trị còn lại. Tuy nhiên, có thể hòa giải xung đột theo những cách khác nhau (phức tạp hơn), chẳng hạn như sử dụng các loại CRDT cho phép "hợp nhất" các giá trị xung đột mà không loại bỏ bất kỳ thông tin nào.

Trong khi pglogical không bao gồm bất kỳ kiểu dữ liệu nào như vậy, nó cho phép thêm chúng một cách riêng biệt và đăng ký chúng trong một danh mục crdt_handlers. Ngoài các hàm kiểu dữ liệu thông thường (đầu vào / đầu ra, ...), mỗi kiểu CRDT phải triển khai một hàm hợp nhất, hàm này nhận chính xác ba đối số (giá trị cục bộ, giá trị từ xa cũ, giá trị từ xa mới) và tạo ra một thông tin hợp nhất giá trị từ ba giá trị đó.

**Limitations** - Hạn chế

Các thuộc tính được CẬP NHẬT sửa đổi được xác định bằng cách so sánh hàng cũ và hàng mới trong một trình kích hoạt. Điều này có nghĩa là nếu thuộc tính không thay đổi giá trị, nó sẽ không được phát hiện là đã sửa đổi ngay cả khi nó được đặt rõ ràng. Ví dụ: CẬP NHẬT t SET a = a sẽ không đánh dấu a là đã sửa đổi cho bất kỳ hàng nào. Tương tự, CẬP NHẬT t SET a = 1 sẽ không đánh dấu a là đã sửa đổi đối với các hàng đã được đặt thành 1.

Đối với câu lệnh INSERT, chúng tôi không có bất kỳ hàng cũ nào để so sánh với hàng mới, vì vậy chúng tôi coi tất cả các thuộc tính sẽ được sửa đổi và gán cho chúng một dấu thời gian mới. Điều này áp dụng ngay cả cho các cột không được bao gồm trong câu lệnh INSERT và đã nhận các giá trị mặc định. Chúng tôi có thể phát hiện thuộc tính nào có giá trị mặc định, nhưng không thể quyết định xem nó có được người dùng đưa vào tự động hay chỉ định rõ ràng hay không.

Điều này có nghĩa là giải quyết xung đột cấp cột không hoạt động đối với xung đột INSERT-INSERT (ngay cả khi câu lệnh INSERT chỉ định các tập con khác nhau của các cột, vì hàng mới hơn sẽ có tất cả các dấu thời gian mới hơn hàng cũ hơn).

Bằng cách xử lý các cột một cách độc lập, rất dễ vi phạm các ràng buộc theo cách không thể thực hiện được khi tất cả các thay đổi xảy ra trên cùng một nút. Hãy xem xét ví dụ một bảng như thế này:

```
CREATE TABLE t (id INT PRIMARY KEY, a INT, b INT, CHECK (a > b));
INSERT INTO t VALUES (1, 1000, 1);
```

... và giả sử một nút thực hiện:

```
UPDATE t SET a = 100;
```

... trong khi một nút khác thực hiện đồng thời:

```
UPDATE t SET b = 500;
```

Mỗi bản cập nhật đó đều hợp lệ khi được thực thi trên hàng ban đầu, và như vậy sẽ được truyền trên mỗi nút. Nhưng khi sao chép sang nút khác, hàng kết quả vi phạm ràng buộc CHECK (A> b) và quá trình sao chép sẽ dừng cho đến khi sự cố được giải quyết theo cách thủ công.

 - Ánh xạ dấu thời gian lưu trữ cột được quản lý tự động. Không chỉ định hoặc ghi đè giá trị trong các truy vấn của bạn, vì nó có thể dẫn đến các tác động không thể đoán trước (dù sao thì chúng tôi cũng bỏ qua giá trị nếu có thể).

 - Ánh xạ dấu thời gian được duy trì bởi trình kích hoạt, nhưng thứ tự mà trình kích hoạt thực thi không quan trọng. Vì vậy, nếu bạn có trình kích hoạt tùy chỉnh sửa đổi các bộ giá trị và được thực thi sau trình kích hoạt pgl_clcd_, các cột đã sửa đổi sẽ không được phát hiện chính xác.

 - Khi sử dụng dấu thời gian thông thường để sắp xếp các thay đổi / cam kết, có thể những thay đổi xung đột có chính xác cùng một dấu thời gian (vì hai hoặc nhiều nút đã xảy ra để tạo cùng một dấu thời gian). Rủi ro này không phải là duy nhất đối với giải quyết xung đột cấp cột, vì nó có thể xảy ra ngay cả đối với giải quyết xung đột cấp hàng thông thường và chúng tôi sử dụng id nút làm điểm kết thúc trong trường hợp này (id nút cao hơn sẽ thắng), điều này đảm bảo rằng các thay đổi được áp dụng trên tất cả các nút.

 - Có thể có sự lệch đồng hồ giữa các nút khác nhau. Mặc dù nó có thể gây ra hành vi hơi không mong muốn (loại bỏ những thay đổi có vẻ mới hơn vì dấu thời gian bị đảo ngược), nhưng độ lệch đồng hồ giữa các nút có thể được quản lý bằng cách sử dụng các tham số bdr.maximum_clock_skew và bdr.maximum_clock_skew_action.

 - Đăng ký bệnh học cơ bản không được loại bỏ bất kỳ thay đổi nào, điều này có thể dễ dàng gây ra các lỗi khác nhau (đặc biệt đối với các loại CRDT). Các đăng ký phải có ignore_redundant_updates được đặt thành false (là mặc định).

Các nhóm hiện có được tạo với giá trị không phải mặc định cho ignore_redundant_updates có thể được thay đổi như sau:

```
SELECT bdr.alter_node_group_config('group', ignore_redundant_updates := false);
```
