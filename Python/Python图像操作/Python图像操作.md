**常用的图像操作库有PIL，opencv以及torchvision下的transforms等。**
<br/>

# opencv
```python
import cv2
```
#### 读写
```python
img = cv2.imread("1.jpg")
cv2.imwrite('1.png', img)
```
其中，读入图像时，可以指定读入方式：
```python
img = cv2.imread('1.jpg', 0)
```
图像路径后的标志位表示图像读入方式，0代表处理为灰度图，1代表普通的RGB形式（默认为1）。还有-1表示在RGB的基础上再读入图像Alpha通道。

#### 展示
```python
cv2.imshow('title',img)
cv2.waitKey()
```
会生成一个图像展示窗口，第一个参数为窗口标题。

cv2.waitKey()的作用是：生成窗口，按任意键关闭窗口。
<br/><br/>

# PIL
```python
from PIL import Image
```
#### 读写
```python
img = Image.open('1.jpg')
img.save('2.jpg')
```
#### 查看图像尺寸及通道数
```python
print(img.size) #尺寸
print(len(img.split())) #通道数
```
#### 格式转换
PIL转numpy：
```python
import numpy as np
n_img = np.array(p_img)
```
numpy转PIL：
```python
from PIL import Image
p_img = Image.fromarray(n_img)
```
RGB转灰度图：
（gray即灰度图）
```python
from PIL import Image
img = Image.open(img_path)
gray = img.convert('L')
```
<br/><br/>

# torchvision下transforms操作
```python
from torchvision import transforms
```

#### transforms.Compose
组合多个操作，逐一应用。

例如将图像放缩后再裁剪：
```python
changeSize = transforms.Compose([
    transforms.Resize((64, 64)),
    transforms.CenterCrop((50, 50)),])
   
img = changeSize(img)
```
#### transforms.CenterCrop
参数：(h, w)，例如：
```python
centerCrop = torchvisiontransforms.CenterCrop((768, 1024))
image = centerCrop(image)
```
原图最中间的1024×768个像素会被取出，图像其他部分会被舍弃。当然，这个参数可以大于原图的尺寸。

#### transforms.ToTensor
将numpy或PIL格式的图像转为tensor的形式，在转换的时候，会对图像进行归一化处理（即像素值除以255）。
