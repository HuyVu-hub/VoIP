# Mục lục

&ensp;[1. Chuẩn bị](#1)

&ensp;[2. Tiến hành](#2)

&ensp;[3. Một số lỗi thường gặp khi cài đặt](#3)

# <a name="1">1.  Chuẩn bị</a>

2 Máy **Debian8 Jessie** đã được cài đặt Permit SSH Login
  - Máy Debian Master có địa chỉ IP 10.0.249.149
  - Máy Debian Slave có địa chỉ IP 10.0.249.151

Cả 2 máy đều có thể ssh login tới nhau.

# <a name="2">2.  Tiến hành</a>

Chúng ta sẽ tiến hành cài đặt BDR cho 2 máy Master và Slave. 

## Trên máy Master thực hiện:

    sed -i 's/^PermitRootLogin .*/PermitRootLogin yes/g' /etc/ssh/sshd_config
    service ssh restart
    apt-get update && apt-get upgrade -y --force-yes

    apt-get install -y --force-yes git

    cd /usr/src
    git clone -b stable http://git.directvoip.co.uk/digitaldaz/fusionpbx-sce-install

    chmod 755 -R /usr/src/fusionpbx-sce-install
    cd fusionpbx-sce-install/debian/
    
 ## Trên máy Slave thực hiện
 
    sed -i 's/^PermitRootLogin .*/PermitRootLogin yes/g' /etc/ssh/sshd_config
    service ssh restart
    apt-get update && apt-get upgrade -y --force-yes

    apt-get install -y --force-yes git

    cd /usr/src
    git clone -b stable http://git.directvoip.co.uk/digitaldaz/fusionpbx-sce-install

    chmod 755 -R /usr/src/fusionpbx-sce-install
    cd fusionpbx-sce-install/debian/
    
 ## Tiến hành cài đặt trên máy Master
 
 Chuyển qua máy Master và thực hiện:
 
    apt-get update && apt-get upgrade -y --force-yes

    apt-get install -y --force-yes git sshpass

    cd /usr/src
    git clone -b stable http://git.directvoip.co.uk/digitaldaz/fusionpbx-sce-install

    chmod 755 -R /usr/src/fusionpbx-sce-install
    cd fusionpbx-sce-install/debian/
    
Chạy dòng lệnh sau:
    ./install.sh --masternode
   
![image](https://user-images.githubusercontent.com/55483458/142440036-b86acaf8-56b7-4bac-aead-d97a3b7b7f6a.png)

![image](https://user-images.githubusercontent.com/55483458/142440136-0dd7c0cf-419c-437d-ab72-39c74ab591a7.png)

**Chú ý: Nhập địa chỉ ip của Máy Master và Slave chính xác, sau đó nhập password root login của máy Slave!!!**
 
## Tiến hành cài đặt đồng thời trên máy Slave khi máy Master đang cài đặt

Chuyển qua máy Slave và thực hiện:

    cd /usr/src/fusionpbx-sce-install/debian
    ./install.sh

## Kết quả sau khi thực hiện xong 
 
Trên máy Master

![image](https://user-images.githubusercontent.com/55483458/142443110-8ec48eea-acb6-4b52-8735-b0d3a9e1f70a.png)

Trên máy Slave

![image](https://user-images.githubusercontent.com/55483458/142440319-b3035ff6-d03a-4bb8-8b42-1bfc20107c42.png)

Cài đặt BDR Management trên FusionPBX

    On your server
    cd /usr/src
    git clone https://github.com/fusionpbx/fusionpbx-apps
    Copy or move the directory 'bdr' into your main FusionPBX directory
    cp -R fusionpbx-apps/bdr /var/www/fusionpbx/app
    chown -R www-data:www-data /var/www/fusionpbx/app/bdr

    Log into the FusionPBX webpage
    Advanced -> Upgrade
    Menu Defaults and Permission Defaults.
    Log out and back in

    Advanced -> Default Settings
    Add a Default Setting
    Category: server
    Subcategory: bdr_fusionpbx_enable
    Type: boolean
    Value: true
    Enabled: True
    Save
    
![image](https://user-images.githubusercontent.com/55483458/142443666-afd126a2-2199-42bc-9bc3-22b406c2a63b.png)

![image](https://user-images.githubusercontent.com/55483458/142443771-85ec6828-e79b-4c6a-a2a8-c7b024423905.png)

Tiến hành đăng nhập lại trên màn hình website quản trị fusionpbx => vào BDR Management

![image](https://user-images.githubusercontent.com/55483458/142443899-5688805a-03f3-479a-bf65-752bb956215a.png)

![image](https://user-images.githubusercontent.com/55483458/142443986-454339de-18e5-4d4e-8622-9a3fb704790d.png)

![image](https://user-images.githubusercontent.com/55483458/142444046-84170297-5d47-40bb-bc45-dd4a44097929.png)

# **=> Đây là màn hình quản trị BDR của FusionPBX, chúng ta có thể thấy có 2 node, 2 node này sẽ tự động cân bằng tải cho lẫn nhau, tự động đồng bộ database.**



# <a name="3">3.  Một số lỗi thường gặp khi cài đặt</a>

- Lỗi missing module php => Đảm bảo module php đã được cài đặt cho Debian8 Jessie
- Lỗi apache2 không nhận được thư mục fusionpbx =>  Chuyển thư mục apache2 gốc ở file /etc/apache2/sites-available/000-default.conf từ /var/www/html thành /var/www/fusionpbx
 
 
 
 
 
 
