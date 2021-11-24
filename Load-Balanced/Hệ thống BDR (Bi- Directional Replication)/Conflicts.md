### Conflicts - Xung đột

BDR là một DBMS hoạt động / tích cực hoặc đa chủ. Nếu được sử dụng không đồng bộ, việc ghi vào (các) hàng giống nhau hoặc có liên quan từ nhiều nút khác nhau có thể dẫn đến xung đột dữ liệu khi sử dụng kiểu dữ liệu chuẩn.

Xung đột không phải là LỖI - chúng là những sự kiện có thể được phát hiện và giải quyết tự động khi chúng xảy ra bởi BDR, trong hầu hết các trường hợp. Độ phân giải phụ thuộc vào bản chất của ứng dụng và ý nghĩa của dữ liệu, vì vậy điều quan trọng là BDR phải cung cấp cho ứng dụng một loạt các lựa chọn về cách giải quyết xung đột.

Theo mặc định, xung đột được giải quyết ở cấp hàng. Tức là, khi các thay đổi từ hai nút xung đột, chúng tôi chọn bộ điều chỉnh cục bộ hoặc bộ từ xa và loại bỏ bộ khác. Ví dụ: chúng tôi có thể so sánh dấu thời gian cam kết cho hai thay đổi xung đột và giữ lại dấu thời gian mới hơn. Điều này đảm bảo rằng tất cả các nút hội tụ đến cùng một kết quả và thiết lập ngữ nghĩa giống thứ tự cam kết trên toàn bộ cụm.

Chương này trình bày chi tiết các xung đột cấp độ hàng với các kiểu dữ liệu chuẩn.

Xử lý xung đột có thể được định cấu hình, như được mô tả ở phần sau của chương này. Xung đột có thể được phát hiện và xử lý khác nhau cho mỗi bảng bằng cách sử dụng trình kích hoạt xung đột, được mô tả trong chương Trình kích hoạt luồng.

Phát hiện và giải quyết xung đột mức cột có sẵn với BDR, được mô tả trong chương CLCD.

Nếu bạn muốn tránh xung đột, bạn có thể sử dụng các tính năng này trong BDR.

 - Các kiểu dữ liệu không có xung đột (CRDT) - được mô tả trong chương CRDT.

 - Nhân rộng Háo hức - được mô tả trong chương Nhân rộng Háo hức.

Theo mặc định, tất cả các xung đột được ghi vào bdr.conflict_history. Nếu có thể xảy ra xung đột thì chủ sở hữu bảng nên theo dõi chúng, phân tích để biết cách tránh chúng hoặc lập kế hoạch để xử lý chúng thường xuyên như một nhiệm vụ ứng dụng. Công cụ LiveCompare cũng có sẵn để quét thường xuyên để phát hiện sự khác biệt.

Một số hệ thống phân cụm sử dụng cơ chế khóa phân tán để ngăn truy cập đồng thời vào dữ liệu. Những điều này có thể hoạt động hợp lý khi các máy chủ ở rất gần, nhưng không thể hỗ trợ các ứng dụng được phân phối theo địa lý nơi độ trễ rất thấp là rất quan trọng đối với hiệu suất có thể chấp nhận được.

Khóa phân tán về cơ bản là một cách tiếp cận bi quan, trong khi BDR ủng hộ một cách tiếp cận lạc quan: tránh xung đột nếu có thể, nhưng cho phép một số loại xung đột xảy ra và giải quyết chúng khi chúng phát sinh.

```
_Ghi chú nâng cấp_

Tất cả các giao diện SQL hiển thị đều nằm trong lược đồ bdr. Tất cả các giao diện không được dùng trước đây trong lược đồ bdr_conflicts hoặc bdr_crdt đã bị xóa và sẽ không hoạt động trên 3,7+ nút hoặc trong các nhóm chứa ít nhất một nút 3,7+. Vui lòng sử dụng những cái trong lược đồ bdr đã có trong tất cả các phiên bản BDR.
```

**Làm thế nào xung đột xảy ra**

Xung đột giữa các nút phát sinh do chuỗi sự kiện không thể xảy ra nếu tất cả các giao dịch liên quan xảy ra đồng thời trên cùng một nút. Bởi vì các nút chỉ trao đổi các thay đổi sau khi giao dịch cam kết, mỗi giao dịch có giá trị riêng trên nút mà nó đã cam kết, nhưng sẽ không hợp lệ nếu được áp dụng trên một nút khác hoạt động xung đột khác cùng một lúc.

