# kernel
CVE
https://blog.lexfo.fr/cve-2017-11176-linux-kernel-exploitation-part1.html?fbclid=IwAR3RgccLYsTOCymab4Ncux9P1gcQjjMp93Bbtw5Lnc9-piQ_QjlclfZ7RAg

tai lieu co dong
https://cyseclabs.com/slides/smep_bypass.pdf?fbclid=IwAR2cZau-3tOT5jkd1JA0Ir0xL6jgoJhFKMmOcabRSf--Y9ocxl8riEJxDcs

github ex kernel-exploitation-part1
https://github.com/xairy/linux-kernel-exploitation?fbclid=IwAR08L7zj3JcUUbQzA_58aTZ6zwzi_F2ZlMoYS0mt_qB03q72HzneJqnixNk


Kernel

Ret2usr ( return to user attack )

Như ta đã biết, khi user thực thi system call thì kernel sẽ thực thi lệnh, ở đây ta thấy có sự luân phiên giữa kernel và user.
User gọi kernel > kernel thực thi > quay về user  > ... 
User và kernel thay phiên nhau thực thi khi có lệnh gọi.
Khi gọi lệnh syscall User không thể truy cập được vào vùng nhớ của kernel nhưng Kernel thì có thể truy cập được
Ví dụ: User gọi lệnh write(), kernel sẽ lấy data trong vùng nhớ của user để làm tham số cho hàm và thực thi lệnh.


Kernel là gì? 

Kernel chỉ đơn giản là code. kernel code thực thi cho các việc như:
	- gõ phím. Khi bạn nhấn 1 phím, kernel code sẽ xử lý và nhận biết phím đó là gì và in ra màn hình cho bạn thấy.
	
	- xử lý mọi gói mạng vào ra.
	
	- viết files lịch sử vào trong ổ đĩa
	
	- cấp phát vùng nhớ
	
	- giao tiếp với card đồ họa

Kernel là phần gần nhất với hệ điều hành, cung cấp dịch vụ cơ bản cho tât cả các phần khác của hệ thống, quản lý phần cứng, phân tán tài nguyên hệ thống.

Kernel space và user space

	- Kernel-space: đơn giản chỉ là vùng chứa code của kernel. Nó là vùng nhớ được bảo vệ với full quyền truy cập vào phần cứng và trạng thái của hệ thống. Hầu hết vùng nhớ được ánh xạ trực tiếp đến vùng nhớ vật lý tại một offset cố định.
	
	- User-space: là tập hợp các vùng mà các tiến trình người dùng chạy trên đó. Nó bao gồm process code (mã tiến trình), dữ liệu (data), và file ánh xạ vùng nhớ (memory mapped files). Trong user space thì vùng nhớ ánh xạ khác nhau từ một không gian địa chỉ đến một cái không gian địa chỉ khác.

Sự chuyển đổi từ user mode sang kernel mode

Sự chuyển đổi từ user sang kernel khi ứng dụng thực thi 1 lệnh syscall. Ví dụ như open('file.txt', O_RDONLY), thì lúc đó kernel sẽ thực thi thay cho tiến trình và được xem là đang chạy trong ngữ cảnh là process. còn khi kernel thực thi lệnh ngắt thì kernel được xem là đang chạy trong ngữ cảnh là interrupt

Ngữ cảnh tiến trình:
	- là khi user space thực thi lệnh syscall, thì chuyển sang kernel chạy. khi đó tiến trình có thể sleep.
	- kernel được ưu tiên thực thi.
	- Xử lý request chậm.
	- Truy câp để xử lý không gian bộ nhớ ảo

Ngữ cảnh Interrupt:
	- là khi xử lý ngắt kernel.
	- kernel không thể thực thi bất kì function nào đang sleep
	- 
	
