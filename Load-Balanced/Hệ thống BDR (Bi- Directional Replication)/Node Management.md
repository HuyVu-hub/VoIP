### Node Management - Quản lý Node

Mỗi cơ sở dữ liệu là thành viên của một nhóm BDR phải được đại diện bởi nút riêng của nó. Một nút là một định danh duy nhất của cơ sở dữ liệu như vậy trong nhóm BDR.

Hiện tại, mỗi nút chỉ có thể là thành viên của một nhóm nút; điều này có thể được mở rộng trong các bản phát hành sau. Mỗi nút có thể đăng ký một hoặc nhiều Bộ sao chép để cung cấp quyền kiểm soát chi tiết đối với việc sao chép.

Một Nhóm BDR cũng có thể chứa không hoặc nhiều nhóm con, cho phép tạo ra nhiều loại kiến trúc khác nhau.

**Creating and Joining a BDR Group** - Tạo và tham gia nhóm BDR

Đối với BDR, mọi nút phải có kết nối với mọi nút khác. Để dễ dàng cấu hình, khi một nút mới tham gia, nó sẽ tự động cấu hình tất cả các nút hiện có để kết nối với nó. Vì lý do này, mọi nút, bao gồm cả nút BDR đầu tiên được tạo, phải biết chuỗi kết nối PostgreSQL (đôi khi được gọi là DSN, cho "tên nguồn dữ liệu") mà các nút khác có thể sử dụng để kết nối với nó. Cả hai định dạng của chuỗi kết nối đều được hỗ trợ. Vì vậy, bạn có thể sử dụng định dạng khóa-giá trị, như host = myhost port = 5432 dbname = mydb hoặc định dạng URI: postgresql: // myhost: 5432 / mydb.

Hàm SQL bdr.create_node_group () được sử dụng để tạo nhóm BDR từ nút cục bộ. Làm như vậy sẽ kích hoạt BDR trên nút đó và cho phép các nút khác tham gia nhóm BDR (chỉ bao gồm một nút tại điểm đó). Tại thời điểm tạo, bạn phải chỉ định chuỗi kết nối mà các nút khác sẽ sử dụng để kết nối với nút này.

Sau khi nhóm nút được tạo, mọi nút khác đều có thể tham gia nhóm BDR bằng cách sử dụng hàm bdr.join_node_group ().

Ngoài ra, sử dụng tiện ích dòng lệnh bdr_init_physical để tạo một nút mới, sử dụng pg_basebackup (hoặc một chế độ chờ vật lý) của một nút hiện có. Nếu sử dụng pg_basebackup, tiện ích bdr_init_physical có thể tùy chọn chỉ định sao lưu cơ sở của cơ sở dữ liệu đích, trái ngược với hành vi sao lưu toàn bộ cụm cơ sở dữ liệu trước đó. Điều này sẽ làm cho hoạt động này hoàn thành nhanh hơn và cũng cho phép nó sử dụng ít dung lượng hơn do loại trừ các cơ sở dữ liệu không mong muốn. Nếu chỉ cơ sở dữ liệu đích được chỉ định, thì các cơ sở dữ liệu bị loại trừ sẽ được dọn dẹp và loại bỏ trên nút mới.

Tiện ích bdr_init_physical thay thế chức năng của tiện ích bdr_init_copy từ BDR1 và BDR2. Nó tương đương với BDR3 của tiện ích pglogical_create_subscriber pglogical_create_subscriber.

```
Cảnh báo
Tại thời điểm này, chỉ có một nút nên tham gia vào nhóm nút BDR hoặc được tách khỏi nó. Nếu một nút mới đang được tham gia trong khi có một phép nối hoặc một phần hoạt động khác đang diễn ra, thì nút mới đôi khi sẽ không có dữ liệu nhất quán sau khi kết thúc kết thúc.
```

Khi một nút BDR mới được tham gia vào nhóm BDR hiện có hoặc một nút được đăng ký vào một ứng dụng ngang hàng ngược dòng, trước khi có thể bắt đầu sao chép, hệ thống phải sao chép dữ liệu hiện có từ (các) nút ngang hàng sang nút cục bộ. Bản sao này phải được phối hợp cẩn thận để dữ liệu cục bộ và từ xa bắt đầu giống hệt nhau; nó không đủ nếu chỉ sử dụng pg_dump. Phần mở rộng BDR cung cấp các phương tiện tích hợp để tạo bản sao ban đầu này.

Trong quá trình kết hợp, tiện ích mở rộng BDR sẽ đồng bộ hóa dữ liệu hiện có bằng cách sử dụng nút nguồn được cung cấp làm cơ sở và tạo tất cả thông tin siêu dữ liệu cần thiết để tự thiết lập cấu trúc liên kết lưới trong nhóm BDR. Nếu kết nối giữa nguồn và nút mới ngắt kết nối trong lần sao chép đầu tiên này, thì quá trình nối sẽ cần được khởi động lại từ đầu.

Nút đang tham gia cụm không được chứa bất kỳ lược đồ hoặc dữ liệu nào đã tồn tại trên cơ sở dữ liệu trong nhóm BDR. Chúng tôi khuyên bạn nên để trống cơ sở dữ liệu mới tham gia ngoại trừ phần mở rộng BDR và ​​pglogical. Đảm bảo rằng tất cả người dùng và vai trò cơ sở dữ liệu được yêu cầu đều được tạo. Tuy nhiên, có thể tùy chọn bỏ qua đồng bộ hóa lược đồ bằng cách sử dụng tham sốnchronize_ architecture của hàm bdr.join_node_group () trong trường hợp này, lược đồ phải tồn tại trên nút mới tham gia.

