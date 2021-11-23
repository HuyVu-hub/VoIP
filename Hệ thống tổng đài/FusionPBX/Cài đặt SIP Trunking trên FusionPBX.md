# Cài đặt SIP Trunking trên FusionPBX với SIP Proxy Websipte: https://serweb.iptel.org/

# Mục lục

&ensp;[1. Chuẩn bị](#1)
&ensp;[2. Tiến hành cài đặt](#2)

## <a name="1">1. Chuẩn bị</a>

  - 1 máy chủ FusionPBX
  - Thiết lập số máy nhanh (Extension) cho user và lễ tân như sau:

![image](https://user-images.githubusercontent.com/55483458/143051218-426df115-dcef-45fc-88c0-4807472073cc.png)

  - 2 tài khoản iptel.org đã được kích hoạt:
    
    + Tài khoản A (dinhnamkhoa): dùng làm SIP Trunking
    + Tài khoản B (namkhoa123123): login vào softphone

![image](https://user-images.githubusercontent.com/55483458/143050317-c2936aa9-98a3-4966-a117-99d426279165.png)

![image](https://user-images.githubusercontent.com/55483458/143050349-8f8fa501-a3be-4e38-ba3d-c634858b5d0e.png)

  - Kịch bản:

    + Outbound: Khi người dùng nhấn số 99XXXXXX (Với XXXXXX là id iptel.org của tài khoản B), tổng đài sẽ chuyển cuộc gọi tới tài khoản B.
    + Inbound: Khi tài khoản B gọi tới tài khoản A, tổng đài sẽ chuyển cuộc gọi đến số máy nhanh (Extension) của lễ tân là 1000.

## <a name="2">2. Tiến hành cài đặt</a>











