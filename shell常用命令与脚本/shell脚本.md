# 系统进程

## 查看瞬时进程

### ps命令

常用组合: ps -ef  | ps aux

参数含义：-e显示所有运行的进程，-f，扩展输出进程信息 

```
F S UID          PID    PPID  C PRI  NI ADDR SZ WCHAN  STIME TTY          TIME CMD
4 S root           1       0  0  80   0 - 42338 ep_pol 11月29 ?      00:32:17 /sbin/init splas
1 S root           2       0  0  80   0 -     0 kthrea 11月29 ?      00:00:03 [kthreadd]
1 I root           3       2  0  60 -20 -     0 rescue 11月29 ?      00:00:00 [rcu_gp]
1 I root           4       2  0  60 -20 -     0 rescue 11月29 ?      00:00:00 [rcu_par_gp]
1 I root           6       2  0  60 -20 -     0 worker 11月29 ?      00:00:00 [kworker/0:0H-ev
```

各列含义：

- F：内核分配给进程的系统标识
- S：进程的状态
  - O：代表正在运行
  - S：代表正在休眠
  - R：代表可运行，正在等待运行
  - Z：代表僵化，进程已经结束，但父进程已经不存在（僵尸进程，应该尽量减少）
  - T：代表停止
- UID：启动这些进程的用户
- PID：进程ID
- PPID：夫进程的进程号
- C：进程生命周期中的cpu利用率
- PRI：进程的优先级，大部分系统（Linux、UCOSII）都是数字越低优先级越高，进程就优先运行 ， Linux中的PRI（new） = PRI（old） + nice ，其中 ， PRI（old）为系统算法决定 
- NI：进程优先级的修正量（nice值）， -20 ~ 19 ，用户可修改 （root用户可修改nice值-20 ~ 19 ，普通用户只能修改nice值0~19 ）。一般情况下，子进程继承父进程的nice值，由于init进程的nice值为0，所有大部分进程的nice值都为 0，NI值越小越优先
- ADDR：进程的内存地址
- SZ：假如进程被换出，需要的交换空间的大致大小
- WCHAN：进程休眠的内核函数地址
- STIME：进程启动时的系统时间
- TTY：进程启动时的终端设备
- TIME：运行进程所需要的CPU累计时间
- CMD：启动的进程名称

参数含义 a：显示所有终端的进程，x：显示所有后台进程，u：基于用户格式显示

```
root@pi-PowerEdge-R620:~# ps aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.3  0.0 169352 12916 ?        Ss   11月29  32:14 /sbin/init splash
root           2  0.0  0.0      0     0 ?        S    11月29   0:03 [kthreadd]
root           3  0.0  0.0      0     0 ?        I<   11月29   0:00 [rcu_gp]
root           4  0.0  0.0      0     0 ?        I<   11月29   0:00 [rcu_par_gp]
root           6  0.0  0.0      0     0 ?        I<   11月29   0:00 [kworker/0:0H-events_highp
root          10  0.0  0.0      0     0 ?        I<   11月29   0:00 [mm_percpu_wq]
root          11  0.0  0.0      0     0 ?        S    11月29   0:00 [rcu_tasks_rude_]
root          12  0.0  0.0      0     0 ?        S    11月29   0:00 [rcu_tasks_trace]
root          13  0.0  0.0      0     0 ?        S    11月29 
```

各列含义：

- VSZ：进程在内存中的大小，以千字节（KB）为单位
- RSS：进程在未换出时占用的物理内存。
- STAT：代表当前进程状态的双字符状态码，第一个字符采用了和Unix风格的S列相同的值，表示进程是在休眠，运行还是在等待，第二个列进一步说明了进程状态
  -  <：该进程运行在高优先级上
  - N：该进程运行在低优先级上
  - L：该进程有页面锁定在内存中
  - s：该进程是控制进程
  - l：该进程是多线程的
  - +：该进程运行在前台



## 查看实时进程

ps命令只能显示系统瞬间的进程信息，无法做到跟踪显示进程信息

### TOP命令

top命令在启动时，默认会按照%cpu来对进程排序，也可以在使用交互的方式修改显示方式。

```
top - 22:11:17 up 6 days,  8:55,  2 users,  load average: 0.22, 0.35, 0.35
Tasks: 706 total,   1 running, 702 sleeping,   0 stopped,   3 zombie
%Cpu(s):  0.3 us,  1.0 sy,  0.0 ni, 98.7 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :  64357.4 total,    371.7 free,   4379.3 used,  59606.4 buff/cache
MiB Swap:    976.0 total,    943.4 free,     32.6 used.  59317.6 avail Mem 

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                                           
2077012 root      20   0 7284512   4.0g   3.9g S   9.2   6.3 663:23.13 vmware-vmx                                        
2077293 root      20   0 7284012   4.1g   4.0g S   5.6   6.5 375:57.05 vmware-vmx                                        
2077026 root      20   0 7286748   4.0g   3.9g S   5.3   6.3 375:42.30 vmware
```