Chúng tôi khuyên bạn nên chọn nút nguồn có kết nối tốt nhất (tức là gần nhất) làm nút nguồn để tham gia, vì điều đó làm giảm thời gian cần thiết để kết thúc kết thúc.

Thủ tục tham gia được điều phối bằng cách sử dụng thuật toán đồng thuận Raft, thuật toán này yêu cầu hầu hết các nút hiện có phải trực tuyến và có thể truy cập được.

Thủ tục nối lôgic (sử dụng hàm bdr.join_node_group ()) thực hiện đồng bộ hóa dữ liệu khi thực hiện các hoạt động SAO CHÉP và sẽ sử dụng nhiều bộ ghi (áp dụng song song) nếu chúng được bật.

Nối nút có thể thực thi đồng thời với các phép nối nút khác trong phần lớn thời gian tham gia. Mỗi lần chỉ có một nút thông thường có thể ở một trong hai trạng thái PROMOTE hoặc PROMOTING, thường khá ngắn. Các nút chỉ dành cho người đăng ký là một ngoại lệ đối với quy tắc này và chúng cũng có thể đồng thời ở trạng thái PROMOTE và PROMOTING.

Lưu ý rằng quá trình kết hợp chỉ sử dụng một nút làm nguồn, vì vậy có thể được thực thi khi các nút ngừng hoạt động, nếu phần lớn các nút có sẵn. Điều này có thể gây ra sự phức tạp khi chạy phép nối lôgic: Trong phép nối lôgic, dấu thời gian cam kết của các hàng được sao chép từ nút nguồn sẽ được đặt thành dấu thời gian cam kết mới nhất trên nút nguồn. Các thay đổi đã cam kết trên các nút có dấu thời gian cam kết sớm hơn mức này (vì các nút không hoạt động hoặc có độ trễ đáng kể) có thể xung đột với các thay đổi từ các nút khác; trong trường hợp này, nút mới tham gia có thể được phân giải khác với các nút khác, gây ra sự phân kỳ. Do đó, chúng tôi khuyên bạn không nên chạy tham gia nút khi có độ trễ nhân rộng đáng kể giữa các nút; nhưng nếu điều này là cần thiết, hãy chạy LiveCompare trên nút mới tham gia để sửa bất kỳ phân kỳ dữ liệu nào sau khi tất cả các nút đều có sẵn và bắt kịp.

pg_dump có thể không thành công khi có hoạt động DDL đồng thời trên nút nguồn do lỗi tra cứu bộ đệm. Vì bdr.join_node_group () sử dụng pg_dump nội bộ, nó có thể bị lỗi nếu có hoạt động DDL đồng thời trên nút nguồn. Thử lại phép nối sẽ hoạt động trong trường hợp như vậy.

**Joining a Heterogeneous Cluster** - Tham gia một cụm không đồng nhất

Nút BDR 3.7 có thể tham gia một cụm BDR đang chạy 3.6.x ở một bản phát hành bảo trì tối thiểu cụ thể (ví dụ: 3.6.25) hoặc kết hợp các nút 3.6 và 3.7. Quy trình này hữu ích khi người dùng muốn nâng cấp không chỉ phiên bản chính BDR mà còn cả phiên bản chính PostgreSQL (hoặc 2ndQPostgres) cơ bản. Điều này có thể đạt được bằng cách tham gia một nút 3.7 chạy trên PostgreSQL (hoặc 2ndQPostgres) 12 hoặc 13 với một cụm BDR chạy 3.6.x trên PostgreSQL (hoặc 2ndQPostgres) 11. Tất nhiên, nút mới cũng có thể chạy trên cùng một PostgreSQL chính phát hành như tất cả các nút trong cụm hiện có.

BDR đảm bảo rằng bản sao hoạt động chính xác theo mọi hướng ngay cả khi một số nút đang chạy 3.6 trên một bản phát hành chính PostgreSQL và các nút khác đang chạy 3.7 trên một bản phát hành chính PostgreSQL khác. Nhưng người dùng nên nhanh chóng đưa cụm về trạng thái đồng nhất bằng cách chia các nút cũ hơn khi đã có đủ các nút mới tham gia vào cụm. Cần phải cẩn thận để không chạy bất kỳ DDL nào có thể không có sẵn trên các phiên bản cũ hơn và ngược lại.

Một nút tham gia với một bản phát hành PostgreSQL chính khác không thể sử dụng bản sao lưu vật lý được thực hiện qua bdr_init_physical và nút phải tham gia bằng phương pháp nối lôgic. Điều này là cần thiết vì các bản phát hành PostgreSQL chính không tương thích trên đĩa với nhau.

Lưu ý rằng khi một nút 3.7 tham gia vào cụm bằng cách sử dụng nút 3.6 làm nguồn, một số cấu hình nhất định như cấu hình giải quyết xung đột sẽ không được sao chép từ nút nguồn. Nút phải được định cấu hình sau khi nó đã tham gia vào cụm.

**Connection DSNs and SSL (TLS)** - Kết nối DSN và SSL (TLS)