Vì bản sao BDR về cơ bản phát lại giao dịch trên các nút khác, hoạt động phát lại có thể thất bại nếu có xung đột giữa giao dịch đang được áp dụng và giao dịch đã được cam kết trên nút nhận.

Lý do mà hầu hết các xung đột không thể xảy ra khi tất cả các giao dịch chạy trên một nút duy nhất là PostgreSQL có các cơ chế giao tiếp giữa các giao dịch để ngăn chặn điều đó - chỉ mục DUY NHẤT, SEQUENCE, khóa hàng và quan hệ, theo dõi phụ thuộc SERIALIZABLE, v.v. Tất cả các cơ chế này đều cách giao tiếp giữa các giao dịch đang diễn ra để ngăn chặn các vấn đề đồng thời không mong muốn.

BDR không có trình quản lý giao dịch phân tán hoặc trình quản lý khóa. Đó là một phần lý do tại sao nó hoạt động tốt với độ trễ và phân vùng mạng. Do đó, các giao dịch trên các nút khác nhau thực hiện hoàn toàn độc lập với nhau, khi sử dụng bản sao mặc định, lười biếng. Ít độc lập hơn giữa các nút có thể tránh được xung đột hoàn toàn, đó là lý do tại sao BDR cũng cung cấp tính năng nhân rộng mong muốn khi điều này là quan trọng.

**Các loại xung đột**

_1. PRIMARY KEY or UNIQUE Conflicts_ - Chìa khóa CHÍNH hoặc Xung đột DUY NHẤT

Xung đột phổ biến nhất là xung đột hàng, trong đó hai hoạt động ảnh hưởng đến một hàng có cùng một khóa theo những cách mà chúng không thể thực hiện trên một nút. BDR có thể phát hiện hầu hết các lỗi đó và sẽ áp dụng trình giải quyết xung đột update_if_newer.

Xung đột hàng bao gồm:

 - INSERT vs INSERT
 - UPDATE vs UPDATE
 - UPDATE vs DELETE
 - INSERT vs UPDATE
 - INSERT vs DELETE
 - DELETE vs DELETE

Dạng xem bdr.node_conflict_resolvers cung cấp thông tin về cách giải quyết xung đột hiện được định cấu hình cho tất cả các loại xung đột đã biết.

_2. INSERT/INSERT Conflicts_ - CHÈN Xung đột

Xung đột phổ biến nhất, CHÈN / CHÈN, phát sinh khi CHÈN trên hai nút khác nhau tạo ra một bộ dữ liệu có cùng các giá trị KHÓA CHÍNH (hoặc nếu không có KHÓA CHÍNH, các giá trị giống nhau cho một ràng buộc DUY NHẤT).

BDR xử lý điều này bằng cách giữ lại bộ mã được chèn gần đây nhất trong số hai bộ, theo dấu thời gian của máy chủ gốc, trừ khi bị ghi đè bởi trình xử lý xung đột do người dùng xác định.

Xung đột này sẽ tạo ra kiểu xung đột insert_exists, kiểu xung đột này được giải quyết theo mặc định bằng cách chọn hàng mới hơn (dựa trên thời gian cam kết) và chỉ giữ lại hàng đó (update_if_newer Resolutionver). Các trình phân giải khác có thể được định cấu hình - xem [Giải quyết xung đột] để biết chi tiết.

Để giải quyết loại xung đột này, bạn cũng có thể sử dụng giải quyết xung đột cấp cột và trình kích hoạt xung đột do người dùng xác định.

Loại xung đột này có thể được loại bỏ một cách hiệu quả bằng cách sử dụng Chuỗi toàn cầu.

_3. INSERTs that Violate Multiple UNIQUE Constraints_ - CHÈN vi phạm Nhiều Ràng buộc DUY NHẤT

Xung đột INSERT / INSERT có thể vi phạm nhiều hơn một ràng buộc DUY NHẤT (trong đó một ràng buộc có thể là KHÓA CHÍNH). Nếu một hàng mới vi phạm nhiều hơn một ràng buộc DUY NHẤT và dẫn đến xung đột với nhiều hàng khác, thì việc áp dụng thay đổi sao chép sẽ tạo ra xung đột multiple_unique_conflicts.

