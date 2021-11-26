### DDL Replication - Nhân bản DDL

DDL là viết tắt của "Data Definition Language": tập hợp con của ngôn ngữ SQL tạo, thay đổi và loại bỏ các đối tượng cơ sở dữ liệu.

Để hoạt động thuận tiện và đúng đắn, BDR sao chép hầu hết các hành động DDL, với những ngoại lệ sau:

 - Mối quan hệ tạm thời hoặc chưa được ghi lại
 - Một số câu lệnh DDL chạy dài nhất định (xem danh sách bên dưới)
 - Khóa lệnh (LOCK)
 - Lệnh bảo trì bảng (VACUUM, ANALYZE, CLUSTER, REINDEX)
 - Các hoạt động của autovacuum
 - Lệnh hoạt động (CHECKPOINT, ALTER SYSTEM)
 - Các hành động liên quan đến Cơ sở dữ liệu hoặc Không gian bảng

Tự động sao chép DDL giúp dễ dàng thực hiện các thay đổi DDL nhất định mà không cần phải phân phối thủ công thay đổi DDL cho tất cả các nút và đảm bảo rằng chúng nhất quán.

Trong bộ sao chép mặc định, DDL được sao chép cho tất cả các nút theo mặc định. Để sao chép DDL, một bộ lọc sao chép DDL phải được thêm vào bộ sao chép. Xem [Lọc sao chép DDL].

BDR khác biệt đáng kể so với PostgreSQL độc lập khi nói đến nhân bản DDL và coi nó giống nhau là vấn đề hoạt động phổ biến nhất với BDR.

Sự khác biệt chính so với sao chép bảng là sao chép DDL không sao chép kết quả của DDL, mà là chính câu lệnh. Điều này hoạt động rất tốt trong hầu hết các trường hợp, mặc dù đưa ra yêu cầu rằng DDL phải thực thi tương tự trên tất cả các nút. Một điểm tinh tế hơn là DDL phải là bất biến đối với tất cả các cài đặt tham số kiểu dữ liệu cụ thể, bao gồm bất kỳ kiểu dữ liệu nào được giới thiệu bởi các phần mở rộng (tức là không được tích hợp sẵn). Ví dụ: câu lệnh DDL phải thực thi chính xác trong mã hóa mặc định được sử dụng trên mỗi nút.

**DDL Replication Options** - Tùy chọn sao chép DDL

Tham số bdr.ddl_replication chỉ định hành vi sao chép.

bdr.ddl_replication = on là mặc định và sẽ sao chép DDL thành tập hợp sao mặc định, theo mặc định có nghĩa là tất cả các nút. Các bộ sao chép không mặc định không sao chép DDL, trừ khi chúng có bộ lọc DDL được xác định cho chúng.

Bạn cũng có thể sao chép DDL thành các bộ sao chép cụ thể bằng cách sử dụng hàm bdr.replicate_ddl_command (). Điều này có thể hữu ích nếu bạn muốn chạy các lệnh DDL khi một nút gặp sự cố hoặc nếu bạn muốn có chỉ mục hoặc phân vùng tồn tại trên một tập hợp con của các nút hoặc tập hợp đại diện, ví dụ: tất cả các nút tại site1.

```
SELECT bdr.replicate_ddl_command(
				'CREATE INDEX CONCURRENTLY ON foo (col7);',
				ARRAY['site1'],     -- the replication sets
                'on');              -- ddl_locking to apply
```

Có thể, nhưng không được khuyến nghị, bỏ qua sao chép DDL tự động và thực thi nó theo cách thủ công trên mỗi nút bằng cách sử dụng các tham số cấu hình bdr.ddl_replication.

```
SET bdr.ddl_replication = off;
```

Khi được đặt, nó sẽ làm cho BDR bỏ qua cả khóa toàn cục và sao chép các lệnh DDL đã thực thi, vì vậy bạn phải chạy DDL theo cách thủ công trên tất cả các nút.

Tham số bdr.ddl_replication chỉ có thể được đặt bởi bdr_superuser, superuser hoặc trong tệp cấu hình.

**Executing DDL on BDR Systems** - Thực thi DDL trên Hệ thống BDR

Nhóm BDR không giống như một máy chủ PostgreSQL độc lập. Nó dựa trên sao chép đa tổng thể không đồng bộ mà không có khóa trung tâm và không có bộ điều phối giao dịch. Điều này có ý nghĩa quan trọng khi thực thi DDL.

DDL thực thi song song sẽ tiếp tục làm như vậy với BDR. Việc thực thi DDL sẽ tôn trọng các tham số ảnh hưởng đến hoạt động song song trên mỗi nút khi nó thực thi, vì vậy có thể nhận thấy sự khác biệt trong cài đặt giữa các nút.

Việc thực thi DDL xung đột cần phải được ngăn chặn, nếu không, quá trình sao chép DDL sẽ kết thúc gây ra lỗi và quá trình sao chép sẽ dừng lại.

BDR cung cấp 3 cấp độ bảo vệ chống lại những vấn đề đó:

