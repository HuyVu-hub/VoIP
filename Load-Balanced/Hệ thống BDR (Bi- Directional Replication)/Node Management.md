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

