# Windows-Forensics-1

Bài tìm hiểu này bắt nguồn từ đây: https://tryhackme.com/room/windowsforensics1

Task1: Introduction to Computer Forensics for Windows

Pháp y máy tính hay còn gọi là điều tra số là một lĩnh vực thiết yếu của an ninh mạng liên quan đến việc thu thập bằng chứng về các hoạt động được thực hiện trên máy tính. Đây là một phần của lĩnh vực Pháp y kỹ thuật số rộng lớn hơn, liên quan đến phân tích pháp y của tất cả các loại thiết bị kỹ thuật số, bao gồm khôi phục, kiểm tra và phân tích dữ liệu được tìm thấy trong các thiết bị kỹ thuật số. Các ứng dụng của pháp y kỹ thuật số và máy tính có phạm vi rộng lớn, từ lĩnh vực pháp lý, nơi nó được sử dụng để hỗ trợ hoặc bác bỏ một giả thuyết trong một vụ án dân sự hoặc hình sự, đến lĩnh vực tư nhân, nơi nó giúp ích trong việc điều tra nội bộ công ty và sự cố và xâm nhập phân tích.

Microsoft Windows nói chung là Hệ điều hành máy tính để bàn được sử dụng nhiều nhất hiện nay. Người dùng cá nhân và Doanh nghiệp thích nó hơn và nó hiện đang chiếm khoảng 80% thị phần Máy tính để bàn. Điều này có nghĩa là điều quan trọng là phải biết cách thực hiện phân tích pháp y trên Microsoft Windows cho một người quan tâm đến Pháp y kỹ thuật số. Trong mô-đun này, chúng ta sẽ tìm hiểu về các cách khác nhau mà chúng ta có thể thu thập dữ liệu pháp y từ Windows Registry và đưa ra kết luận về hoạt động được thực hiện trên hệ thống Windows dựa trên dữ liệu này.

Nói tóm lại là windows chiếm phần lớn nên việc điều tra số trên win là quan trọng và trong bài này sẽ tập trung tìm hiểu về Windows Registry

Task 2: Windows Registry and Forensics

Windows Registry là một tập hợp các cơ sở dữ liệu có chứa dữ liệu cấu hình của hệ thống. Dữ liệu cấu hình này có thể là về phần cứng, phần mềm hoặc thông tin của người dùng. Nó cũng bao gồm dữ liệu về các tệp, chương trình được sử dụng gần đây hoặc các thiết bị được kết nối với hệ thống. 

Như bạn có thể hiểu, dữ liệu này có lợi từ quan điểm điều tra số. Trong bài nàynày, chúng ta sẽ học cách đọc dữ liệu này để xác định thông tin cần thiết về hệ thống. Bạn có thể xem registry bằng regedit.exe, một tiện ích Windows tích hợp để xem và chỉnh sửa registry. Chúng ta sẽ khám phá các công cụ khác để tìm hiểu về registry trong các bài sắp tới.


Cấu trúc của Registry:

Registry trong bất kỳ hệ thống Windows nào đều chứa 5 root keys sau :

1. HKEY_CURRENT_USER
2. HKEY_HKEY_USERS
3. HKEY_LOCAL_MACHINE
4. HKEY_CLASSES_ROOT
5. HKEY_CURRENT_CONFIG

Bạn có thể xem các phím này khi mở tiện ích. Để mở trình registry editor, hãy nhấn đồng thời phím Windows và phím R. Nó sẽ mở ra một lời nhắc giống như sau:

