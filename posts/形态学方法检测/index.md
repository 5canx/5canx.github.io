# 形态学检测方法

<!--more-->

# 书法文字检测形态学

## # 检测书法文字
# 步骤：
# 1、读取图片，灰度、二值化处理
```python
# 二值化
# 参数1：输入图片
# 参数2：比较的阈值
# 参数3：超出阈值被设定的值
# 参数4：模式
# 作用：将画面像素与比较阈值对比，小于它则设为0（黑色），大于它设为目标值
r,black_img = cv2.threshold(gray,100,255,cv2.THRESH_BINARY_INV)
plt.imshow(black_img,cmap='gray')
```
边缘检测
```python
#边缘检测
edges = cv2.Canny(图像值,30,200)
plt.imshow(edges,cmap='gray')
```
# 3、膨胀连接
# 4、闭合孔洞
# 5、边缘检测
# 6、画检测框


---

> 作者: [Scan](https://www.scan.work/)  
> URL: https://5canx.github.io/posts/%E5%BD%A2%E6%80%81%E5%AD%A6%E6%96%B9%E6%B3%95%E6%A3%80%E6%B5%8B/  

