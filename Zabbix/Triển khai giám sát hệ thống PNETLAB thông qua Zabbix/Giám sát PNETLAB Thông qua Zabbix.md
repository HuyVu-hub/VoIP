# Triển khai hệ thống giám sát Zabbix trên PNET

Chúng ta chuẩn bị sẵn một máy chủ Zabbix server đã cài đặt trước

![](.//media/image1.png)

## Cài đặt snmp trên máy chủ PNETlab

![](.//media/image2.png)

![](.//media/image3.png)

-   Chỉnh sửa file cấu hình, cho phép IP từ Zabbix-Server

![](.//media/image4.png)


![](.//media/image5.png)

-   Cho phép ip thông qua firewall

![](.//media/image6.png)


![](.//media/image7.png)


## Triển khai trên Zabbix-server

### Cài đặt snmp trên Zabbix-server

![](.//media/image8.png)


![](.//media/image9.png)


![](.//media/image10.png)
 => Comment mibs

![](.//media/image11.png)


-   Kiểm tra kết nối (192.168.17.134 là địa chỉ của pnet server)

![](.//media/image12.png)

### Thêm Pnetlab Server vào Zabbix-Fontend

![](.//media/image13.png)

### Thêm item và triggers để bắt sự kiện thay đổi hostname hệ thống của PNETLAB Server

-   Lấy MIB của hostname thông qua snmp

![](.//media/image14.png)

-   Lấy OID của hostname

![](.//media/image15.png)

 => OID: .1.3.6.1.2.1.1.5.0

-   Thêm item trên Monitor Zabbix

![](.//media/image16.png)

![](.//media/image17.png)

-   Thêm Triggers để ứng phó với sự kiện

![](.//media/image18.png)

Chọn Add => Select Item đã tạo, đặt giá trị gốc ở mục Result

![](.//media/image19.png)

![](.//media/image20.png)

-   Tiến hành kiểm tra thử khi hostname bị thay đổi

![](.//media/image21.png)
![](.//media/image21.png)


-   Xuất hiện Problem

![](.//media/image22.png)

-   Khi chúng ta thay đổi lại hostname thì vấn đề sẽ được giải quyết

![](.//media/image23.png)


![](.//media/image24.png)

### Tạo các Item và Trigger khác để theo dõi

-   Uptime

![](.//media/image25.png)
![](.//media/image26.png)


-   Number of CPU

![](.//media/image27.png)

![](.//media/image28.png)

-   Một số item khác.....

![](.//media/image29.png)

![](.//media/image30.png)
