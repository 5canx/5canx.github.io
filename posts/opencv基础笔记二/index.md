# OpenCV——基础笔记(二)


<!--more-->

## 图片修改与翻转

### 修改大小

```python
img_size = cv2.resize(img,(1000,300))   #修改大小
plt.imshow(img)
img_size.shape  #是以 宽，长 显示而不是长，宽
```
### 翻转
```python
# 0 垂直翻转  1  水平翻转 -1 水平锤直都翻转
img_flip= cv2.flip(img_fix,值)
```

## 创建黑色画布

```python
img = np.zeros((300, 300, 3), dtype = "uint8")

pts = np.array([[10, 50], [200, 50], [90, 200]], np.int32)

pts = pts.reshape((-1, 1, 2))
```

## 基础画线画图

### 画线

```python
cv2.line(img, (起始点), (结束点), (颜色), thickness=10)
cv2.imshow('窗口名称', img)
cv2.waitKey(0)  #无限等待，按任意键退出
cv2.destroyAllWindows()  #关闭窗口
```

### 画圆

```python
cv2.circle(img=img,center=(圆心的坐标),radius=半径,color=(0,0,255),thickness=10)  #圆心坐标先是 Y轴 再是X轴
cv2.imshow('窗口名称', img)
cv2.waitKey(0)  #无限等待，按任意键退出
cv2.destroyAllWindows()  #关闭窗口
```

### 画矩形 / 多边形

#### 矩形

```python
cv2.rectangle(img, (左上角坐标), (右下角坐标), (0, 255, 0), 10)
cv2.imshow('窗口名称', img)
cv2.waitKey(0)  #无限等待，按任意键退出
cv2.destroyAllWindows()  #关闭窗口
```

#### 多边形（以三角形为例）

```python
pts = np.array([[300, 10], [200, 50], [150, 140]], np.int32)
#三角形的三个点

pts = pts.reshape((-1, 1, 2))
cv2.polylines(img, [pts], True, (0, 255, 0), 3)  
#True线条是否闭合  pts为列表

cv2.imshow('窗口名称', img)
cv2.waitKey(0)  #无限等待，按任意键退出
cv2.destroyAllWindows()  #关闭窗口
```

{{< admonition type=info title="注意" open=true >}}
创建一个新的图像或者调整图像的大小时为（Y，X）形式表示

其余都为（X，Y）形式表示

{{< /admonition >}}


---

> 作者: [Scan](https://www.scan.work/)  
> URL: https://hack-scan.github.io/posts/opencv%E5%9F%BA%E7%A1%80%E7%AC%94%E8%AE%B0%E4%BA%8C/  

