# 路径问题
这是玄学，不是问题。遇到了请绝对路径改相对路径，相对路径改绝对路径，并祈祷有效。
<br/><br/>

# Import
#### import pyx
pyx文件的import需要Cython库的支持，因此要先装Cython，并在import相应pyx库前加入：
```python
import pyximport
pyximport.install()
```
若再出现fatal error: numpy/arrayobject.h: No such file or directory错误，则在cmd执行创建相应库的代码时：
```bash
python setup.py build_ext --include-dirs=C:\ProgramData\Anaconda3\envs\pytorch\Lib\site-packages\numpy\core\include
```
后面的地址因人而异，填自己电脑里的numpy地址即可。

#### 缺少concurrent模块
python 3.X版本自带，python 2.X安装：pip install futures

#### 同级文件引用

同文件夹下的a.py要引用b.py：

```python
#This code file is named "a.py"
from . import b
```
<br/><br/>

# Tensorflow显存未释放
Tensorflow运行时默认会加载全部显存，这样一来可能其他程序就无法执行了，解决方式：
```python
import tensorflow as tf
 
config = tf.ConfigProto()
config.gpu_options.allow_growth = True
session = tf.Session(config=config, ...)
```

来多少任务用多少显存，不会直接霸占全部，或者直接设置最大使用量（30%）：
```
config = tf.ConfigProto()
config.gpu_options.per_process_gpu_memory_fraction = 0.3
session = tf.Session(config=config, ...)
```
#### Keras
```python
import os
import tensorflow as tf
 
os.environ["CUDA_VISIBLE_DEVICES"] = "0"
from keras.backend.tensorflow_backend import set_session
config = tf.ConfigProto()
config.gpu_options.per_process_gpu_memory_fraction = 0.3
set_session(tf.Session(config=config))
```
通过os设置程序所使用的显卡（标号从“0”开始），后面设置使用显存占全部显存的百分比（30%）。

执行结束，释放显存：
```python
from keras import backend as K
K.clear_session()
```
参考博文：https://www.jianshu.com/p/3f8f46b0b1f2?tdsourcetag=s_pctim_aiomsg

<br/><br/>

# Pytorch下的维度问题
#### slice index 1 of dimension 0 out of bounds
在跑深度学习模型的时候遇到的错误，一般是模型参数的问题，如果经检查发现参数没问题，改下batch_size试试（改大或改小，不改维度）……

#### invalid argument 0: Sizes of tensors must match except in dimension 0.
可能情况一：数据集中图片尺寸不一致，统一尺寸即可。

可能情况二：数据集中样本总数与batch size不匹配，如样本总数无法整除以batch size，更改batch size即可。

#### 1only batches of spatial targets supported
图像分割任务中，训练时经模型预测输出的内容格式为[batchsize, n_class, height, weight]，而图像分割任务中的标签一般是单通道的，格式为[batchsize, channel=1, height, weight]。若损失函数为CrossEntropyLoss()，则在计算损失函数时应将真实标签格式调整为[batchsize, height, weight]，否则便会报出上述错误。因此，需要对标签的tensor降维：
```python
true_labels = true_labels.squeeze()
```
<br/><br/>

# 其他
#### %matplotlib inline
用于Jupyter Notebook的.ipynb文件中，作用是可令matplotlib的画图函数以Jupyter Notebook为画布绘制图像，在Jupyter中使用很方便，但移植到普通的.py中则是错误命令，注释掉即可。

#### 文件读写时的转义字符
```python 
f = open('a.txt','w')
f.write('132\n321')
f.close()
 
f = open('a.txt','r')
str = f.read()    # str = '132\n321'
f.close()
 
f = open('a.txt','rb')
str = f.read()    # str = '132\r\n321'
f.close()
```
Pycharm编译器下，正常往txt中写一个\n，文本读入(参数为'r')是一个\n，但在二进制读入（参数为'rb'）的情况下变成了\r\n。

而正常往txt中写一个\r，二进制读入的情况下是\r，但文本读入却变成了\n。

按照网上的说法，\r表示回车（Carriage Return，CR），作用是使光标回到行头，\n表示换行（Line Feed，LF），作用是使光标移动到下一行，两者一起使用才相当于按下“Enter”键。

在Pycharm中，编译器会以二进制格式读入文件后在\n前添加\r，在文本格式读入文件后自动将\r转换成\n。（仅在Windows系统下）

若要避免前者的发生，对字符串进行赋值、replace操作是没用的，应在写文件的时候：
```python
f = open('a.txt','w',newline='\n')
```
这样编译器就不会自作主张在\n前添加\r了。

不同操作系统的文件系统采用了不同的回车换行操作，在Unix系统中，每行结尾只有“\n”，即LF模式，而Windows系统中文件每行结尾为“\r\n”，即CRLF模式。
