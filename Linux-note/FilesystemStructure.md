﻿### Filesystem Structure

Trên nhiều hệ điều hành, bao gồm cả linux, `filesystem` có cấu trúc giống như cây. Cây thường được miêu tả ngược, thường bắt đầu thừ thư mục `root` (/) tất cả các thứ mục khác sẽ là con của thư mục này.

Đơn giản filesystem là các quy chuẩn về: cách thức cấp phát không gian lưu trữ cho file, quản lý thuộc tính của file, cách tổ chức sắp xếp dữ liệu trên các thiết bị sao cho việc tìm kiếm, truy cập tới dữ liệu nhanh chóng và thuận tiện.

Các định dạng filesystem mà linux supports:
* ext3, ext4, btrfs, xfs (native Linux filesystems)
* vfat, ntfs, hfs (filesystems from other operating systems)

Mỗi hệ thống tiệp tin nằm trên một partition của đĩa cứng. Partitions giúp tổ chức nội dung của ổ đĩa theo các loại data nó chứa và cách nó sử dụng. Ví dụ, các chương trình quan trọng cần thiết để chạy hệ thống thường được lưu trữ trên một partition riêng biệt hơn là chứa các file của người dùng thông thường. Ngoài ra các file tạm thời được tạo và hủy trong quá trình hoạt động bình thường của linux thường nằm trên một partition riêng; theo cách này, việc sử dụng tất cả các không gian có sẵn trên một patition cụ thể có thể không ảnh hưởng đến hoạt động bình thường của hệ thống.

Trước khi bạn có thể bắt đầu sử dụng filesystem, bạn cần mount nó lên filesystem tree tại **mountpoint**. Đây đơn giản là một thư mục (có thể để trống hoặc không) nơi các hệ thống tệp tin được gắn vào (mounted). Đôi khi bạn cần tạo một directory nếu nó không tồn tại. Nếu bạn mount một filesystem trên một directory không rỗng thì các nội dung trước đó sẽ được che đi và không thể truy cập cho đến khi filesystem unmounted. Vì vậy các mount point thường là các directory rỗng.

Khi cài đặt cần tạo ra ít nhất 2 phân vùng, một để mount root cho `\`, một cho `swap`. 

* Bạn có thể mount tự động thông qua file `/etc/fstab`, kernel sẽ đọc thông tin ở đây khi nó khởi động. Nếu sửa nội dung file này thì cần mount lại: 

		$ sudo mount -a



* Mount bằng tay: sử dụng câu lệnh mount, ví dụ gắn thư mục windows: Nếu ổ `C:` định dạng ntfs cài windows có tên /dev/sda1 muốn mount vào hệ thông linux để sử dụng thì đầu tiên cần tạo một thư mục để gắn nó, rồi sau đó mount:

```sh
	$ mkdir /mnt/Windows
	$ mount -v -t ntfs /dev/sda1 /mnt/Windows
```

Lúc này /dev/sda1 là đường dẫn cần mount, /mnt/Windowns là mountpoint, từ giờ có thể truy cập đến ổ C qua /mnt/Windows

Nếu không muốn dùng nữa thì unmount đi

```sh
	$ sudo umount /mnt/WindowsXP 
	hoặc
	$ sudo umount /dev/sda1​
```

Lưu ý: một số điểm cần lưu ý khi mount:
* Các thiết bị không có mặt trong file /etc/fstab thì chỉ có root mới có thể mount được
* Người dùng bình thường chỉ có thể mount được những thiết bị có trong file /etc/fstab thôi

### The home directories
Trong bất kỳ một hệ thống UNIX nào, mỗi user đều có một thư mục home và thường được đặt ở `/home`. Với user root thì thư mục home sẽ nằm ở `/root`

### The device directory

`/dev` thư mục bao gồm các device note, một kiểu của pseudo-file được sử dụng bới hầu hết các hardware và software device, ngoại trừ các network devices.

	/dev/sda1
	/dev/lp1
	/dev/dvd1

### The variable directory

`/var` bao gồm các file có thể thay đổi size và nội dung khi hệ thống đang chạy, một số thư mục sau:

* System log files: /var/log
* Packages files: /var/lib
* Print queues: /var/spool
* Temp files: /var/tmp
* FTP home directory: /var/ftp
* Web Server directory: /var/www

`/var` có thể được đặt trên một patition riêng vì vậy khi không gian lưu trữ hoặc kích thước file tăng có thể không ảnh hưởng đến hệ thống.

### File System Table

file `etc/fstab` là file dạng văn bản (plain text) gồm:
* Đường dẫn đến file đại diện cho các thiết bị
* Mount point: cho biết các thiết bị được moun vào thư mục nào
* Các tùy chọn: chỉ ra các thiết bị được mount như thế nào...

Nội dung file `cat /etc/fstab`

```sh
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/sda1 during installation
UUID=c81416d6-2c2d-44fa-b775-d3ab9f5da404 /               ext4    errors=remount-ro 0       1
# swap was on /dev/sda5 during installation
UUID=f2a778d8-c362-4ab8-b81f-f91f1ebc692d none            swap    sw              0       0
/dev/fd0        /media/floppy0  auto    rw,user,noauto,exec,utf8 0       0
```

* Cột một: loại thiết bị phân vùng, cho biết đường dẫn tới file đại diện cho device đó (/dev/fd0)
* Cột 2: đường dẫn của mount point (/media/floppy0)
* Cột 3: là kiểu filesystem. `auto` có nghĩa là không phải là một loại filesystem, hệ thống sẽ tự động nhận diện khi thiết bị được mount.
* Cột 4: là các tùy chọn mount
| Optition | |
|----------|-|
|auto | tự động mount thiết bị khi máy tính khởi động.|
|noauto | không tự động mount, nếu muốn sử dụng thiết bị thì sau khi khởi động vào hệ thống bạn cần chạy lệnh mount.|
|user | cho phép người dùng thông thường được quyền mount. |
|nouser | chỉ có người dùng root mới có quyền mount.|
|exec | cho phép chạy các file nhị phân (binary) trên thiết bị.|
|noexec | không cho phép chạy các file binary trên thiết bị.|
|ro (read-only) | chỉ cho phép quyền đọc trên thiết bị.|
|rw (read-write) | cho phép quyền đọc/ghi trên thiết bị.|
|sync | thao tác nhập xuất (I/O) trên filesystem được đồng bộ hóa.|
|async | thao tác nhập xuất (I/O) trên filesystem diễn ra không đồng bộ.|
|defaults | tương đương với tập các tùy chọn rw, suid, dev, exec, auto, nouser, async|

* Cột 5 là tùy chọn sao lưu cho chương trình dump, công cụ sao lưu filesystem. `0` là không sao lưu, `1` là thực hiện sao lưu.
* Cột 6: tùy chọn cho chương trình fsck, công cụ dò lỗi trên filesystem. Điền `0` là không kiểm tra, `1` là thực hiện kiểm tra.



Có thể sử dụng lệnh: `$ blkid` để tìm **UUID** hoặc **vol_id --uuid**

Command `df -Th` hiển thị thông tin về các filesystem được mount gồm type và cách sử dụng về không gian đang sử dụng và sẵn có

```sh
# df -hT
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  203M     0  203M   0% /dev
tmpfs          tmpfs      44M  2.5M   42M   6% /run
/dev/sda1      ext4       19G  4.4G   14G  25% /
tmpfs          tmpfs     220M  4.0K  220M   1% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     220M     0  220M   0% /sys/fs/cgroup
tmpfs          tmpfs      44M     0   44M   0% /run/user/0
```