title: NS2的相关学习
date: 2015-11-22 14:27:19
tags:
- NS2
- 网络仿真
---
## 1.NS2在Linux下的安装
### 1.安装NS2支持包
 1.支持包安装之前先执行以下指令
```
    sudo appt-get update
    sudo appt-get upgrande
    sudo appt-get dist-upgrade
```
 2.安装支持包
```
    sudo apt-get install build-essential
    sudo apt-get install tcl8.5 tcl8.5-dev tk8.5 tk8.5-dev
    sudo apt-get install libxmu-dev libxmu-headers 
```
<!--more-->
### 2.NS2的安装过程
 - 【有些步骤如果不能执行，请使用root】
 1.首先下载软件包，此处采用的NS2版本是2.35，下载地址是[NS2](http://www.isi.edu/nsnam/ns/)
 2.解压NS2压缩包
```
    tar zxvf ns-allinone-2.35.tar.gz
```
 3.将解压后的文件夹ns-allinone-2.35移动到你想选择的目录下
 4.在上述目录下运行安装命令
```
    cd /ns-allinone-2.35 
    ./install   
```
 5.配置环境变量。在当前用户目录下编辑.bashrc
```
    vim .bashrc
```
  - 如果未安装vim可以使用gedit指令
```
    gedit .bashrc
```
  - 在该文件末尾添加如下(具体路径根据你的安装位置和用户名进行调整)：
```
    - export PATH=$PATH:/usr/local/NS2/bin:/usr/local/NS2/tcl8.5.10/unix:/usr/local/NS2/tk8.5.10/unix

    - export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/NS2/otcl-1.14:/usr/local/NS2/lib

    - export TCL_LIBRARY=$TCL_LIBRARY:/usr/local/NS2/tcl8.5.10/library

```
### 3.NS2验证安装是否正确
 - 打开终端输入
```
    ns
```
如果出现%说明安装成功。
### 4.NS2界面化
 1.安装nam
```
    cd NS2/nam-1.15
    ./configure && make && make install
```
如果出现错误信息，请根据错误信息安装需要的支持包。
 2.启动NS窗口
```
    cd NS2/ns-2.35/tcl/ex
    ns simple.tcl
```
出现测试界面
## 2.NS2的简单演示
 - 找到.tcl文件的位置
![定位并打开simple.tcl文件](http://ww1.sinaimg.cn/large/6681f234jw1ey9us2yxxgj20hk049abg.jpg)
 - ns2的界面
![ns2界面1](http://ww4.sinaimg.cn/large/6681f234jw1ey9us6b1s0j216m0oago1.jpg)
![ns2界面2](http://ww4.sinaimg.cn/large/6681f234jw1ey9usw0xy8j216k0o9mym.jpg)
 - simple.tcl的代码
```
set ns [new Simulator]

$ns color 0 blue
$ns color 1 red
$ns color 2 white

set n0 [$ns node]
set n1 [$ns node]
set n2 [$ns node]
set n3 [$ns node]

set f [open out.tr w]
$ns trace-all $f
set nf [open out.nam w]
$ns namtrace-all $nf

$ns duplex-link $n0 $n2 5Mb 2ms DropTail
$ns duplex-link $n1 $n2 5Mb 2ms DropTail
$ns duplex-link $n2 $n3 1.5Mb 10ms DropTail

$ns duplex-link-op $n0 $n2 orient right-up
$ns duplex-link-op $n1 $n2 orient right-down
$ns duplex-link-op $n2 $n3 orient right

$ns duplex-link-op $n2 $n3 queuePos 0.5

set udp0 [new Agent/UDP]
$ns attach-agent $n0 $udp0
set cbr0 [new Application/Traffic/CBR]
$cbr0 attach-agent $udp0

set udp1 [new Agent/UDP]
$ns attach-agent $n3 $udp1
$udp1 set class_ 1
set cbr1 [new Application/Traffic/CBR]
$cbr1 attach-agent $udp1

set null0 [new Agent/Null]
$ns attach-agent $n3 $null0

set null1 [new Agent/Null]
$ns attach-agent $n1 $null1

$ns connect $udp0 $null0
$ns connect $udp1 $null1

$ns at 1.0 "$cbr0 start"
$ns at 1.1 "$cbr1 start"

set tcp [new Agent/TCP]
$tcp set class_ 2
set sink [new Agent/TCPSink]
$ns attach-agent $n0 $tcp
$ns attach-agent $n3 $sink
$ns connect $tcp $sink
set ftp [new Application/FTP]
$ftp attach-agent $tcp
$ns at 1.2 "$ftp start"

$ns at 1.35 "$ns detach-agent $n0 $tcp ; $ns detach-agent $n3 $sink"

puts [$cbr0 set packetSize_]
puts [$cbr0 set interval_]

$ns at 3.0 "finish"

proc finish {} {
	global ns f nf
	$ns flush-trace
	close $f
	close $nf

	puts "running nam..."
	exec nam out.nam &
	exit 0
}

$ns run
```
 - simple.tcl的运行效果
![simple.tcl的运行效果](http://ww3.sinaimg.cn/large/6681f234jw1ey9us3uq6xj216k0o6tb1.jpg)

