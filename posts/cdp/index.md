# CDP基本认识


<!--more-->

## 初识CDP

Chrome DevTools Protocol （`Chrome 开发者工具协议`）[ 官方文档](https://chromedevtools.github.io/devtools-protocol/)

它是 Chrome DevTools（F12 开发者工具）的底层协议，`Selenium 4`是基于这个底层协议。

打开`谷歌浏览器`开启`CDP`因为默认是关闭的

![](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202506272025887.png)

![](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202506272025249.png)

简单获取一下cookie

![image-20250627202923928](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202506272029956.png)

## 基于Python连接做断点操作

```python
from DrissionPage import ChromiumPage, ChromiumOptions
from test1 import set_log_breakpoint
import time
import threading

co = ChromiumOptions()
co.set_browser_path('/Applications/Google Chrome.app/Contents/MacOS/Google Chrome')
co.set_argument('--remote-debugging-port=9222')
co.set_argument('--user-data-dir=/Users/scan/Library/Application Support/Google/Chrome/Default')
co.set_argument('--auto-open-devtools-for-tabs')
co.set_argument('--remote-allow-origins=*')
page = ChromiumPage(addr_or_opts=co)

def console_monitor():
    page.console.start()
    try:
        while True:
            for msg in page.console.steps():
                # 分级显示不同日志类型
                if msg.level == 'error':
                    print(f"🔴 [ERROR] {msg.text}")
                elif msg.level == 'warning':
                    print(f"🟠 [WARNING] {msg.text}")
                elif '日志断点触发' in msg.text:
                    print(f"🔵 [BREAKPOINT] {msg.text}")
                else:
                    print(f"⚪ [LOG] {msg.text}")
            time.sleep(0.1)
    except Exception as e:
        print(f"❌ 监控线程异常: {str(e)}")
    finally:
        print("🛑 控制台监听器已停止")

def main():
    # 启动监控线程（带状态提示）
    print("🧵 正在启动监控线程...")
    monitor_thread = threading.Thread(target=console_monitor, daemon=True)
    monitor_thread.start()
    print("✅ 监控线程已启动")
    # 打开目标页面（带加载进度）
    print("🌐 正在加载页面 https://www.baidu.com ...")
    page.get("https://www.baidu.com/")
    page.wait.doc_loaded()
    print("✅ 页面加载完成")
    # 设置日志断点（带详细状态报告）
    print("⏳ 正在设置日志断点...")
    try:
        set_log_breakpoint(
            js_url="https://pss.bdstatic.com/r/www/cache/static/protocol/https/amd_modules/@baidu/search-sug_ce0f74a.js",
            line=1795,  ##行数
            column=35,  ##列数
            condition="console.log('日志断点触发:', dataArray.map(x=>x.value)) || false",
            target_page_url="https://www.baidu.com/"
        )
        print("✅ 日志断点设置成功")
    except Exception as e:
        print(f"❌ 断点设置失败: {str(e)}")
    # 主线程保持运行（带退出提示）
    print("🚀 程序已进入监控状态，按Ctrl+C退出...")
    try:
        while True:
            time.sleep(1)
    except KeyboardInterrupt:
        print("\n🛑 用户请求终止程序")
    finally:
        page.close()
if __name__ == "__main__":
    main()
```

```python
#LogPoint.py
import json
import requests
from websocket import create_connection


def get_target_ws_url(target_page_url: str) -> str:
    """获取目标页面的WebSocket调试地址"""
    targets = requests.get('http://localhost:9222/json').json()
    for t in targets:
        if t['url'].startswith(target_page_url):
            return t['webSocketDebuggerUrl']
    raise Exception(f"未找到匹配页面: {target_page_url}")


def set_log_breakpoint(js_url: str, line: int, column: int, condition: str, target_page_url: str):
    """同步即时设置断点（无需等待脚本加载）"""
    ws_url = get_target_ws_url(target_page_url)
    ws = create_connection(ws_url)
    msg_id = 1

    # 1. 启用Debugger
    ws.send(json.dumps({
        "id": msg_id,
        "method": "Debugger.enable"
    }))
    _wait_for_id(ws, msg_id)
    msg_id += 1

    # 2. 直接设置断点（关键修改点）
    print(f"⚡ 直接设置断点: {js_url}:{line}")
    ws.send(json.dumps({
        "id": msg_id,
        "method": "Debugger.setBreakpointByUrl",
        "params": {
            "url": js_url,
            "lineNumber": line,
            "columnNumber": column,
            "condition": condition
        }
    }))

    # 3. 获取设置结果
    resp = _wait_for_id(ws, msg_id)
    if "error" in resp:
        raise Exception(f"断点设置失败: {resp['error']}")
    print(f"✅ 断点已激活 (ID: {resp['result']['breakpointId']})")

    # 4. 保持连接监听断点触发
    while True:
        msg = json.loads(ws.recv())
        if msg.get("method") == "Debugger.paused":
            print("🔥 断点命中!")
            # 可在此处添加断点命中后的处理逻辑
            ws.send(json.dumps({
                "id": msg_id + 1,
                "method": "Debugger.resume"
            }))


def _wait_for_id(ws, expect_id):
    """等待指定ID的响应"""
    while True:
        msg = json.loads(ws.recv())
        if msg.get("id") == expect_id:
            return msg
```

### 效果

![q](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202506272038958.gif)

## 抓包

### 发送拦截请求(类似于抓包)

```json
{"command":"Fetch.enable","parameters":{"patterns":[{"urlPattern":"*.mi.com/*","resourceType":"XHR","requestStage":"Request"}],"handleAuthRequests":false}}
```

### 获取拦截请求

```json
Fetch.requestPaused
```

![截屏2025-06-28 17.15.01](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202506281717152.png)



---

> 作者: [Scan](https://www.scan.work/)  
> URL: https://5canx.github.io/posts/cdp/  