Trong trường hợp có xung đột như vậy, một số hàng phải bị xóa để tiếp tục nhân rộng. Tùy thuộc vào cài đặt trình phân giải cho multiple_unique_conflicts, quá trình áp dụng sẽ thoát với lỗi, bỏ qua hàng đến hoặc tự động xóa một số hàng. Thao tác xóa tự động sẽ luôn cố gắng bảo toàn hàng bằng KHÓA CHÍNH CHÍNH XÁC và xóa các hàng khác.

```
Cảnh báo:

Trong trường hợp có nhiều hàng xung đột theo cách này, nếu kết quả của việc giải quyết xung đột là tiến hành thao tác chèn, một số dữ liệu sẽ luôn bị xóa!
```

Cũng có thể xác định một hành vi khác bằng cách sử dụng trình kích hoạt xung đột.

_4. UPDATE/UPDATE Conflicts_ - CẬP NHẬT Xung đột

Trường hợp hai CẬP NHẬT đồng thời trên các nút khác nhau thay đổi cùng một bộ mã (nhưng không phải là PRIMARY KEY của nó), xung đột UPDATE/UPDATE có thể xảy ra khi phát lại.

Những điều này có thể tạo ra các loại xung đột khác nhau dựa trên cấu hình và tình huống. Nếu bảng được định cấu hình bằng [Phát hiện Xung đột Phiên bản Hàng], thì hàng (khóa) ban đầu được so sánh với hàng cục bộ; nếu chúng khác nhau, xung đột update_differing sẽ được tạo ra. Khi sử dụng [Phát hiện xung đột nguồn gốc], nguồn gốc của hàng được kiểm tra (nguồn gốc là nút mà hàng cục bộ hiện tại đến từ đó); nếu điều đó đã thay đổi, xung đột update_origin_change sẽ được tạo ra. Trong tất cả các trường hợp khác, UPDATE thường được áp dụng mà không tạo ra xung đột.

Cả hai xung đột này đều được giải quyết theo cách giống như insert_exists, như được mô tả ở trên.

_5. UPDATE Conflicts on the PRIMARY KEY_ - CẬP NHẬT Xung đột trên KEY CHÍNH

BDR hiện không thể thực hiện giải quyết xung đột trong đó KEY CHÍNH được thay đổi bằng thao tác CẬP NHẬT. Có thể cập nhật khóa chính, nhưng bạn phải đảm bảo rằng không thể có xung đột với các giá trị hiện có.

Xung đột khi cập nhật khóa chính là [Xung đột phân kỳ] và cần sự can thiệp của người vận hành thủ công.

Có thể cập nhật PK trong PostgreSQL, nhưng có một số vấn đề trong cả PostgreSQL và BDR.

Hãy tạo một lược đồ ví dụ rất đơn giản để giải thích:

```
CREATE TABLE pktest (pk integer primary key, val integer);
INSERT INTO pktest VALUES (1,1);
```

Có thể cập nhật cột Khóa chính, vì vậy SQL này thành công:

```
UPDATE pktest SET pk=2 WHERE pk=1;
```

... nhưng nếu chúng ta có nhiều hàng trong bảng, ví dụ:

```
INSERT INTO pktest VALUES (3,3);
```

... thì một số CẬP NHẬT sẽ thành công:

```
UPDATE pktest SET pk=4 WHERE pk=3;

SELECT * FROM pktest;
 pk | val 
----+-----
  2 |   1
  4 |   3
(2 rows)
```

... nhưng các UPDATE khác sẽ không thành công với lỗi ràng buộc:

```
UPDATE pktest SET pk=4 WHERE pk=2;
ERROR:  duplicate key value violates unique constraint "pktest_pkey"
DETAIL:  Key (pk)=(4) already exists
```

Vì vậy, đối với các ứng dụng PostgreSQL CẬP NHẬT PK, hãy hết sức cẩn thận để tránh lỗi thời gian chạy, ngay cả khi không có BDR.

Với BDR, tình hình trở nên phức tạp hơn nếu cho phép CẬP NHẬT từ nhiều địa điểm cùng một lúc.

Việc thực thi hai thay đổi này đồng thời hoạt động:

