# cv2.imread 和 PIL 用法汇总和区别

1. `image.open()`得到的是img数据类型是Image对象，不是普通的数组；`cv2.imread()`得到的img数据类型是np.array()类型
2. 对于`Image.open()`函数默认彩色图像读取通道的顺序为RGB，而`cv2.imread()`读取通道的顺序为BGR。同时，当图像格式为RGBA时，Image.open(‘—.jpg’)读取的格式为RGBA（其中A表示图像的alpha通道，即RGBA共四个通道），而cv2.imread(’—.jpg’)读取的格式是BGR，只有三个通道。
   
### openCV用法
使用函数 `cv2.imread(filepath,flags)` 读入一张图片
#### 1.读入图像
* filepath：图片的完整路径
* flags:读入图片的标志
    * cv2.IMREAD_COLOR:默认参数，读入一张彩色图片，忽略alpha通道
    * cv2.IMREAD_GRAYSCALE：读入灰度图片
    * cv2.IMREAD_UNCHANGED：顾名思义，读入完整图片，包括alpha通道
  
```python
import numpy as np
import cv2

img = cv2.imread('1.jpg',cv2.IMREAD_GRAYSCALE)
```
#### 2.显示图像
使用函数`cv2.imshow(wname,img)`显示图像，第一个参数是显示图像的窗口的名字，第二个参数是要显示的图像（imread读入的图像），窗口大小自动调整为图片大小。
```python
cv2.imshow('image',img)
cv2.waitKey(0)
cv2.destroyAllWindows() #dv2.destroyWindow(wname)
```
cv2.waitKey顾名思义等待键盘输入，单位为毫秒，即等待指定的毫秒数看是否有键盘输入，若在等待时间内按下任意键则返回按键的ASCII码，程序继续运行。若没有按下任何键，超时后返回-1。参数为0表示无限等待。不调用waitKey的话，窗口会一闪而逝，看不到显示的图片。
cv2.destroyAllWindow()销毁所有窗口
cv2.destroyWindow(wname)销毁指定窗口
#### 3.保存图像
使用函数`cv2.imwrite(file，img，num)`保存一个图像。第一个参数是要保存的文件名，第二个参数是要保存的图像。可选的第三个参数，它针对特定的格式：对于JPEG，其表示的是图像的质量，用0 - 100的整数表示，默认95；对于png ,第三个参数表示的是压缩级别，默认为3。
注意:
cv2.IMWRITE_JPEG_QUALITY类型为 long，必须转换成 int。
cv2.IMWRITE_PNG_COMPRESSION，从0到9 压缩级别越高图像越小

```python
cv2.imwrite('1.png',img, [int(cv2.IMWRITE_JPEG_QUALITY), 95])
cv2.imwrite('1.png',img, [int(cv2.IMWRITE_PNG_COMPRESSION), 9])
```
### PIL用法
使用Image.open读出图像，如果不使用.convert(‘RGB’)进行转换的话，读出来的图像是RGBA四通道的，A通道为透明通道，使用convert(‘RGB’)转换后就去掉透明通道

save函数
image.save()函数
注意： 针对于保存为JPG/JPEG格式的图片的情况。
在使用Pillow中的Image.save()方法，使用默认参数保存jpg图片的过程中发现图片被压缩的很严重，导致原来很大的大小变成几十K。这是因为在保存为jpg的过程中，内部使用压缩算法对图片进行的压缩处理。
自定义压缩质量参数：

imObj.save(img_name, quality=(1:100))

quality参数： 保存图像的质量，值的范围从1（最差）到95（最佳）。 默认值为75，使用中应尽量避免高于95的值; 100会禁用部分JPEG压缩算法，并导致大文件图像质量几乎没有任何增益。使用此参数后，图片大小会增加。
自定义图片大小：

imObj.save(new_name, quality=(1:100), subsampling=0/1/2)

subsampling参数：子采样，通过实现色度信息的分辨率低于亮度信息来对图像进行编码的实践。
采样值0,1和2，对应于4：4：4, 4：2：2 和 4：2：0。
