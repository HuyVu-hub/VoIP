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