```
node1: UPDATE pktest SET pk=pk+1 WHERE pk = 2;
node2: UPDATE pktest SET pk=pk+1 WHERE pk = 4;

SELECT * FROM pktest;
 pk | val 
----+-----
  3 |   1
  5 |   3
(2 rows)
```
 
... nhưng việc thực hiện đồng thời hai thay đổi tiếp theo này sẽ gây ra lỗi khác nhau, vì cả hai thay đổi đều được chấp nhận. Nhưng khi các thay đổi được áp dụng trên nút khác, điều này sẽ dẫn đến xung đột update_missing.

```
node1: UPDATE pktest SET pk=1 WHERE pk = 3;
node2: UPDATE pktest SET pk=2 WHERE pk = 3;
```

... để dữ liệu khác nhau trên mỗi nút:

```
node1:
SELECT * FROM pktest;
 pk | val 
----+-----
  1 |   1
  5 |   3
(2 rows)

node2:
SELECT * FROM pktest;
 pk | val 
----+-----
  2 |   1
  5 |   3
(2 rows)
```

Tình huống này có thể được xác định và giải quyết bằng cách sử dụng LiveCompare.

Xung đột đồng thời đưa ra các vấn đề. Việc thực hiện đồng thời hai thay đổi này không dễ giải quyết:

```
node1: UPDATE pktest SET pk=6, val=8 WHERE pk = 5;
node2: UPDATE pktest SET pk=6, val=9 WHERE pk = 5;
```
Cả hai thay đổi đều được áp dụng cục bộ, gây ra sự khác biệt giữa các nút. Nhưng sau đó áp dụng cho mục tiêu không thành công trên cả hai nút với lỗi vi phạm giá trị khóa trùng lặp LỖI, khiến quá trình sao chép tạm dừng và hiện yêu cầu giải pháp thủ công.

Lỗi vi phạm khóa trùng lặp này hiện có thể tránh được và quá trình sao chép sẽ không bị hỏng, nếu bạn đặt xung đột update_pkey_exists bỏ qua, cập nhật hoặc update_if_newer. Điều này vẫn có thể dẫn đến phân kỳ tùy thuộc vào bản chất của bản cập nhật.

Bạn có thể tránh phân kỳ trong các trường hợp như mô tả ở trên khi cùng một khóa cũ được cập nhật đồng thời bởi cùng một khóa mới bằng cách đặt update_pkey_exists thành update_if_newer. Tuy nhiên, trong một số tình huống nhất định, sự phân kỳ sẽ xảy ra ngay cả với update_if_newer, cụ thể là khi 2 hàng khác nhau được cập nhật đồng thời vào cùng một khóa chính mới.

Do đó, chúng tôi khuyên bạn không nên cho phép CẬP NHẬT PK trong các ứng dụng của mình, đặc biệt là với BDR. Nếu có một số phần trong ứng dụng của bạn thay đổi Khóa chính, thì để tránh những thay đổi đồng thời, hãy thực hiện những thay đổi đó bằng cách sử dụng bản sao Eager.

```
Cảnh báo

Trong trường hợp giải quyết xung đột của update_pkey_exists xung đột dẫn đến cập nhật, một trong các hàng sẽ luôn bị xóa!
```

_6. UPDATEs that Violate Multiple UNIQUE Constraints_ - CẬP NHẬT Vi phạm Nhiều Ràng buộc DUY NHẤT

Giống như [CHÈN vi phạm nhiều ràng buộc DUY NHẤT], trong đó CẬP NHẬT đến vi phạm nhiều hơn một chỉ mục DUY NHẤT (và / hoặc KHÓA CHÍNH), BDR sẽ làm tăng xung đột multiple_unique_conflicts.

BDR hỗ trợ các ràng buộc duy nhất được hoãn lại. Nếu một giao dịch có thể cam kết trên nguồn thì nó sẽ áp dụng rõ ràng trên đích, trừ khi thấy xung đột. Tuy nhiên, Khóa chính hoãn lại không thể được sử dụng làm NHẬN DIỆN REPLICA, do đó, các trường hợp sử dụng đã bị giới hạn bởi điều đó và cảnh báo ở trên về việc sử dụng nhiều ràng buộc duy nhất.

_7. UPDATE/DELETE Conflicts_ - CẬP NHẬT / XÓA xung đột