The DSN of a node is simply a libpq connection string, since nodes connect using libpq. As such, it can contain any permitted libpq connection parameter, including those for SSL. Note that the DSN must work as the connection string from the client connecting to the node in which it is specified. An example of such a set of parameters using a client certificate is shown here:

```
sslmode=verify-full sslcert=bdr_client.crt sslkey=bdr_client.key
sslrootcert=root.crt
```

Với thiết lập này, các tệp bdr_client.crt, bdr_client.key và root.crt phải có trong thư mục dữ liệu trên mỗi nút, với các quyền thích hợp. Đối với chế độ xác minh đầy đủ, chứng chỉ SSL của máy chủ sẽ được kiểm tra để đảm bảo rằng nó được ký trực tiếp hoặc gián tiếp với Tổ chức phát hành chứng chỉ root.crt và tên máy chủ hoặc địa chỉ được sử dụng trong kết nối khớp với nội dung của chứng chỉ. Trong trường hợp tên, tên này có thể khớp với Tên thay thế của Chủ đề hoặc, nếu không có tên nào như vậy trong chứng chỉ, trường Tên chung của Chủ đề (CN). Postgres hiện không hỗ trợ Tên thay thế Chủ đề cho địa chỉ IP, vì vậy nếu kết nối được thực hiện theo địa chỉ chứ không phải tên, kết nối đó phải khớp với trường CN.

CN của chứng chỉ ứng dụng khách phải là tên của người dùng tạo kết nối BDR. Đây thường là postgres của người dùng. Mỗi nút sẽ yêu cầu các dòng phù hợp cho phép kết nối trong tệp pg_hba.conf; Ví dụ:

```
hostssl all         postgres 10.1.2.3/24 cert
hostssl replication postgres 10.1.2.3/24 cert
```

Một thiết lập khác có thể là sử dụng mật khẩu SCRAM-SHA-256 thay vì chứng chỉ máy khách và không bận tâm về việc xác minh danh tính máy chủ miễn là chứng chỉ được ký đúng cách. Ở đây, các tham số DSN có thể chỉ là:

```
sslmode=verify-ca sslrootcert=root.crt
```

... và các dòng pg_hba.conf tương ứng sẽ như thế này:

```
hostssl all         postgres 10.1.2.3/24 scram-sha-256
hostssl replication postgres 10.1.2.3/24 scram-sha-256
```

Trong trường hợp như vậy, người dùng postgres sẽ cần một tệp .pgpass chứa đúng mật khẩu.

**Witness Nodes**

Nếu cụm có số nút chẵn, có thể có lợi khi tạo thêm một nút để giúp phá vỡ quan hệ trong trường hợp chia tách mạng (hoặc phân vùng mạng, như nó đôi khi được gọi).

Thay vì tạo một nút kích thước đầy đủ bổ sung, bạn có thể tạo một nút vi mô, đôi khi được gọi là nút Nhân chứng. Đây là một nút BDR bình thường được cố ý thiết lập để không sao chép bất kỳ bảng hoặc dữ liệu nào lên nó.

**Logical Standby Nodes** - Các nút chờ logic

BDR cho phép bạn tạo một "nút chờ logic", còn được gọi là "nút giảm tải", "nút chỉ đọc", "nút chỉ nhận" hoặc "bản sao đọc logic". Một nút chính có thể có 0, một hoặc nhiều nút chờ logic.

Với một nút chờ vật lý, nút không bao giờ xuất hiện đầy đủ, buộc nó phải ở chế độ khôi phục liên tục. BDR cho phép một cái gì đó tương tự. bdr.join_node_group có tùy chọn pause_in_standby để làm cho nút ở trạng thái được kết hợp nửa chiều như một nút dự phòng hợp lý. Các nút chờ logic nhận các thay đổi nhưng không gửi các thay đổi được thực hiện cục bộ đến các nút khác.

Sau đó, nếu muốn, hãy sử dụng bdr.promote_node () để chuyển chế độ chờ logic thành một nút gửi / nhận bình thường, đầy đủ.

Chế độ chờ logic được gửi dữ liệu bởi một nút nguồn, được xác định bởi DSN trong bdr.join_node_group. Các thay đổi từ tất cả các nút khác được nhận từ một nút nguồn này, giảm thiểu băng thông giữa nhiều trang web.

Có nhiều lựa chọn để có tính khả dụng cao:

 - Nếu nút nguồn chết, một chế độ chờ vật lý có thể được nâng cấp lên chế độ chính. Trong trường hợp này, nút chính mới có thể tiếp tục cấp dữ liệu cho bất kỳ / tất cả các nút dự phòng hợp lý.

 - Nếu nút nguồn chết, một chế độ chờ logic có thể được chuyển thành một nút đầy đủ và thay thế nguồn trong một hoạt động chuyển đổi dự phòng tương tự như hoạt động chính duy nhất. Lưu ý rằng nếu có nhiều nút dự phòng hợp lý, các nút khác không thể đi theo nút chính mới, do đó, hiệu quả của kỹ thuật này chỉ giới hạn ở một chế độ chờ hợp lý.

