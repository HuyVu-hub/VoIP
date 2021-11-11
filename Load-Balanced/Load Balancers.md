### Load Balancers

- cân bằng tải
- điều phối giao thông
- tính khả dụng cao
- Bảo vệ
- càng đơn giản càng tốt

![image](https://user-images.githubusercontent.com/69178270/141262482-ea1e4b09-3188-4698-94ae-db5587a7d247.png)

![image](https://user-images.githubusercontent.com/69178270/141262822-3ea8b301-ca32-4a2c-8caf-bd696e71837a.png)

![image](https://user-images.githubusercontent.com/69178270/141262866-0efe9e3a-1cdc-48d7-8fa0-00a052e5ce31.png)

![image](https://user-images.githubusercontent.com/69178270/141262931-7ebafa24-2ace-455a-ac05-8cc1475afe69.png)

**Mô-đun điều phối - Cân bằng tải**

_Thuật toán điều động:_ tính giá trị số nguyên bằng hàm băm

- hash over callid: đảm bảo rằng tất cả các yêu cầu trong hộp thoại đều chuyển đến cùng một hộp
- hash over from uri: đảm bảo rằng tất cả các yêu cầu từ cùng một người dùng sẽ chuyển đến cùng một hộp
- hash over to uri: đảm bảo rằng các đăng ký AoR đi đến cùng một hộp
- hash over request-uri: đảm bảo rằng các yêu cầu đến cùng một đích được xử lý bởi
cùng một hộp
- hash over config variable: cho những nhu cầu khác nhau

_Thuật toán cân bằng_

- call load distribution: đếm các cuộc gọi đang hoạt động trên mỗi địa chỉ đích
- weight based distribution: mỗi địa chỉ đích đã chỉ định một trọng số
(tỷ lệ phần trăm) đối với nó
- priority based distribution: mỗi địa chỉ đích đã chỉ định một mức độ ưu tiên cho nó
(thực tế cũng giống như quá trình fork nối tiếp)
- round-robin (next destination): khuyến khích, thuật toán nổi tiếng với phân phối khá công bằng

**Tệp đích của người điều phối**

![image](https://user-images.githubusercontent.com/69178270/141263741-b4f89f39-b8d9-4834-819f-c99b60043a1c.png)

**Cấu hình điều phối viên**

![image](https://user-images.githubusercontent.com/69178270/141263799-8f4620aa-0015-4bf9-99c5-98f778d318f6.png)

```
# Dispatch requests
route[DISPATCH] {
# round robin dispatching on gateways group '1'
if(!ds_select_dst("1", "4"))
{
! ! send_reply("404", "No destination");
! ! exit;
}
xlog("L_DBG", "--- SCRIPT: going to <$ru> via <$du>\n");
t_on_failure("RTF_DISPATCH");
t_relay();
 exit;
}
# Failure re-route
failure_route[RTF_DISPATCH] {
if (t_is_canceled()) {
! ! exit;
}
# next DST - only for 500 or local timeout
if (t_check_status("500")
!!! or (t_branch_timeout() and !t_branch_replied()))
{
! ! if(ds_next_dst())
! ! {
!!! t_on_failure("RTF_DISPATCH");
!!! t_relay();
!!! exit;
! ! }
}
}
```

**Mô-đun điều phối - Tính năng**

 - tiện ích mở rộng đơn giản, nhẹ - nhanh chóng và đáng tin cậy
 - có thể kết hợp các thuật toán cân bằng tải và điều phối
nếu cần trong cùng một tệp cấu hình
 - có thể làm việc với tệp văn bản phẳng hoặc cơ sở dữ liệu để tải định tuyến
Hồ sơ
 - có thể tải lại các bản ghi định tuyến trong thời gian chạy (không cần khởi động lại)
 - có thể tự động phát hiện các điểm đến ngoài dịch vụ
 - có thể ping đích để tự động tắt / kích hoạt
 - mang lại tính linh hoạt cao trong tệp cấu hình
 - tuyến sự kiện, quyền truy cập vào các thuộc tính và danh sách các điểm đến
 - có thể phát hiện xem lưu lượng truy cập có đến từ một địa chỉ trong
danh sách điểm đến

**Mở rộng quy mô cân bằng tải**
Cấu hình điều phối viên - Chuyển tiếp trạng thái

```
# Dispatch requests
route[DISPATCH] {
# round robin dispatching on gateways group '1'
if(!ds_select_dst("1", "4"))
{
! ! send_reply("404", "No destination");
! ! exit;
}
xlog("L_DBG", "--- SCRIPT: going to <$ru> via <$du>\n");
t_on_failure("RTF_DISPATCH");
t_relay();
 exit;
}
# Failure re-route
failure_route[RTF_DISPATCH] {
if (t_is_canceled()) {
! ! exit;
}
# next DST - only for 500 or local timeout
if (t_check_status("500")
!!! or (t_branch_timeout() and !t_branch_replied()))
{
! ! if(ds_next_dst())
! ! {
!!! t_on_failure("RTF_DISPATCH");
!!! t_relay();
!!! exit;
! ! }
}
}
```

Cấu hình điều phối viên - Chuyển tiếp không trạng thái

```
# Dispatch requests
route[DISPATCH] {
# round robin dispatching on gateways group '1'
if(!ds_select_dst("1", "4"))
{
! ! send_reply("404", "No destination");
! ! exit;
}
xlog("L_DBG", "--- SCRIPT: going to <$ru> via <$du>\n");
forward();
 exit;
}
```

![image](https://user-images.githubusercontent.com/69178270/141264620-bb68e9eb-0e9e-485c-be4d-ac276498475f.png)

**Cân bằng tải DNS SRV**

```
_sip._udp.kamailio.com. IN SRV   10 1 5060 sip1.kamailio.com.
_sip._udp.kamailio.com. IN SRV   10 1 5065 sip2.kamailio.com.
_sip._udp.kamailio.com. IN SRV   10 1 6065 sip3.kamailio.com.
```

![image](https://user-images.githubusercontent.com/69178270/141264719-ed5ed676-560b-4271-ab49-f69a34dd5cc9.png)

**Cân bằng tải thành / dev / null**

```
request_route {
 ;
}
```

**Cân bằng tải cho một máy chủ**

```
request_route {
 forward(1.2.3.4);
}
```

```
request_route {
 rewritehostport(“1.2.3.4”);
 forward();
}
```

**Bộ cân bằng tải Round Robin không trạng thái**

Per Process Round Robin Routing! (Định tuyến vòng quanh mỗi quy trình!)

```
loadmodule “sl.so”
loadmodule “textops.so”
loadmodule “pv.so”
modparam("pv", "varset", "i=i:0")
request_route {
 if(!is_method(“INVITE”)) {
 sl_send_reply(“404”, “Not Found”);
 exit;
 }
 $var(i) = ($var(i) + 1 ) mod 2;
 if($var(i)==1) {
 rewritehostport(“1.2.3.4”);
 } else {
 rewritehostport(“2.3.4.5”);
 }
 forward();
}
```

**Cấu hình không trạng thái Round Robin Redirect Balancer**

```
loadmodule “sl.so”
loadmodule “textops.so”
loadmodule “pv.so”
modparam("pv", "varset", "i=i:0")
request_route {
 if(!is_method(“INVITE”)) {
 sl_send_reply(“404”, “Not Found”);
 exit;
 }
 $var(i) = ($var(i) + 1 ) mod 2;
 if($var(i)==1) {
 rewritehostport(“1.2.3.4”);
 } else {
 rewritehostport(“2.3.4.5”);
 }
 sl_send_reply(“301”, “Moved Temporarily”);
}
```

**Bộ cân bằng tải Round Robin không trạng thái**

```
loadmodule “sl.so”
loadmodule “textops.so”
loadmodule “pv.so”
loadmodule “cfgutils.so”
modparam("pv", "shvset", "i=i:0")
modparam("cfgutils", "lock_set_size", 1)
request_route {
 if(!is_method(“INVITE”)) {
 sl_send_reply(“404”, “Not Found”);
 exit;
 }
 lock(“balancing”);
 $shv(i) = ($shv(i) + 1 ) mod 2;
 $var(x) = $shv(i);
 unlock(“balancing”);
 if($var(x)==1) {
 rewritehostport(“1.2.3.4”);
 } else {
 rewritehostport(“2.3.4.5”);
 }
 forward();
}
```

**Khả năng mở rộng - Phân vùng**

![image](https://user-images.githubusercontent.com/69178270/141265509-fe5c0360-f63e-49ca-9724-38276ed48b5b.png)

**Khả năng mở rộng**

![image](https://user-images.githubusercontent.com/69178270/141265593-0a7fc649-6d63-4925-9d1f-6365cedccd64.png)

![image](https://user-images.githubusercontent.com/69178270/141265640-5195d573-bbb6-401b-b0cd-241bac069467.png)


