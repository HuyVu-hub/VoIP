# Cài đặt SIP Trunking trên FusionPBX với SIP Proxy Websipte: https://serweb.iptel.org/

# Mục lục

&ensp;[1. Chuẩn bị](#1)

&ensp;[2. Tiến hành cài đặt](#2)

&ensp;[3. Triển khai softphone](#3)

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

  - Bước 1: Cấu hình Gateway cho FusionPBX

Vào tùy chọn Accounts => Gateways

![image](https://user-images.githubusercontent.com/55483458/143052463-126a439b-9ed5-4b8c-a519-b2ab2d5e1b1c.png)

Lưu ý: Ở username và Password chúng ta điền tên tài khoản và mật khẩu đăng nhập của tài khoản iptel.org A (dinhnamkhoa)

![image](https://user-images.githubusercontent.com/55483458/143052932-c86e6270-65b2-4d73-8600-211a36d8eeaf.png)

  - Bước 2: Cấu hình Outbound Route

Vào tùy chọn Dialplan => Outbound Routes

![image](https://user-images.githubusercontent.com/55483458/143053080-91722abf-4c15-4774-b9dc-15c85dc44cb8.png)

Chọn Add và thiết lập như sau:

![image](https://user-images.githubusercontent.com/55483458/143053497-416348d5-7819-4d9c-8d09-4baad201295f.png)

![image](https://user-images.githubusercontent.com/55483458/143053598-62e612a7-7cfe-41d0-83a7-6b22d80d906a.png)

  - Bước 3: Cấu hình Inbound Routes

Vào Dialplan => Destinations => Add và thiết lập như sau:

![image](https://user-images.githubusercontent.com/55483458/143062450-1adf56be-0483-441d-a2f0-72cc1ed5765e.png)

![image](https://user-images.githubusercontent.com/55483458/143062637-4a597e90-0f66-4110-af00-51a5c04e67cb.png)

*Lưu ý ở Action chúng ta lựa chọn số máy nhánh để hệ thống chuyển cuộc gọi đến và ở Destination ta nhập username của tài khoản iptel.org A (dinhnamkhoa)!!!*

Tiếp theo ta vào tùy chọn Dialplan => Inbound Routes => Add:

![image](https://user-images.githubusercontent.com/55483458/143062791-0d63f328-2f75-4954-b905-06c7cee1917e.png)

## <a name ="3">3. Triển khai softphone</a>

  - Bước 1: Tiến hành đăng nhập tài khoản iptel.org B (namkhoa123123) trên softphone

![image](https://user-images.githubusercontent.com/55483458/143063508-82cfb8ea-fc63-4e73-a342-45fe4dd9af0a.png)

  - Bước 2: Đăng nhập số máy nhánh cho User và Lễ Tân trên Softphone

![image](https://user-images.githubusercontent.com/55483458/143064273-be4fbb0c-fc96-439e-abd6-82cea2099d00.png)

![image](https://user-images.githubusercontent.com/55483458/143064356-4809e896-de57-4056-8903-93081fa36505.png)

![image](https://user-images.githubusercontent.com/55483458/143064406-12467f73-bcc5-4114-b2fd-94d59dbea0b4.png)

  - Bước 3: User gọi đến số 99XXXXXX (XXXXXX là id iptel.org của tài khoản B, ở đây là 553990)

![image](https://user-images.githubusercontent.com/55483458/143064866-72e4b2a0-0faa-4c35-af9d-76730f3cf4a1.png)

![image](https://user-images.githubusercontent.com/55483458/143064976-55652968-6425-48bd-8d05-714a24f20696.png)

**=>  Từ bên tài khoản iptel.org B nhận được cuộc gọi từ người dùng nội bộ trong hệ thống FusionPBX**

  - Bước 4: Tài khoản iptel.org B (namkhoa123123) gọi tới id của Tài khoản iptel.org A (dinhnamkhoa) là 591460

![image](https://user-images.githubusercontent.com/55483458/143067045-04c6d30f-13a7-464b-ac03-36d3b97725d2.png)

Ta sẽ thấy được số máy nhánh 1000 của Lễ Tân nhận được cuộc gọi từ tài khoản iptel B:

![image](https://user-images.githubusercontent.com/55483458/143067708-6dad8a4f-2c68-42b8-972a-4a84eda87837.png)




















