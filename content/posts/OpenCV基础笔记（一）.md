---
title: "OpenCV——基础笔记(一)"
date: 2023-07-22T20:20:07+08:00
draft: true

description: 理解图像的本质,图片、颜色通道、转化方式、备份原图。
keywords:
license:
weight: 0
tags:
  - OpenCV
  - Python
  - 深度学习
categories:
  - Python
  - 深度学习
---
<!--more-->

## 导入需要的包

```python
import numpy as np
import matplotlib.pyplot as plt 
%matplotlib inline  ##嵌入画图在jupyter中使用，开发工具中去除
from PIL import Image
```

## 测试打开图片

```python
img = Image.open('123.jpeg')
img
```

## 转换为Numpy元组

```python
img_1 = np.asanyarray(img)
img_1.shape
#(960, 720, 3)  长，宽，3个颜色通道分别为Red、Green、Blue
```

![image-20230722164915639](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202307221649709.png)

## 复制一份图片

``` python
img_1_copy = img_1.copy()  #保持原图可二次修改
```

## 颜色通道修改

```python
plt.imshow(img_1_copy[:,:,0]) 
#单独显示了红色通道。 
img[:,:] = [255, 255, 0]  
# RGB值(255, 255, 0)代表黄色——颜色的混合
```

## Matplotlib的颜色映射

```
Matplotlib 提供了许多预定义的颜色映射。以下是一些常见的颜色映射：

- 'viridis'：默认的颜色映射，从深蓝色到黄色的渐变。
- 'plasma'：从深紫色到橙色的渐变。
- 'inferno'：从黑色到深红色的渐变。
- 'magma'：从黑色到白色，中间包含紫色和橙色的渐变。
- 'cividis'：从深蓝色到黄色的渐变，设计用于色盲友好。
- 'Greys'：从黑色到白色的灰度渐变。
- 'Purples'：从浅紫色到深紫色的渐变。
- 'Blues'：从浅蓝色到深蓝色的渐变。
- 'Greens'：从浅绿色到深绿色的渐变。
- 'Oranges'：从浅橙色到深橙色的渐变。
- 'Reds'：从浅红色到深红色的渐变。
```

### 几个栗子，参考参考

# 原图：一只小盲仔





![image-20230722171201568](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202307221712660.png)

![image-20230722171111728](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202307221711827.png)

![image-20230722171123073](https://fastly.jsdelivr.net/gh/hack-scan/Blog-pic/posts/202307221711187.png)

> Matplotlib的imshow()函数期望图像是RGB格式的。所以，如果你在OpenCV中处理图像（它使用BGR），然后想用Matplotlib显示图像，你需要将其从BGR转换为RGB。

```python
import cv2
from matplotlib import pyplot as plt

img_bgr = cv2.imread('image.jpg')

img_rgb = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)

plt.imshow(img_rgb)
plt.show()  #显示所有已经创建的图像
```



{{< admonition type=info title="注意" open=true >}}
图上所示：img_1_copy[:，:，0]) 



0、1、2 分别代表图像的所有行数，所有列，索引值



索引值说明：    0、1、2分别对应红、绿、蓝（RGB）   
如果使用OpenCV打开就是蓝、绿、红(BGR)。
{{< /admonition >}}