#### 第一行显示

```
top - 22:01:59 up 6 days,  8:45,  2 users,  load average: 0.82, 0.54, 0.41
# 分别显示 当前系统时间，运行时间，登录的系统用户，以及当前系统平均负债
# 系统负载有3个值，分别表示最近1分钟、最近5分钟和最近15分钟平均负债
```

值越大，说明负载越高，由于进程短期的突发性活动可能导致1分钟的高负债较为常见，如果15分钟内平均负载较高则说明系统存在问题

#### 第二行显示

```
Tasks: 706 total,   1 running, 702 sleeping,   0 stopped,   3 zombie
```

第二行显示了进程的概要信息，分别显示了进程总数，运行的进程、休眠进程、暂停进程以及僵尸进程的数量。

#### 第三至五行显示

```
%Cpu(s):  0.3 us,  1.0 sy,  0.0 ni, 98.7 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :  64357.4 total,    371.7 free,   4379.3 used,  59606.4 buff/cache
MiB Swap:    976.0 total,    943.4 free,     32.6 used.  59317.6 avail Mem 
# 显示了cpu的使用情况情况
#	物理内存的使用情况
#	交换分区的使用情况
```

显示了cpu的概要信息，top命令是根据进程的属主（用户还是系统）和进程的状态将cpu利用率分成几类输出。

#### 最后显示

```
    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND                                           
2077012 root      20   0 7284512   4.0g   3.9g S   9.2   6.3 663:23.13 vmware-vmx                                        
2077293 root      20   0 7284012   4.1g   4.0g S   5.6   6.5 375:57.05 vmware-vmx 
```

- PID：进程号
- USER：进程属主
- PR：进程的优先级
- NI：进程的谦让（nice）值
- VIRT：进程占据虚拟内存总量
- RES：进程占用物理内存总量
- SHR：进程和其他进程共享的内存总量
  - S：进程状态
  - O：代表正在运行
  - S：代表正在休眠
  - R：代表可运行，正在等待运行
  - Z：代表僵化，进程已经结束，但父进程已经不存在（僵尸进程，应该尽量减少）
  - T：代表停止
- %CPU：进程的cpu占用率
- %MEM：进程使用的内存占可用内存的比例
- TIME+：自进程启动到目前为止共占用的cpu时间
- COMMAND：进程对应的命令行



## 结束进程

在Linux中，进程之间通过信号来通讯，进程的信号就是预定义好的一个消息，进程能够识别并做出反应，进程如何处理信号是由程序人员在开发时就定义好的，大多数程序都能接受和处理标准的Unix进程的信号。常见的进程信号如下：

| 信号 | 名称 |             描述             |
| :--: | :--: | :--------------------------: |
|  1   | HUB  |             挂起             |
|  2   | INT  |             中断             |
|  3   | QUIT |           结束运行           |
|  9   | KILL |          无条件终止          |
|  11  | SEGV |            段错误            |
|  15  | TERM |          尽可能终止          |
|  17  | STOP |   无条件停止运行，但不终止   |
|  18  | TSTP | 停止或暂停，但继续在后台运行 |
|  19  | CONT |   在STOP或TSTP之后恢复执行   |

在Linux有两个命令可以向运行中的进程发送进程信号。

### kill命令

kill命令可用通过进程编号（PID）给进程发送信号。默认情况下，kill命令会向命令行中列出的全部PID发送一个TERM信号（当前用户需要是root或者进程的属主）。

kill命令不能使用命令名来发送信号，因此有时候不太方便，需要使用其他命令来发送信号。

```
kill 进程编号
如果信号无效，可以使用-s，指定其他信号进行强制终止
kill -s HUP
```



### killall命令

killall命令，支持通过进程名（命令名）来结束进程，而且killall还支持通配符匹配进程。

```
killall http*
```



# 磁盘管理

## 磁盘挂载

### mount命令

用来挂在磁盘到系统目录，mount命令进行磁盘挂载前，磁盘设备需要先创建文件系统。默认情况下，mount命令会输出当前系统上挂载的设备列表和挂载属性