Lưu ý rằng trong trường hợp một chế độ chờ mới được tạo bởi một nút BDR hiện có, các vị trí sao chép cần thiết để hoạt động sẽ không được đồng bộ hóa với chế độ chờ mới cho đến khi có ít nhất 16 MB LSN trôi qua kể từ khi vị trí nhóm được nâng cao lần cuối. Trong trường hợp cực đoan, điều này có thể yêu cầu đủ 16 MB trước khi các vị trí được đồng bộ hóa / tạo trên bản sao phát trực tuyến. Nếu quá trình chuyển đổi dự phòng hoặc chuyển đổi xảy ra trong khoảng thời gian này, chế độ chờ phát trực tuyến không thể được thúc đẩy để thay thế nút BDR của nó, vì vị trí nhóm và các vị trí phụ thuộc khác chưa tồn tại.

Trên EDB Postgres Extended và EDB Postgres Advaced, điều này được giải quyết tự động. Quá trình đồng bộ hóa vị trí ở chế độ chờ giải quyết vấn đề này bằng cách gọi một hàm ở thượng nguồn. Chức năng này di chuyển vị trí nhóm trong toàn bộ cụm BDR bằng cách thực hiện chuyển mạch WAL và yêu cầu tất cả các nút ngang hàng BDR phát lại các cập nhật tiến độ của chúng. Những nguyên nhân trên khiến vị trí nhóm tăng lên trong một khoảng thời gian ngắn. Điều này làm giảm thời gian chờ cần thiết cho quá trình đồng bộ hóa của vị trí ban đầu, cho phép chuyển đổi dự phòng nhanh hơn nếu cần.

Trên PostgreSQL, điều quan trọng là phải đảm bảo rằng quá trình đồng bộ hóa của vị trí đã hoàn tất ở chế độ chờ trước khi quảng bá nó. Truy vấn sau có thể được chạy ở chế độ chờ trong cơ sở dữ liệu đích để theo dõi và đảm bảo rằng các vị trí đã được đồng bộ hóa với ngược dòng. Quảng cáo có thể tiếp tục khi truy vấn này trả về true.

```
SELECT true FROM pg_catalog.pg_replication_slots WHERE
    slot_type = 'logical' AND confirmed_flush_lsn IS NOT NULL;
```

Cũng có thể thúc đẩy quá trình đồng bộ hóa vị trí trong toàn bộ cụm BDR bằng cách thực hiện thủ công các công tắc WAL và bằng cách yêu cầu tất cả các nút ngang hàng BDR phát lại cập nhật tiến độ của chúng. Hoạt động này sẽ làm cho vị trí nhóm di chuyển trước trong một khoảng thời gian ngắn và cũng đẩy nhanh hoạt động đồng bộ vị trí ở chế độ chờ. Các truy vấn sau có thể được chạy trên bất kỳ nút ngang hàng BDR nào trong cơ sở dữ liệu đích cho việc này:

```
SELECT bdr.run_on_all_nodes('SELECT pg_catalog.pg_switch_wal()');
SELECT bdr.run_on_all_nodes('SELECT bdr.request_replay_progress_update()');
```

Sử dụng truy vấn giám sát từ bên trên ở chế độ chờ để kiểm tra xem các truy vấn này có thực sự giúp đồng bộ hóa vị trí nhanh hơn ở chế độ chờ đó hay không.

Bản thân các nút dự phòng logic có thể được bảo vệ bằng cách sử dụng các nút chờ vật lý, nếu muốn, vì vậy Master-> LogicalStandby-> PhysicalStandby. Lưu ý rằng bạn không thể chuyển từ LogicalStandby sang LogicalStandby.

Lưu ý rằng chế độ chờ logic không cho phép ghi các giao dịch, vì vậy các hạn chế của chế độ chờ vật lý không áp dụng. Điều này có thể được sử dụng để mang lại lợi ích to lớn, vì nó cho phép chế độ chờ logic có thêm chỉ mục, thời gian lưu giữ dữ liệu lâu hơn, bảng công việc trung gian, NGHE / THÔNG BÁO, bảng tạm thời, chế độ xem cụ thể hóa và các khác biệt khác.

Bất kỳ thay đổi nào được thực hiện cục bộ đối với các dự phòng logic cam kết trước khi khuyến mại sẽ không được gửi đến các nút khác. Tất cả các giao dịch cam kết sau khi khuyến mãi sẽ được gửi trở đi. Nếu bạn thực hiện ghi vào chế độ chờ hợp lý, bạn nên cẩn thận để làm an toàn cơ sở dữ liệu trước khi thăng hạng.

Bạn có thể thực hiện các thay đổi DDL đối với các nút dự phòng hợp lý nhưng chúng sẽ không được sao chép, cũng như không cố gắng thực hiện các khóa DDL toàn cầu. Các chức năng BDR hoạt động tương tự như DDL cũng sẽ không được sao chép. Xem [Bản sao DDL]. Nếu bạn đã thực hiện các thay đổi DDL không tương thích đối với chế độ chờ hợp lý, thì cơ sở dữ liệu được cho là một nút khác nhau. Việc thúc đẩy một nút phân kỳ hiện sẽ dẫn đến việc sao chép không thành công. Do đó, bạn nên lập kế hoạch để đảm bảo rằng một nút dự phòng hợp lý được giữ không có các thay đổi khác nhau nếu bạn có ý định sử dụng nó như một nút dự phòng hoặc đảm bảo rằng các nút khác nhau không bao giờ được thúc đẩy.

**Physical Standby Nodes** - Nút chờ vật lý

BDR cũng cho phép tạo các nút chuyển đổi dự phòng vật lý truyền thống. Chúng thường nhằm thay thế trực tiếp một nút BDR trong cụm sau một quy trình xúc tiến ngắn. Như với bất kỳ cụm Postgres tiêu chuẩn nào, một nút có thể có bất kỳ số lượng bản sao vật lý nào này.

