# Linux关机命令和重启命令



在Linux中，常用的关机命令shutdown、halt、poweroff、init；重启命令有：reboot。本文将主要为大家带来一些常用的关机命令以及各种关机命令之间的区别和具体用法。
### 关机命令：
1. halt：立刻关机 
1. poweroff：立刻关机
1. shutdown -h now：立刻关机(root用户使用) 
1. shutdown -h 10：10分钟后自动关机 
注意，如果是通过shutdown命令设置关机的话，可以用shutdown -c命令取消重启



### 重启命令
1. reboot 立即重启
1. shutdown -r now 立刻重启(root用户使用) 
1. shutdown -r 10 过10分钟自动重启(root用户使用)  
1. shutdown -r 20:35 在时间为20:35时候重启(root用户使用) 

如果是通过shutdown命令设置重启的话，可以用shutdown -c命令取消重启

### 具体说明：
在Linux中，shutdown可以说是最安全的关机命令，可以通过参数h或-r的配合来完成关机或重启。不过需要注意，只有拥有root权限后，才能够使用该命令。

shutdown 参数说明:

　　[-t] 在改变到其它runlevel之前﹐告诉init多久以后关机。

　　[-r] 重启计算器。

　　[-k] 并不真正关机﹐只是送警告信号给

　　每位登录者〔login〕。

　　[-h] 关机后关闭电源〔halt〕。

　　[-n] 不用init﹐而是自己来关机。不鼓励使用这个选项﹐而且该选项所产生的后果往往不总是你所预期得到的。

　　[-c] cancel current process取消目前正在执行的关机程序。所以这个选项当然没有时间参数﹐但是可以输入一个用来解释的讯息﹐而这信息将会送到每位使用者。

　　[-f] 在重启计算器〔reboot〕时忽略fsck。

　　[-F] 在重启计算器〔reboot〕时强迫fsck。

　　[-time] 设定关机〔shutdown〕前的时间。




其实halt命令就是调用shutdown -h。当halt执行时﹐杀死应用进程﹐执行sync系统调用﹐文件系统写操作完成后就会停止内核。



#### 参数说明:

　　[-n] 防止sync系统调用﹐它用在用fsck修补根分区之后﹐以阻止内核用老版本的超级块〔superblock〕覆盖修补过的超级块。

　　[-w] 并不是真正的重启或关机﹐只是写

　　wtmp〔/var/log/wtmp〕纪录。

　　[-d] 不写wtmp纪录〔已包含在选项[-n]中〕。

　　[-f] 没有调用shutdown而强制关机或重启。

　　[-i] 关机〔或重启〕前﹐关掉所有的网络接口。

　　[-p] 该选项为缺省选项。就是关机时调用poweroff。




对于poweroff，可以说是目前最常用的关机命令了，其用法和 halt 也是基本相同的，这里就不多说了。




reboot的工作过程halt是基本差不多的﹐不过它的作用是引发主机重启﹐而halt则是关机。它的参数也与halt相差不多。




init是Linux系统操作中不可缺少的程序之一。init的进程号始终为1，所以发送TERM信号给init会终止所有的用户进程，守护进程等。init定义了8个运行级别，init 0为关机，init 1为重启。