ddl_locking = 'dml' là tùy chọn tốt nhất cho các hoạt động, có thể sử dụng được khi bạn thực thi DDL chỉ từ một nút tại một thời điểm. Đây không phải là mặc định, nhưng bạn nên sử dụng cài đặt này nếu bạn có thể kiểm soát DDL được thực thi từ đâu, để đảm bảo rằng không có xung đột giữa các nút. Các xung đột nội bộ đã được PostgreSQL xử lý.

ddl_locking = on là tùy chọn nghiêm ngặt nhất và là tốt nhất khi DDL có thể được thực thi đồng thời từ bất kỳ nút nào và bạn muốn đảm bảo tính đúng đắn.

ddl_locking = off là tùy chọn ít nghiêm ngặt nhất và nguy hiểm khi sử dụng chung. Tùy chọn này bỏ qua các khóa hoàn toàn và do đó tránh mọi chi phí hiệu suất, làm cho nó trở thành một tùy chọn hữu ích khi tạo một lược đồ cơ sở dữ liệu mới và trống.

Các tùy chọn này chỉ có thể được đặt bởi bdr_superuser, superuser hoặc trong tệp cấu hình.

Khi sử dụng lệnh bdr.replicate_ddl_command, có thể đặt tham số này trực tiếp thông qua đối số thứ ba, chỉ sử dụng cài đặt bdr.ddl_locking được chỉ định cho các lệnh DDL được truyền cho hàm đó.

**DDL Locking Details** - Chi tiết khóa DDL

Có hai loại khóa được sử dụng để thực thi tính đúng đắn của DDL được sao chép với BDR.

Loại đầu tiên được gọi là Global DDL Lock và chỉ được sử dụng khi ddl_locking = on. Khóa DDL toàn cầu ngăn không cho bất kỳ DDL nào khác thực thi trên cụm trong khi mỗi câu lệnh DDL chạy. Điều này đảm bảo tính đúng đắn đầy đủ trong trường hợp chung, nhưng rõ ràng là quá nghiêm ngặt đối với nhiều trường hợp đơn giản. BDR có được một khóa toàn cầu đối với các hoạt động DDL lần đầu tiên trong một giao dịch mà các thay đổi giản đồ được thực hiện. Điều này có hiệu quả tuần tự các giao dịch thực thi DDL trong cụm. Nói cách khác, trong khi DDL đang chạy, không có kết nối nào khác trên bất kỳ nút nào có thể chạy lệnh DDL khác, ngay cả khi nó ảnh hưởng đến (các) bảng khác nhau.

Để có được khóa các hoạt động DDL, nút BDR đang thực thi DDL liên hệ với các nút khác trong nhóm BDR và ​​yêu cầu họ cấp cho nó quyền độc quyền để thực thi DDL. Yêu cầu khóa được gửi qua luồng sao chép thông thường và các nút cũng phản hồi qua luồng sao chép. Vì vậy, điều quan trọng là các nút (hoặc ít nhất là phần lớn các nút) phải chạy mà không có nhiều độ trễ sao chép. Nếu không, có thể mất một thời gian rất dài để nút có được khóa DDL. Khi đa số các nút đồng ý, việc thực thi DDL sẽ được thực hiện.

Thứ tự khóa DDL được quyết định bằng cách sử dụng giao thức Raft. Các câu lệnh DDL được thực thi trên một nút sẽ được thực hiện theo cùng một trình tự trên tất cả các nút khác.

Để đảm bảo rằng nút đang chạy DDL đã nhìn thấy ảnh hưởng của tất cả các DDL trước đó chạy trong cụm, nó sẽ đợi cho đến khi bắt kịp với nút đã chạy DDL trước đó. Nếu nút đang chạy DDL hiện tại bị tụt hậu trong quá trình sao chép so với nút đã chạy DDL trước đó, thì sẽ mất rất nhiều thời gian để có được khóa. Do đó, tốt hơn là chạy các DDL từ một nút duy nhất hoặc các nút gần như bắt kịp với các thay đổi sao chép bắt nguồn từ các nút khác.

Loại thứ hai được gọi là Khóa DML quan hệ. Loại khóa này được sử dụng khi ddl_locking = on hoặc ddl_locking = dml và câu lệnh DDL có thể khiến các câu lệnh DML trong chuyến bay bị lỗi, chẳng hạn như khi chúng tôi thêm hoặc sửa đổi một ràng buộc, chẳng hạn như ràng buộc duy nhất, kiểm tra ràng buộc hoặc KHÔNG ĐỦ hạn chế. Khóa DML quan hệ chỉ ảnh hưởng đến một quan hệ tại một thời điểm. Các khóa DML quan hệ đảm bảo rằng không có DDL nào thực thi trong khi có những thay đổi trong hàng đợi có thể gây ra lỗi sao chép tạm dừng.

