# FFmpegH264DecodeAndOpenGL
FFmpegH264DecodeAndOpenGL(ffmpeg解码，h264解码，openGL渲染解码后的yuv)
教程讲解：http://www.jianshu.com/p/2ff327a748d8

修复OpenGLFrameView返回上一个控制器时崩溃的bug，（项目采用iOS-ARC模式，系统在推出控制器时自动调用dealloc方法里的release，不用程序员自己再次手写release，若是写了反而会崩溃，直接在
- (void)dealloc
{
 if(_renderer!=nil)
    {
//        [_renderer release];
        
//        [_renderer performSelector:releaseSelector()];

         _renderer = nil;
        
    }

就可以了，注释的地方不要加上去，谢谢🙏）


不管是手机摄像头还是一些芯片,他所采集到的实际上都是一帧一帧的图像,当帧数大于24-30的时候,对这些图像进行连续的播放,对人的感觉就像是视频在播放 . 常见的图片压缩格式有:png,jpg等等.

我们为什么要进行压缩呢?做一个假设,一张图片是2M,如果一段视频是30帧,那么他如果不经过处理,这段视频一秒钟的数据量就是60M,这在本地或者局域网倒是问题比较小,对一些发达国家来说或许也勉强可以,但对中国目前的互联网环境来说,一分钟传输60M文件,还是压力很大的,所以我们便不得不去研究压缩,在这里顺便提一句,一位前辈曾告诉过我,音视频开发到了后面,网络就成了瓶颈,这句话刚开始其实还不是很明白,但现在已经开始理解起来了.

H.264的压缩算法可以很有效的对单幅图片进行压缩,它的原理就是IBP帧算法,两幅图片对前后不同的地方进行传输,这样就可以对图像进行大幅度的压缩.如下图所示,画的不太好,意思一下.黄色箭头就是两幅图不同的地方（小人的头歪了）.


小人的头歪了
H.264压缩思想示例

后来推出了H.265标准在720p画质以下时区别是不大的,在传播4k这样的画质时就可以看出明显区别了,H.265的压缩率会更高.

像iPhone和安卓手机内部都是有硬编码的芯片的,他的效率非常高,如果没有硬编码芯片,我们就需要使用FFmpeg,X264对其进行编码了.（h264在ios8上是可以硬解的）而硬解码的芯片一般第三方是没有权限去使用的，所以这时候我们就要用到FFmpeg了，像这种解码的方式我们就称其为软解，这种方式的解码效率相比硬解码来讲是偏低的，而我们的目的就是通过一些手段和方法来提高软解码的效率。

H.264解码工具

在项目这里的三个文件是封装好的解码工具,注释这里我没有详细的注释,若果有不明白的地方,请翻阅第三篇文章,基本是类似的.

在这个工具类中取消了使用sws_scale()函数来进行yuv->rgb的过程,他太消耗性能,我们可以使用libyuv或者交给Opengl的shader让他利用硬件来进行转换,这个效率非常高.下面推荐一篇关于yuv->rgb优化算法的一篇文章:链接.：http://blog.csdn.net/wxzking/article/details/4339650

这是雷博关于sws_scale()函数的介绍:链接：http://blog.csdn.net/leixiaohua1020/article/details/44346687

这是雷博关于sws_scale()函数性能的测试:链接：http://blog.csdn.net/leixiaohua1020/article/details/12029505


Opengl ES渲染工具
这个大体的逻辑就是将YUV420P数据通过rander转化为RGB数据,再利用纹理贴图显示出来,这是我找到的一个比较好的渲染工具类.工具类本身是继承自UIView的,上面的scrollerview可以实现缩放等功能.


Opengl ES渲染工具类
工程中还有很多bug和不完善的 地方（但是不会影响我们学习ffmpeg解码和openGL渲染yuv（原本是想用SDL显示解码后的yuv，试了多次sdl失败就改用openGL渲染）），端午节来后会陆续修改，争取能够用openGL可以完整的渲染视频的yuv

最后demo地址：

谢谢大家！！！

更多探索欢迎加入iOS-ffmpeg：qq群：群号:224110749

