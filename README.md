# kernel
CVE
https://blog.lexfo.fr/cve-2017-11176-linux-kernel-exploitation-part1.html?fbclid=IwAR3RgccLYsTOCymab4Ncux9P1gcQjjMp93Bbtw5Lnc9-piQ_QjlclfZ7RAg

tai lieu co dong
https://cyseclabs.com/slides/smep_bypass.pdf?fbclid=IwAR2cZau-3tOT5jkd1JA0Ir0xL6jgoJhFKMmOcabRSf--Y9ocxl8riEJxDcs

github ex kernel-exploitation-part1
https://github.com/xairy/linux-kernel-exploitation?fbclid=IwAR08L7zj3JcUUbQzA_58aTZ6zwzi_F2ZlMoYS0mt_qB03q72HzneJqnixNk


KKernel là gì? 

Kernel chỉ đơn giản là code. kernel code thực thi cho các việc như:

	- gõ phím. Khi bạn nhấn 1 phím, kernel code sẽ xử lý và nhận biết phím đó là gì và in ra màn hình cho bạn thấy.
	- xử lý mọi gói mạng vào ra.
	- viết files lịch sử vào trong ổ đĩa
	- cấp phát vùng nhớ
	- giao tiếp với card đồ họa

Kernel là phần gần nhất với hệ điều hành, cung cấp dịch vụ cơ bản cho tât cả các phần khác của hệ thống, quản lý phần cứng, phân tán tài nguyên hệ thống.

Một tiến trình trong máy tính Windows có cả 2 chế độ: user và kernel.

Kernel space và user space

	- Kernel-space: đơn giản chỉ là vùng chứa code của kernel. Nó là vùng nhớ được bảo vệ với full quyền truy cập vào phần cứng và trạng thái của hệ thống. Hầu hết vùng nhớ được ánh xạ trực tiếp đến vùng nhớ vật lý tại một offset cố định.
	- User-space: là tập hợp các vùng mà các tiến trình người dùng chạy trên đó. Nó bao gồm process code (mã tiến trình), dữ liệu (data), và file ánh xạ vùng nhớ (memory mapped files).

Tiến trình cung cấp cho ứng dụng một vùng địa chỉ ảo riêng, và vì riê nên nó có giới hạn. một tiến trình chạy trong user mode không thể
truy cập vào địa chỉ ảo của hệ điều hành, để tránh ứng dụng có thể sửa đổi, gây tổn hại đến dữ liệu hệ điều hành.

Sự chuyển đổi từ user sang kernel khi ứng dụng thực thi 1 lệnh syscall bằng cách gọi trực tiếp trong trong shell hoặc gọi trong thư viện. Ví dụ như open('file.txt', O_RDONLY), thì lúc đó kernel sẽ thực thi thay cho tiến trình. 

Như ta đã biết, khi user thực thi system call thì kernel sẽ thực thi lệnh, ở đây ta thấy có sự luân phiên giữa kernel và user.
User gọi kernel > kernel thực thi > quay về user  > ... 
User và kernel thay phiên nhau thực thi khi có lệnh gọi.

Ví dụ: User gọi lệnh write(), kernel sẽ lấy data trong vùng nhớ của user để làm tham số cho hàm và thực thi lệnh sau đó quay về user land để thực thi tiếp
NOTE:Khi gọi lệnh syscall User không thể truy cập được vào vùng nhớ của kernel nhưng Kernel thì có thể truy cập được


Return to User attack

return to user(ret2usr) tấn công khai thác nhân của hệ điều hành (kernel), cho phép local users có thể chiếm quyền thực thi bất kì lệnh nào với quyền leo được đó. attacker tấn công bằng cách kiểm soát được luồng thực thi chương trình,
tận dụng lỗi để khai thác, chuyển hướng luồng thực thi return về vùng nhớ ở user có chứa shellcode hoặc code thực thi leo quyền
có 2 cách để thực hiện:

	- chuyển hướng trực tiếp từ return address hàm của kernel space trỏ sang hàm leo quyền trong user space. 
	- chuyển hướng gián tiếp từ con trỏ struct return về một struct ở user space rồi từ user space trỏ tới hàm leo quyền escalate_privs()
	
Cách 1: exploit con trỏ hàm

	- Tìm con trỏ hàm mà có thể ghi đè
	- tạo vùng nhớ chứa payload leo quyền ở user space
	int __attribute__((regparm(3))) (*commit_creds)(unsigned long cred);
	unsigned long __attribute__((regparm(3))) (*prepare_kernel_cred)(unsigned long cred);
	commit_creds = 0xffffffffxxxxxxxx;
	prepare_kernel_cred = 0xffffffffxxxxxxxx;
	void escalate_privs() {commit_creds(prepare_kernel_cred(0)); }
	- Thực thi hàm escalate_privs() để leo quyền.