Để có được khóa DML chung trên một bảng, nút BDR đang thực thi DDL liên hệ với tất cả các nút khác trong nhóm BDR, yêu cầu chúng khóa bảng chống lại việc ghi và chúng tôi đợi trong khi tất cả các thay đổi đang chờ xử lý đối với bảng đó được xử lý. Khi tất cả các nút được bắt kịp hoàn toàn, người khởi tạo khóa DML có thể tự do thực hiện các thay đổi lược đồ đối với bảng và sao chép chúng sang các nút khác.

Lưu ý rằng khóa DML toàn cầu giữ một KHÓA ĐỘC QUYỀN trên bảng trên mỗi nút, do đó sẽ chặn DML, các DDL, VACUUM khác và các lệnh chỉ mục chống lại bảng đó trong khi nó chạy. Điều này đúng ngay cả khi khóa DML toàn cầu được giữ cho một lệnh thường không có KHÓA ĐỘC QUYỀN hoặc cao hơn.

Việc chờ các hoạt động DML đang chờ xử lý cạn kiệt có thể mất nhiều thời gian hoặc lâu hơn nếu quá trình sao chép hiện đang bị tụt hậu. Điều này có nghĩa là các thay đổi lược đồ ảnh hưởng đến việc biểu diễn hàng và các ràng buộc, không giống như các thay đổi dữ liệu, chỉ có thể được thực hiện trong khi tất cả các nút được định cấu hình đều có thể truy cập được và theo kịp tốc độ ghi hiện tại một cách hợp lý. Nếu các lệnh DDL như vậy hoàn toàn phải được thực hiện trong khi một nút không hoạt động, thì nút xuống trước tiên phải được xóa khỏi cấu hình.

Nếu một câu lệnh DDL không được sao chép, sẽ không có khóa toàn cục nào được thu thập.

Hành vi khóa được chỉ định bởi tham số bdr.ddl_locking, như được giải thích trong Thực thi DDL trên hệ thống BDR:

 - ddl_locking = on có Global DDL Lock và nếu cần, sẽ Relation DML Lock.
 - ddl_locking = dml bỏ qua Global DDL Lock và, nếu cần, Relation DML Lock.
 - ddl_locking = off bỏ qua cả Khóa DDL Toàn cầu và Khóa DML Quan hệ.

Cũng lưu ý rằng một số chức năng BDR thực hiện các thay đổi DDL, vì vậy đối với các chức năng đó, hành vi khóa DDL sẽ áp dụng. Điều này sẽ được ghi chú trong tài liệu cho mỗi chức năng.

Do đó, ddl_locking = dml chỉ an toàn khi chúng ta có thể đảm bảo rằng không có DDL xung đột nào sẽ được thực thi từ các nút khác, bởi vì với cài đặt này, các câu lệnh chỉ yêu cầu Global DDL Lock sẽ không sử dụng khóa chung.

ddl_locking = off chỉ an toàn khi người dùng có thể đảm bảo rằng không có DDL xung đột và không có hoạt động DML xung đột nào trên các đối tượng cơ sở dữ liệu mà chúng tôi thực thi DDL. Nếu bạn tắt khóa và sau đó gặp khó khăn, bạn có thể mất các thay đổi trong chuyến bay đối với dữ liệu; bất kỳ vấn đề nào gây ra sẽ cần được giải quyết bởi nhóm ứng dụng người dùng.

Trong một số trường hợp, việc thực thi đồng thời DDL có thể được tuần tự hóa đúng cách. Nếu những lỗi tuần tự hóa này xảy ra, DDL có thể được thực thi lại.

Bản sao DDL không hoạt động trên các nút Logical Standby cho đến khi chúng được thăng cấp.

Lưu ý rằng một số chức năng quản lý BDR hoạt động giống như DDL, nghĩa là chúng sẽ cố gắng thực hiện các khóa toàn cục và các hành động của chúng sẽ được sao chép, nếu tính năng sao chép DDL đang hoạt động. Danh sách đầy đủ các hàm được sao chép được liệt kê trong [Các hàm BDR hoạt động giống như DDL].

DDL được thực thi trên các bảng tạm thời không bao giờ cần khóa toàn cục.

ALTER hoặc DROP của một đối tượng được bẻ khóa trong transactioon hiện tại không yêu cầu khóa DML toàn cầu.

Giám sát các khóa DDL toàn cầu và khóa DML toàn cầu được trình bày trong chương Giám sát.

**Minimizing the Impact of DDL** - Giảm thiểu tác động của DDL

Lời khuyên vận hành tốt cho bất kỳ cơ sở dữ liệu nào, những điểm này càng trở nên quan trọng hơn với BDR:

 - Để giảm thiểu tác động của DDL, các giao dịch thực hiện DDL phải ngắn, không nên kết hợp với nhiều thay đổi hàng và nên tránh kiểm tra lại khóa ngoại hoặc các ràng buộc khác trong thời gian dài.

 - Đối với ALTER TABLE, vui lòng sử dụng ADD CONSTRAINT KHÔNG HỢP LỆ, tiếp theo là một giao dịch khác với VALIDATE CONSTRAINT, thay vì chỉ sử dụng ADD CONSTRAINT. Lưu ý rằng VALIDATE CONSTRAINT sẽ đợi cho đến khi được phát lại trên tất cả các nút, điều này gây ra độ trễ đáng kể để nhận xác nhận.

 - Khi lập chỉ mục, hãy sử dụng tùy chọn CONCURRENTLY bất cứ khi nào có thể.