Tuy nhiên, có một số điều kiện tiên quyết tối thiểu để điều này hoạt động bình thường do việc sử dụng các khe sao chép và các yêu cầu chức năng khác trong BDR:

 - Kết nối giữa BDR Primary và Standby sử dụng tính năng sao chép trực tuyến thông qua một khe sao chép vật lý.
 - Chế độ chờ có:
    - recovery.conf (đối với PostgreSQL <12, đối với PostgreSQL 12+ các cài đặt này phải nằm trong postgres.conf):
    - primary_conninfo trỏ đến Primary
    - primary_slot_name đặt tên cho một vị trí nhân bản vật lý trên Chính để chỉ được sử dụng trong Chế độ chờ này
  - postgresql.conf:
    - shared_preload_libraries = 'pglogical, bdr' ở mức tối thiểu
    - hot_standby = bật
    - hot_standby_feedback = on
  - Chế độ sơ cấp có:
    - postgresql.conf:
    - pglogical.standby_slot_names phải chỉ định vị trí nhân bản vật lý được sử dụng cho chính_slot_name của Chế độ chờ.

Mặc dù điều này là đủ để tạo ra một nút BDR ở chế độ chờ vật lý hoạt động, nhưng có một số mối quan tâm bổ sung cần được giải quyết.

Sau khi được thiết lập, Chế độ chờ yêu cầu đủ thời gian và lưu lượng WAL để kích hoạt bản sao ban đầu của các khe sao chép khác liên quan đến BDR của Chính, bao gồm cả khe nhóm BDR. Ở mức tối thiểu, các vị trí trên Chế độ chờ chỉ ở trạng thái "sống" và sẽ tồn tại sau khi chuyển đổi dự phòng nếu chúng báo cáo có giá trị khác không được xác nhận_flush_lsn như được báo cáo bởi pg_replication_slots.

Do đó, các nút chờ vật lý trong các cụm BDR mới được khởi tạo với lượng hoạt động ghi thấp nên được kiểm tra trước khi giả định rằng chuyển đổi dự phòng sẽ hoạt động bình thường. Không thực hiện biện pháp phòng ngừa này có thể dẫn đến Chế độ chờ có một tập hợp con không đầy đủ các vị trí sao chép bắt buộc cần thiết để hoạt động như một nút BDR, và do đó chuyển đổi dự phòng bị hủy bỏ.

Cơ chế bảo vệ đảm bảo các nút chờ vật lý được cập nhật và có thể được quảng bá (như được định cấu hình pglogical.standby_slot_names) ảnh hưởng đến độ trễ sao chép tổng thể của Nhóm BDR vì quá trình sao chép nhóm chỉ xảy ra khi các nút chờ vật lý được cập nhật.

Vì những lý do này, thường nên sử dụng các nút dự phòng hợp lý hoặc nhóm chỉ đăng ký thay vì các nút chờ vật lý vì cả hai đều có các đặc điểm hoạt động tốt hơn so với.

Khi tiện ích mở rộng bdr-enterprise được cài đặt, bạn có thể đảm bảo vị trí nhóm được nâng cao theo cách thủ công trên tất cả các nút (càng nhiều càng tốt), điều này giúp đẩy nhanh quá trình tạo các vị trí sao chép liên quan đến BDR ở chế độ chờ vật lý bằng cú pháp SQL sau:

```
SELECT bdr.move_group_slot_all_nodes();
```

Khi chuyển đổi dự phòng, Chế độ chờ phải thực hiện một trong hai hành động để thay thế Chế độ chính:

    1. Giả sử kiểm soát cùng một địa chỉ IP hoặc tên máy chủ như Chính.
    2. Thông báo cho cụm BDR về sự thay đổi địa chỉ bằng cách thực thi hàm [bdr.alter_node_interface] trên tất cả các nút BDR khác.
Khi điều này được thực hiện, các nút BDR khác sẽ thiết lập lại giao tiếp với nút Chờ -> Chính mới được quảng bá. Vì các khe sao chép chỉ được đồng bộ hóa theo định kỳ, Chính mới này có thể phản ánh LSN thấp hơn mong đợi của các nút BDR hiện có. Nếu đúng như vậy, BDR sẽ tua nhanh từng khe trễ đến vị trí cuối cùng được sử dụng bởi mỗi nút BDR.

Hãy đặc biệt lưu ý đến thông số pglogical.standby_slot_names. Mặc dù đây là thông số cấu hình pglogical, nhưng điều quan trọng là phải đặt trong một cụm BDR nơi có mối quan hệ Chính -> Chế độ chờ vật lý. Trong khi pglogical sử dụng điều này để đảm bảo các máy chủ dự phòng vật lý luôn nhận được lưu lượng WAL trước các bản sao logic, trường hợp sử dụng BDR lại khác.

BDR duy trì một vị trí nhóm luôn phản ánh trạng thái của nút cụm hiển thị độ trễ nhiều nhất đối với bất kỳ bản sao gửi đi nào. Với việc bổ sung một bản sao vật lý, BDR phải được thông báo rằng có một thành viên nút không tham gia, bất kể, sẽ ảnh hưởng đến trạng thái của rãnh nhóm.

