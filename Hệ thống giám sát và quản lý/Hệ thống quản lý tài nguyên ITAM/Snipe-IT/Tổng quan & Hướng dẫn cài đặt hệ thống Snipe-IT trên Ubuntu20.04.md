# Mục Lục

&ensp;[I.   Tổng quan về Snipe-IT](#1)

&ensp;[II.  Hướng dẫn cài đặt Snipe-IT trên Ubuntu20.04](#2)

#   <a name="1"> I. Tổng quan về Snipe-IT</a>

Snipe-it là một chương trình quản lý tài sản dự trên mã nguồn mở linux, đây là chương trình quản lý tài sản CNTT hiệu quả cho doanh nghiệp.

Snipe-IT làm được rất nhiều điều nhưng tập trung vào hai điều chính. Họ cung cấp cho người quản trị một nền tảng dựa trên đám mây để theo dõi và quản lý tất cả tài sản của công ty bạn và họ thực hiện điều này một cách rất an toàn. Cần theo dõi máy tính xách tay được nhân viên nào sử dụng? Điện thoại di động của họ? ..... Tất cả sẽ được đăng ký trong Snipe-IT cùng với tất cả dữ liệu tương ứng và sau đó có thể được ký điện tử bởi bên nhận. Không còn trang tính Excel nào nữa. Không còn thủ tục giấy tờ với việc in và ký, quét và lưu trữ các thỏa thuận của người dùng cuối.

Group tại Snipe-IT cung cấp bảo mật dữ liệu cho người dùng, chi tiết các dịch vụ của phần mềm giúp khách hàng của họ khi họ quản lý tài sản của mình. Các tính năng như xác thực hai lớp và một tùy chọn để thực thi bảo mật mật khẩu là tiêu chuẩn khá nhiều. Tuy nhiên, nó cũng có các tính năng như phần mềm trung gian để thực thi Chính sách bảo mật nội dung (CSP), Hạn chế quyền truy cập của người dùng và bảo vệ CSRF.

Snipe-IT đảm bảo rằng bản thân nền tảng cũng được bảo mật, với nhiều lớp bảo vệ để bảo mật để đảm bảo rằng các tài sản đang được quản lý được giữ an toàn và nguyên vẹn. Nền tảng này cung cấp một kết nối bảo mật qua TLS (SSL), cơ sở dữ liệu và ổ đĩa được mã hóa và sao lưu dữ liệu thường xuyên. Công cụ này là một phần mềm open source, đó là cách họ chứng minh  với khách hàng về tính bảo mật của nó.

![image](https://user-images.githubusercontent.com/55483458/139250527-4a37b06a-10b2-4a31-bc73-9a3f4281b38d.png)

Tất nhiên, có những vài điều cần xem xét trước khi chọn Snipe-IT làm công cụ quản lý tài sản. Một lưu ý chính là nó chỉ phù hợp với các công ty vừa và nhỏ. Nó cũng không có sẵn cho Android hoặc iOS.    
    
Mặc dù vậy, Snipe-IT là một công cụ hữu ích để giữ an toàn cho tài sản của bạn. Nó miễn phí để tự lưu trữ, nhưng nếu bạn cần lưu trữ cho mình, giá bắt đầu từ $ 39.99 mỗi tháng. Bên cạnh đó, không thể tìm thấy những tính năng này ở nơi khác với cùng mức giá. Tuy nhiên, sản phẩm không đa năng và không thể sử dụng trên các thiết bị khác nhau nếu bạn không có mặt tại nhà.


#   <a name ="2">II.  Hướng dẫn cài đặt Snipe-IT trên Ubuntu20.04</a>

## 1. Update Server & Install dependencies:

    # apt update -y
    # apt upgrade -y
    
Install unzip dependency 

    # apt-get install unzip -y

## 2. Install Apache webserver

    # apt install apache2 -y

Sau khi cài đặt thành công, start và enable apache2 service:

    # systemctl start apache2 && systemctl enable apache2

Trong trường hợp, bạn đã kích hoạt tường lửa và yêu cầu chặn tường lửa của máy chủ web apache, hãy mở một cổng trong tường lửa:

    # ufw allow 80/tcp
    # ufw allow 443/tcp
    # ufw reload
    

Enable Apache’s mod_rewrite module. Snipe-IT yêu cầu tiện ích mở rộng này viết lại các URL một cách rõ hơn.

    # sudo a2enmod rewrite

Restart apache

    # systemctl restart apache2

## 3. Install MariaDB

    # apt install mariadb-server mariadb-client -y

Start và enable mariadb service 

    # systemctl start mariadb && systemctl enable mariadb

Cấu hình mặc định của MariaDB sẽ không được bảo mật. Nếu bạn muốn, sử dụng câu lệnh sau để bảo mật cài đặt:

    # mysql_secure_installation

# 4. Cài đặt PHP và PHP Composer - Công cụ quản lý (cài đặt, cập nhật) thư viện PHP

Ở đây chúng ta đang cài đặt phiên bản PHP 7.4 mặc định và các mudule khác để triển khai web

    # apt install php php-bcmath php-bz2 php-intl php-gd php-mbstring php-mysql php-zip php-opcache php-pdo php-calendar php-ctype php-exif php-ffi php-fileinfo php-ftp php-iconv php-intl php-json php-mysqli php-phar php-posix php-readline php-shmop php-sockets php-sysvmsg php-sysvsem php-sysvshm php-tokenizer php-curl php-ldap -y

Tải xuống trình cài đặt Composer

    # curl -sS https://getcomposer.org/installer | php

Di chuyển tệp thực thi composer.phar tới / usr / local / bin /

    # mv composer.phar / usr / local / bin / composer

## 5. Tạo Database

Tạo database and database user cho Snipe-IT.

    # mysql -u root -p
    
Thực thi các lệnh sau:

    CREATE DATABASE snipe_it;
    CREATE USER 'snipe_it_user'@'localhost' IDENTIFIED BY 'password';
    GRANT ALL PRIVILEGES ON snipe_it.* TO 'snipe_it_user'@'localhost';
    FLUSH PRIVILEGES;
    EXIT;
    
# 6. Cài đặt Snipe-IT    
    
Điều hướng đến thư mục gốc của máy chủ web server

    # cd / var / www /

Sử dụng git để sao chép kho lưu trữ Snipe-IT mới nhất từ ​​URL https://github.com/snipe/snipe-it và sao chép các tệp đã tải xuống vào thư mục snipe-it.

    # git clone https://github.com/snipe/snipe-it snipe-it
    
Chuyển sang thư mục snipe-it.

    # cd / var / www / snipe-it

Sao chép file cấu hình Snipe-IT vào /var/www/snipe-it/.env.

    # cp /var/www/snipe-it/.env.example /var/www/snipe-it/.env

Chỉnh sửa tệp cấu hình.

    # nano /var/www/snipe-it/.env

Trong tệp cấu hình Snipe-IT, hãy tìm các dòng sau:

    APP_URL = null
    APP_TIMEZONE = 'UTC'

Đặt APP_URL thành tên miền của bạn hoặc đó là địa chỉ IP public. 
Nếu bạn sử dụng múi giờ khác với UTC, hãy thay đổi múi giờ thành múi giờ được PHP hỗ trợ và đặt nó trong dấu ngoặc kép.

    APP_URL = example.com
    APP_TIMEZONE='Asia/Ho_Chi_Minh'

Tìm kiếm các dòng:

    DB_DATABASE = null
    DB_USERNAME = null
    DB_PASSWORD = null

Thay đổi các giá trị đó thành thông tin cơ sở dữ liệu bạn đã thiết lập ở Bước 3.

    DB_DATABASE = snipe_it
    DB_USERNAME = snipe_it_user
    DB_PASSWORD = password

Lưu và đóng tập tin.

Đặt quyền sở hữu cho thư mục Snipe-IT.

    # chown -R www-data:www-data /var/www/snipe-it
    # chmod -R 755 /var/www/snipe-it
    
Cài đặt các dependencies Snipe-IT với Composer ( Lưu ý: cài dưới quyền root )

    # composer update --no-plugins --no-scripts
    # composer install --no-dev --prefer-source --no-plugins --no-scripts

Khi Composer chạy xong, hãy tạo một giá trị Laravel APP_Key trong file /var/www/snipe-it/.env mà bạn đã tạo trước đó

    # php artisan key:generate

## 7. Tạo Virtual Host File

Đầu tiên, vô hiệu hóa file Apacheconf mặc định và tạo file vhost conf mới.

 Tắt tệp cấu hình Apache mặc định.
 
    # a2dissite 000-default.conf

 Tạo tệp cấu hình Apache mới.

    # nano /etc/apache2/sites-available/snipe-it.conf

Dán thông tin bên dưới và thay thế example.com bằng tên miền hoặc địa chỉ IP công cộng của máy chủ của bạn.

    <VirtualHost *:80>
        ServerName example.com
        DocumentRoot /var/www/snipe-it/public
    <Directory /var/www/snipe-it/public>
        Options Indexes FollowSymLinks MultiViews
        AllowOverride All
        Order allow,deny
        allow from all
    </Directory>
    </VirtualHost>

Lưu và thoát khỏi file.

Kích hoạt file cấu hình mới 

    # a2ensite snipe-it.conf

Khởi động lại máy chủ web Apache của bạn để áp dụng các thay đổi.

    # systemctl restart apache2

## 8. Chạy trình hướng dẫn cài đặt

Sử dụng trình duyệt và truy cập đến IP máy chủ của bạn hoặc tên miền mà bạn đã đề cập trong tệp conf vhost.

![image](https://user-images.githubusercontent.com/55483458/139046831-505025bd-337b-4040-955d-ad08cdeb1ef7.png)

Sau khi cài đặt Snipe-IT xong, chúng ta sẽ thấy được giao diện của hệ thống:

![image](https://user-images.githubusercontent.com/55483458/139048322-bf675ab4-5b5d-4eba-96c2-0946570dbdd5.png)


## :D Vậy là xong phần cài đặt Snipe-IT trên ubuntu20.04 :D Chúc các bạn thành công