Một cách thay thế để thực thi DDL đang chạy dài là tắt tính năng sao chép DDL và sau đó thực thi câu lệnh DDL riêng biệt trên mỗi nút. Điều đó vẫn có thể được thực hiện bằng cách sử dụng một câu lệnh SQL duy nhất, như được hiển thị trong ví dụ bên dưới. Lưu ý rằng các quy tắc khóa toàn cầu vẫn được áp dụng, vì vậy hãy cẩn thận đừng tự khóa mình với kiểu sử dụng này, đây sẽ được coi là một giải pháp thay thế hơn so với cách sử dụng thông thường.

```
SELECT bdr.run_on_all_nodes($ddl$
        CREATE INDEX CONCURRENTLY index_a ON table_a(i);
$ddl$);
```

Chúng tôi khuyên bạn nên sử dụng kỹ thuật bdr.run_on_all_nodes () ở trên với CREATE INDEX CONCURRENTLY, lưu ý rằng phải tắt tính năng sao chép DDL trong cả phiên vì CREATE INDEX CONCURRENTLY là một lệnh đa giao dịch. CREATE INDEX nên được tránh trên các hệ thống sản xuất vì nó ngăn cản việc ghi trong khi thực thi. REINDEX được sao chép trong các phiên bản lên đến BDR3.6, nhưng không phải trong BDR3.7 trở lên. Nên tránh sử dụng REINDEX vì AccessExclusiveLocks mà nó nắm giữ.

Thay vào đó, nên sử dụng REINDEX CONCURRENTLY (hoặc reindexdb --concurrently), có sẵn trong PG12 + hoặc 2QPG11 +.

REINDEX hoặc REINDEX HIỆN TẠI trên một chỉ mục không hợp lệ sẽ không thực thi trên nút BDR. Các chỉ mục không hợp lệ phải được loại bỏ và tạo lại. Các chỉ mục không hợp lệ phải được loại bỏ bằng cách sử dụng DROP INDEX .. NẾU TỒN TẠI. DROP INDEX hoặc DROP INDEX NGAY LẬP TỨC mà không có mệnh đề IF EXISTS trên một chỉ mục không hợp lệ sẽ không thành công trên một nút BDR khi bật tính năng sao chép DDL.

Sao chép DDL có thể bị vô hiệu hóa khi sử dụng các tiện ích dòng lệnh như sau:

```
$ export PGOPTIONS="-c bdr.ddl_replication=off"
$ pg_restore --section=post-data
```

Nhiều câu lệnh DDL có thể được hưởng lợi từ việc tập hợp thành một giao dịch duy nhất thay vì được kích hoạt dưới dạng các câu lệnh riêng lẻ, do đó, khóa DDL chỉ phải được thực hiện một lần. Điều này có thể không mong muốn nếu các khóa cấp bàn gây trở ngại cho các hoạt động bình thường.

Nếu DDL đang giữ hệ thống quá lâu, bạn có thể hủy DDL trên nút gốc một cách an toàn và an toàn vì bạn sẽ hủy bất kỳ câu lệnh nào khác, ví dụ: với Control-C trong psql hoặc với pg_cancel_backend (). Bạn không thể hủy khóa DDL từ bất kỳ nút nào khác.

Có thể kiểm soát thời gian khóa toàn cầu bằng cài đặt thời gian chờ khóa toàn cầu (tùy chọn). Bdr.global_lock_timeout sẽ giới hạn thời gian chờ đợi để có được khóa toàn cầu có thể mất trước khi nó bị hủy; bdr.global_lock_statement_timeout giới hạn độ dài thời gian chạy của bất kỳ câu lệnh nào trong giao dịch có khóa toàn cục và bdr.global_lock_idle_timeout đặt thời gian không hoạt động tối đa cho phép (thời gian giữa các câu lệnh) cho một giao dịch giữ bất kỳ khóa toàn cục nào. Tất cả các thời gian chờ này có thể bị vô hiệu hóa bằng cách đặt giá trị của chúng thành 0.

Khi hoạt động DDL đã được cam kết trên nút gốc, nó không thể bị hủy bỏ hoặc bị hủy bỏ. Nhóm BDR phải đợi nó áp dụng thành công trên các nút khác đã xác nhận khóa toàn cầu và để chúng xác nhận phát lại. Đây là lý do tại sao điều quan trọng là giữ cho các giao dịch DDL ngắn và nhanh chóng.

**Handling DDL With Down Nodes** - Xử lý DDL với các nút xuống

Nếu nút khởi tạo khóa DDL chung bị hỏng sau khi nó đã có được khóa toàn cầu (DDL hoặc DML), thì khóa vẫn hoạt động. Các khóa toàn cầu sẽ không hết thời gian chờ, ngay cả khi thời gian chờ đã được đặt. Trong trường hợp nút hoạt động trở lại, nó sẽ tự động giải phóng tất cả các khóa chung mà nó nắm giữ.

