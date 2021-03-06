﻿### Vị trí Ứng dụng

Dựa trên các bản phân phối, chương trình, các gói phần mềm riêng được cài đặt trong các thư mục khác nhau. Nhìn chung các chương trình thực thi nên ở các thư mục sau:

	/bin
	/usr/bin
	/sbin
	/usr/sbin
	/opt

Một cách khác là sử dụng tiện ích `which`. Ví dụ, để tìm chính xác vị trí của chương trình diff trong file system

	$ which python
	/usr/bin/python

Nếu vẫn không tìm thấy chương trình thì ta có thể dùng `whereis`, bởi vì phạm vi tìm kiếm của nó rộng trong cả hệ thống file
	
	$ whereis  python
	python: /usr/bin/python3.5 /usr/bin/python3.5-config /usr/bin/python3.5m /usr/bin/python2.7 /usr/bin/python /usr/bin/python2.7-config /usr/bin/python3.5m-config /usr/lib/python3.5 /usr/lib/python2.7 /etc/python3.5 /etc/python2.7 /etc/python /usr/local/lib/python3.5 /usr/local/lib/python2.7 /usr/include/python3.5 /usr/include/python3.5m /usr/include/python2.7 /usr/share/python /usr/share/man/man1/python.1.gz

### Truy cập Directories

Một số command hữu ích: 

| Command | Result |
|---------|--------|
| cd | Chuyển về thư mục home |
| cd ..| Chuyển về thư mục cha |
| cd - | Chuyển về thư mục vừa rời đi trước đi |
| cd / | Chuyển đến thư mục `root`(/) |
| cd /home/trang | Chuyển đến thư mục `/home/trang` |

### Khám phá filesystem

Command `tree` là cách hay để có thể nhìn toàn cảnh về cây thư mục của hệ thống. Một số các command hữu ích sau:

| Command | Result |
|---------|--------|
| ls | Liệt kê nội dung của thư mục đang hiện tại làm việc |
| ls -a | Liệt kê thêm các file ẩn |
| ls -la | Hiển thị thêm các thông tin chi tiết của file và thư mục |
| tree | Hiển thị cây thư mục của toàn bộ hệ thống filesystem |
| tree -d | Chỉ hiện thị cây các thư mục mà bỏ qua danh sách các file |

### Hard link và Symbolic link

Một số sự khác biệt cơ bản của hard link và soft link

* Hard link: Khi tạo ra một file mới hard link đến file cũ thì hai file này sẽ cùng tham chiếu tới 1 vùng nhớ chứa địa chỉ của data, nên khi thay đổi nội dung từ 1 file thì file kia cũng thay đổi theo và khi xóa file cũ đi thì file mới đó không ảnh hưởng.

* Soft link: Khi tạo 2 file soft link tới nhau thì file mới sẽ trỏ tới vùng địa chỉ của file mới, nên khi xóa file cũ đi, file mới sẽ không thể truy cập đến dữ liệu được nữa.

Command `ln` có thể sử dụng để tạo hard link hoặc soft link, cũng như symnolic link hoặc symlink. Hai loại này rất phổ biến trên các hệ điều hành dựa trên UNIX-based.

Giả sử có file1.txt và tạo hard link là file2.txt

```sh
	$ ln file1.txt file2.txt
```

Kiểm tra sự tồn tại của hai file này.
```sh
	$ ls -l file*
	-rw-rw-r-- 2 trang trang 11 Jun  6 03:45 file1.txt
	-rw-rw-r-- 2 trang trang 11 Jun  6 03:45 file2.txt
```

Thêm option `-i` ta sẽ thấy được ở cột đầu tiên là số i-node, và hai số này giống hệt nhau tức là đang cùng trỏ tới một vùng nhớ

```sh
	$ ls -li file*
	337729 -rw-rw-r-- 2 trang trang 11 Jun  6 03:45 file1.txt
	337729 -rw-rw-r-- 2 trang trang 11 Jun  6 03:45 file2.txt
```

Symbolic or Soft links được tạo với option `-s` như sau:

```sh
	$ ln -s file1.txt file4.txt
	trang@ip-172-31-22-1:~/test$ ls -li file*
	337729 -rw-rw-r-- 2 trang trang 11 Jun  6 03:45 file1.txt
	337729 -rw-rw-r-- 2 trang trang 11 Jun  6 03:45 file2.txt
	304423 lrwxrwxrwx 1 trang trang  9 Jun  6 04:06 file4.txt -> file1.txt
```

Ở đây `file4.txt` không còn xuất hiện như tệp thông thường nữa, nó là một điểm trỏ tới `file1.txt` và cũng có số inode khác nhau (cột đầu tiên), quyền sẽ luôn là `lrwxrwxrwx`. Chúng cực kỳ tiện lợi khi có thể dễ dàng sửa đổi để trỏ tới các điểm khác. Một cách dễ dàng tạo shortcut từ thư mục home của bạn cho một đường dẫn dài là tạo một symbolic link.

Không giống như hard link, soft link có thể trỏ đến các đối tượng ngày trên các filesystem khác nhau (hoặc các partitions), cái mà có thể có hoặc không có sẵn thậm chí không tồn tại. Trong trường hợp link không trỏ tới các đối tượng tồn tại hoặc có sẵn, thì đường link đó sẽ bị treo, lơ lửng.

Hardlink cũng rất hữu ích, chúng tiết kiệm băng thông, nhưng phải cẩn thận với việc sử dụng nó.