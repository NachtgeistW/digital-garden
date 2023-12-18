---
type: web-bookmark
date: 2023-12-04 12:41
dg-publish: false
archive: 
tags:
---
原文：
- [\#鹅厂技术干货# \#... - @鹅厂程序员的微博 - 微博 (weibo.com)](https://weibo.com/7483028645/JwIa9vlB3?pagetype=fav)
- [APP＆游戏需要关注Jank卡顿及卡顿率吗？ (weibo.com)](https://weibo.com/ttarticle/p/show?id=2309404577962614718697)

---

**导语**

　　本次技术干货分享主要是讲解 PerfDog 卡顿 Jank 定义、原理及影响。内容将分为五部分：**FrameTime、FPS、流畅度、Jank、影响。**从深层次分析在性能测试工作中这五部分起到的重要意义。

  

  

## FrameTime

  

- **定义：两帧画面间隔耗时(也可简单认为单帧渲染耗时)**

​

![[Pasted image 20231204124608.png]]

​

从图中可看出画面中B帧在GPU渲染耗时(帧生成时间)大于显示器刷新间隔,占用两次显示器刷新耗时。也就是说有一次画面没刷新。当出现多次没有画面刷新(也就是说画面没变化)，则可能是一次卡顿。

**从这里就得出结论：**玩家用户真正看到的是屏幕新画面刷新间隔时间，而不是eglSwapbuffers-GPU渲染完成(并未有提交屏幕显示)间隔时间。所以后面所提到Frametime统统指的是屏幕Display-Frametime。

**PerfDog工具优点：**PerfDog统计的FPS和Frametime都是用户看到的屏幕Display新画面真实刷新FPS和帧耗时。所以大家可以直接通过Frametime来判断测试过程中是否出现卡顿。

  

  

  

## FPS

- **FPS 的定义：**帧率（1秒内平均画面刷新次数）。

  

- **平均帧率：**传统常说的 FPS，1秒内平均画面刷新次数。

  

- **瞬时帧率：**单帧耗时 FrameTime 算出来实时 FPS，每一帧画面刷新耗时换算出的实时帧率。

  

画面渲染流程图如下，每一帧FrameTime。

​

![[Pasted image 20231204124619.png]]

​

PerfDog统计帧率及FrameTime如下图：

​

![[Pasted image 20231204124624.png]]

​

游戏与APP区别

- 强制刷新
- DirtyRedraw机制
- 脏区重绘

FPS帧率与卡顿是否有关系？我们分为Andriod和iOS两个场景去进一步了解：

**iOS**  
    

苹果WDDC18年开发者大会

①     FramePacing

  

比如下面两个游戏画面,左边的试图以60帧运行,但实际只能达到40帧;右边的则持续稳定在30帧运行:

​

![[Pasted image 20231204124637.png]]

​

上图左边帧率高,反而看起来有一卡一顿的现象,这就是Micro Stuttering(微型口吃)导致的,左边FrameTime如下图：

  

通过FrameTime可以看出，左边高帧率FPS=40帧率中出现一次FrameTim>=117ms，理论平均FrameTime=25ms。所以非均匀渲染，虽然帧率高达40，但依然觉得非常卡。

  

右边低帧率30帧运行FrameTime如下图：

​

![[Pasted image 20231204124644.png]]

​

从上面FrameTime可以看出，帧率FPS=30，FrameTime均匀为33ms，画面会非常平滑。

  

**总结：帧率高，未必流畅。**

**Android 端**

    测试系统版本：Android4.4

①     流畅度机制-黄油计划(Jank)

  

Google Jank 计算思路：考虑视觉惯性，以硬件vsync时间间隔，连续1次vsync没有新画面刷新，则认为是一次卡顿，也就是说下一次vsync时间点没有新画面刷新，则认为是一次Jank。

​

![[Pasted image 20231204124655.png]]

## 流畅度

![[Pasted image 20231204124701.png]]

  

- **视觉惯性**

视觉预期帧率，用户潜意识里认为下帧也应该是当前帧率刷新比如一直60帧，用户潜意识里认为下帧也应该是60帧率刷新一直25帧，用户潜意识里认为下帧也应该是25帧率刷新如果是60帧一下跳变为25帧，扰乱用户视觉惯性。

  

  

- **电影帧**

电影帧率(18-24)，一般是24帧。电影帧单帧耗时：1000ms/24=40ms。电影帧率是一个临界点。低于这个帧率，人眼基本能感觉画面不连续性

  

  

## PerfDog-Jank

**PerfDog Jank 计算思路**：考虑视觉惯性，假设以前三帧的平均帧耗时为参考，作为vsync时间间隔，连续两次vsync没有新渲染画面刷新，则认为是一次潜在卡顿，也就是说下一帧耗时大于前三帧平均帧耗时2倍，则认为一次潜在卡顿。同时单帧耗时满足大于两倍电影帧耗时1000ms/24*2 (由于人眼低于24帧才能辨别画面不连续性)，则认为是一次真正卡顿。同时若单帧耗时大于3倍电影帧耗时，则认为是一次严重卡顿。

  

**注解：**为什么是两次vsync？GPU一般是3重缓冲buffer，当前帧已占用一个buffer，即剩余2缓冲buffer，人眼一般可容忍2帧延迟。

  

为什么是两帧电影帧耗时？低于24帧画面，人眼就能感知到画面不连续性，电影一般都是24帧。即电影帧耗时1000ms/24=41.67ms，两帧电影帧耗时也就是41.67ms*2，三帧电影帧耗时是41.67ms*3。

  

**PerfDog Jank计算方法：**

同时满足两条件，则认为是一次卡顿Jank.

Display FrameTime>前三帧平均耗时2倍。

Display FrameTime>两帧电影帧耗时(1000ms/24*2=84ms)。

  

同时满足两条件，则认为是一次严重卡顿BigJank.

Display FrameTime >前三帧平均耗时2倍。

Display FrameTime >三帧电影帧耗时(1000ms/24*3=125ms)。

  

  

**Google-Jank如图：**

  

​

![[Pasted image 20231204124741.png]]

​

**PerfDog-Jank如图：**

​

![[Pasted image 20231204124747.png]]

​

**真实测试FrameTime计算Jank，如下图**

​

![[Pasted image 20231204124752.png]]

​

**PerfDog-Jank如图：**

​

![[Pasted image 20231204124757.png]]

​

**备注：帧率低，并不是顿卡，如帧率都是只有几帧了，还有什么顿卡而言？**

**目前主流游戏对比Jank(10分钟出现卡顿次数)如下：**

  

​

![[Pasted image 20231204124808.png]]

​

**PerfDog-web平台**

​

![[Pasted image 20231204124814.png]]

​

  

  

## PerfDog-Stutter(卡顿率)

  

**PerfDog Stutter 定义：**测试过程中，卡顿时长的占比。即Stutter(卡顿率)=卡顿时长/总时长

  

**PerfDog Stutter计算思路**：基于PerfDog Jank的基础上，一次Jank卡顿，会有一次卡顿时间Jank time。测试过程中可能有多次Jank卡顿，即有多次卡顿时间Jank time。测试总时长为Time。

  

Stutter(卡顿率)=∑Jank time / Time

  

**说明：**Jank为卡顿次数，Stutter为卡顿率，Jank和Stutter趋势有一致性，但并非完全线性，因为每次Jank卡顿严重性是不一样的。同时也说明了，没有Jank卡顿出现，自然也就卡顿率是0了。

  

**目前主流游戏对比如下：**

  

​

![[Pasted image 20231204124823.png]]

​

  

  

## 影响

  

- **游戏需要重点关注FPS、Jank、卡顿率？**

**游戏流畅度是最影响用户体验的，所以需要重点关注FPS、Jank及卡顿率。**

**备注：比如游戏中已预知的卡顿，如新UI弹出等造成卡顿，可认为是干扰，需要剔除，不应算在Jank，可通过web云上，框选右键删除干扰数据。**

- **APP需要重点关注FPS、Jank及卡顿率？**

APP也需要关注FPS、Jank及卡顿率。只是需要区分使用场景，如：

  

- 静态页面窗口

只需关注FPS，理论FPS应该为0，否则，说明有冗余刷新，容易引起手机发热及耗电。

  

- 有滚动动画页面窗口

只需关注FPS，FPS处于合适值即可，无需高频刷新。

  

- 快速滑动页面窗口。

需要关注FPS、Jank及卡顿率。手机交互灵敏度就是来源于此，Android系统才出黄油计划Jank。一般滑动状态下，帧率越高越好，Jank越小越好。

  

- 播放视频页面窗口。

需要关注FPS、 Jank及卡顿率，视频卡顿直接影响用户。视频一般帧率18-24帧，Jank=0。比如微信播放视频、视频播放器等。