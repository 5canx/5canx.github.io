# Markdown编辑器——Typora破解版

<!--more-->

{{< admonition abstract>}}
本文介绍破解版使用、便捷化的方法                       

（不修改系统时间不用管理员运行）        

{{< /admonition >}}

## 运行并且修改RunAsDate.exe的参数

![images](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305260819092.png)


>参数修改如上图即可，Typora(RunAsDate)为你生成的破解版软件名




## 注册表编辑
{{< admonition type=notice title="将file.ico、RunAsDate.exe、Typora.exe找到并且修改为你自己的路径**注意为反斜杠**" open=true >}}
```bat title="This is a tip" open=false
Windows Registry Editor Version 5.00
[HKEY_CLASSES_ROOT\Applications\Typora-run.bat]
[HKEY_CLASSES_ROOT\Applications\Typora-run.bat\DefaultIcon]

@="D:\\Typora\\resources\\assets\\file.ico"

[HKEY_CLASSES_ROOT\Applications\Typora-run.bat\shell]
[HKEY_CLASSES_ROOT\Applications\Typora-run.bat\shell\open]
[HKEY_CLASSES_ROOT\Applications\Typora-run.bat\shell\open\command]

@="C:\\Users\\safex\\Downloads\\runasdate-x64\\RunAsDate.exe /movetime 13\\02\\2020 00:00:00 \"

D:\\Typora\\Typora.exe\" \"%1\""

[HKEY_CLASSES_ROOT\Applications\Typora-run.bat\SupportedTypes]
".md"=""
".markdown"=""
".mdown"=""
".mkd"=""
".mmd"=""
```
{{< /admonition >}}

> 修改后去运行Typora-run.bat、Typora-run.reg

## 设置中修改md文件打开方式

![image-20230526091443921](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202305260914983.png)

{{< admonition success >}}
打开md文件，正常启动Typora，则说明破解成功！
{{< /admonition >}}

{{< link href="https://wwme.lanzoum.com/i5Ot40xao6ze" content=" 安装包" download="安装包" card=true >}}

---

> 作者: [Scan](https://www.scan.work/)  
> URL: https://5canx.github.io/posts/typora%E7%A0%B4%E8%A7%A3%E4%BD%BF%E7%94%A8/  