![image](https://user-images.githubusercontent.com/72652376/178678381-44d73d4c-6fd9-438c-ac29-03fb8ad48aef.png)

Tại đây, bạn có thể thấy các root keys trong ngăn bên trái trong chế độ xem dạng cây hiển thị các registry keys được bao gồm và các giá trị trong khóa đã chọn được hiển thị trong ngăn bên phải. Bạn có thể nhấp chuột phải vào giá trị được hiển thị trong ngăn bên phải và chọn thuộc tính để xem các thuộc tính của giá trị này.

Đây là cách Microsoft định nghĩa từng root keys này. Để biết thêm chi tiết và thông tin về các khóa đăng ký Windows sau, vui lòng truy cập tài liệu của Microsoft (Link đây https://docs.microsoft.com/en-US/troubleshoot/windows-server/performance/windows-registry-advanced-users)

|       Tên Keys       |      Mô tả        |
| :------------:|:-------------:|
|    **HKEY_CURRENT_USER**	          |     Chứa gốc của thông tin cấu hình cho người dùng hiện đang đăng nhập. Thư mục, màu màn hình và cài đặt Control Panelcủa người dùng được lưu trữ tại đây. Thông tin này được liên kết với hồ sơ của người dùng. Khóa này đôi khi được viết tắt là **HKCU**.         |
|     **HKEY_USERS**         | Chứa tất cả các hồ sơ người dùng được tải trên máy tính. HKEY_CURRENT_USER là một khóa con của HKEY_USERS. HKEY_USERS đôi khi được viết tắt là HKU.    |
|    **HKEY_LOCAL_MACHINE**	        |Chứa thông tin cấu hình cụ thể cho máy tính (cho bất kỳ người dùng nào). Khóa này đôi khi được viết tắt là HKLM.|
|**HKEY_CLASSES_ROOT**|Là một khóa con. Thông tin được lưu trữ ở đây đảm bảo rằng chương trình chính xác sẽ mở khi bạn mở tệp bằng Windows Explorer. Khóa này đôi khi được viết tắt là **HKCR.HKEY_LOCAL_MACHINE\Software** .|
|    **HKEY_CURRENT_CONFIG**	 |Chứa thông tin về cấu hình phần cứng được máy tính cục bộ sử dụng khi khởi động hệ thống.|
|    **HKEY_LOCAL_MACHINE**	        |Chứa thông tin cấu hình cụ thể cho máy tính (cho bất kỳ người dùng nào). Khóa này đôi khi được viết tắt là **HKLM**.|


Task 3:

Nếu bạn đang truy cập một hệ thống đang hoạt động, bạn sẽ có thể truy cập registry bằng cách sử dụng regedit.exe và bạn sẽ tìm kiếm được tất cả các root keys tiêu chuẩn mà chúng ta đã tìm hiểu trong Task 2. Tuy nhiên, nếu bạn chỉ có quyền truy cập vào image, bạn phải biết registry nằm ở đâu trên image. Phần lớn các hives này nằm trong thư mục và là:**C:\Windows\System32\Config**

NOTE : hives hiểu đơn giản là tập hợp các registry key xuất hiện dưới dạng các thư mục ở phía bên trái màn hình, khi tất cả các key khác đã được thu nhỏ. Nó tương ứng với lệnh hivelist trong volatility. Cho nên ta cứ gọi là hives cho nó chuyên nghiệp

1. DEFAULT (mounted on HKEY_USERS\DEFAULT)
2. SAM (mounted on HKEY_LOCAL_MACHINE\SAM)
3. SECURITY (mounted on HKEY_LOCAL_MACHINE\Security)
4. SOFTWARE (mounted on HKEY_LOCAL_MACHINE\Software)
5. SYSTEM (mounted on HKEY_LOCAL_MACHINE\System)

Hives chứa thông tin người dùng :

Ngoài những Hives này, hai Hives khác chứa thông tin người dùng có thể được tìm thấy trong thư mục Hồ sơ người dùng. Đối với Windows 7 trở lên, thư mục hồ sơ của người dùng được đặt tại nơi có hives: C:\Users\<username>\

1. NTUSER.DAT (mounted on HKEY_CURRENT_USER khi một người dùng thực hiện đăng nhập)
2. USRCLASS.DAT (mounted on HKEY_CURRENT_USER\Software\CLASSES)

USRCLASS.DAT nằm trong thư mục : C:\Users\<username>\AppData\Local\Microsoft\Windows

![image](https://user-images.githubusercontent.com/72652376/178684679-832af9ed-900f-4bb5-a1bc-96c1d21ec756.png)

NTUSER.DAT nằm trong thư mục :C:\Users\<username>\

![image](https://user-images.githubusercontent.com/72652376/178685380-79474aaa-d5eb-4b79-bb90-b74a1306d94d.png)

Và lưu ý 2 tệp này là các tệp ẩn
