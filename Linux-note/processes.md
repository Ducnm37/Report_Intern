﻿## Linux processes

Một `processes` đơn giản là một thể hiện của một hay nhiều luồng (threads) thực hiện trên cùng một máy. 

Hệ điều hành sẽ theo dõi các tiến trình thông qua một ID có 5 chứ số mà được biết như là pid hay process ID. Mỗi tiến trình có duy nhất một pid.

Tại cùng một lúc không thể có 2 tiến trình có cùng pid.

Khi bạn bắt đầu một tiến trình (đơn giản là chạy một lệnh), có 2 các để chạy nó:
* Foreground Process: Mặc định khi bắt đầu các tiến trình là Foreground, nhận input từ bàn phím và output tới màn hình. Trong khi chương trình chạy thì không thể chạy bất cứ tiến trình nào khác
* Background Process: chạy mà không kết nối với bàn phím của bạn. Lợi thế là khi đó đang chạy tiến trình background vẫn có thể chạy các tiến trình khác.

Để bắt đầu một tiến trình Background thì thêm dấu `&` vào cuối câu lệnh ví dụ

`$ ls &`

ở đây lệnh `ls` muốn có một đầu vào, nó tiến vào trạng thái dừng tới khi bạn chuyển nó vào trong foreground.

Liệt kê các tiến trình đang chạy

```sh
$ ps
  PID TTY          TIME CMD
 6085 pts/1    00:00:00 bash
 6116 pts/1    00:00:00 ps
$ ps -f
UID        PID  PPID  C STIME TTY          TIME CMD
ubuntu    6085  6084  0 08:58 pts/1    00:00:00 -bash
ubuntu    6117  6085  0 09:03 pts/1    00:00:00 ps -f
```

Trong đó:
|  |Miêu tả|
|--|--|
|UID	|ID người sử dụng mà tiến trình này thuộc sở hữu (người chạy nó).|
|PID	|Process ID.|
|PPID	|Process ID gốc (ID của tiến trình mà bắt đầu nó).|
|C	|CPU sử dụng của tiến trình.|
|STIME	|Thời gian bắt đầu tiến trình.|
|TTY	|Kiểu terminal liên kết với tiến trình.|
|TIME	|Thời gian CPU bị sử dụng bởi tiến trình.|
|CMD	|Lệnh mà bắt đầu tiến trình này.|

Dừng tiến trình

	kill <pid>


### Command top

Lệnh `top` hiển thị các thông tin để có thể giám sát các thông số CPU, RAM,... các tiến trình đang hoặt động trên hệ thống

```sh
top - 09:28:52 up 154 days, 23:04,  2 users,  load average: 0.00, 0.00, 0.00
Tasks: 113 total,   1 running, 112 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.3 us,  0.0 sy,  0.0 ni, 99.7 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  1014648 total,   204156 free,    65596 used,   744896 buff/cache
KiB Swap:        0 total,        0 free,        0 used.   727912 avail Mem 

  PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND                        
    1 root      20   0  185436   5344   3272 S  0.0  0.5   0:42.19 systemd                        
    2 root      20   0       0      0      0 S  0.0  0.0   0:00.00 kthreadd                       
    3 root      20   0       0      0      0 S  0.0  0.0   0:16.85 ksoftirqd/0                    
    5 root       0 -20       0      0      0 S  0.0  0.0   0:00.00 kworker/0:0H                   
    7 root      20   0       0      0      0 S  0.0  0.0   0:10.29 rcu_sched                      
    8 root      20   0       0      0      0 S  0.0  0.0   0:00.00 rcu_bh  
```

Command `pstree` hiển thị các tiến trình đang chạy trên hệ thống dưới dạng một sơ đồ cây thể hiện mối quan hệ của các tiến trình con và tiến trình cha cùng toàn bộ các tiến trình khác mà nó tạo ra. Các luồng được thể hiện bằng dấu ngoặc nhọn

```sh
$ pstree
systemd─┬─accounts-daemon─┬─{gdbus}
        │                 └─{gmain}
        ├─acpid
        ├─2*[agetty]
        ├─atd
        ├─cron
        ├─dbus-daemon
        ├─2*[dhclient]
        ├─2*[iscsid]
        ├─lvmetad
        ├─lxcfs───10*[{lxcfs}]
        ├─mdadm
        ├─polkitd─┬─{gdbus}
        │         └─{gmain}
        ├─rsyslogd─┬─{in:imklog}
        │          ├─{in:imuxsock}
        │          └─{rs:main Q:Reg}
        ├─snapd───6*[{snapd}]
        ├─sshd─┬─sshd───sshd───bash───pstree
        │      └─sshd───sshd───bash───sudo───bash───su───bash
        ├─systemd───(sd-pam)
        ├─systemd-journal
        ├─systemd-logind
        └─systemd-udevd
```

### Scheduling processes

Lệnh `at` được sử dụng để thực thi 1 chương trình được chỉ định thời gian cụ thể, cần cài đặt `atd` để sử dụng. Trên Ubuntu 16 đã được cài đặt mặc định.

Lệnh `atq`  được sử dụng để liệt kê danh sách các job đang được đặt lịch bởi command `at`

Tiền ích `cron` là một chương trình lập lịch dựa trên thời gian. Cron được định hướng bởi một file câu hính là `/etc/crontab` chưa các lệnh shell khác nhau cần chạy vào đứng thời điểm đã được lên lịch. Mỗi dòng trong file này đại diện cho một công việc. Lệnh `crontab -e` sẽ mở crontab editor để chỉnh sửa hoặc thêm các jobs mới. Mỗi dòng sẽ gồm 6 dòng.

* MIN minutes 0-59
* HOUR Hour field 0-23
* DOM Day of month 1-31
* MON	Month field	1-12
* DOW	Day Of Week	0-6 (0 = Sunday)
* CMD	Command	bất kỳ một command nào được thực thi

Ví dụ 

	* * * * * /usr/local/bin/execute/this/script.sh

Lệnh trên nghĩa là thực hiện lệnh mỗi phút một lần.

	30 08 10 06 * /home/sysadmin/full-backup

sẽ đặt lịch cho full-backup lúc 8h30 ngày 10 tháng 6 không quan tâm là ngày nào trong tuần

### Delaying processes

Khi có một công việc nào đó cần trì hoãn hoặc tạm dùng thì ta có thể sử dụng lệnh `sleep` sẽ dùng lệnh thực thi trong một thời gian cụ thể rồi chạy tiếp.

```sh
$ cat script.sh
#!/bin/bash
echo "The system will go to sleep fo 30 seconds ..."
sleep 15
echo "The system is awaked"
$ chmod u+x script.sh
$ ./script.sh
The system will go to sleep fo 30 seconds ...
The system is awaked
```

Tổng quan về sự chuyển đổi giữa các tiến trình

<img src = "img/1.png">
