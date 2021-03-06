﻿## Linux Security

Mặc định, Linux có nhiều loại tài khoản để phân chia cho các processes và workloads:

1. root
2. system
3. normal
4. network

Với môi trường làm việc an toàn, nên giới hạn các quyền hạn ở mức tối thiểu cho các tài khoản và loại bỏ các tài khoản không hoạt động. Command `last` sẽ show ra lịch sử người dùng đăng nhập vào hệ thống, xác định các tài khoản ít và đã lâu rồi không sử dụng để loại bỏ.

```sh
$last
root     pts/0        c-73-159-152-17. Sat Jan 13 11:51 - 12:22  (00:31)    
quy      pts/1        118.70.190.112   Thu Jan 11 16:12 - 18:58  (02:46)    
trang    pts/0        14.189.223.95    Thu Jan 11 13:57 - 23:59  (10:02)    
quy      pts/1        123.25.21.138    Thu Jan 11 13:31 - 14:47  (01:16)    
loc      pts/0        116.104.133.254  Thu Jan 11 13:24 - 13:53  (00:28)    
root     pts/0        112.137.129.90   Thu Jan 11 10:01 - 12:12  (02:11)    
(unknown :0           :0               Thu Jan 11 09:57   still logged in   
reboot   system boot  3.10.0-514.16.1. Thu Jan 11 09:56 - 09:21 (160+23:24) 
root     pts/1        c-73-159-152-17. Wed Jan 10 19:57 - 20:01  (00:03)    
trang    pts/0        14.189.223.95    Wed Jan 10 14:37 - 00:22  (09:45)  
```

user `root` sẽ có tài khoản cao nhất trong hệ thống. Nó có quyền quản trị hệ thống, tạo xóa, hay thay đổi mật khẩu tài khoản, kiểm tra log, cài đặt phần mềm,...

Một tài khoản người dùng thường có thể thực hiện những hoạt động yêu cầu sự cấp quyền ; tuy nhiên nên cấu hình cho phép những hoạt động đó được thực thi. Chạy một network client hoặc chia sẻ file qua mạng là những hành động
không yêu cầu tài khoản root để được thực thi.

### Sudo và Su Command

|Command `su`|Command `sudo`|
|------------|--------------|
|- Dùng để nâng quyền, bạn cần nhập password cho root|- Xin quyền root, bạn cần nhập password của chính bạn|
|- Sau khi nâng quyền, người dùng có thể thực hiện bất cứ lệnh nào mà root có thể thực hiện mà không yêu cầu lại password|- Những gì user có thể thực hiện được kiểm soát và có thể bị hạn chế, sau một thời gian nhất định phải nhập lại mật khẩu.
||- Phải thuộc nhóm `sudo` mới có thể dùng lệnh|
|- Giới hạn một số tính năng đăng nhập|- Các tính năng đăng nhập chi tiết có sẵn|

### Sudo command

Cấp quyền `sudo` cho người dùng sẽ ít nguy hiểm hơn `su`

Mặc định, `sudo` sẽ kích hoạt cho mỗi người dùng, tuy nhiên với một số các distro như Ubuntu thì chỉ kích hoạt cho ít nhất một người dùng chính.

Khi sử dụng `sudo <command>` lệnh sẽ được thực hiện với quyền root, sau khi thực hiện xong sẽ trở về với quyền của người dùng bình thường.

File cấu hình `/etc/sudoers` và `/etc/sudoers.d`. Có thể sửa file `/etc/sudoers` bằng lệnh `visudo` với editor `nano`

Có thể xem log về việc sử dụng `sudo` thất bại trong `/var/log/secure`

```sh
Jun 21 09:43:44 venus su: pam_unix(su:session): session opened for user trang by root(uid=0)
Jun 21 09:45:41 venus sudo: pam_unix(sudo:auth): authentication failure; logname=root uid=1005 euid=0 tty=/dev/pts/0 ruser=trang rhost=  user=trang
Jun 21 09:45:50 venus sudo:   trang : user NOT in sudoers ; TTY=pts/0 ; PWD=/root ; USER=root ; COMMAND=apt-get install nfs-common
Jun 21 09:46:00 venus su: pam_unix(su:session): session closed for user trang
```

Khi `sudo` được thực hiện, sẽ có một tiến trình trỏ tới `/etc/sudoers` các các file trong `etc/sudoer.d` để xác định người dùng đó có được cấp quyền `sudo` không, nếu được cấp thì có những quyền gì. Nếu không được phép thực hiện lệnh đó thì sẽ ghi lại log về thông tin đăng nhập.

### Tiến trình riêng biệt (The process isolation)

