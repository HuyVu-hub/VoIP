1.  # Chuẩn bị môi trường và cài đặt các gói

    1.  ## Chuẩn bị môi trường

### Disable SELinux

![](.//media/image1.png) 

![](.//media/image2.png)


### Stop Firewalld

![](.//media/image3.png)


## Cài đặt các gói cần thiết

### yum install epel-release

![](.//media/image4.png) 

### rpm -ivh http://repo.okay.com.mx/centos/8/x86_64/release/okay-release-1-5.el8.noarch.rpm

![](.//media/image5.png) 

### yum install fusionpbx-all fusionpbx-fail2ban-rules freeswitch-fail2ban-rules fail2ban-server fail2ban-systemd

![](.//media/image6.png) 

2.  # Cấu hình database và các file cài đặt

    1.  ## Cấu hình file jail.local

cat
/usr/share/doc/fusionpbx-fail2ban-rules/README.fusionpbx-fail2ban-rules.txt
/usr/share/doc/freeswitch-fail2ban-rules/README.freeswitch-fail2ban-rules.txt
\> /etc/fail2ban/jail.local

![](.//media/image7.png) 

## Cấu hình firewall cho phép các dịch vụ đã cài thông qua

![](.//media/image8.png)


## Tiến hành kiểm tra database và thay đổi mật khẩu

![](.//media/image9.png)


### Kiểm tra database

![](.//media/image10.png)


### Kiểm tra User

![](.//media/image11.png)


### Thay đổi password mặc định của User

![](.//media/image12.png) 

## Kiểm tra mật khẩu mặc định của file Socket Configuration

![](.//media/image13.png) 

-   ClueCon

# Khởi chạy Web Installer và đăng nhập

Ta gõ theo cú pháp : **\<Địa chỉ IP máy CentOS>/fusionpbx** lên trình
duyệt web để khởi chạy Web Installer

![](.//media/image14.png) 

Nhập vào pass ClueCon đã lấy từ mục trước vào ô Password

![](.//media/image15.png) 

Tiếp theo nhập username và password bất ký bạn muốn.

![](.//media/image16.png) 

Tiếp theo cấu hình cho database, nhập vào password database bạn đã thay
đổi ở mục trước

![](.//media/image17.png) 

Hoàn tất cài đặt và đăng nhập vào FusionPBX, ta sẽ thấy giao diện chính
của hệ thống

![](.//media/image18.png)


![](.//media/image19.png) 

**Tới đây là cài đặt đã thành công :D Chúc các bạn may mắn nhé**