Vì Chế độ chờ không giao tiếp trực tiếp với các nút BDR khác, nên tham số standby_slot_names thông báo cho BDR coi các vị trí được đặt tên là các ràng buộc cần thiết trên vị trí nhóm. Khi được đặt, vị trí nhóm sẽ được giữ nếu Chế độ chờ hiển thị độ trễ, ngay cả khi vị trí nhóm thông thường đã được nâng cao.

Như với bất kỳ bản sao vật lý nào, kiểu chờ này cũng có thể được định cấu hình như một bản sao đồng bộ. Xin nhắc lại, điều này yêu cầu:

 - Ở chế độ chờ:
   - Chỉ định một application_name duy nhất trong primary_conninfo
 - Trên chính:
   - Bật đồng bộ_mã_độ
   - Bao gồm tên_dung_dịch_điều_khiển trong tên_công_độ_đồng_hĩa

Có thể kết hợp Chế độ chờ vật lý và các nút BDR khác trong tên miền sync_standby_names. CAMO và Eager All Node Replication sử dụng các cơ chế đồng bộ hóa khác nhau và không hoạt động với sao chép đồng bộ. Hãy chắc chắn rằng tên miền đồng bộ không bao gồm đối tác CAMO (nếu CAMO được sử dụng) hoặc không có nút BDR nào (nếu sử dụng Eager All Node Replication), mà chỉ các nút không phải BDR, ví dụ: a Chế độ chờ vật lý.

**Node Restart và Down Node Recovery**

BDR được thiết kế để phục hồi sau khi khởi động lại nút hoặc ngắt kết nối nút. Nút bị ngắt kết nối sẽ tự động tham gia lại nhóm bằng cách kết nối lại với từng nút ngang hàng và sau đó sao chép mọi dữ liệu bị thiếu từ nút đó.

Khi một nút khởi động, mỗi kết nối sẽ bắt đầu hiển thị bdr.node_slots.state = catchup và bắt đầu sao chép dữ liệu bị thiếu. Việc bắt kịp sẽ tiếp tục trong một khoảng thời gian tùy thuộc vào lượng dữ liệu bị thiếu từ mỗi nút ngang hàng, có thể sẽ tăng lên theo thời gian, tùy thuộc vào khối lượng công việc của máy chủ.

Nếu số lượng hoạt động ghi trên mỗi nút không đồng nhất, thì khoảng thời gian bắt kịp từ các nút có nhiều dữ liệu hơn có thể mất nhiều thời gian hơn đáng kể so với các nút khác. Cuối cùng, trạng thái vị trí sẽ thay đổi thành bdr.node_slots.state = streaming.

Các nút ngoại tuyến trong khoảng thời gian dài hơn, chẳng hạn như giờ hoặc ngày, có thể bắt đầu gây ra các vấn đề về tài nguyên vì nhiều lý do khác nhau. Người dùng không nên lập kế hoạch cho sự cố mất điện kéo dài mà không hiểu các vấn đề sau.

Mỗi nút giữ lại thông tin thay đổi (sử dụng một khe sao chép cho mỗi nút ngang hàng) để sau này nó có thể phát lại các thay đổi đối với một nút tạm thời không thể truy cập được. Nếu một nút ngang hàng vẫn ngoại tuyến vô thời hạn, thông tin thay đổi được tích lũy này cuối cùng sẽ khiến nút hết dung lượng lưu trữ cho nhật ký giao dịch PostgreSQL (WAL trong pg_wal) và có thể khiến máy chủ cơ sở dữ liệu ngừng hoạt động với lỗi tương tự như sau:

```
PANIC: could not write to file "pg_wal/xlogtemp.559": No space left on device
```

... hoặc báo cáo các triệu chứng khác liên quan đến ngoài đĩa đệm.

Ngoài ra, các khe cắm cho các nút ngoại tuyến cũng giữ lại xmin danh mục, ngăn chặn việc hút bụi các bảng danh mục.

Trên EDB Postgres Extended, các nút ngoại tuyến cũng giữ đóng băng dữ liệu để tránh mất dữ liệu giải quyết xung đột (xem: Phát hiện xung đột nguồn gốc).

Quản trị viên nên theo dõi việc ngừng hoạt động của nút (xem: giám sát) và đảm bảo rằng các nút có đủ dung lượng đĩa trống. Nếu khối lượng công việc có thể dự đoán được, thì có thể tính toán lượng không gian được sử dụng theo thời gian, cho phép dự đoán thời gian tối đa mà một nút có thể ngừng hoạt động trước khi các vấn đề quan trọng phát sinh.

Các khe sao chép được tạo bởi BDR không được xóa theo cách thủ công. Nếu điều đó xảy ra, cụm bị hỏng và nút đang sử dụng vị trí phải được tách khỏi cụm, như được mô tả bên dưới.

Lưu ý rằng trong khi một nút ngoại tuyến, các nút khác có thể chưa nhận được cùng một tập dữ liệu từ nút ngoại tuyến, do đó, điều này có thể xuất hiện dưới dạng phân kỳ nhỏ giữa các nút. Sự mất cân bằng này giữa các nút được sửa chữa tự động trong quá trình phân chia. Các phiên bản sau này có thể thực hiện điều này sớm hơn.

**Xóa nút khỏi nhóm BDR**

