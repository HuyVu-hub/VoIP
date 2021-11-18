### Tổng quan về kiến trúc

BDR cung cấp bản sao lôgic đa tổng thể được kết hợp lỏng lẻo bằng cách sử dụng cấu trúc liên kết lưới. Điều này có nghĩa là bạn có thể ghi vào bất kỳ máy chủ nào và các thay đổi sẽ được gửi trực tiếp, từng hàng một tới tất cả các máy chủ khác thuộc cùng một nhóm BDR.

![image](https://user-images.githubusercontent.com/69178270/142341800-e05a6d6d-580a-4b1a-95e4-349b8a2e8c27.png)

Theo mặc định, BDR sử dụng sao chép không đồng bộ, chỉ áp dụng các thay đổi trên các nút ngang hàng sau khi cam kết cục bộ. Một tính năng sao chép tất cả các nút háo hức tùy chọn cho phép xác nhận tất cả các nút bằng cách sử dụng đồng thuận.

### Kiến trúc cơ bản

**Multiple Groups**

Một nút BDR là thành viên của ít nhất một Nhóm nút và trong kiến ​​trúc cơ bản nhất, có một nhóm nút duy nhất cho toàn bộ cụm BDR.

**Multiple Masters**

Mỗi nút (cơ sở dữ liệu) tham gia vào một nhóm BDR đều nhận được các thay đổi từ các thành viên khác và có thể được ghi trực tiếp bởi người dùng.

Điều này khác biệt với Chế độ chờ nóng hoặc Chế độ chờ ấm, trong đó chỉ có một máy chủ chính chấp nhận ghi và tất cả các nút khác là các nút chờ sao chép từ máy chủ hoặc từ chế độ chờ khác.

Bạn không cần phải viết thư cho tất cả các bậc thầy, mọi lúc; đó là một cấu hình thường xuyên để ghi trực tiếp hầu hết vào chỉ một bản gốc. Tuy nhiên, nếu bạn chỉ muốn sao chép một chiều thì việc sử dụng pglogical có thể phù hợp hơn.

**Asynchronous, by default** - Không đồng bộ, theo mặc định

Các thay đổi được thực hiện trên một nút BDR không được sao chép sang các nút khác cho đến khi chúng được cam kết cục bộ. Kết quả là dữ liệu không hoàn toàn giống nhau trên tất cả các nút tại bất kỳ thời điểm nào; một số nút sẽ có dữ liệu chưa đến các nút khác. Các giải pháp sao chép dựa trên khối của PostgreSQL cũng mặc định là sao chép không đồng bộ. Trong BDR, vì có nhiều cái chính và kết quả là nhiều luồng dữ liệu, dữ liệu trên các nút khác nhau có thể khác nhau ngay cả khi synchronous_commitvà synchronous_standby_namesđược sử dụng.

**Mesh Topology** - Cấu trúc liên kết lưới

BDR được cấu trúc xung quanh một mạng lưới nơi mọi nút kết nối với mọi nút khác và tất cả các nút trao đổi dữ liệu trực tiếp với nhau. Không có chuyển tiếp dữ liệu trong BDR ngoại trừ các trường hợp đặc biệt như thêm nút và xóa nút. Dữ liệu có thể đến từ bên ngoài cụm BDR hoặc được gửi trở đi bằng cách sử dụng bản sao lôgic pglogical hoặc gốc PostgreSQL.

**Logical Replication** - Sao chép lôgic

Sao chép lôgic là một phương pháp sao chép các hàng dữ liệu và các thay đổi của chúng, dựa trên nhận dạng bản sao của chúng (thường là một khóa chính). Chúng tôi sử dụng thuật ngữ logic trái ngược với sao chép vật lý , sử dụng địa chỉ khối chính xác và sao chép từng byte. Các thay đổi chỉ mục không được sao chép, do đó tránh được hiện tượng khuếch đại ghi và giảm băng thông.

Việc sao chép lôgic bắt đầu bằng cách sao chép một ảnh chụp nhanh của dữ liệu từ nút nguồn. Khi điều đó được thực hiện, các cam kết sau này sẽ được gửi đến các nút khác khi chúng xảy ra trong thời gian thực. Các thay đổi được sao chép mà không cần thực thi lại SQL, do đó, dữ liệu chính xác đã viết sẽ được sao chép một cách nhanh chóng và chính xác.

Các nút áp dụng dữ liệu theo thứ tự thực hiện các cam kết trên nút nguồn, đảm bảo tính nhất quán của giao dịch được đảm bảo cho các thay đổi từ bất kỳ nút đơn nào. Các thay đổi từ các nút khác nhau được áp dụng độc lập với các nút khác để đảm bảo việc nhân rộng các thay đổi một cách nhanh chóng.

Dữ liệu sao chép được gửi ở dạng nhị phân, khi đó là an toàn.

**High Availability** - Tính khả dụng cao

Mỗi nút chính có thể được bảo vệ bởi một hoặc nhiều nút dự phòng, vì vậy bất kỳ nút nào gặp sự cố đều có thể nhanh chóng được thay thế và tiếp tục. Mỗi nút dự phòng có thể là một nút dự phòng logic hoặc vật lý.

Việc nhân rộng tiếp tục giữa các nút hiện được kết nối ngay cả khi một hoặc nhiều nút hiện không khả dụng. Khi nút phục hồi, quá trình sao chép có thể bắt đầu lại từ nơi nó đã dừng lại mà không bỏ lỡ bất kỳ thay đổi nào.

Các nút có thể chạy các mức phát hành khác nhau, thương lượng các giao thức cần thiết để giao tiếp. Do đó, các cụm BDR có thể sử dụng các bản nâng cấp luân phiên, ngay cả đối với các phiên bản chính của phần mềm cơ sở dữ liệu.

DDL được tự động sao chép giữa các nút theo mặc định. Việc thực thi DDL có thể được người dùng kiểm soát để cho phép nâng cấp ứng dụng luân phiên, nếu muốn.

**Limits** - Hạn mức

BDR đã được thử nghiệm với tối đa 99 nút chính trong một cụm, nhưng nó hiện được thiết kế để sử dụng với tối đa 32 nút chính. Mỗi nút chính có thể được bảo vệ bởi nhiều nút chờ vật lý hoặc logic; không có giới hạn cụ thể về số lượng nút dự phòng, nhưng cách sử dụng điển hình sẽ là có 2-3 nút dự phòng cho mỗi nút chính, với tối đa thông thường là 32 nút dự phòng cho mỗi nút chính.

BDR giả định rằng sẽ có không quá 1024 nút (tính cả nút chính và điểm chờ lôgic cho tổng số) khi sử dụng chuỗi phần cứng thời gian, không tính các nút đã bị loại bỏ trước đó (chia / bỏ) khỏi một nhóm.

BDR đưa ra giới hạn rằng tối đa 10 cơ sở dữ liệu trong bất kỳ phiên bản PostgreSQL nào có thể là các nút BDR trên các nhóm nút BDR khác nhau. Việc có nhiều nút / cơ sở dữ liệu trong một phiên bản là một phần của cùng một nhóm nút BDR không được hỗ trợ.

### Tùy chọn & Hiệu suất kiến trúc

**Đặc trưng hiệu suất BDR**

BDR có thể được cấu hình theo một số kiến ​​trúc khác nhau, mỗi kiến ​​trúc có các đặc điểm về hiệu suất và khả năng mở rộng khác nhau.

Nhóm là khối xây dựng cơ bản của BDR Cluster bao gồm 2+ nút (máy chủ). Trong một Nhóm, mỗi nút ở một AZ khác nhau, với bộ định tuyến và sao lưu chuyên dụng, mang lại Máy chuyển mạch ngay lập tức và Tính khả dụng cao. Mỗi Nhóm có một Bộ sao chép chuyên dụng được xác định trên đó. Nếu Nhóm mất một nút, nó có thể dễ dàng sửa chữa / thay thế bằng cách sao chép một nút hiện có từ Nhóm.

Các kiến trúc sau có sẵn:

 - Multimaster / Nhóm đơn
 - BDR AlwaysOn
 - BDR trên toàn thế giới
 - BDR AutoScale

Kiến trúc đơn giản nhất là chỉ có một Nhóm, vì vậy trước tiên hãy kiểm tra điều đó:

**_BDR MultiMaster trong một Nhóm_**

Theo mặc định, BDR sẽ giữ một bản sao của mỗi bảng trên mỗi nút trong Nhóm và mọi thay đổi sẽ được truyền tới tất cả các nút trong Nhóm.

Vì các bản sao của dữ liệu ở khắp mọi nơi, các SELECT chỉ cần truy cập vào nút cục bộ. Trên một cụm chỉ đọc, hiệu suất trên bất kỳ một nút nào sẽ không bị ảnh hưởng bởi số lượng nút. Do đó, việc thêm các nút sẽ làm tăng tuyến tính tổng thông lượng SELECT có thể có.

INSERT, UPDATE và DELETE (DML) được thực hiện cục bộ, sau đó các thay đổi sẽ được truyền đến tất cả các nút trong Nhóm. Chi phí áp dụng DML ít hơn so với thực thi ban đầu, vì vậy nếu bạn chạy đồng thời khối lượng công việc ghi thuần túy trên nhiều nút, một cụm nhiều nút sẽ có thể xử lý nhiều TPS hơn một nút duy nhất.

Xử lý xung đột có chi phí sẽ hành động để giảm thông lượng. Thông lượng sau đó phụ thuộc vào mức độ cạnh tranh mà ứng dụng hiển thị trong thực tế. Các ứng dụng có mức độ cạnh tranh rất thấp sẽ hoạt động tốt hơn so với một nút duy nhất; các ứng dụng có tính cạnh tranh cao có thể hoạt động kém hơn một nút duy nhất. Những kết quả này phù hợp với bất kỳ công nghệ đa tổng thể nào, chúng không phải là một khía cạnh hay đặc thù của BDR.

Việc nhân rộng háo hức có thể tránh được xung đột, nhưng vốn dĩ đắt hơn.

Các thay đổi được gửi đồng thời đến tất cả các nút để giảm thiểu độ trễ sao chép. Thêm nhiều nút hơn có nghĩa là sử dụng nhiều CPU hơn để sao chép, vì vậy TPS cao nhất sẽ giảm một chút khi mỗi nút mới được thêm vào.

Nếu khối lượng công việc cố gắng sử dụng tất cả tài nguyên CPU thì điều này sẽ hạn chế tài nguyên sao chép, sau đó có thể ảnh hưởng đến độ trễ sao chép.

**_BDR AlwaysOn_**

Kiến trúc AlwaysOn được xây dựng từ 2 Nhóm, ở 2 vùng riêng biệt. Mỗi Nhóm cung cấp HA và IS, nhưng chúng đồng thời cung cấp Khôi phục sau thảm họa (DR), vì vậy chúng tôi gọi kiến ​​trúc này là AlwaysOn với Tính khả dụng rất cao.

Các bảng được tạo trên cả hai Nhóm, vì vậy bất kỳ thay đổi nào cũng được thực hiện cho tất cả các nút, không chỉ cho các nút trong Nhóm cục bộ.

Một nút là mục tiêu cho ứng dụng chính. Tất cả các nút khác được mô tả là nút bóng (hoặc "bản sao đọc-ghi"), chờ tiếp quản khi cần thiết. Nếu một nút bị mất liên lạc, chúng tôi chuyển ngay sang nút bóng để tiếp tục xử lý. Nếu một Nhóm không thành công, chúng ta có thể chuyển sang Nhóm khác. Khả năng mở rộng không phải là mục tiêu của kiến ​​trúc này.

Vì chúng ta chỉ viết chủ yếu cho một nút, nên khả năng tranh chấp giữa các nút giảm xuống gần như bằng không và do đó tác động đến hiệu suất giảm đi nhiều.

CAMO rất mong muốn nhân rộng trong Nhóm địa phương, lười biếng đối với các Nhóm khác.

Các ứng dụng phụ có thể thực thi đối với các nút bóng, mặc dù các ứng dụng này sẽ được giảm bớt hoặc bị gián đoạn nếu ứng dụng chính bắt đầu sử dụng nút đó.

Tính năng trong tương lai: Một nút được bầu làm trình sao chép chính cho các Nhóm khác, hạn chế chi phí sao chép của CPU khi cụm phát triển và giảm thiểu băng thông cho các Nhóm khác.

**_BDR trên toàn thế giới_**

Trong kiến trúc này, nhiều Nhóm BDR tồn tại trong nhiều Khu vực trên toàn thế giới, nhưng tất cả các nút trong một Nhóm luôn nằm trong một Khu vực.

Nếu các bảng khối lượng lớn được định cấu hình để chúng chỉ sao chép trong Nhóm của riêng chúng, thì TPS ghi đỉnh sẽ chia tỷ lệ tuyến tính theo số lượng Nhóm. Điều này cho phép phân tích dữ liệu dựa trên vị trí đầu vào của nó, còn được gọi là phân tích địa lý.

Điều này làm cho kiến trúc này rất phù hợp để sử dụng trong IoT, giám sát hoặc các ứng dụng thu thập dữ liệu khối lượng lớn khác, trong đó vị trí là cách ứng dụng được phân vùng tự nhiên. Dữ liệu khối lượng lớn không rời khỏi Khu vực của nó, cho phép chúng tôi tránh chi phí băng thông cao do sao chép giữa các khu vực cũng như cho phép chúng tôi tuân theo luật mạnh mẽ về quyền riêng tư dữ liệu và quyền tài phán dữ liệu.

BDR cho phép các truy vấn đọc nhiều nút trong kiến trúc này. Các truy vấn nhiều nút lớn sẽ mở rộng tuyến tính về thời gian phản hồi khi số lượng nút tăng lên. Thông lượng bị hạn chế vì mỗi truy vấn chạy trên một nút trong mọi cụm con.

**_BDR AutoScale_**

Trong kiến ​​trúc này, chúng tôi sử dụng một mảng Nhóm để tạo một nền tảng máy tính song song.

Theo mặc định, các bảng được tạo trên tất cả các nút. Đối với các bảng được phân vùng theo phạm vi, người dùng có thể chỉ định một khóa phân phối cho phép phân vùng được phân vùng trên các Nhóm để cho phép phân vùng tự động. Ví dụ: TimeStamp, CustomerId hoặc WarehouseId. Lưu ý rằng các giá trị khóa riêng lẻ cũng có thể được sử dụng thay vì phạm vi, hỗ trợ hiệu quả cho việc phân vùng dựa trên danh sách.

Các bảng giống hệt nhau trên tất cả các nút được gọi là Bảng sao chép. Các bảng được phân đoạn giữa các nhóm được gọi là Bảng phân tán hoặc Bảng được chia nhỏ. Các bảng sao chép có thể được nối với bất kỳ bảng nào khác. Các bảng được phân phối chỉ có thể được kết hợp với các bảng được sao chép hoặc với các bảng được phân phối khác có cùng phân phối dữ liệu chính xác.

Các bảng phân phối có thể không phải lúc nào cũng được phân phối đồng đều một cách hoàn hảo. Nếu có các phân đoạn được truy cập nhiều, chúng tôi có thể thêm các nút bổ sung vào Nhóm đó để giải quyết khối lượng công việc không cân bằng.

Hình thức sharding này cho phép hiệu suất ghi OLTP mở rộng tuyến tính, nếu ứng dụng gửi các giao dịch trực tiếp đến nhóm thích hợp, được gọi là Định tuyến trực tiếp. Trong chế độ này, kiến trúc có thể được sử dụng tương tự như các hệ thống NoSQL phân tán như Apache Cassandra. Autoscale không hỗ trợ phân vùng băm vì nó gây ra sự cố khi cụm cần mở rộng hoặc thu hẹp, hạn chế khả năng mở rộng quy mô một cách đàn hồi khi cần thiết.

Nếu khối lượng công việc không thể gửi các giao dịch trực tiếp đến đúng nhóm, chúng tôi thực hiện chúng dựa trên một nút Điều phối viên, sau đó định tuyến chúng đến bất kỳ nút nào trong Nhóm thích hợp. Điều phối viên sau đó hoạt động như một người ủy quyền để thực hiện Định tuyến gián tiếp hoặc Proxy. Mỗi Nhóm sẽ có một nút ghi ưu tiên và 1-2 nút bóng khác; các lần đọc sẽ được chuyển hướng đến các nút bóng theo mặc định, hoặc nút ghi ưu tiên nếu nó là nút duy nhất còn lại.

Nút Điều phối viên thêm độ trễ và có thể hạn chế khả năng mở rộng khi phần thời gian dành cho trình điều phối tăng lên. Khối lượng công việc với nhiều yêu cầu chỉ đọc nhỏ gây ra% thời gian của điều phối viên cao hơn, trong khi các truy vấn hỗ trợ quyết định lớn hiển thị một% nhỏ thời gian của điều phối viên, vì vậy hãy mở rộng quy mô là tốt nhất.

Định tuyến phân đoạn yêu cầu quyền truy cập vào Siêu dữ liệu phân phối phân đoạn, được giữ trong các bảng danh mục BDR. Các nút điều phối viên lưu trữ thông tin này trong bảng danh mục của chúng, do đó có quyền truy cập cục bộ trực tiếp. Siêu dữ liệu phân phối Shard cũng có thể được lưu trong bộ nhớ đệm trong các chương trình phía máy khách hoặc trong phần mềm trung gian định tuyến để cho phép Định tuyến trực tiếp diễn ra từ các thành phần đó.

AutoScale cho phép các truy vấn đọc nhiều nút trong kiến ​​trúc này. Các truy vấn nhiều nút lớn sẽ mở rộng tuyến tính về thời gian phản hồi khi số lượng nút tăng lên. Thông lượng bị hạn chế vì mỗi truy vấn chạy trên một nút trong mọi cụm con.

AutoScale chưa hỗ trợ các giao dịch ghi nhiều nút khi sử dụng Điều phối viên, nhưng những giao dịch này sẽ được hỗ trợ trong các bản phát hành trong tương lai. Hiện tại, các hoạt động này được chấp nhận nhưng không phải là nguyên tử. Các giao dịch ghi nhiều nút sẽ hạn chế khả năng mở rộng trong một kiến ​​trúc phân đoạn.

### Triển khai

BDR3 dự định sẽ được triển khai ở một trong số ít các cấu hình tốt đã biết, sử dụng TPAexec hoặc phương pháp quản lý cấu hình và kiến ​​trúc triển khai đã được Bộ phận Hỗ trợ Kỹ thuật phê duyệt.

Việc triển khai thủ công không được khuyến khích và có thể không được hỗ trợ.

Vui lòng tham khảo TPAexec Architecture User Manualcho kiến trúc của bạn.

Thông báo nhật ký và tài liệu hiện chỉ có sẵn bằng tiếng Anh.

### Đồng hồ và Múi giờ

BDR đã được thiết kế để hoạt động với các nút trong nhiều múi giờ, cho phép một cụm cơ sở dữ liệu thực sự trên toàn thế giới. Các máy chủ riêng lẻ không cần phải được định cấu hình với múi giờ phù hợp, mặc dù vậy chúng tôi khuyên bạn nên sử dụng log_timezone = UTC để đảm bảo rằng nhật ký máy chủ có thể đọc được của con người có thể truy cập và so sánh được nhiều hơn.

Đồng hồ máy chủ nên được đồng bộ hóa bằng NTP hoặc các giải pháp khác.

Đồng bộ hóa đồng hồ không quan trọng đối với hiệu suất, như trường hợp của một số giải pháp khác. Độ lệch đồng hồ có thể ảnh hưởng đến Phát hiện xung đột nguồn gốc, mặc dù BDR cung cấp các biện pháp kiểm soát để báo cáo và quản lý bất kỳ độ lệch nào tồn tại. BDR cũng cung cấp Phát hiện Xung đột Phiên bản Hàng, như được mô tả trong Phát hiện Xung đột .
