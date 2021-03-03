[翻墙](https://portal.blinkload.zone/user)

[评估三维结果](https://vision.middlebury.edu/mview/)

[三维重建博客整理开源资料](https://www.cnblogs.com/YongQiVisionIMAX/p/13545977.html)

[大量总结资料，包括sfm的使用](https://www.geek-share.com/detail/2714834785.html)

[顶会解读](https://bbs.cvmart.net/topics/62/%E9%A1%B6%E4%BC%9A%E6%96%87%E7%AB%A0%E8%A7%A3%E8%AF%BB)



思路1:从开源资料网址找思路

思路2:从解读汇总找思路



It is the truth that China has did as much as he can and COVID-19 has been controlled in China, which is appreciated by WHO. Virus is the common enemy of mankind, isn't it? I am confused that it seems that you are proved of the youtube without vpn. In fact, I use the VPN for study and  



--------



[colmap 可以运行的](https://github.com/colmap/colmap)

[colmap教程](https://colmap.github.io/tutorial.html)

---------

[mve,可运行，Multi-View Environment](https://github.com/simonfuhrmann/mve)

**mve里面很多论文以后写论文也可以用到**

端到端几何重构的软件解决方案很少见也就不足为奇了。 原因在于技术复杂性以及创建此类集成工具所需的工作。 许多项目涉及管线的各个部分，例如，VisualSfM [Wu13]或Bundle [SSS06]用于运动结构，PMVS [FP10]用于多视图立体视图，以及泊松曲面重建[KH13]用于网格重建。 一些商业软件项目提供了涵盖SfM，MVS，表面重建和纹理化的端到端管道。 这包括Arc3D，Agisoft Photoscan和Acute3D Smart3DCapture。 我们 

----

[纹理信息,Algorithm to texture 3D reconstructions from multi-view stereo images](https://www.gcc.tu-darmstadt.de/home/proj/texrecon/)

------

# 一些想法

全局线性漂移，采用数学建模拼纸片算法



PMVS中初始化特征匹配和面片筛选过程中进行优化

几种组合，采用visual， visual+cmvs， bunder， bunder+cmvs,openmvg,openmvg+openmvs(openmvg+pmvs),scolmap

跑同一个数据集



-----

# 几个问题

## 三维重建步骤

###  sfm算法



SfM的输入是一段motion或者一时间系列的2D图群，如下图所示 [1]，这里不需要任何相机的信息。然后通过2D图之间的匹配可以推断出相机的各项参数。**Corresponding points可以用SIFT，SURF来匹配，也可以用最新的AKAZE（SIFT的改进版，2010）来匹配。而Corresponding points的跟踪则可以用Lucas-Kanede的Optical Flow来完成**。

在SfM中，误匹配会造成较大的Error，所以要对匹配进行筛选，目前流行的方法是RANSAC（Random Sample Consensus）。2D的误匹配点可以应用3D的Geometric特征来进行排除。

Bundler [2] 就是一种SfM的方法，Bundler使用了基于SIFT的匹配算法，并且对匹配进行了过滤去噪处理。下图显示了一组测试数据（一时间系列的2D图群）：



**将这些图片保存到同一个文件夹，然后将文件夹的目录输入，Bundler会自行处理，之后会得到一群Corresponding points。比如其中的一组Corresponding points (A1,A2,A3,...Am)，其实他们来自同一个三维点A的Projection。所以通过这些点可以重建三维点A。然后将很多组Corresponding points 进行重建，则得到了一群三维的点，这里称为3D点阵。**



作者：犀利哥的大实话
链接：https://zhuanlan.zhihu.com/p/29845703
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。





SFM算法是一种基于各种收集到的无序图片进行三维重建的离线算法。在进行核心的算法structure-from-motion之前需要一些准备工作，挑选出合适的图片。

   先从图片中提取焦距信息(之后初始化BA( Bundle adjust)需要)，然后利用SIFT等特征提取算法去提取图像特征，用kd-tree模型去计算两张图片特征点之间的欧式距离进行特征点的匹配，从而找到特征点匹配个数达到要求的图像对。对于每一个图像匹配对，计算对极几何，估计F矩阵并通过ransac算法优化改善匹配对。这样子如果有特征点可以在这样的匹配对中链式地传递下去，一直被检测到，那么就可以形成轨迹。
   之后进入structure-from-motion部分，关键的第一步就是选择好的图像对去初始化整个BA过程。首先对初始化选择的两幅图片进行第一次BA，然后循环添加新的图片进行新的BA，最后直到没有可以继续添加的合适的图片，BA结束。得到相机估计参数和场景几何信息，即稀疏的3D点云。其中两幅图片之间的bundle adjust用的是稀疏光束平差法sba软件包，这是一种非线性最小二乘的优化目标函数算法。

### 1.1 图像坐标系

[坐标系博客](https://blog.csdn.net/lhanchao/article/details/51867327)





### 2.1计算符合特征的图片

### 2.1.1特征检测 

​    对于特征检测这一步，使用的是具有尺度和旋转不变性的SIFT描述子,其鲁棒性较强，适合用来提取尺度变换和旋转角度的各种图片特征点信息，其准确性强，在这种离线算法不需要考虑时间成本的情况下也较有优势。SIFT算法通过不同尺寸的高斯滤波器(DOG)计算得到特征点的位置信息(x,y),同时还提供一个描述子descriptor信息，在一个特征点周围4*4的方格直方图中，每一个直方图包含8个bin的梯度方向，即得到一个4*4*8=128维的特征向量。除此之外，SIFT算法计算得到的尺寸scale和方向orientation两个信息并没有用上。

### 2.1.2特征匹配



第二步是匹配和建立track,图像对两两匹配,一般采用欧式距离.有两种方法：



-  粗暴匹配,对所有特征点都穷举计算距离
- 邻近搜索,**建立KD树,缩小搜索范围,能提高效率,但也有可能不是最优,所以邻域取值是关键,越大越准确,越大计算量越大**



[论文结构博客](https://blog.csdn.net/qq_33826977/article/details/79834735)



### 3. PMVS算法

[PMVS博客](https://blog.csdn.net/lhanchao/article/details/51885998)

## 几种组合之间的关系

## 一般论文侧重点



# 时间线

- 2.28
  1. 下载的两个paper带主页网页和代码，数据集。在实验室电脑桌面/论文 目录.  
  2.  github标记的数据集[github链接](https://github.com/AIBluefisher/ComputerVisionDatasets/tree/master/3d_reconstruction)

- 3.1 
  1. unbuntu编译好bunder和cmvs
  2. 编译好windows的visual+cmvs
  3. 计划：尝试ubuntu和windows的几种组合。 
- 3.3
  1. 

[bounder](http://www.cs.cornell.edu/~snavely/bundler/bundler-v0.3-manual.html#S6)

[dataset_bounder](http://phototour.cs.washington.edu/datasets/)

