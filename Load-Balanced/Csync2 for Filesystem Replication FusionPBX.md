# Hướng dẫn cài đặt Csync2 để đồng bộ FileSytem cho FusionPBX trên Debian

### **Khuyến nghị ưu tiên triển khai BDR, nếu BDR bị lỗi các bạn có thể sử dụng cách này!!!**

- Ở đây chúng ta có 2 máy Debian10, gọi tạm là 2 node Master - Slave
  + Máy Debian Master có IP Address: 10.0.249.149 và có hostname là Master
  + Máy Debian Slave có IP Address: 10.0.249.251 và có hostname là Slave

## 1.  Cài đặt truy cập thông qua hostname

- Trên cả 2 node chỉnh sửa file /etc/hosts

    10.0.249.252    Master.local Master
    10.0.249.252    Slave.local Slave

- Đảm bảo 2 máy có thể ping nhau thông qua hostname

![image](https://user-images.githubusercontent.com/55483458/142564492-d6d59282-3ef6-4182-8e3a-dd13ff02c730.png)

![image](https://user-images.githubusercontent.com/55483458/142564512-e422aaf4-08c2-4076-90e3-356255d445cd.png)

- Ở mỗi node, set hotsname như sau
  + Máy Master:

      echo Master.local > /etc/hostname
    
  + Máy Slave:

      echo Slave.local > /etc/hostname

- Khởi động lại hệ thống ở cả 2 node
    
## 2. Cài đặt csync2

- Trên cả 2 node cài đặt csync2:

    apt-get update
    apt-get install csync2

- Trên node Master tạo file key generate:

    openssl genrsa -out /etc/csync2_ssl_key.pem 1024
    openssl req -batch -new -key /etc/csync2_ssl_key.pem -out /etc/csync2_ssl_cert.csr
    openssl x509 -req -days 3600 -in /etc/csync2_ssl_cert.csr -signkey /etc/csync2_ssl_key.pem -out /etc/csync2_ssl_cert.pem
    csync2 -k /etc/csync2.key

Tiếp theo chỉnh sửa file cấu hình /etc/csync2.cfg

    # please see the REAMDE file how to configure csync2
    group ClusterSync
    {
    host Master.local;
    host Slave.local;
    key /etc/csync2.key;
    include /var/lib/freeswitch/recordings;
    include /usr/share/freeswitch/sounds;
    include /var/lib/freeswitch/storage;
    exclude *~ .*;
    }

Copy certs và keys tới node Slave

    scp /etc/csync2* root@node2:/etc/

Tiếp theo thực hiện các lệnh sau:

    csync2 -fr /
    csync2 -xv

Lặp lại 1 lần nữa:

    csync2 -fr /
    csync2 -xv

**Quá trình này sẽ mất một lúc vì đây là lần đồng bộ hóa ban đầu và sẽ dài dòng.**


## 3. Cài đặt crontab để tự động đồng bộ và kiểm tra

- Trên mỗi node, hãy thực hiện crontab -e và thiết lập cấu hình phù hợp, trong trường hợp này mình sử dụng:

  + Trên node Master:

    0-59 / 2 * * * * / usr / sbin / csync2 -x

  + Trên node Slave:

    1-59 / 2 * * * * / usr / sbin / csync2 -x

Điều này có nghĩa là mỗi phút, node Master sẽ đồng bộ và node Slave sẽ đồng bộ ngay sau đó vào phút thứ hai.


- Nếu bạn muốn kiểm tra:

Trên node Master:

    touch /var/lib/freeswitch/records/test

Hãy đợi một vài phút sau đó trên nút hai, bạn sẽ thấy tệp:

    ls /var/lib/freeswitch/recordss/test

    root@Slave:~# ls /var/lib/freeswitch/recordings/test
    ls: cannot access /var/lib/freeswitch/recordings/test: No such file or directory
    root@Slave:~# ls /var/lib/freeswitch/recordings/test
    /var/lib/freeswitch/recordings/test
    root@Slave:~#

Bây giờ hãy xóa tệp khỏi node Slave và chờ 1 vài phút:

    rm /var/lib/freeswitch/recordings/test

Trên node Master:

    ls /var/lib/freeswitch/recordings/test

    root@Master:~# ls /var/lib/freeswitch/recordings/test
    /var/lib/freeswitch/recordings/test
    root@Master:~# ls /var/lib/freeswitch/recordings/test
    ls: cannot access /var/lib/freeswitch/recordings/test: No such file or directory
    root@Master:~#


# **Và vậy là chúng ta đã hoàn thành xong cài đặt csync2 :D Cảm ơn đã theo dõi**
