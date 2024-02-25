**常用的图像操作库有PIL，opencv以及torchvision下的transforms等。**
<br/>

# opencv
```py
import cv2
```
## 读写
```py
img = cv2.imread("1.jpg")
cv2.imwrite('1.png', img)
```
其中，读入图像时，可以指定读入方式：
```py
img = cv2.imread('1.jpg', 0)
```
图像路径后的标志位表示图像读入方式，0代表处理为灰度图，1代表普通的RGB形式（默认为1）。还有-1表示在RGB的基础上再读入图像Alpha通道。

## 展示
```py
cv2.imshow('title',img)
cv2.waitKey()
```
会生成一个图像展示窗口，第一个参数为窗口标题。

cv2.waitKey()的作用是：生成窗口，按任意键关闭窗口。
<br/><br/>

# PIL
```py
from PIL import Image
```
## 读写
```py
img = Image.open('1.jpg')
img.save('2.jpg')
```
## 获取图像信息

## 查看图像尺寸及通道数
```py
print(img.size) #尺寸
print(len(img.split())) #通道数
```
## 修改
### 缩放
```py
img.resize((h, w))   # 缩放至h*w像素
```
### 格式转换
PIL转numpy：
```py
n_img = np.array(p_img)
```
numpy转PIL：
```py
from PIL import Image
p_img = Image.fromarray(n_img)
```
RGB图像转灰度图：
```py
from PIL import Image
img = Image.open(img_path)
grayImg = img.convert('L')
```
<br/><br/>

# torchvision下transforms操作
```py
from torchvision import transforms
```

## transforms.Compose
组合多个操作，逐一应用。

例如将图像放缩后再裁剪：
```py
changeSize = transforms.Compose([
    transforms.Resize((64, 64)),
    transforms.CenterCrop((50, 50)),])
   
img = changeSize(img)
```
## transforms.CenterCrop
参数：(h, w)，例如：
```py
centerCrop = torchvisiontransforms.CenterCrop((768, 1024))
image = centerCrop(image)
```
原图最中间的1024×768个像素会被取出，图像其他部分会被舍弃。当然，这个参数可以大于原图的尺寸。

## transforms.ToTensor
将numpy或PIL格式的图像转为tensor的形式，在转换的时候，会对图像进行归一化处理（即像素值除以255）。