Có thể cho một nút CẬP NHẬT một hàng mà nút khác đồng thời XÓA. Trong trường hợp này, xung đột CẬP NHẬT / XÓA có thể xảy ra khi phát lại.

Nếu hàng DELETEd vẫn có thể phát hiện được (hàng đã xóa không bị VACUUM xóa), xung đột update_recently_deleted sẽ được tạo ra. Theo mặc định, CẬP NHẬT sẽ bị bỏ qua, nhưng giải pháp cho việc này có thể được định cấu hình; xem [Giải quyết xung đột] để biết chi tiết.

Hàng đã xóa có thể được dọn dẹp khỏi cơ sở dữ liệu vào thời điểm nhận được CẬP NHẬT trong trường hợp nút cục bộ bị chậm lại trong quá trình sao chép. Trong trường hợp này, BDR không thể phân biệt giữa xung đột UPDATE / DELETE và [INSERT / UPDATE xung đột] và sẽ chỉ tạo ra xung đột update_missing.

Một loại khác của DELETE và UPDATE xung đột là thao tác DELETE đến sau hàng được UPDATEd cục bộ. Trong tình huống này, kết quả phụ thuộc vào loại phát hiện xung đột được sử dụng. Khi sử dụng mặc định, [Phát hiện xung đột nguồn gốc], không có xung đột nào được phát hiện, dẫn đến việc XÓA được áp dụng và hàng bị xóa. Nếu bạn bật [Phát hiện xung đột phiên bản hàng], xung đột delete_recently_updated sẽ được tạo ra. Giải pháp mặc định cho loại xung đột này là áp dụng DELETE và xóa hàng, nhưng điều này có thể được định cấu hình hoặc xử lý thông qua trình kích hoạt xung đột.

_8. INSERT/UPDATE Conflicts_ - CHÈN / CẬP NHẬT Xung đột

Khi sử dụng chế độ hoạt động không đồng bộ mặc định, một nút có thể nhận được CẬP NHẬT của một hàng trước khi nhận được CHÈN CHÈN ban đầu. Điều này chỉ có thể xảy ra với 3 nút trở lên đang hoạt động (xem [Xung đột với 3 nút trở lên] bên dưới).

Khi điều này xảy ra, xung đột update_missing được tạo ra. Trình giải quyết xung đột mặc định là insert_or_skip, mặc dù insert_or_error hoặc bỏ qua có thể được sử dụng để thay thế. Các trình phân giải thực hiện insert-or-action trước tiên sẽ cố gắng CHÈN một hàng mới dựa trên dữ liệu từ CẬP NHẬT khi có thể (khi toàn bộ hàng đã được nhận). Để có thể tạo lại hàng, bảng cần phải có ĐẦY ĐỦ IDENTITY REPLICA hoặc hàng không được chứa bất kỳ dữ liệu TOASTed nào.

Xem [Chi tiết hỗ trợ TOAST] để biết thêm thông tin về dữ liệu TOASTed.

_9. INSERT/DELETE Conflicts_ - CHÈN / XÓA xung đột

Tương tự như xung đột CHÈN / CẬP NHẬT, nút cũng có thể nhận được thao tác XÓA trên một hàng mà nó chưa nhận được CHÈN. Điều này lại chỉ có thể thực hiện được với 3 nút trở lên được thiết lập (xem [Xung đột với 3 nút trở lên] bên dưới).

BDR hiện không thể phát hiện loại xung đột này: hoạt động INSERT sẽ không tạo ra bất kỳ loại xung đột nào và INSERT sẽ được áp dụng.

Thao tác DELETE sẽ luôn tạo ra xung đột delete_missing, xung đột này được giải quyết theo mặc định bằng cách bỏ qua thao tác.

_10. DELETE/DELETE Conflicts_ - XÓA xung đột

Xung đột DELETE / DELETE phát sinh khi hai nút khác nhau đồng thời xóa cùng một bộ dữ liệu.

Điều này sẽ luôn tạo ra xung đột delete_missing, xung đột này được giải quyết theo mặc định bằng cách bỏ qua thao tác.

Xung đột này là vô hại vì cả hai DELETE đều có tác dụng như nhau, vì vậy một trong số chúng có thể được bỏ qua một cách an toàn.
