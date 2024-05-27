# 简介

## 0. 为什么要用Linux
开源项目绝大多数都是首先有Linux版本，随后部分适配Windows、Mac，甚至有的项目不适配Windows、Mac。也就是真要玩AI，一个linux环境还是更好的。

对于多数开源应用，Linux下的教程通常多于其它。

虽然WLS可以解决部分问题，但WSL也有很多不容易解决的问题，事实上，WSL对新手并不友好。

如果你做AI的不是主力电脑，或者你的主力电脑符合以下情况，那么你完全可以由windows过渡到linux。

1. 有多台设备，且不玩大型网游（或者steam上Windows才能运行的游戏）
2. 只有一台设备且不玩网游、不需要运行只有Windows才能运行的软件

针对第一种情况，直接将高算力设备作为linux设备，低算力设备作为一个终端设备即可，作为终端，那么该设备是Windows、Mac、Linux均可，没什么太大区别。
针对第二种情况，可以先用双系统过渡，最终决定切换到Linux或者继续用Windows+WSL。

本教程不讲双系统，因为个人认为意义不大。

## 1. 为什么选择ubuntu
虽然snap被很多人诟病，且ubuntu确实比Debian稍微臃肿，但是不可否认，现阶段ubuntu的生态和教程是最多的，而相对于Debian，多数情况Ubuntu更加友好。一般使用，体会不出来snap和所谓的“臃肿”的影响，当你觉得对你有影响时，也就不需要我这篇新手向教程了。

简单额你恐怕3m说下Desktop，Desktop版确实会安装一些额外的包，占用一些系统资源，但是如果说作为主力机，还是用来跑AI的设备，那么配置应该不会在乎那么一点点开销。而直接使用desktop，比安装server后自己安装X桌面，更为易上手和稳定。

如果不是作为主力机，那么完全不建议安装桌面，直接使用Server版即可。
## 2. 为什么建议准备多个硬盘
linux的目录管理方式，如果重装系统，系统挂载点`/`（系统分区）需要格式化，但是各挂载点可以不需要格式化。通常`/home`如果不变，重新安装后，挂载`/home`，很多配置不需要重做（例如conda环境），且不会破坏已经下载到`/home`的文件。
作为LLM的服务器，以ollama为例，模型保存在`/usr/share/ollama/.ollama/models`，如果把`/home`和`/usr/share/ollama/.ollama/models/`使用独立硬盘挂载，重装系统或者更换电脑，都是几乎误伤的。
事实上，在一个硬盘进行分区也可以实现无伤重装系统，但是用一个硬盘独立存放数据，操作容错更高（重新分区系统盘，不小心也容易破坏磁盘），同时也便于更换硬件，例如直接从电脑A把数据盘安装到电脑B并进行正确挂载，电脑B就可以快速部署。
# 一、准备工作
## 0. 准备硬件
显卡、硬盘
## 1. 镜像下载：

