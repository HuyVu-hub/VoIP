# Hướng dẫn cài đặt Zabbix-Server trên hệ điều hành Ubuntu 20.04

### Các bạn chuẩn bị sẵn một máy chủ Ubuntu 20.04 đang chạy với quyền root.

## Bước 1: Thiết lập LAMP

Zabbix yêu cầu ngôn ngữ lập trình PHP để chạy, MySQL làm máy chủ cơ sở dữ liệu và một máy chủ Web như Apache hoặc Nginx. Tôi sử dụng máy chủ web Apache cho bài viết này. Hãy cài đặt tất cả các gói cần thiết trên hệ thống của bạn bằng cách chạy các lệnh sau.
    
            $ sudo apt update

            $ sudo apt install apache2 libapache2-mod-php
          
            $ sudo apt install mysql-server
         
            $ sudo apt install php php-mbstring php-gd php-xml php-bcmath php-ldap php-mysql

Tiếp theo, bạn cần đặt mật khẩu cho người dùng gốc MySQL. Hãy nhập theo các lệnh dưới đây và bạn sẽ có mật khẩu cho tài khoản root của máy chủ cơ sở dữ liệu MySQL.

    $ sudo mysql_secure_installation
    
Ta sẽ cập nhật các biến cấu hình PHP. Chỉnh sửa tệp cấu hình PHP ở /etc/php/7.4/apache2/php.ini cho Apache và cập nhật múi giờ theo yêu cầu của bạn

    $ nano /etc/php/7.4/apache2/php.ini
    
> memory_limit 256M
> 
> upload_max_filesize 16M
> 
> post_max_size 16M
> 
> max_execution_time 300
> 
> max_input_time 300
> 
> max_input_vars 10000
> 
> date.timezone = 'Asia/Kolkata'

## Bước 2: Cấu hình Zabbix Repository

**Zabbix offical team  cung cấp kho lưu trữ theo định dạng gói apt cho hệ thống dựa trên Debian. Ta sẽ sử dụng các lệnh sau để thêm kho lưu trữ:**

    $ wget https://repo.zabbix.com/zabbix/5.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_5.0-1+focal_all.deb
    $ sudo dpkg -i zabbix-release_5.0-1+focal_all.deb
    
## Bước 3: Cài đặt Zabbix Server    

Chúng ta sẽ tiến hành cài đặt Zabbix server packages. Ở đây gói zabbix-server-mysql bao gồm máy chủ Zabbix với hỗ trợ MySQL trong khi đó gói zabbix-frontend-php cung cấp giao diện web cho máy chủ Zabbix.

    $ sudo apt update
    $ sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-agent zabbix-apache-conf

## Bước 4: Tạo Zabbix Database và User

Tiến hành login vào MySQL để tạo database và user:

    $ mysql -u root -p
    
    mysql> CREATE DATABASE zabbixdb character set utf8 collate utf8_bin;
    mysql> CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'password';
    mysql> GRANT ALL PRIVILEGES ON zabbixdb.* TO 'zabbix'@'localhost' WITH GRANT OPTION;
    mysql> FLUSH PRIVILEGES;
    
Sau khi tạo xong, chúng ta load schema mặc định của Zabbix database

    cd /usr/share/doc/zabbix-server-mysql
    zcat create.sql.gz | mysql -u zabbix -p zabbixdb
    
## Bước 5: Update file cấu hình của Zabbix    

Tiến hành chỉnh sửa file cấu hình zabbix ở đường dẫn /etc/zabbix/zabbix_server.conf để zabbix-server có thể kết nối tới database

    $ sudo nano /etc/zabbix/zabbix_server.conf

> DBHost=localhost
> 
> DBName=zabbixdb
> 
> DBUser=zabbix
> 
> DBPassword=password

Bây giờ ta sẽ khởi động lại các dịch vụ

    $ sudo systemctl enable zabbix-server 
    $ sudo systemctl restart zabbix-server 
    $ sudo systemctl restart apache2
    
## Bước 6: Cấu hình Firewall

Tiến hành cấu hình Firewall để cho phép Zabbix hoạt động trên các port 10050 và 10051
    
    $ sudo firewall-cmd --permanent --add-service=http
    $ sudo firewall-cmd --permanent --add-port=10050/tcp
    $ sudo firewall-cmd --permanent --add-port=10051/tcp

Reload lại firewall

    $ sudo firewall-cmd --reload
    
## Bước 7: Khởi chạy Zabbix Web Installer

Nhập địa chỉ ip của máy chủ zabbix-server trên trình duyệt của bạn theo cú pháp sau để vào được trình cài đặt:

    https://<your IP or your domain>/zabbix/
    
Ta sẽ thấy được màn hình sau:

![image](https://user-images.githubusercontent.com/55483458/137264426-604dd3e4-95c0-4823-bb64-27607f4408ab.png)

Click **Next Step**

![image](https://user-images.githubusercontent.com/55483458/137264463-5919a863-daa9-4b9b-ad66-b0d2787b12ed.png)

Đảm bảo rằng tất cả các yêu cầu được máy chủ đáp ứng đầy đủ. Sau đó bấm vào nút **Next Step**, Bạn sẽ thấy trang sau:

![image](https://user-images.githubusercontent.com/55483458/137261675-fe17473b-4595-426e-b041-b249206a995b.png)

 *** Hãy đảm bảo rằng thông tin zabbix-database không có sai sot gì, và click **Next Step**
 
 ![alt text](https://s3-ap-southeast-1.amazonaws.com/kipalog.com/k0ohgo6qpt_zabbix-installer-server-details-ubuntu-20.04.png)
 
 Ở đây có mục **Name**, bạn có thể đặt tên cho zabbix-server là gì tùy ý ban. Sau đó nhấn nút **Next Step**, chúng ta sẽ thấy một giao diện đăng nhập của Zabbix-Monitoring
 
 ![image](https://user-images.githubusercontent.com/55483458/137262589-85eda290-4f15-42a4-8e43-5fe3639ad324.png)

Ta sẽ sử dụng thông tin đăng nhập mặc đinh sau:
    
    Username: Admin
    Password: zabbix
 
 Sau khi đăng nhập thành công, đây chính là màn hình giao diện chính của Zabbix Monitoring
 
 ![image](https://user-images.githubusercontent.com/55483458/137274669-e15e6bf6-8de2-4d01-8026-0be100a880e9.png)

 



