Linux bảo mật hơn các hệ điều hành khác là do các tiến trình chạy độc lập với nhau. Một tiến trình không thể truy câp tài nguyên của các tiến trình khác kể cả khi nó đang chạy cùng phiên cua một người dùng.

Một cơ chế đã được bổ sung vào để bảo mật và hạn chế tối thiểu các mối nguy hại đã được giới thiệu gần đây:

* `Control Groups`: cho phép người quản trị phân nhóm các tiến trình và cấp tài nguyên hữu hạn cho mỗi nhóm (cgroup).
* `Linux Containers` : cho phép chạy nhiều phiên bản Linux trên cùng một hệ thống.
* `Virtualization` : phần cứng được tính toán sao cho không chỉ tách biệt các tiến trình, đồng thời cũng cũng phải tách biệt với phần cứng mà các máy ảo sử dụng trên cùng một host vật lý.

### Mã hóa mật khẩu

Hầu hết các phiên bản linux đều sử dụng một thuật toán mã hóa là SHA-512, được phát triển bởi NSA để mã hóa mật khẩu. SHA-512 được sử dụng rộng rãi để bảo vệ các ứng dụng và giao thức như TLS, SSL, PHP, S/MINE và IPSec.

### Password aging

Password aging là một phương thức nhắc nhở người dùng tạo password mới sau một thời gian sử dụng, nhằm nâng cao tính bảo mật. Điều này có thể củng cố cho việc bảo mật, nếu hệ thống bị xâm nhập thì cũng chỉ sử dụng được trong một thời gian nhất định.

Sử dụng `chage` để cấu hình thông tin mật khẩu cho người dùng.

Ví dụ cấu hình 30 ngày thay đổi cho user `trang`

```sh
$ chage -M 7 trang
$ chage --list trang
Last password change				: Jan 08, 2018
Password expires					: Jan 15, 2018
Password inactive					: never
Account expires						: never
Minimum number of days between password change		: 0
Maximum number of days between password change		: 7
Number of days of warning before password expires	: 7
```

### Public/Private keys authentication

Public Key Encryption sẽ cho phép server và client tin tưởng lẫn nhau mà không cần password. Private key sẽ được cài đặt trên server, public key sẽ được chia sẻ cho clients. Private key phải được giữ bí mật, public key có thể được phân phôi tự do giữa các client. Hai khóa sẽ được mã hóa cùng nhau nên chúng là một cặp. Server cần phải ủy quyền cho public key thì client mới sử dụng được.

Sử sụng Public Key Encryption bạn sẽ không cần password để đăng nhập, và có thể vô hiệu hóa hoàn toàn việc sử dụng password để login, nếu không có key thì sẽ không thể truy cập hệ thống.

Tạo private key cho client và public key cho server như sau:

```sh
root@ip-172-31-22-1:~# ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:sS/+hP+n1w8oBPA7Aiqd2e9WD9i8IwS60qlOOldZo/8 root@ip-172-31-22-1
The key's randomart image is:
+---[RSA 2048]----+
|      .          |
|       o         |
|    .   +        |
| . =..o  =       |
|. =..=.=S .      |
| .. +.o.==   .   |
| o + o..o++ . .. |
|= =  .+.o+..  o..|
|+*   ..oEoo.o+  o|
+----[SHA256]-----+

root@ip-172-31-22-1:~# cd /root/.ssh
root@ip-172-31-22-1:~/.ssh# ll
total 20
drwx------ 2 root root 4096 Jun 21 03:31 ./
drwx------ 6 root root 4096 Jun  8 03:36 ../
-rw------- 1 root root  547 Jan  8 10:24 authorized_keys
-rw------- 1 root root 1675 Jun 21 03:31 id_rsa
-rw-r--r-- 1 root root  401 Jun 21 03:31 id_rsa.pub
root@ip-172-31-22-1:~/.ssh# chmod 700 ~/.ssh
root@ip-172-31-22-1:~/.ssh# chmod 600 ~/.ssh/id_rsa
```

Ở đây sẽ tạo ra hai file `id_rsa` và `id_rsa.pub` trong thư mục ẩn `/root/.ssh` lần lượt là private và public key. Cài public key đó vào `authorized keys list` rồi xóa nó khỏi server 

	cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
	rm -rf ~/.ssh/id_rsa.pub

Copy private key lên client và sẽ sử dụng nó để login

	scp ~/.ssh/id_rsa root@172.31.22.2:root/.ssh/
	rm -rf ~/.ssh/id_rsa

Sử dụng private key để login vào server

	ssh -i ~/.ssh/id_rsa root@172.31.22.1

