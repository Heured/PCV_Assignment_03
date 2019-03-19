# PCV_Assignment_03
try warp.image_in_image
## 单应性变换
1. 术语：projective transformation = homography = collineation
  
2. 齐次坐标：使用n+1维坐标来表示n维坐标，比如在二维笛卡尔坐标系中加入变量w来形成二维齐次坐标：(x,y)->(x,y,w)
  齐次坐标具有规模不变性，同一点可以被无数多个齐次坐标来表示。(x,y,1)->(ax,ay,a)齐次坐标可以通过同除坐标最后一项得到笛卡尔坐标。
  
3. 单应性变换是对齐次坐标下点的线性变换，可以通过矩阵运算来表达x'=Hx
  
![单应性变换](https://github.com/Heured/PCV_Assignment_03/blob/master/imgToShow/单应性变化.png)(H为非奇异矩阵)
  
4.一些基础变换
  
(1)刚体变换(rigid transformation): 旋转和平移/rotation，translation，3个自由度，点与点之间距离不变
  
![刚体变化](https://github.com/Heured/PCV_Assignment_03/blob/master/imgToShow/刚体变换.PNG)(R为2*2旋转矩阵，t为2维列向量，0^T为0的二维行向量)
  
(2)相似变换(similarity transformation): 增加了缩放尺度, 四个自由度，点与点之间的距离比不变
  
![相似变换](https://github.com/Heured/PCV_Assignment_03/blob/master/imgToShow/相似变换.PNG)(s为缩放尺度)
  
(3)仿射变换(affine transformation)：仿射变换和相似变换近似，不同之处在于相似变换具有单一旋转因子和单一缩放因子，仿射变换具有两个旋转因子和两个缩放因子，因此具有6个自由度，不具有保角性和保持距离比的性质，但是原图平行变换后仍然是平行线
  
![仿射变换](https://github.com/Heured/PCV_Assignment_03/blob/master/imgToShow/仿射变换.PNG)
![仿射变换2](https://github.com/Heured/PCV_Assignment_03/blob/master/imgToShow/仿射变换2.PNG)
  
(4)投影变换(projective transformation)：也叫做单应性变换。投影变换是齐次坐标下非奇异的线性变换。然而在非齐次坐标系下是非线性的，这说明齐次坐标的发明是很有价值的。投影变换比仿射变换多2个自由度，具有8个自由度。上面提到的仿射变换具有“不变”性质，在投影变换中已不复存在了。尽管如此，它还有一项不变性，那就是在原图中保持共线的3个点，变换后仍旧共线。投影变换表示如下：
  
![投影变换](https://github.com/Heured/PCV_Assignment_03/blob/master/imgToShow/投影变换.PNG)(其中V=(v1,v2)^T)
  
(5)透视变换：透视变换将图像投影到一个新的视平面，是二维到三维再到另一个二维(x', y')空间的映射。透视变换前两行和仿射变换相同，第三行用于实现透视变换。透视变换前后，原来共线的三个点，变换之后仍然共线。
  
![透视变换](https://github.com/Heured/PCV_Assignment_03/blob/master/imgToShow/透视变换.PNG)
  
以上公式设变换之前的点是z值为1的点，它三维平面上的值是x,y,1，在二维平面上的投影是x,y，通过矩阵变换成三维中的点X,Y,Z，再通过除以三维中Ｚ轴的值，转换成二维中的点x’,y’.  
从以上公式可知，仿射变换是透视变换的一种特殊情况．它把二维转到三维，变换后，再转映射回之前的二维空间（而不是另一个二维空间）
  
  
  
## 图像扭曲
对图像块应用仿射变换被称为图像扭曲。扭曲操作可以用SciPy工具包中的ndimage包来简单完成。


## alpha通道
阿尔法通道是一个8位的灰度通道，该通道用256级灰度来记录图像中的透明度信息，定义透明、不透明和半透明区域，其中白表示不透明，黑表示透明，灰表示半透明。例如：一个使用16位存储的图片，可能5位表示红色，5位表示绿色，5位表示蓝色，1位是阿尔法。在这种情况下，它要么表示透明要么不是。一个使用32位存储的图片，每8位表示红绿蓝，和阿尔法通道。在这种情况下，就不光可以表示透明还是不透明，阿尔法通道还可以表示256级的半透明度。
  
  
## 图像中的图像
作为仿射扭曲的一个简单例子：将图像或者图像的一部分放置在另一幅图像中，使得它们能够和指定区域或者标记物对齐。
  
  
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
  
  
解决方法：以如下方式修改（加注释的是没改前的，加注释下一行是修改后的）
  
```
#import matplotlib.delaunay as md 
from scipy.spatial import Delaunay

def triangulate_points(x,y):
    """ Delaunay triangulation of 2D points. """
    
    #centers,edges,tri,neighbors = md.delaunay(x,y)
    tri = Delaunay(np.c_[x,y])
    
    return tri

```
  
  
  
结果：
  
![emmmm](https://github.com/Heured/PCV_Assignment_03/blob/master/imgToShow/Figure_1.png)