Nếu nó ngừng hoạt động trong một khoảng thời gian dài (hoặc mãi mãi), hãy xóa nút khỏi nhóm BDR để giải phóng các khóa chung. Đây có thể là một lý do để thực thi DDL khẩn cấp bằng cách sử dụng lệnh SET làm bdr_superuser để cập nhật giá trị bdr.ddl_locking.

Nếu một trong các nút khác gặp sự cố sau khi nó đã xác nhận khóa toàn cục, nhưng trước khi lệnh nhận được nó đã được thực thi, thì việc thực thi lệnh đó yêu cầu khóa sẽ tiếp tục như khi nút đó đã khởi động.

Như đã đề cập trong phần trước, khóa DDL chung chỉ yêu cầu phần lớn các nút phản hồi và do đó, nó sẽ hoạt động nếu một phần của cụm gặp sự cố, miễn là phần lớn đang chạy và có thể truy cập được, trong khi khóa DML không được. có được trừ khi toàn bộ cụm có sẵn.

Nếu chúng ta có khóa DDL toàn cục hoặc DML toàn cục và một nút khác gặp sự cố, lệnh sẽ tiếp tục bình thường và khóa sẽ được giải phóng.

**Statement Specific DDL Replication Concerns** - Tuyên bố cụ thể về mối quan tâm nhân rộng DDL

Không phải tất cả các lệnh đều có thể được sao chép tự động. Các lệnh như vậy thường không được phép, trừ khi tắt tính năng sao chép DDL bằng cách tắt bdr.ddl_replication.

BDR ngăn một số câu lệnh DDL chạy khi nó hoạt động trên cơ sở dữ liệu. Điều này bảo vệ tính nhất quán của hệ thống bằng cách không cho phép các câu lệnh không thể được sao chép một cách chính xác hoặc bản sao chưa được hỗ trợ. Các tuyên bố được hỗ trợ với một số hạn chế được đề cập trong [Tuyên bố có hạn chế của DDL]; trong khi các lệnh hoàn toàn không được phép trong BDR được đề cập trong các câu lệnh DDL bị cấm.

Nếu một tuyên bố không được phép theo BDR, thường có thể tìm cách khác để làm điều tương tự. Ví dụ: bạn không thể thực hiện ALTER TABLE để thêm cột có giá trị mặc định dễ thay đổi, nhưng nói chung có thể diễn đạt lại thành một chuỗi các câu lệnh ALTER TABLE và UPDATE sẽ hoạt động.

Nói chung, các câu lệnh không được hỗ trợ sẽ không được thực thi, gây ra lỗi feature_not_supported (SQLSTATE 0A000).

Lưu ý rằng bất kỳ DDL nào tham chiếu hoặc dựa vào một đối tượng tạm thời không thể được sao chép bởi BDR và sẽ tạo ra một LỖI, nếu được thực thi với tính năng sao chép DDL được bật.

**BDR DDL Command Handling Matrix** - Ma trận xử lý lệnh BDR DDL

Bảng sau đây mô tả tiện ích hoặc lệnh DDL nào được phép, những lệnh nào được sao chép và loại khóa chung nào mà chúng thực hiện khi chúng được sao chép.

Đối với một số câu lệnh phức tạp hơn như ALTER TABLE, các câu lệnh này có thể khác nhau tùy thuộc vào (các) lệnh con được thực thi. Mỗi lệnh như vậy đều có giải thích chi tiết trong bảng sau.

