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

The Amcache Hive:

Ngoài những tệp trên, có hive rất quan trọng khác được gọi là Amcache Hive. Windows tạo hive này để lưu thông tin về các chương trình đã chạy gần đây trên hệ thống.
Hive ở : C:\Windows\AppCompat\Programs\Amcache.hve

Transaction Logs and Backups:

Một số nguồn dữ liệu pháp y rất quan trọng khác là các bản sao lưu và nhật ký giao dịch đăng ký. Các bản ghi giao dịch có thể được coi là nhật ký thay đổi của tổ chức đăng ký (transaction logs and backups). Windows thường sử dụng nhật ký giao dịch (transaction logs )khi ghi dữ liệu vào registry hive. Điều này có nghĩa là các bản ghi giao dịch (transaction logs) thường có thể có những thay đổi mới nhất trong registry mà chưa đến được với registry hives. Nhật ký giao dịch cho mỗi hive được lưu trữ dưới dạng tệp .LOG trong cùng thư mục với hive  đó. Nó có cùng tên với registry hive, nhưng phần mở rộng là .LOG. 

Ví dụ: transaction log cho hive SAM sẽ được đặt trong tên tệp SAM.LOG. Đôi khi có thể có nhiều nhật ký giao dịch. Trong trường hợp đó, chúng sẽ có .LOG1, .LOG2, v.v., làm phần mở rộng của chúng. Cần thận trọng khi xem xét nhật ký giao dịch khi thực hiện pháp y đăng ký C:\Windows\System32\Config

Registry backups ngược lại với Transaction logs. Đây là những bản sao lưu của registry hives nằm trong thư mục. Các hives này được sao chép vào danh mục mỗi 10 ngày. Nó có thể là một nơi tuyệt vời để xem xét nếu bạn nghi ngờ rằng một số registry keys có thể đã bị deleted/modified ( xóa/sửa đổi ) gần đây.
C:\Windows\System32\Config
C:\Windows\System32\Config\RegBack


Task4 :  Data Acquisition

Phần này nói về việc bạn thực hiện thu thập dữ liệu trên 1 image thì nên sử dụng công cụ gì cho thích hợp. về cơ bản thì các công cụ đều tương tự như nhau nhưng có 2 công cụ nổi bật là Autopsy,  FTK Imager (do tôi hay dùng cái đó :v)

Task 5:  Exploring Windows Registry

Sau khi quá trình thu thập dữ liệu hoàn thành, thì ta cần một công cụ để có thể đọc được các dữ liệu đó (trong trường hợp này là các Registry)

Một công cụ khá phổ biến là Zimmerman's Registry Explorer:
Link: https://ericzimmerman.github.io/#!index.md

Task 6 :System Information and System Accounts

Phần này bắt đầu vào quá trình phân tích sau khi giới thiệu các công cụ thực hiện thu thập và đọc các dữ liệu như thế nào

Bây giờ chúng ta đã học cách đọc registry data, hãy cùng tìm hiểu xem nơi đăng ký để thực hiện phân tích pháp y của chúng ta.

Khi chúng tôi bắt đầu thực hiện phân tích pháp y, bước đầu tiên là tìm hiểu về thông tin hệ thống. Nhiệm vụ này sẽ bao gồm việc thu thập thông tin liên quan đến hệ thống và thông tin tài khoản của máy.

OS Version
Nếu chúng tôi chỉ có dữ liệu phân loại để thực hiện pháp y, chúng tôi có thể xác định phiên bản hệ điều hành mà từ đó dữ liệu này được lấy qua registry. Để tìm phiên bản hệ điều hành, chúng tôi có thể sử dụng registry key: SOFTWARE\Microsoft\Windows NT\CurrentVersion