desktop：[https://ubuntu.com/download/desktop](https://ubuntu.com/download/desktop)

server：[https://ubuntu.com/download/server](https://ubuntu.com/download/server)

## 2. 镜像烧录：

**Windows：**

我个人习惯用PowerISO，也可以用UltraISO等，以PowerISO为例：

PowerISO：[https://www.poweriso.com/download.htm](https://www.poweriso.com/download.htm)

制作启动U盘

RAW格式

**Linux：**

查看U盘的设备描述符：

	lsblk

使用dd命令将镜像写入U盘：

	dd if=./ubuntu-24.04-server-amd64.iso of=/dev/sdX bs=1M

文件名ubuntu-24.04-server-amd64.iso根据实际文件名和地址修改，/dev/sdX改为U盘实际的设备符。

**MAC：**

查找 U 盘的设备描述符：

	diskutil list

卸载插入的U盘：

	diskutil unmountDisk /dev/diskX

/dev/diskX改为U盘实际的设备符。

使用dd命令将镜像写入U盘：

	sudo dd if=./ubuntu-24.04-server-amd64.iso of=/dev/diskX bs=1M
	
文件名ubuntu-24.04-server-amd64.iso根据实际文件名和地址修改，/dev/diskX改为U盘实际的设备符。

## 3. 修改bios设置
关闭安全启动（
## 4. 启动U盘系统
重启电脑，选择U盘启动。
# 二、安装Ubuntu24.04
## 一、安装ubuntu_desktop
如果你可能会用显示器操作这台服务器，或者可能用它作为主力机，那么我还是建议安装ubuntu_desktop，而不是安装ubuntu_server后安装X桌面。原因如下：
1. ubuntu_desktop默认安装的X桌面更易上手，且不需要要额外配置或者只需要进行少量配置。
2. ubuntu_server自己安装X桌面，需要一定门槛，即便资源开销略低于ubuntu_desktop，但是得不偿失。
3. 安装和配置系统以高效为主，当你真的需要自己安装server并自行安装X桌面，那么u你已经不是新手，无需看本教程。
## 二、安装ubuntu_server
# 三、安装后的设置
## 0. 更新系统并安装vim
### 0. 远程登陆
在windows或者mac上或者linux上打开终端，输入：
	
	ssh XXX@10.10.10.22

其中`XXX`是用户名，`10.10.10.22`更改为自己的服务器IP，服务器ip可以在路由器后台查，也可以在服务器的本地输入以下指令获得：
	
	ip a

根据自己的局域网网段，从输出信息中应该不难判断出服务器的内网IP。

### 1. 更新系统并安装vim

	sudo apt update
	sudo apt install vim
## 1. Desktop安装Nvidia驱动
由于ubuntu_desktop安装过程中，勾选了闭源的Nvidia驱动，所以不需要额外安装。
## 2. Server安装Nvidia驱动

下载Nvidia驱动和cuda：

首先建立一个目录，用来存放驱动和cuda：
	
	mkdir nvidia
	cd nvidia

下载驱动：

[https://www.nvidia.com/Download/index.aspx?lang=zh](https://www.nvidia.com/Download/index.aspx?lang=zh)

选择自己的显卡并点击Search，弹出的页面点击Dowanload,再次弹出的页面，右键Agree & Download，复制链接地址，在终端中输入wget(空格)，把刚才复制的i地址粘贴，然后删除.run之后的部分，如：

	wget https://us.download.nvidia.com/XFree86/Linux-x86_64/550.78/NVIDIA-Linux-x86_64-550.78.run

下载cuda：

[https://developer.nvidia.com/cuda-toolkit](https://developer.nvidia.com/cuda-toolkit)

点击Download Now --> Linux --> x86_64 --> Ubuntu --> 22.04（如果有24.04选24.04，截至2024年5月28日还没有更新24.04版本，所以我选择了22.04，是正常可用的）---> runfile(local)

复制下方的下载连接。
此时我们只下载，先不安装。

	wget https://developer.download.nvidia.com/compute/cuda/12.5.0/local_installers/cuda_12.5.0_555.42.02_linux.run


禁用nouveau

	sudo vim /etc/modprobe.d/blacklist.conf

在末尾加入：

	blacklist nouveau
	blacklist lbm-nouveau
	options nouveau modeset=0
	alias nouveau off
	alias lbm-nouveau off

按esc,随后输入:wq退出。

更新initramfs并重启计算机

	sudo update-initramfs -u
	sudo reboot

重启后
	cd nvidia
	sudo sh NVIDIA-Linux-x86_64-550.78.run
	sudo sh cuda_12.5.0_555.42.02_linux.run

随后重启
	sudo reboot

## 3. 设置防火墙和ssh
	
	sudo apt update
	sudo apt install openssh-server
	sudo ufw allow ssh
	sudo ufw enable

这里使用ufw防火墙管理访问，默认阻止所有端口的访问。此处允许ssh的端口22。未来，我们很有可能把服务器暴露在公网，以便随时随地访问我们搭建的服务，所以要提前打好安全基础。

这里建议配置ssh的key登陆，而不是密码登陆。因为key具有更高的安全性，可以防止暴力破解。且在登陆时不需要输入密码。

首先生成ssh用的rsa密钥对：

	ssh-keygen -t rsa -b 4096 -C "XXX"

XXX输入你的用户名，程序会问你保存文件的路径和是否输入密码，如果不懂就一路回车（可以不需要输入密码，这个密码是对密钥文件加密的）。

	mv ~/.ssh/id_rsa.pub ~/.ssh/authorized_keys

这里只是面对新手，公玥文件简单替换authroized_keys。

	cd ~/.ssh

把id_rsa文件复制到本地，如果不会操作，可以
	
	cat id_rsa

复制到剪贴板，然后打开本地的记事本，把剪贴板内容粘贴到id_rsa文件，保存。（复制粘贴私玥的操作虽然有少量安全风险，但对新手而言，安全性和便利性也远高于直接使用密码登陆）

	sudo vim /etc/ssh/sshd_config
找到PasswordAuthentication yes，按i进入编辑模式。在前面加#号，`esc` `:wq` 保存退出。

此时退出ssh，然后重新登陆。

	exit

这次使用key登陆：

	ssh -i /path/to/id_rsa XXX@10.10.10.22

`/path/to/id_rsa` 是刚才保存密钥文件的路径，`XXX@10.10.10.22` `xxx`改为你的用户名，`10.10.10.22` 改为你自己的服务器的ip。

Ubuntu中还有一个临时允许密码登陆的文件，如果刚才的登陆成功且没有要求你a输密码，则key登陆有效，则删除这个临时的密码登陆配置文件：
	
	sudo rm /etc/ssh/ssh_config.d/50-cloud-init.conf 

# 四、总结
1. 系统和数据分离（可以单盘多分区，但更建议多盘物理分离）
2. 要安装nvidia的闭源驱动，安全启动要关闭
3. 基础的安全配置做好，为以后服务器稳定在公网运行打下基础。
