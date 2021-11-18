### Tổng quátTổng quan về kiến trúc

BDR cung cấp bản sao lôgic đa tổng thể được kết hợp lỏng lẻo bằng cách sử dụng cấu trúc liên kết lưới. Điều này có nghĩa là bạn có thể ghi vào bất kỳ máy chủ nào và các thay đổi sẽ được gửi trực tiếp, từng hàng một tới tất cả các máy chủ khác thuộc cùng một nhóm BDR.

![image](https://user-images.githubusercontent.com/69178270/142341800-e05a6d6d-580a-4b1a-95e4-349b8a2e8c27.png)

Theo mặc định, BDR sử dụng sao chép không đồng bộ, chỉ áp dụng các thay đổi trên các nút ngang hàng sau khi cam kết cục bộ. Một tính năng sao chép tất cả các nút háo hức tùy chọn cho phép xác nhận tất cả các nút bằng cách sử dụng đồng thuận.

### Kiến trúc cơ bản

Multiple Groups