![image](https://user-images.githubusercontent.com/72652376/178690448-2a0803c0-bfa4-401d-bfd1-d6e02bfed852.png)

Current control set:

Hives chứa dữ liệu cấu hình của máy được sử dụng để điều khiển khởi động hệ thống được gọi là Bộ điều khiển (Control Sets). Thông thường, chúng ta sẽ thấy hai Bộ điều khiển, ControlSet001 và ControlSet002, trong hive SYSTEM trên một máy. Trong hầu hết các trường hợp, ControlSet001 sẽ trỏ đến Control Set mà máy khởi động bằng và ControlSet002 sẽ là cấu hình. 
Vị trí của nó sẽ là:

SYSTEM\ControlSet001

SYSTEM\ControlSet002

Windows tạo Control Set khi máy đang hoạt động, được gọi là CurrentControlSet(). Để có được thông tin hệ thống chính xác nhất, đây là hive mà chúng tôi sẽ tham khảo. Chúng tôi có thể tìm ra Control Set nào đang được sử dụng làm Bộ điều khiển hiện tại bằng cách xem giá trị registry sau: HKLM\SYSTEM\CurrentControlSet

Tương tự, cấu hình có thể được tìm thấy bằng cách sử dụng giá trị registry sau: last known good

Computer Name:
Điều quan trọng là phải thiết lập Computer Name trong khi thực hiện phân tích pháp y để đảm bảo rằng chúng tôi đang làm việc trên máy mà chúng tôi phải làm việc. Chúng ta có thể tìm thấy Computer Name từ vị trí sau: SYSTEM\CurrentControlSet\Control\ComputerName\ComputerName

![image](https://user-images.githubusercontent.com/72652376/178692518-5c6cd7cb-d88d-4025-b3ab-b07c530460a9.png)

Time Zone Information:

Để chính xác, điều quan trọng là phải thiết lập múi giờ của máy tính. Điều này sẽ giúp chúng tôi hiểu được trình tự thời gian của các sự kiện khi chúng xảy ra. Để tìm Thông tin múi giờ, chúng ta có thể xem vị trí sau: SYSTEM\CurrentControlSet\Control\TimeZoneInformation

![image](https://user-images.githubusercontent.com/72652376/178692682-f9efbc9d-9c22-4883-9083-d402a53bfab6.png)

Thông tin về múi giờ rất quan trọng vì một số dữ liệu trong máy tính sẽ có dấu thời gian theo UTC / GMT và những dữ liệu khác trong múi giờ địa phương. Kiến thức về múi giờ địa phương giúp thiết lập tiến trình khi hợp nhất dữ liệu từ tất cả các nguồn.

Network Interfaces and Past Networks:

Registry key sau sẽ cung cấp danh sách các giao diện mạng trên máy mà chúng tôi đang điều tra:
SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces

Các thông tin có thể có:
![image](https://user-images.githubusercontent.com/72652376/178693169-7a3507b8-3603-47e5-8846-6fc53b2ec7b3.png)

Mỗi Giao diện được đại diện bằng một khóa con định danh duy nhất (GUID), chứa các giá trị liên quan đến cấu hình TCP / IP của giao diện. Khóa này sẽ cung cấp cho chúng tôi thông tin như địa chỉ IP, địa chỉ IP DHCP và Mặt nạ mạng con, Máy chủ DNS, v.v. Thông tin này rất quan trọng vì nó giúp bạn đảm bảo rằng bạn đang thực hiện pháp y trên máy mà bạn phải thực hiện.

Các mạng trước đây mà một máy nhất định đã kết nối có thể được tìm thấy ở các vị trí sau:

SOFTWARE\Microsoft\Windows NT\CurrentVersion\NetworkList\Signatures\Unmanaged

SOFTWARE\Microsoft\Windows NT\CurrentVersion\NetworkList\Signatures\Managed

Các registry keys này chứa các mạng trong quá khứ cũng như lần cuối cùng chúng được kết nối. Thời gian ghi cuối cùng của registry keys chỉ thời gian cuối cùng các mạng này được kết nối.
![image](https://user-images.githubusercontent.com/72652376/178693474-768d0d8a-479f-43fa-b6a0-02b634ac5421.png)

Autostart Programs (Autoruns):

Các registry keys sau bao gồm thông tin về các chương trình hoặc lệnh chạy khi người dùng đăng nhập.

NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Run

NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\RunOnce

SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce

SOFTWARE\Microsoft\Windows\CurrentVersion\policies\Explorer\Run

SOFTWARE\Microsoft\Windows\CurrentVersion\Run

registry key sau chứa thông tin về các dịch vụ: SYSTEM\CurrentControlSet\Services

Lưu ý: Giá trị của phím Start trong ảnh chụp màn hình bên dưới.

![image](https://user-images.githubusercontent.com/72652376/178693988-1e882b41-846d-4e3c-837f-e22cc04c6b70.png)

Trong registry key này, nếu khóa được đặt thành 0x02, điều này có nghĩa là dịch vụ này sẽ bắt đầu lúc boot.

SAM hive và thông tin người dùng:

SAM chứa thông tin tài khoản người dùng, thông tin đăng nhập và thông tin nhóm. Thông tin này chủ yếu nằm ở vị trí sau: SAM\Domains\Account\Users

![image](https://user-images.githubusercontent.com/72652376/178694472-42cf65b0-8041-4be8-bb24-ab4760838247.png)

Thông tin có ở đây bao gồm số nhận dạng tương đối (RID - relative identifier) của người dùng, số lần người dùng đăng nhập, lần đăng nhập cuối cùng, lần đăng nhập thất bại gần đây nhất, lần thay đổi mật khẩu gần đây nhất, hết hạn mật khẩu, chính sách mật khẩu và gợi ý mật khẩu và bất kỳ nhóm nào mà người dùng là một phần của.











