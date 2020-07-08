# Linux(一)概述

## Linux 概述
### 一. 认识操作系统
1. 操作系统的作用

把计算机系统中对硬件设备的操作封装起来，供应用软件调用。
1. 常见操作系统

* PC 端 OS

* 移动端 OS

* 服务端 OS
### 二、Linux 来历
1. Unix的局限

硬件绑定：早期的Unix系统都是针对专门的硬件系统开发的，不同厂商都是为自己的服务器开发专门的Unix操作系统。

版权受限：出于商业等方面因素的考虑，AT&T在1979年发行第七版Unix系统时收回了Unix的版权。
1. 用于教学的 Minix

在Unix收回版权的背景下，出于学院教学的需要，荷兰阿姆斯特丹的Vrije大学计算机科学系的Andrew S. Tanenbaum教授开发了一个“类Unix”系统：Minix。之所以称为类Unix，是由于Tanenbaum教授为了避免版权纠纷在开发过程中刻意完全不看Unix本身代码，但同时要做到在使用时让用户的操作方式和使用Unix时一样。
1. 受到启发的 Linux

Minix最有名的学生用户是Linus Torvalds，他在芬兰的赫尔辛基大学用Minix操作平台建立了一个新的操作系统的内核，他把它叫做Linux。

Linux是 Linus Torvalds受到Minix的影响而开发的（Linus Torvalds不喜欢他的386电脑上的MS-DOS操作系统，安装了Minix，并以它为样本开发了原始的Linux内核）。

​ “Talk is cheap,show me the code!”
### 三、Linux作为服务器端系统的优势

Linux内核最初只是由芬兰人林纳斯·托瓦兹（Linus Torvalds）在赫尔辛基大学上学时出于个人爱好而编写的。

Linux是一套免费使用和自由传播的类Unix操作系统，是一个基于POSIX和UNIX的多用户、多任务、支持多线程和多CPU的操作系统。Linux能运行主要的UNIX工具软件、应用程序和网络协议。它支持32位和64位硬件。Linux继承了Unix以网络为核心的设计思想，是一个性能稳定的多用户网络操作系统。

目前市面上较知名的发行版有：Ubuntu、RedHat、CentOS、Debain、Fedora、SuSE、OpenSUSE。

Linus的优势主要体现在下面几个方面：
* 性能强劲，安全稳定
Linux本来就是基于Unix概念而发展出来的操作系统，当然也继承了Unix稳定高效的特点。使用Linux系统的主机连续工作1年以上不死机、不重启是非常常见的。所以很多电影、动画中的特效制作这样需要强大运算能力的工作都是运行在Linux系统之上。
* 可定制
如果你对Linux足够了解，完全可以使用Linux内核搭配需要的组件构成一个定制版系统，甚至你可以修改Linux源码进行深度定制
* 免费或少许费用
学习Linux可以免费使用Linux的各种发行版，在商业用途中往往也只是支付很少的费用即可
* 硬件配置要求低
Linux内核只有几KB大小，仅运行内核的话需要的系统开销很小，以命令行方式操作Linux也一样。以图形化界面方式运行Linux需要的资源也比Windows更少。
* 嵌入移动设备
由于Linux只需要很少的资源就能够驱动所有硬件设备工作，所以非常适合嵌入到手机等移动设备中，例如现在我们使用的Android系统就是以Linux为核心的
### 四、Linux发行版

Linus和他的虚拟团队的工作仅仅是开发了Linux内核以及附带的一些工具，尚不能作为一个完整的可以交给终端用户使用的操作系统。为了方便用户使用，很多的商业公司或非营利团体，就将Linux 内核（包括工具）与可运行的软件整合起来，再加上系统的安装工具。这个『内核+软件+工具』的完全可安装的整体，我们称之为Linux distribution，这就是Linux的发行版，港台腔叫发行套件。这是Linux这样的开放式系统和Windows、Mac等这些封闭式系统的一个显著差别。

初学Linux通常会选择CentOS，这其实是RedHat收费后去掉收费功能而发布的一个免费的社区版。

#### 主要的Linux发行版有：

* Red Hat: http://www.redhat.com

* Fedora: http://fedoraproject.org/

* Debian: http://www.debian.org/

* Ubuntu: http://www.ubuntu.com/

* kali : http://www.kali.org.cn/forum-68-1.htmlKali 

* CentOS: http://www.centos.org/



我们可以从网易开源镜像站获取CentOS系统的镜像文件

http://mirrors.163.com/
