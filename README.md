# PCV_Assignment_03
try warp.image_in_image
## 单应性变换
### 直接线性变换算法
### 仿射变换
## 图像扭曲
对图像块应用仿射变换被称为图像扭曲。扭曲操作可以用SciPy工具包中的ndimage包来简单完成：
## alpha通道
阿尔法通道是一个8位的灰度通道，该通道用256级灰度来记录图像中的透明度信息，定义透明、不透明和半透明区域，其中白表示不透明，黑表示透明，灰表示半透明。
### image in image
原图：
  
图1：
  
![图1](https://github.com/Heured/PCV_Assignment_03/blob/master/data/P90317-102402-1.jpg)
  
图2：
  
![图2](https://github.com/Heured/PCV_Assignment_03/blob/master/data/P90319-152912.jpg)
代码如下：
  
```python
 # -*- coding: utf-8 -*-
from PCV.geometry import warp, homography
from PIL import Image
from pylab import *
from scipy import ndimage

# example of affine warp of im1 onto im2

im1 = array(Image.open('./data/P90317-102402-1.jpg').convert('L'))
im2 = array(Image.open('./data/P90319-152912.jpg').convert('L'))
# set to points
tp = array([[532,1984,2014,599],[824,810,3354,3371],[1,1,1,1]])
#tp = array([[675,826,826,677],[55,52,281,277],[1,1,1,1]])
im3 = warp.image_in_image(im1,im2,tp)
figure()
gray()
subplot(141)
axis('off')
imshow(im1)
subplot(142)
axis('off')
imshow(im2)
subplot(143)
axis('off')
imshow(im3)


# set from points to corners of im1
m,n = im1.shape[:2]
fp = array([[0,m,m,0],[0,0,n,n],[1,1,1,1]])
# first triangle
tp2 = tp[:,:3]
fp2 = fp[:,:3]
# compute H
H = homography.Haffine_from_points(tp2,fp2)
im1_t = ndimage.affine_transform(im1,H[:2,:2],
(H[0,2],H[1,2]),im2.shape[:2])
# alpha for triangle
alpha = warp.alpha_for_triangle(tp2,im2.shape[0],im2.shape[1])
im3 = (1-alpha)*im2 + alpha*im1_t
# second triangle
tp2 = tp[:,[0,2,3]]
fp2 = fp[:,[0,2,3]]
# compute H
H = homography.Haffine_from_points(tp2,fp2)
im1_t = ndimage.affine_transform(im1,H[:2,:2],
(H[0,2],H[1,2]),im2.shape[:2])
# alpha for triangle
alpha = warp.alpha_for_triangle(tp2,im2.shape[0],im2.shape[1])
im4 = (1-alpha)*im3 + alpha*im1_t
subplot(144)
imshow(im4)
axis('off')
show()
```
  
  
遇到问题：运行时，warp.py会import matplotlib.delaunay，但是不知道是什么原因环境里的matplotlib没有delaunay，
鉴于本次实验并没有用到delaunay所以我把这段代码注释了
  
```
from scipy.spatial import Delaunay as md
#import matplotlib.delaunay as md 
```
  
  
结果：
  
![emmmm](https://github.com/Heured/PCV_Assignment_03/blob/master/imgToShow/Figure_1.png)
