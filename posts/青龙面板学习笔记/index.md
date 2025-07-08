# 青龙面板学习笔记

<!--more-->

> 系统环境：阿里云Linux服务器、Docker


# 青龙面板学习笔记

## 使用docker安装青龙面板
```sh
docker pull whyour/qinglong:latest
```
运行后直接启动
```sh
docker run --name qinglong -d \
  -p 5700:5700 \
  -v /opt/ql/data:/ql/data \
  -e QlBaseUrl="/" \ #设置环境变量 QlBaseUrl 和 QlPort，青龙面板服务启动时使用
  -e QlPort="5700" \
  --hostname qinglong \  #设置容器主机名为 qinglong
  --restart unless-stopped \ #设置容器随docker启动自动启动
  whyour/qinglong:latest
```
访问 ip:5700

如果已经创建容器，没有随着docker自动启动的话就运行：


```sh
docker ps -a
docker start [容器名称/ID]
```
如图：
![image-20240406214313753](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202404062143798.png)

## 进入容器进行操作

运行如下命令
```sh
docker exec -it [容器名] /bin/bash
```
出现![image-20240406212625752](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202404062126914.png)
内置命令有(task  ql)：

> task
```sh
# 依次执行，如果设置了随机延迟，将随机延迟一定秒数
task <file_path>                                             
# 依次执行，无论是否设置了随机延迟，均立即运行，前台会输出日，同时记录在日志文件中
task <file_path> now                                         
# 并发执行，无论是否设置了随机延迟，均立即运行，前台不产生日，直接记录在日志文件中，且可指定账号执行
task <file_path> conc <env_name> <account_number>(可选的) 
# 指定账号执行，无论是否设置了随机延迟，均立即运行 
task <file_path> desi <env_name> <account_number>      
# 设置任务超时时间   
task -m <max_time> <file_path>
# 使用 -- 分割，-- 后面的参数会传给脚本，下面的例子，脚本就可接收到参数 -u whyour -p password
task <file_path> -- -u whyour -p password
```
> ql
```sh
# 更新并重启青龙
ql update
# 运行自定义脚本extra.sh
ql extra
# 添加单个脚本文件
ql raw <file_url>
# 添加单个仓库的指定脚本
ql repo <repo_url> <whitelist> <blacklist> <dependence> <branch> <extensions>
# 删除旧日志
ql rmlog <days>
# 启动tg-bot
ql bot
# 检测青龙环境并修复
ql check
# 重置登录错误次数
ql resetlet                                                  
# 禁用两步登录
ql resettfa
```

![image-20240406213020285](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202404062130368.png)

more..待完善


---

> 作者: [Scan](https://www.scan.work/)  
> URL: https://5canx.github.io/posts/%E9%9D%92%E9%BE%99%E9%9D%A2%E6%9D%BF%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/  

