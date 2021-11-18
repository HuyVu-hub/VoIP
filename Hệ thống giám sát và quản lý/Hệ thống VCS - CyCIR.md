### Giải Pháp Điều Phối, Tự Động Phản Ứng An Ninh Thông Tin Tập Trung VCS - CyCIR (SOAR)

VCS - CyCIR là giải pháp điều phối, tự động hóa phản ứng an ninh thông tin tập trung (SOAR - Security Orchestration, Automation and Response) giúp xác định, ưu tiên và tiêu chuẩn hóa cho các chức năng ứng phó sự cố.

Được xây dựng dựa trên công nghệ tư động hóa thông qua việc tích hợp với các công nghệ bảo mật, CNTT theo các kịch bản xử lý (playbook) được định nghĩa động, VCS-CyCir giúp tổ chức đạt được mục tiêu tối ưu hóa hiệu quả trong quá trình quản lý và vận hành các hệ thống ATTT

![image](https://user-images.githubusercontent.com/69178270/142329885-b146806c-6731-4f19-aec7-c641d0f3aa90.png)

**Sự cần thiết của giải pháp SOAR**

Trong bối cảnh công nghệ thông tin (CNTT) ngày càng phát triển, các cuộc tấn công mạng đang trở nên ngày một đa dạng về quy mô và mục đích dẫn tới thiệt hại do các sự cố an ninh mạng tăng đều qua các báo cáo hàng năm.

Số cuộc tấn công mạng tăng cao dẫn đến khối lượng công việc của đội ngũ chuyên trách về an toàn thông tin (ATTT) cho mỗi đơn vị cũng tăng theo tỷ lệ thuận dẫn đến tình trạng quá tải về nguồn lực, kéo theo sự giảm sút về chất lượng công việc.

Bên cạnh đó, việc giám sát và phản ứng ATTT trên nhiều công cụ, giải pháp riêng lẻ cũng khiến cho các tổ chức phải đối mặt với những thách thức khác như:

 - Các giải pháp hoạt động độc lập, thiếu sự gắn kết dẫn tới quá nhiều cảnh báo trùng lặp cho cùng một đối tượng.
 - Thiếu tầm nhìn xuyên suốt trong quá trình điều tra, phản ứng.
 - Quá nhiều công đoạn xử lý thủ công, gây ảnh hưởng đến chất lượng công việc.
 - Thiếu hụt nhân sự có kỹ năng để vận hành, làm chủ các công cụ về

Các thách thức trên dẫn đến nhu cầu tất yếu của các tổ chức để tìm kiếm 1 nền tảng giúp nâng cao hiệu quả của quá trình vận hành, phản ứng sự cố ATTT.

**Những tính năng của dịch vụ**

_1. Điều phối các giải phá ATTT_

VCS-CyCir cho phép các công cụ bảo mật riêng biệt phối hợp chặt chẽ với nhau để nâng cao năng suất làm việc trong các quy trình bảo mật phức tạp. Bên cạnh việc hỗ trợ sẵn nhiều công cụ và kịch bản (playbook) phổ biến, VCS-CyCir cũng cung cấp khả năng tùy biến để tích hợp các công nghệ bảo mật và phát triển các playbook theo nhu cầu của tổ chức.

_2. Tự động hóa vận hành ATTT_

Workflow engine được tích hợp trong VCS-CyCir nhằm cung cấp khả năng thực hiện tự động hóa chuỗi các hành động theo kịch bản định nghĩa chỉ trong vài giây, so với hàng giờ khi thực hiện thủ công. Điều này giúp giảm công sức thực hiện các công việc lặp đi lặp lại để nâng cao hiệu năng công tác phản ứng sự cố.
Engine này cũng cho phép người dùng theo dõi và can thiệp vào luồng  xử lý tự động khi cần thiết.

Bên cạnh đó, VCS-CyCir cũng cung cấp giao diện trực quan giúp người dùng xây dựng các playbook, sử dụng giao diện hỗ trợ sẵn có hoặc định nghĩa playbook mới bằng ngôn ngữ Python.

_3. Quản lý sự cố và phối hợp vận hành_

VCS-CyCir lưu vết tất cả thông tin trong quá trình điều tra, phản ứng và tổng hợp, chủ động cung cấp cho chuyên gia phân tích trên một giao diện quản trị tập trung duy nhất, giúp chuyên gia có được góc nhìn toàn diện về sự cố, rút ngắn thời gian phân tích, ra quyết định để phản ứng hiệu quả với sự cố. Các thông tin quản lý bao gồm:

 - **Phối hợp vận hành:** Các thành viên trong nhóm có thể dễ dàng tương tác về các vấn đề cụ thể trong từng trường hợp để nhanh chóng đưa ra các quyết định hoặc phân công công việc đến người phù hợp.

 - **Quản lý thông tin tình báo nguy cơ ATTT:** Với việc tích hợp với các nền tảng tình báo an ninh thông tin, VCS-CyCir quản lý và cung cấp các thông tin tình báo nguy cơ liên quan đến sự cố theo cách chủ động, trực quan nhất cho chuyên gia phân tích, giúp tối ưu hóa quá trình phân tích và xử lý sự cố.

 - **Hỗ trợ công tác điều tra, truy vết:** VCS-CyCir cung cấp cho người dùng bộ công cụ tối ưu, thuận tiện nhất phục vụ cho quá trình điều tra, truy vết tấn công mạng.

_4. Dashboard và báo cáo ATTT_

VCS-CyCir hỗ trợ các công cụ trích xuất ra các báo cáo và dashboard chuyên biệt cho cả 3 lớp người dùng của tổ chức: Chuyên gia phân tích, SOC Manager và Giám đốc An ninh thông tin (CISO).

Tất cả các sự kiện, hành động được lưu trữ giúp cho tổ chức đo lường được hiệu quả của đôi ngũ vận hành SOC theo nhiều góc nhìn khác nhau.

![image](https://user-images.githubusercontent.com/69178270/142331742-e0400401-780d-49f2-90bc-c91af657e080.png)

**Mô Hình Triển Khai và Vận Hành VCS-CYCIR**

![image](https://user-images.githubusercontent.com/69178270/142331765-23317796-1a00-46d6-8688-e6638e994a89.png)

Trong đó:

 - **Tầng Data Source:** Bao gồm các giải pháp, các API đóng vai trò cung cấp các cảnh báo đầu vào cho hệ thống VCS-CyCir.

 - **3rd Party Products:** Là các công nghệ, giải pháp ATTT được tích hợp với VCS-CyCir, hỗ trợ làm giàu dữ liệu trong quá trình điều tra, phân tích.

 Đồng thời đưa ra hành động cụ thể cho các giải pháp ATTT khác trong hệ thống để thực hiện ứng phó khi có sự cố ATTT.

**· VCS-CyCir Core Engine:**

 - **Workflow:** Định nghĩa và tự động hóa các quy trình vận hành
 - **Orchestration:** Tích hợp, điều phối các công nghệ bảo mật làm việc với
 - **Automation:** Tự động hóa các tác vụ thủ công trong quá trình vận hành

