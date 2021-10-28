# Mục Lục

&ensp;[1. Đồng bộ ldap từ Domain Controller Windows Server 2019](#1)

&ensp;[2. Importing Assets trong Snipe-IT](#2)

# <a name ="1"> Đồng bộ ldap từ Domain Controller Windows Server 2019 </a>

## Ở đây chúng ta sẽ chuẩn bị 2 máy 

- Máy Domain Controller - Windows Server 2019 đã thăng domain, có địa chỉ IP 192.168.17.133

![image](https://user-images.githubusercontent.com/55483458/139097614-3b267214-62b8-4ef7-b363-08cfb1a3069e.png)

Tạo OU và User trên DC

![image](https://user-images.githubusercontent.com/55483458/139176912-0b1f5ae3-f6e5-46c7-b3f5-51b7bafe11e5.png)

- Máy Snipe-IT Asset Management - Ubuntu20.04, có địa chỉ IP 192.168.17.131

![image](https://user-images.githubusercontent.com/55483458/139097955-919a5ca2-1f85-4c1b-84d1-ac50f2070fec.png)

## Đồng bộ ldap từ DC

- Vào giao diện admin của Snipe-IT => chọn LDAP

![image](https://user-images.githubusercontent.com/55483458/139225529-f9382d54-78fd-41d5-a535-f3065b061946.png)

- Chọn các mục theo tùy chọn dưới đây (Ở đây domain của mình là sv1.com)

![image](https://user-images.githubusercontent.com/55483458/139225763-14d33823-ce77-427b-bbcd-3b4c9eb4d190.png)

![image](https://user-images.githubusercontent.com/55483458/139225811-bf5ac83c-c118-4612-bb01-5823968262cc.png)

![image](https://user-images.githubusercontent.com/55483458/139225987-f67ca651-3a7d-4032-8818-6ae3f584ac8a.png)

Nhấn Save và thực hiện kiểm tra đồng bộ LDAP

![image](https://user-images.githubusercontent.com/55483458/139226098-b552a2d1-6d42-4a4d-8e7b-5cd65fd9382e.png)

Kiểm tra đăng nhập user

![image](https://user-images.githubusercontent.com/55483458/139226191-4195a888-06ee-4cb6-ad01-dcbb710a0bfe.png)

- Vào giao diện People của Snipe-IT => LDAP Sync

![image](https://user-images.githubusercontent.com/55483458/139226858-528747e9-cad5-4cd8-92ae-f38032eb2b5e.png)

![image](https://user-images.githubusercontent.com/55483458/139226949-8f16cced-13a2-4c0c-a31d-41b1a9f5e688.png)

- Chọn Synchronize, ta sẽ thấy được các user từ ldap server

![image](https://user-images.githubusercontent.com/55483458/139227243-75fc55dd-9269-4c0a-b304-8a7674114be0.png)

![image](https://user-images.githubusercontent.com/55483458/139227296-cd9f3a1b-7b67-4ee0-90da-6f7d396dbf4b.png)

# <a name ="2">2. Importing Assets trong Snipe-IT</a>

## Chúng ta sẽ tiến hành import assets từ một file CSV có sẵn

![image](https://user-images.githubusercontent.com/55483458/139231448-719a6fc2-4511-4e4c-a365-a9c3393136e3.png)

- Tiếp theo vào giao diện Import của Snipe-IT, chọn file Import

![image](https://user-images.githubusercontent.com/55483458/139231824-cbfd0833-8327-4b55-85bf-f2434287ce75.png)

![image](https://user-images.githubusercontent.com/55483458/139231882-28dfd4e0-0e28-45ce-9e32-1edf4e56b4c3.png)

![image](https://user-images.githubusercontent.com/55483458/139231900-1f432f0f-a4bb-4e6f-abc7-8dc6c340f51e.png)

- Chọn **Process** => Chọn **Asset** ở mục **Import Type**

![image](https://user-images.githubusercontent.com/55483458/139233467-c163205e-0220-4294-b0c9-25f6b1d55b7f.png)

Chúng ta sẽ thấy các asset đã được đưa vào hệ thống

![image](https://user-images.githubusercontent.com/55483458/139234137-60e58baa-f6b0-4537-a878-bdac32126014.png)

![Uploading image.png…]()