```
root@pi-PowerEdge-R620:~# mount
sysfs on /sys type sysfs (rw,nosuid,nodev,noexec,relatime)
proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
udev on /dev type devtmpfs (rw,nosuid,noexec,relatime,size=32916176k,nr_inodes=8229044,mode=755,inode64)
devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000)
tmpfs on /run type tmpfs (rw,nosuid,nodev,noexec,relatime,size=

# (rw,nosuid,nodev,noexec,relatime)里边显示的是挂载属性
```

命令格式

```
mount [-fnrsvw] [-t vfstype] [-o options] device dir

方法1:mount 设备名 挂载点
[root@bogon ~]# mount /dev/sda1 /mnt/sda1
[root@bogon ~]# mount /dev/sda2 /mnt/sda2

方法2：mount -L 卷标名 挂载点

方法3：mount -U uuid 挂载点
```



#### /etc/mtab文件

/etc/mtab文件记录系统挂载情况，实际为/proc/self/mounts的内存信息软连接。当mount命令挂载磁盘时，默认会向/etc/mtab文件进行注册，当使用mount命令显示挂载信息时，实际上就是读取了/etc/mtab文件，但是当用户使用mount -n参数时，系统是不会进行注册的，因此mount命令也无法查看隐藏挂载的磁盘。

通过查看内存进程方式显示挂载情况，能够显示所有隐藏的挂载设备

```
cat /proc/mounts
```



#### **常用选项**

```
-a 挂在/etc/fstab文件中指定的设备和文件系统
-r readonly，只读挂载
-w read and write, 读写挂载
-n 隐藏挂载，不更新/etc/mtab，（df等看不到挂载信息，centos7无效，应为之间看的内存信息）
-a 自动挂载所有支持自动挂载的设备(定义在了/etc/fstab文件中，且挂载选项中有auto功能)
-L 'LABEL'以卷标指定挂载设备
-U 'UUID' 以UUID指定要挂载的设备
-B, --bind绑定目录到另一个目录上（文件夹挂在到文件夹上）
-o 给文件系统添加特定的挂载选项
```

-o options：(挂载文件系统的选项)，多个选项使用逗号分隔

```
defaults：相当于rw, suid, dev, exec, auto, nouser, async
async    异步模式
sync    同步模式,内存更改时，同时写磁盘
atime/noatime    是否记录文件时间戳
diratime/nodiratime    目录的访问时间戳
auto/noauto    是否支持自动挂载,是否支持-a选项
exec/noexec    是否支持将文件系统上运行应用程序(是否能够运行可执行文件)
dev/nodev    是否支持在此文件系统上使用设备文件
suid/nosuid    是否支持suid和sgid权限
remount    重新挂载
ro，norecovery    只读
rw    读写
user/nouser    是否允许普通用户挂载此设备，/etc/fstab使用
acl    启用此文件系统上的acl功能
loop    使用loop设备

```

指定设备挂载属性

```

[root@bogon ~]# mount -r /dev/sda1 /mnt/sda1  # 只读挂载

[root@bogon ~]# touch /mnt/sda1 f1.txt
touch: setting times of ‘/mnt/sda1’: Read-only file system
```



mount命令支持设备在不卸载的情况下进行重新挂载

```
# 以读写方式重新挂载
[root@bogon ~]# mount -o remount,rw /mnt/sda1
[root@bogon ~]# touch /mnt/sda1 f1.txt
```



## 磁盘使用情况

### df命令

```
[root@bogon ~]# df
Filesystem     1K-blocks     Used Available Use% Mounted on
/dev/sda1        3135488    33024   3102464   2% /mnt/sda1
/dev/sda2        3030800     9216   2847916   1% /mnt/sda2

```







su - wang -c "python.sh"	# 已wang身份用运行脚本，无法登录的用户无法使用

sudo -u sshd "echo"	# 这个才是正解





man 
man -k 关键字	# 可以进行搜索
man 2 intro	# man 可以选择需要查看的章节
info	# 也可以查看帮助信息



ls -F 	# 能够区分文件夹、文件，显示*表示可执行文件
ls -R 	# 递归显示

ls -s --time=atime 	# 查看最近访问时间，ls默认显示的是修改时间

file 文件名	# 查看文件类型，

cat -n |-b 	# 显示行号
cat -T  	#  使用^I替换tab制表符

less | more	# 用于分页查看

du -c -h -s 	# 查看当前目录文件占用磁盘情况







nc命令：将数据发送给ip：端口

删除当下文件名中的空格

```
#!/bin/bash
for file in *; do
old_file_name=${file}
#echo $old_file_name
new_file_name=`echo ${old_file_name} | tr -d " "`
mv "${old_file_name}" "${new_file_name}"
done

```