Vì BDR được thiết kế để phục hồi sau sự cố ngừng hoạt động của nút kéo dài, bạn phải thông báo rõ ràng cho hệ thống nếu bạn đang xóa một nút vĩnh viễn. Nếu bạn tắt vĩnh viễn một nút và không thông báo cho các nút khác, thì hiệu suất sẽ bị ảnh hưởng, và cuối cùng toàn bộ hệ thống sẽ ngừng hoạt động.

Loại bỏ nút, còn được gọi là chia tay, được thực hiện bằng cách sử dụng hàm bdr.part_node (). Bạn phải chỉ định tên nút (như được thông qua trong quá trình tạo nút) để xóa một nút. Hàm bdr.part_node () có thể được gọi từ bất kỳ nút nào đang hoạt động trong nhóm BDR, bao gồm cả nút đang bị xóa.

Cũng giống như thủ tục tham gia, việc chia tay được thực hiện bằng cách sử dụng sự đồng thuận của Raft và yêu cầu phần lớn các nút phải trực tuyến để hoạt động.

Quá trình chia tay ảnh hưởng đến tất cả các nút. Người lãnh đạo Raft sẽ quản lý một cuộc bỏ phiếu giữa các nút để xem nút nào có dữ liệu gần đây nhất từ ​​nút chia tay. Sau đó, tất cả các nút còn lại sẽ tạo kết nối thứ cấp, tạm thời, với nút mới nhất để cho phép chúng bắt kịp mọi dữ liệu bị thiếu.

Một nút được chia tách vẫn được BDR biết đến, nhưng sẽ không tiêu tốn tài nguyên. Một nút mà giếng của tôi được thêm lại dưới cùng tên với một nút đã chia. Trong một số trường hợp hiếm hoi, có thể nên xóa tất cả siêu dữ liệu của một nút được chia tách bằng hàm bdr.drop_node ().

**Uninstalling BDR**

Bỏ phần mở rộng BDR sẽ xóa tất cả các đối tượng BDR trong một nút, bao gồm cả các bảng siêu dữ liệu. Điều này có thể được thực hiện bằng lệnh sau:

```
DROP EXTENSION bdr;
```

Nếu cơ sở dữ liệu phụ thuộc vào một số đối tượng cụ thể của BDR, thì không thể bỏ phần mở rộng BDR. Những ví dụ bao gồm:

Các bảng sử dụng trình tự dành riêng cho BDR như timeshard hoặc galloc

 - Cột sử dụng kiểu dữ liệu CRDT
 - Các chế độ xem phụ thuộc vào một số bảng danh mục BDR

Những phụ thuộc đó phải được loại bỏ trước khi loại bỏ phần mở rộng BDR, chẳng hạn như bằng cách loại bỏ các đối tượng phụ thuộc, thay đổi loại cột thành tương đương không phải BDR hoặc thay đổi kiểu trình tự trở lại cục bộ.

```
Cảnh báo
Việc loại bỏ phần mở rộng BDR chỉ phải được thực hiện nếu nút đã được tách thành công khỏi nhóm nút BDR của nó hoặc nếu nó là nút cuối cùng trong nhóm: việc bỏ BDR và siêu dữ liệu bệnh học sẽ phá vỡ quá trình sao chép đến / từ các nút khác.
```

```
Cảnh báo
Khi thả một nút BDR cục bộ hoặc phần mở rộng BDR trong cơ sở dữ liệu cục bộ, bất kỳ phiên nào đã tồn tại trước đó vẫn có thể cố gắng thực thi một quy trình công việc BDR cụ thể và do đó không thành công. Vấn đề có thể được giải quyết bằng cách ngắt kết nối phiên và sau đó kết nối lại máy khách hoặc khởi động lại phiên bản.
Hơn nữa, lỗi "không thể mở mối quan hệ với OID (...)" có thể xảy ra khi (1) chia một nút khỏi một cụm BDR, sau đó (2) bỏ phần mở rộng BDR (3) tạo lại nó, và cuối cùng (4) đang chạy pglogical.replication_set_add_all_tables (). Khởi động lại phiên bản sẽ giải quyết được vấn đề.
```

Các cân nhắc tương tự cũng áp dụng cho phần mở rộng bệnh học, được yêu cầu bởi BDR.

Nếu pglogical chỉ được sử dụng bởi BDR, thì có thể loại bỏ cả hai phần mở rộng bằng một câu lệnh duy nhất:

```
DROP EXTENSION pglogical, bdr;
```

Ngược lại, nếu nút cũng đang sử dụng pglogical độc lập với BDR, ví dụ: để sao chép một chiều một số bảng vào cơ sở dữ liệu từ xa, thì chỉ nên bỏ phần mở rộng BDR.

```
Cảnh báo
Việc loại bỏ BDR khỏi cơ sở dữ liệu sử dụng độc lập pglogical có thể chặn đăng ký pglogical hiện tại hoạt động thêm với lỗi "Trình quản lý khóa toàn cầu BDR chưa được khởi tạo". Khởi động lại phiên bản sẽ giải quyết được vấn đề.
```

Ngoài ra còn có một hàm bdr.drop_node (), nhưng nó chỉ được sử dụng trong trường hợp khẩn cấp, nếu có vấn đề khi chia tay.

**Node Management Interfaces**

Các nút có thể được thêm và loại bỏ động bằng cách sử dụng các giao diện SQL.

_1. bdr.create_node_

Hàm này tạo một nút.

Tóm tắt:

```
bdr.create_node(node_name text, local_dsn text)
```

