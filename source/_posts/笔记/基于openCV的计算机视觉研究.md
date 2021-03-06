---
title: 基于openCV的计算机视觉研究
date: 2016-11-08
tags: [技术研究]
categories: 笔记
---

计算机视觉随着 AR，VR，自动驾驶，人工智能等技术的崛起，已经成为一个迅速发展并且富有前景的领域。openCV，计算机视觉开源库，与前端工程师经常打交道的 openGL 算是兄弟函数库。

openGL 作为专业的图形编程接口，为程序员提供了功能强大，调用方便的底层图形库。openCV 则致力于提供实现图像处理和计算机视觉的通用算法，降低了学习计算机设觉领域的门槛，让开发者可以更快的收获机器视觉带来的乐趣。

说了这么多然而我是一个前端工程师啊，我更关心人类的视觉体验，无缘无故怎么会闲的蛋疼跑来弄计算机视觉呢；好吧这其实是我的毕业设计题目，之前没有事先找老师拿题目，在选择毕业设计的那个早晨，我睡过了。。许多我擅长的软件题目都被选了，所以我还感兴趣的就剩下了比较难啃的计算机视觉还有深度学习相关的课题，老师说如果我下学期要去实习，推荐我选相对简单的计算机视觉。选好题目和实验室学长打过招呼拿了一些学习资料后，由于各种事情搁置了一段时间，最近闲起来，决定开始学习这个火热的领域。

回到正题，openCV 是用 C++编写的，但是依然有大量的 C 语言接口。该库也有大量的 Python,Java,MATLAB 的接口，由于我最近刚好在复习 C 语言，平时写算法如果不能用 ES6 也都是用 C，所以我觉得用 C 语言来完成后续的编程工作。

学习过程中，感受到了很多资料的缺失和落后，国外要好一点，而且社区也远远没有前端技术社区的氛围，有一些小坑解决起来还挺浪费时间的，今天我已经把 openCV 在 macOS 的开发环境，包括 xCode 的一些设置，作为前端 ST 信仰粉，一直秉持不到万不得已不用 IDE 的态度，然而这次为了调试还是用 xCode 吧，基于 sublime text 的 latex 论文书写环境也配置好了，接下来就是一边理论学习，一边实践操作的过程了，希望年前能有一个比较好的完成度，同时对这个领域能有一些比较深入的见解。

---

下面记录一下搭建环境的过程，后续会继续记录基于 openCV 的计算机视觉的研究成果~

### macOS 搭建基于 Xcode 的 openCV 开发环境

#### 下载源码：

mac 下运用 openCV 的函数需要自行编译源代码，官方的解释是由于包括 linux 和 macOS 的各类 Unix 系统各自带的编译器不同，所以需要多几个步骤，可以从官网上选择相应版本的 zip 下载。openCV 是使用非常宽松的 BSD 协议的开源项目，BSD 比起 MIT 而言在修改代码，重新封装之后不要求一定要公开源码，现在计算机视觉开发尚未普及，这么做也是保护使用 openCV 公司的商业机密，让开发者可以毫无顾虑的使用 openCV 这个视觉库，所以也可以直接从 github 上下载 openCV，

#### 编译源码：

下载 mac 编译源码的工具 cmake，直接一行 homebrew 代码搞定：

```
brew install cmake
```

然后解压下载的 zip 源代码，进入解压后的文件目录，创建一个存放 openCV 函数的目录 release，在该目录下执行命令开始编译。

```
mkdir release
cd release
cmake -G "Unix Makefiles"
make
make install
```

部分命令可能需要 sudo 权限。

#### Xcode 开发环境：

新建命令行 command line tool 工程，选择 C 或者 C++语言，
可以复制下面这段`hello world`式的开始程序来调试，用通常的打印信息的函数可以检验大多数编程环境，但是由于这里涉及到代码库是否成功引入等问题，所以运用一些 openCV 的通用函数来看能否成功运行。

```
#include <highgui.h>
int main(int argc, char **argv)
{
    IplImage *img = cvLoadImage( argv[1],-1);
    cvNamedWindow( "Example1", CV_WINDOW_AUTOSIZE);
    cvShowImage( "Example1", img);
    cvWaitKey(0);
    cvReleaseImage( &img);
    cvDestroyWindow( "Example1");
    return 0;
}
```

这段代码功能很简单，可以读取本地的一张图片并展示出来，这里参数 argv[1]需要替换为要显展示图片的地址，第二个参数有三个取值：-1 为默认读取图像的原通道数；0 为强制转化读取图像为灰度图；1 为读取彩色图，每个函数的用途非常简单好用：`IplImage` 是一个存储图片的结构体，`cvLoadImage` 为存储图片的方式，`cvNamedWindow` 创建一个名字为`Example1`的窗口，第二个参数`CV_WINDOW_AUTOSIZE`表示窗口大小根据加载的图片自动变化，`cvWaitKey(0)`含义是读取到键盘操作继续进行程序，`cvReleaseImage`和`cvDestroyWindow`是释放结构体和窗口的空间，可以省略，加上更严谨。

然后开始运行左上角的三角按钮，或者 cmd+B 构建，会提示找不到 highgui.h 的文件，所以我们还需要给这个项目配置头文件路径。

#### 配置头文件路径：

点一下左侧文件的项目根，中间代码区域找到 Bulid Setting，在搜索栏中输入 search
找到 Header search Paths 这一项添加 openCV 三个默认的编译存储地址：

```
/usr/local/include
/usr/local/include/opencv
/usr/local/include/opencv2
```

Library search Paths 这一项添加

```
/usr/local/lib
```

![](/pic/1-2.png)

完成之后编译运行程序，正常来说，应该还有报错和 40 多个 warning 警告，警告大部分是未使用的函数报警，这个无关紧要，分析错误查了一波资料哦后发现还缺少在项目中引入一些 openCV 的`.dylib`文件。

#### 引入.dylib 文件：

`.dylib`位于编译目录 release 的 lib 目录中，在项目根右击创建一个新组 lib，添加所有的`.dylib`文件进去即可成功实现第一个`helloworld`程序。
![](/pic/1-1.png)
