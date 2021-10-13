<h1> KỊCH BẢN TỔNG ĐÀI </h1>

![image](https://user-images.githubusercontent.com/55483458/137066746-03b8bbc7-4170-475d-bade-14adc4f47d3a.png)

# MỤC LỤC
&ensp;[1. Thiết lập các extension number (Số máy nhánh) cho các phòng ban và tổng đài](#1)

&ensp;[2. Cài đặt nhạc chờ tự động](#2)

&ensp;[3. Thiết lập kịch bản - Người gọi ấn số để chuyển đến các phòng ban!!!](#3)

## <a name ="1">1. Thiết lập các extension number (Số máy nhánh) cho các phòng ban và tổng đài</a>

**Vào applications chọn Extensions**
![image](https://user-images.githubusercontent.com/55483458/137067290-d660e5a2-9be2-4cdf-b7b2-82267033230d.png)

![image](https://user-images.githubusercontent.com/55483458/137067394-91e33bb0-0e08-431f-bee9-8af02f1b3524.png)

**Chọn Add New SIP [chan_pjsip] Extension, tùy theo loại traffic SIP mà bạn dùng nhé**

Lưu ý:  + Giao thức pjsip sử dụng traffic SIP UDP, hoạt động trên port 5060 

        + Giao thức sip sử dụng traffic SIP TCP, hoạt động trên port 5160 
        
**Tạo các Extension theo kịch bản**

![image](https://user-images.githubusercontent.com/55483458/137124522-4ffab3f1-96f1-4140-94ac-7db86b37b148.png)

Chuyển qua thẻ Advance => Sửa nội dung của CID Num Alias (Số CID để sử dụng cho các cuộc gọi nội bộ, nếu khác với số máy nhánh. Nó được sử dụng để hóa trang thành một người dùng khác. Một ví dụ phổ biến là một nhóm những người hỗ trợ muốn CallerID nội bộ của họ hiển thị số hỗ trợ chung (một ring group hoặc một queue). Sẽ không có ảnh hưởng đến các cuộc gọi bên ngoài.

![image](https://user-images.githubusercontent.com/55483458/137125432-8d71a555-ba01-4572-8e41-efc6190a26d8.png)

**=>> Làm tương tự với các phòng ban nội bộ khác và tổng đài. Chúng ta sẽ có danh sách các extension sau **

![image](https://user-images.githubusercontent.com/55483458/137129111-7dd3ef3c-48c5-43e1-a5d5-a99571ba6e17.png)


## <a name ="2">2. Cài đặt nhạc chờ tự động</a>

**Vào thẻ Admin => Chọn System Recording. Chọn Add Recording và thiết lập tùy chọn theo các hình dưới đây**

![image](https://user-images.githubusercontent.com/55483458/137126372-2944e6d9-d2c1-4c24-9b16-d20eed89fc62.png)

![image](https://user-images.githubusercontent.com/55483458/137126970-dda82440-08d1-4955-85e7-ae3b706e00c9.png)

Bạn có thể chọn file record tùy ý, ở đây mình đã chuẩn bị sẵn một file ghi âm!!!

Chọn Submit và chờ hệ thống upload file record....

![image](https://user-images.githubusercontent.com/55483458/137127497-88869acc-21fb-41c7-bcd7-ea1fbda85783.png)

![image](https://user-images.githubusercontent.com/55483458/137127534-776a0cf8-eb8e-4281-94d7-727f58202229.png)

**Chúng ta tiến hành cấu hình IVR. Vào thẻ Applications => Chọn IVR (Hệ thống trả lời tự động) => Add IVR và thiết lập tùy chọn**

![image](https://user-images.githubusercontent.com/55483458/137127771-6a67c6ec-7bb7-46b8-a840-9fbafa58f064.png)

!!!!Ở thẻ Announcement ta chọn object đã thiết lập ở thẻ System Recording lúc trước!!!!
![image](https://user-images.githubusercontent.com/55483458/137127876-720f3cbb-d9ae-4380-982e-6f8cf258cbf0.png)

Có rất nhiều tùy chọn bạn có thể sử dụng, ví dụ như **Timeout (Khoảng thời gian chờ cuộc gọi), ALERT_INFO (Báo động thông tin cho các thiết bị SIP), Invalid Retry Recording (Nhắc phát khi nhận được phản hồi không hợp lệ / không khớp, trước khi nhắc người gọi thử lại) ,....**

**Chọn Submit để hoàn thành thiết lập**

![image](https://user-images.githubusercontent.com/55483458/137128710-3e79eeec-b75a-4caf-9d7f-9282ce53d2f2.png)

**Quay lại thẻ Extension, Bấm Edit Tổng Đài Bedu**

Chuyển qua thẻ Advanced => Thiết lập một số tùy chọn sau

![image](https://user-images.githubusercontent.com/55483458/137129687-95479a74-efa1-4cbd-962f-331f2388e140.png)

**=> Submit**

## <a name ="3">3. Thiết lập kịch bản - Người gọi ấn số để chuyển đến các phòng ban!!!</a>

**Tạo Ring Group cho 2 Tư vấn viên**
+ Vào thẻ Applications chọn Ring Groups => Add Ring Group và thiết lập theo hình dưới đây

![image](https://user-images.githubusercontent.com/55483458/137130693-bc4363f5-bc5c-4e17-89ac-d409cd7080fa.png)

***

![image](https://user-images.githubusercontent.com/55483458/137131980-f63760ce-75e6-4b90-aa3d-5e8de7faff11.png)


=> Submit

**Edit lại TONGDAIIVR trong tùy chọn IVR**

![image](https://user-images.githubusercontent.com/55483458/137130988-471535c8-6184-4ab0-8486-a95d89ade979.png)

**Chỉnh sửa 2 tư vấn viên 1021 và 1022 trong tùy chọn Extensions như sau (Trong thẻ Advanced)**

![image](https://user-images.githubusercontent.com/55483458/137131639-467be5c8-2eca-4f4f-b5a4-3c2ebe7bc81b.png)

=> Submit

**Chọn APPLY CONFIG để lưu lại cấu hình**
![image](https://user-images.githubusercontent.com/55483458/137132716-fdea9317-2f14-4ea0-9b2e-0600e037abc7.png)


## Vậy là chúng ta đã hoàn thành cấu hình tổng đài theo kịch bản đề ra :D Việc còn lại là sử dụng điện thoại để test thôi (Bạn có thể sử dụng softphone nhé :D) 
## Chúc bạn may mắn!!!






        
        
        
        
        
        
        