Thông số

 - node_name - tên của nút mới; chỉ một nút được phép trên mỗi cơ sở dữ liệu. Tên nút hợp lệ bao gồm chữ thường, số, dấu gạch ngang và dấu gạch dưới.
 - local_dsn - chuỗi kết nối đến nút

Ghi chú:
 
Hàm này chỉ tạo một bản ghi cho nút cục bộ với chuỗi kết nối công khai được liên kết. Chỉ có thể có một bản ghi cục bộ, vì vậy khi nó được tạo, hàm sẽ bị lỗi nếu chạy lại.

Chức năng này là một chức năng giao dịch - nó có thể được khôi phục và những thay đổi do nó thực hiện sẽ hiển thị cho giao dịch hiện tại.

Chức năng sẽ giữ khóa trên nút bdr mới được tạo cho đến khi kết thúc giao dịch.

_2. bdr.drop_node_

Đánh rơi một nút. Chức năng này không nhằm mục đích sử dụng thường xuyên và chỉ nên được thực thi theo hướng dẫn của Bộ phận hỗ trợ kỹ thuật.

Hàm này loại bỏ siêu dữ liệu cho một nút nhất định khỏi cơ sở dữ liệu cục bộ. Nút có thể là:

 - Nút cục bộ, trong trường hợp đó, tất cả siêu dữ liệu của nút bị xóa, bao gồm cả thông tin về các nút ở xa;
 - Một nút từ xa, trong trường hợp này, chỉ siêu dữ liệu cho nút cụ thể đó mới bị xóa.

Tóm tắt

```
bdr.drop_node(node_name text, cascade boolean DEFAULT false, force boolean DEFAULT false)
```

Thông số

 - node_name - Tên của một nút hiện có.
 - cascade - Có phân tầng tới các đối tượng phụ thuộc hay không, thao tác này cũng sẽ xóa nút pglogical được liên kết. Tùy chọn này nên được sử dụng một cách thận trọng!
 - force - Ngăn chặn tất cả các hoạt động kiểm tra thông minh và buộc xóa tất cả siêu dữ liệu cho nút BDR đã cho bất chấp nguy cơ có thể gây ra sự mâu thuẫn. Bộ phận hỗ trợ kỹ thuật chỉ sử dụng nút buộc thả xuống trong trường hợp khẩn cấp liên quan đến việc chia tay.

Ghi chú:

Trước khi chạy điều này, bạn nên chia nút bằng bdr.part_node ().

Hàm này loại bỏ siêu dữ liệu cho một nút nhất định khỏi cơ sở dữ liệu cục bộ. Nút có thể là nút cục bộ, trong trường hợp này, tất cả siêu dữ liệu của nút đều bị xóa, bao gồm cả thông tin về các nút ở xa đều bị xóa; hoặc nó có thể là nút từ xa, trong trường hợp này, chỉ siêu dữ liệu cho nút cụ thể đó mới bị xóa.

_3. bdr.create_node_group_

Hàm này tạo một nhóm BDR với nút cục bộ là thành viên duy nhất của nhóm.

Tóm tắt

```
bdr.create_node_group(node_group_name text,
                      parent_group_name text,
                      join_node_group boolean DEFAULT true,
                      node_group_type text DEFAULT NULL)
```

Thông số

 - node_group_name - Tên của nhóm BDR mới; như với tên nút, tên nhóm hợp lệ phải bao gồm chữ thường, số và dấu gạch dưới, dành riêng.
 - parent_group_name - Tên của nhóm mẹ cho nhóm con.
 - join_node_group - Điều này giúp một nút quyết định có tham gia vào nhóm do nó tạo hay không. Giá trị mặc định là true. Điều này được sử dụng khi một nút đang tạo một nhóm phân đoạn mà nó không muốn tham gia. Điều này chỉ có thể sai nếu tên_nhóm_mã_cấp được chỉ định.
 - node_group_type - Các giá trị hợp lệ là NULL, "chỉ dành cho người đăng ký", "datanode", "bộ điều phối đọc" và "bộ điều phối ghi". Loại 'chỉ dành cho người đăng ký' được sử dụng để tạo một nhóm các nút chỉ nhận các thay đổi từ các nút được tham gia đầy đủ trong cụm, nhưng chúng không bao giờ gửi các thay đổi sao chép đến các nút khác. Xem [Subscriber-Only Nodes] để biết thêm chi tiết. Datanode ngụ ý rằng nhóm đại diện cho một phân đoạn, trong khi các giá trị khác ngụ ý rằng nhóm đại diện cho các điều phối viên tương ứng. Ngoại trừ "chỉ dành cho người đăng ký", ba giá trị còn lại được dành riêng để sử dụng với một tiện ích mở rộng riêng biệt được gọi là tỷ lệ tự động. NULL ngụ ý một nhóm nút mục đích chung thông thường sẽ được tạo.

Ghi chú

Hàm này sẽ chuyển yêu cầu đến nhân viên đồng thuận cục bộ đang chạy cho nút cục bộ.

Chức năng không phải là giao dịch. Việc tạo nhóm là một quá trình nền, vì vậy khi chức năng đã kết thúc, các thay đổi sẽ không thể được khôi phục lại. Ngoài ra, các thay đổi có thể không hiển thị ngay lập tức đối với giao dịch hiện tại; bdr.wait_for_join_completion có thể được gọi để đợi cho đến khi chúng được thực hiện.

Việc tạo nhóm không giữ bất kỳ ổ khóa nào.