![image](https://user-images.githubusercontent.com/69178270/143541710-2ecc124f-9f28-49de-91f2-d1de23b1ef4a.png)

![image](https://user-images.githubusercontent.com/69178270/143541810-02e7e9e9-e65d-40db-9271-d37e1f73efc0.png)

![image](https://user-images.githubusercontent.com/69178270/143541845-691a7d6e-fefb-463b-b9af-ce7f4cb5ed00.png)

![image](https://user-images.githubusercontent.com/69178270/143541913-40a85e44-867c-4281-9aba-c0422d60469f.png)

![image](https://user-images.githubusercontent.com/69178270/143541929-added897-f84f-4c30-8fba-11cf952368c4.png)

![image](https://user-images.githubusercontent.com/69178270/143541962-681e7815-5411-4cd9-ad48-bd01c6f80911.png)

![image](https://user-images.githubusercontent.com/69178270/143542002-5e4a1688-4d59-4022-8aeb-11ca6bfd2a22.png)

![image](https://user-images.githubusercontent.com/69178270/143542045-9f7a76ba-69e0-414a-8784-fb95e68a1bbf.png)

![image](https://user-images.githubusercontent.com/69178270/143542270-ccd5ba79-ae54-47a5-957f-ec8c1232711e.png)

![image](https://user-images.githubusercontent.com/69178270/143542314-55c48954-723c-49fb-894a-ee5dc8ffd926.png)

![image](https://user-images.githubusercontent.com/69178270/143542453-8be55276-a4df-4631-b50e-3e38060ef27d.png)

![image](https://user-images.githubusercontent.com/69178270/143542507-c6e7ef8b-179b-4097-8a08-6a776c93c875.png)

![image](https://user-images.githubusercontent.com/69178270/143544276-c44f9464-dd78-4495-9fe5-37ecf0e0cb26.png)

**ALTER SEQUENCE**

Nói chung ALTER SEQUENCE được hỗ trợ, nhưng khi sử dụng chuỗi toàn cục, một số tùy chọn không có tác dụng.

ALTER SEQUENCE ... RENAME không được hỗ trợ trên chuỗi galloc (chỉ). ALTER SEQUENCE ... SET SCHEMA không được hỗ trợ trên chuỗi galloc (chỉ).

**ALTER TABLE**

Nói chung, các lệnh ALTER TABLE được phép. Tuy nhiên, có một số lệnh phụ không được hỗ trợ.

_1. ALTER TABLE Các lệnh không được phép_

Một số biến thể của ALTER TABLE hiện không được phép trên nút BDR:

 - ADD COLUMN ... DEFAULT (biểu thức không thay đổi) - Điều này không được phép vì hiện tại nó sẽ dẫn đến dữ liệu khác nhau trên các nút khác nhau. Xem Thêm cột để biết giải pháp được đề xuất.
 - ADD CONSTRAINT ... EXCLUDE - Ràng buộc loại trừ hiện không được hỗ trợ. Các ràng buộc loại trừ không có nhiều ý nghĩa trong một hệ thống không đồng bộ và dẫn đến các thay đổi không thể thực hiện lại.
 - ALTER TABLE ... SET WITH [OUT] OIDS - Không được hỗ trợ vì những lý do tương tự như trong CREATE TABLE.
 - ALTER COLUMN ... SET STORAGE bên ngoài - Sẽ bị từ chối nếu cột là một trong các cột của danh tính bản sao cho bảng.
 - RENAME - không thể đổi tên bảng được phân vùng tự động.
 - SET SCHEMA - không thể đặt giản đồ của bảng được phân vùng tự động.
 - ALTER COLUMN ... TYPE - Việc thay đổi kiểu của cột không được hỗ trợ nếu lệnh khiến toàn bộ bảng được viết lại, điều này xảy ra khi thay đổi không phải là cưỡng chế nhị phân. 
Lưu ý rằng chỉ có thể cho phép các thay đổi cưỡng chế nhị phân theo một chiều. Ví dụ: thay đổi từ VARCHAR (128) thành VARCHAR (256) là cưỡng chế nhị phân và do đó được phép, trong khi thay đổi VARCHAR (256) thành VARCHAR (128) không phải là cưỡng chế nhị phân và do đó thường không được phép. Đối với ALTER COLUMN ... TYPE, nó có thể được cho phép nếu cột được tự động chuyển sang kiểu mới (nó không chứa mệnh đề USING). Xem ví dụ bên dưới. Việc ghi lại bảng sẽ giữ một AccessExclusiveLock trong thời gian dài trên các bảng lớn hơn, vì vậy các lệnh như vậy có thể không khả thi trên các cơ sở dữ liệu có sẵn trong mọi trường hợp. Xem Thay đổi Loại của Cột để biết các giải pháp thay thế được đề xuất.
 - ALTER TABLE ... ADD FOREIGN KEY - Không được hỗ trợ nếu người dùng hiện tại không có quyền đọc bảng được tham chiếu hoặc nếu bảng được tham chiếu đã bật hạn chế RLS mà người dùng hiện tại không thể bỏ qua.

Ví dụ sau không thành công vì nó cố gắng thêm một giá trị không đổi của loại dấu thời gian vào một cột loại timestamptz. Việc truyền giữa dấu thời gian và dấu thời gian phụ thuộc vào múi giờ của phiên và do đó không phải là bất biến.

```
ALTER TABLE foo
  ADD expiry_date timestamptz DEFAULT timestamp '2100-01-01 00:00:00' NOT NULL;
```

Bắt đầu BDR 3.7.4, có thể thêm một số loại ràng buộc nhất định, chẳng hạn như ràng buộc CHECK và NGOẠI KHÓA, có thể được thêm vào mà không cần sử dụng khóa DML. Nhưng điều này đòi hỏi quy trình 2 bước đầu tiên tạo ràng buộc NOT VALID và sau đó xác thực ràng buộc trong một giao dịch riêng biệt thông qua lệnh ALTER TABLE ... VALIDATE CONSTRAINT. Xem Thêm một CONSTRAINT để biết thêm chi tiết.

_2. Khóa bảng ALTER_

Các biến thể sau của ALTER TABLE sẽ chỉ sử dụng khóa DDL chứ không phải khóa DML:

 - ALTER TABLE ... ADD COLUMN ... (immutable) DEFAULT
 - ALTER TABLE ... ALTER COLUMN ... SET DEFAULT expression
 - ALTER TABLE ... ALTER COLUMN ... DROP DEFAULT
 - ALTER TABLE ... ALTER COLUMN ... TYPE nếu nó không yêu cầu viết lại (hiện chỉ có trên EDB Postgres Extended và EDB Postgres Advanced)
 - ALTER TABLE ... ALTER COLUMN ... SET STATISTICS
 - ALTER TABLE ... VALIDATE CONSTRAINT
 - ALTER TABLE ... ATTACH PARTITION
 - ALTER TABLE ... DETACH PARTITION
 - ALTER TABLE ... ENABLE TRIGGER (ENABLE REPLICA TRIGGER sẽ vẫn sử dụng khóa DML)
 - ALTER TABLE ... CLUSTER ON
 - ALTER TABLE ... SET WITHOUT CLUSTER
 - ALTER TABLE ... SET ( storage_parameter = value [, ... ] )
 - ALTER TABLE ... RESET ( storage_parameter = [, ... ] )
 - ALTER TABLE ... OWNER TO

Tất cả các biến thể khác của ALTER TABLE có một khóa DML trên bảng đang được sửa đổi. Một số biến thể của ALTER TABLE có các hạn chế, được lưu ý bên dưới.

_3. Ví dụ về BẢNG ALTER_

Ví dụ tiếp theo này hoạt động vì sự thay đổi kiểu là cưỡng chế nhị phân và do đó không gây ra việc ghi lại bảng, vì vậy nó sẽ thực thi như một thay đổi chỉ dành cho danh mục.

```
CREATE TABLE foo (id BIGINT PRIMARY KEY, description VARCHAR(20));
ALTER TABLE foo ALTER COLUMN description TYPE VARCHAR(128);
```

Tuy nhiên, không thể thực hiện thay đổi này để đảo ngược lệnh trên vì thay đổi từ VARCHAR (128) thành VARCHAR (20) không phải là cưỡng chế nhị phân.

```
ALTER TABLE foo ALTER COLUMN description TYPE VARCHAR(20);
```

Xem sau để biết các giải pháp thay thế được đề xuất.

Sẽ rất hữu ích khi cung cấp ngữ cảnh cho các loại ALTER TABLE ... ALTER COLUMN TYPE (ATCT) hoạt động có thể thực hiện được nói chung và trong môi trường không sao chép.

Một số thao tác ATCT chỉ cập nhật siêu dữ liệu của loại cột bên dưới và không yêu cầu ghi lại dữ liệu bảng bên dưới. Đây thường là trường hợp khi loại cột hiện có và loại mục tiêu là cưỡng chế nhị phân. Ví dụ:

```
CREATE TABLE sample (col1 BIGINT PRIMARY KEY, col2 VARCHAR(128), col3 INT);
ALTER TABLE sample ALTER COLUMN col2 TYPE VARCHAR(256);
```

Cũng có thể thay đổi kiểu cột thành kiểu dữ liệu VARCHAR hoặc TEXT vì tính cưỡng chế nhị phân. Một lần nữa, đây chỉ là một bản cập nhật siêu dữ liệu của loại cột bên dưới.

```
ALTER TABLE sample ALTER COLUMN col2 TYPE VARCHAR;
ALTER TABLE sample ALTER COLUMN col2 TYPE TEXT;
```

Tuy nhiên, nếu bạn muốn giảm kích thước của col2, thì điều đó sẽ dẫn đến việc ghi lại dữ liệu bảng bên dưới. Viết lại một bảng thường bị hạn chế.

```
ALTER TABLE sample ALTER COLUMN col2 TYPE VARCHAR(64);
ERROR:  ALTER TABLE ... ALTER COLUMN TYPE that rewrites table data may not affect replicated tables on a BDR node
```

Để đưa ra một ví dụ với các kiểu không phải văn bản, hãy xem xét col3 ở trên với kiểu INTEGER. Thao tác ATCT cố gắng chuyển đổi thành SMALLINT hoặc BIGINT sẽ không thành công theo cách tương tự như trên.

```
ALTER TABLE sample ALTER COLUMN col3 TYPE bigint;
ERROR:  ALTER TABLE ... ALTER COLUMN TYPE that rewrites table data may not affect replicated tables on a BDR node
```

Trong cả hai trường hợp không thành công ở trên, tồn tại một phép gán tự động được truyền từ kiểu hiện tại sang kiểu đích. Tuy nhiên, không có cưỡng chế nhị phân, điều này dẫn đến việc ghi lại dữ liệu bảng bên dưới.

Trong những trường hợp như vậy, trong môi trường DBA được kiểm soát, có thể thay đổi loại cột thành loại có thể truyền tự động, bằng cách áp dụng nâng cấp luân phiên cho loại cột này trong môi trường không sao chép trên tất cả các nút, từng nút một . Nếu DDL không được sao chép và thay đổi loại cột thành loại có thể truyền tự động như trên, thì có thể cho phép ghi lại cục bộ trên nút thực hiện thay đổi, cùng với hoạt động đồng thời trên các nút khác trên cùng bảng này. Thao tác ATCT không sao chép này sau đó có thể được lặp lại trên tất cả các nút lần lượt để mang lại sự thay đổi mong muốn của loại cột trên toàn bộ cụm BDR. Lưu ý rằng vì điều này liên quan đến việc viết lại, hoạt động sẽ vẫn thực hiện khóa DML trong một khoảng thời gian ngắn và do đó yêu cầu toàn bộ cụm phải khả dụng. Với các chi tiết cụ thể ở trên, quá trình nâng cấp luân phiên của hoạt động thay thế không tái tạo có thể được thực hiện như sau:

```
-- foreach node in BDR cluster do:
SET bdr.ddl_replication TO FALSE;
ALTER TABLE sample ALTER COLUMN col2 TYPE VARCHAR(64);
ALTER TABLE sample ALTER COLUMN col3 TYPE BIGINT;
RESET bdr.ddl_replication;
-- done
```

Do các phôi gán tự động có sẵn cho nhiều loại dữ liệu, hoạt động ATCT cục bộ không sao chép này hỗ trợ nhiều loại chuyển đổi. Cũng lưu ý rằng các hoạt động ATCT sử dụng mệnh đề USING có khả năng bị lỗi vì thiếu phôi gán tự động. Một số chuyển đổi phổ biến với phôi chuyển nhượng tự động được đề cập bên dưới.

```
-- foreach node in BDR cluster do:
SET bdr.ddl_replication TO FALSE;
ATCT operations to-from {INTEGER, SMALLINT, BIGINT}
ATCT operations to-from {CHAR(n), VARCHAR(n), VARCHAR, TEXT}
ATCT operations from numeric types to text types
RESET bdr.ddl_replication;
-- done
```

Trên đây không phải là danh sách đầy đủ các hoạt động ATCT có thể được phép trong môi trường không sao chép. Rõ ràng, không phải tất cả các hoạt động ATCT sẽ hoạt động. Các trường hợp không thực hiện được phép gán tự động sẽ không thành công ngay cả khi chúng ta tắt tính năng sao chép DDL. Vì vậy, trong khi chuyển đổi từ kiểu số sang kiểu văn bản hoạt động trong môi trường không sao chép, chuyển đổi ngược lại từ kiểu văn bản sang kiểu số sẽ không thành công.

```
SET bdr.ddl_replication TO FALSE;
-- conversion from BIGINT to TEXT works
ALTER TABLE sample ALTER COLUMN col3 TYPE TEXT;
-- conversion from TEXT back to BIGINT fails
ALTER TABLE sample ALTER COLUMN col3 TYPE BIGINT;
ERROR:  ALTER TABLE ... ALTER COLUMN TYPE which cannot be automatically cast to new type may not affect replicated tables on a BDR node
RESET bdr.ddl_replication;
```

Mặc dù các hoạt động ATCT trong môi trường không sao chép hỗ trợ nhiều kiểu chuyển đổi, điều quan trọng cần lưu ý là việc ghi lại vẫn có thể không thành công nếu dữ liệu bảng bên dưới chứa các giá trị không thể gán cho kiểu dữ liệu mới. Ví dụ: kiểu hiện tại cho một cột có thể là VARCHAR (256) và chúng tôi đã thử thao tác ATCT không sao chép để chuyển đổi nó thành VARCHAR (128). Nếu có bất kỳ dữ liệu hiện có nào trong bảng lớn hơn 128 byte, thì thao tác ghi lại cục bộ sẽ không thành công.

```
INSERT INTO sample VALUES (1, repeat('a', 200), 10);
SET bdr.ddl_replication TO FALSE;
ALTER TABLE sample ALTER COLUMN col2 TYPE VARCHAR(128);
INFO:  in rewrite
ERROR:  value too long for type character varying(128)
```

Nếu dữ liệu bảng bên dưới đáp ứng các đặc điểm của kiểu mới, thì việc ghi lại sẽ thành công. Tuy nhiên, có khả năng sao chép sẽ không thành công nếu các nút khác (chưa thực hiện nâng cấp kiểu dữ liệu cuộn không sao chép) giới thiệu dữ liệu mới rộng hơn 128 byte đồng thời cho hoạt động ATCT cục bộ này. Điều này sẽ khiến quá trình nhân rộng trong cụm bị tạm dừng. Vì vậy, điều quan trọng là phải nhận thức được các giới hạn và đặc điểm của kiểu dữ liệu ở cấp độ cơ sở dữ liệu và ứng dụng trong khi thực hiện các hoạt động nâng cấp kiểu dữ liệu cuộn không sao chép này. Khuyến nghị và khuyến khích thực hiện và kiểm tra các hoạt động ATCT như vậy trong môi trường DBA được kiểm soát và nhận thức đầy đủ. Chúng ta cần lưu ý rằng các hoạt động ATCT này là không đối xứng và việc sao lưu một số thay đổi nhất định không thành công có thể dẫn đến việc ghi lại bảng trong thời gian dài.

Cũng lưu ý rằng hoạt động ALTER có thể truyền ngầm ở trên không thể được thực hiện trong các khối giao dịch.

