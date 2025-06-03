# Selenium——进阶使用


<!--more-->

## 浏览器的控制

``` python
driver.set_window_size(宽，高)#设置宽高
driver.maximize_window()#全屏显示

driver.forward()  #浏览器的上一步
driver.back()  #浏览器的后退
driver.refresh()  #浏览器的刷新
```

## 浏览器的窗口切换

### 获取窗口句柄

``` python
handles=driver.window_handles 
print(handles)
```

### 切换窗口命令

```python
driver.switch_to.window(handles[0])
```

| handles[0]  | **切换到最开始打开的窗口** |
| :---------: | :------------------------: |
| handles[-1] |  **切换到最新打开的窗口**  |
| handles[-2] |  **倒数第二个打开的窗口**  |

## 进阶姿势

### JS代码执行

> executeScript是同步方法，用它执行js代码会阻塞主线程执行，直到js代码执行完毕；
> executeAsyncScript方法是异步方法，它不会阻塞主线程执行。

``` python
#值可写多个JS变量
execute_script()
executeAsyncScript()
```



### 上传文件的元素操作

``` python
WebElement adFileUpload =driver.findElement(By.id("上传"));
String filePath = "C:\1.php";adFileUpload.sendKeys(filePath)
```

### 隐式等待

> 如果在30秒内执行就继续下一步，没有执行就等待30秒，30秒如果元素还未出现则跳过，不抛出异常

``` python
driver.implicitly_wait(30)
```

### 显式等待

> 如果执行就继续下一步，没有执行就等待30秒，30秒如果元素还未出现则抛出异常，不继续
> demo:    ```python
> result=WebDriverWait(driver,10).until(expected_conditions.alert_is_present())  ```
``` python
alert_is_present()  #判断页面是否出现alert框
title_is()  #判断页面title内容是与传入的title_text内容完全匹配
title_contains()  #判断页面title标签的内容包含
partial_title()  #只需要部分匹配即可
text_to_be_present_in_element_value()  #判断text是否出现在元素的value属性值中
text_to_be_present_in_element() #判断文本内容test是否出现在某个元素中，判断的是元素的text
presence_of_all_elements_located(*locator*)#判断页面至少有一个如果元素出现，如果满足条件，返回所有满足定位表达式的页面元素
```

{{< admonition tip >}}
不等待某个元素加载，使用time.sleep方法，方便快捷。
{{< /admonition >}}

## 键盘操作

### 第一种方法——Selenium自带的Keys包

```python
from selenium.webdriver.common.keys import Keys
```

常见的键盘操作：

``` python
# 删除键
sent_keys(Keys.BACK_SPACE)    
# 空格键
sent_keys(Keys.SPACE)
# tab 键
sent_keys(Keys.TAB)
# delete 键
sent_keys(Keys.DELETE)
# + 键
sent_keys(Keys.ADD)
# enter 键
sent_keys(Keys.ENTER)
# shift 键
sent_keys(Keys.SHIFT)
# ctrl+A：全选
sent_keys(Keys.CONTROL, 'a')
# ctrl+C：复制
sent_keys(Keys.CONTROL, 'c')
# ctrl+V：粘贴
sent_keys(Keys.CONTROL, 'v')
# ctrl+X：剪切
sent_keys(Keys.CONTROL, 'x')
# F1 键
sent_keys(Keys.F1)
# 数字9 键
sent_keys(Keys.NUMPAD9)
```

![2289881-20220608220557218-225439528](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202306041615294.png)

### 第二种方法——Python的第三方库——pywin32库

``` python
import win32api
import win32con

class KeyboardKeys(object):
    #模拟键盘按键类
    VK_CODE={
        'enter':0x0D,
        'ctrl':0x11,
        'v':0x56
    }

    @staticmethod
    def keyDown(keyName):
        #按下按键
        win32api.keybd_event(KeyboardKeys.VK_CODE[keyName],0,0,0)


    @staticmethod
    def keyUp(keyName):
        #释放按键
        win32api.keybd_event(KeyboardKeys.VK_CODE[keyName],0,win32con.KEYEVENTF_KEYUP,0)


    @staticmethod
    def oneKey(key):
        #模拟单个按键
        KeyboardKeys.keyDown(key)
        KeyboardKeys.keyUp(key)

    @staticmethod
    def twoKeys(key1,key2):
        #模拟两个组合键
        KeyboardKeys.keyDown(key1)
        KeyboardKeys.keyDown(key2)
        KeyboardKeys.keyUp(key2)
        KeyboardKeys.keyUp(key1)
```
{{< admonition question >}}Keybd_event() 共有四个参数：

第一个为按键的虚拟键值，如回车键为vk_return，tab键为vk_tab；

第二个参数为扫描码，一般不用设置，用0代替就行；

第三个参数为选项标志，如果为keydown则置0即可，如果为keyup则设成"KEYEVENTF_KEYUP"；

第四个参数一般也是置0即可。

{{< /admonition >}}

## 鼠标操作

```python
from selenium.webdriver.common.action_chains import ActionChains
```



### 全局鼠标操作语法如下：

1.初始化ActionChians类（动作链条）：
```
 actions = ActionChains(driver) 
```
2.找到要操作的元素：
```
 element = find_element (值)
```
3.调用鼠标操作方法： 
```
actions.move_to_element(element) 
```
4.执行鼠标操作方法： 

```
actions.perform()
```

调用 perform() 方法时，队列中的操作会依次进行。

### 常用方法

```python
double_click(on_element=None) #双击鼠标左键

#常用于拖拽验证码环节，不包括图像补全验证码。
source = driver.find_element_by_id(xxx)
target = driver.find_element_by_id(xxx)
drag_and_drop(source, target)
action.drag_and_drop(source, target).perform()  #拖拽元素

move_by_offset(xoffset, yoffset)  #鼠标从1位置移动到2坐标
release(on_element=None)  #在元素上释放按住的鼠标按钮（在某个元素位置松开鼠标左键）
```
{{< admonition type=info title="注意" open=true >}}
最终调用 perform() 方法时，队列中的操作才会依次进行。
{{< /admonition >}}



---

> 作者: [Scan](https://www.scan.work/)  
> URL: http://localhost:1313/posts/chromedriver%E8%BF%9B%E9%98%B6%E4%BD%BF%E7%94%A8/  

