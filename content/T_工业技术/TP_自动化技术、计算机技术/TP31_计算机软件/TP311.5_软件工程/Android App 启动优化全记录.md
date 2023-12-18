---
type: web-bookmark
date: 2023-12-06 17:50
dg-publish: false
archive: 
tags:
---
原文：[Android App 启动优化全记录 · Android Performance](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/)

---

本文参考了目前大部分 Android 应用启动优化的方案，将大家的方案做一个汇总，如果你有这方面的需求，只需要对照这篇文章，看看其他人的方案，查漏补缺。很多方案是要根据具体的业务去做优化的，所以这里也没有对每一种方案进行详细的介绍，要用到哪一个方案的时候，可以具体去网上查找对应方案的具体实现方法，这里只是做一个汇总

另外我还加上了部分系统厂商所做的启动相关的优化，不过只写了一些我知道的，还有一些厂商有黑科技，就不在这里的讨论范围了。知道厂商做的事情，可能也会帮助到你，比如联系厂商做白名单、接入厂商 SDK 等

# 应用启动概述

## 应用启动的一般流程[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E5%BA%94%E7%94%A8%E5%90%AF%E5%8A%A8%E7%9A%84%E4%B8%80%E8%88%AC%E6%B5%81%E7%A8%8B)

应用的启动，从桌面点击应用图标到主界面用户可操作，大致遵循下面的流程：

[![](https://androidperformance.com/images/15740895170994.jpg)](https://androidperformance.com/images/15740895170994.jpg)

可以看到应用启动过程中，最重要的两个进程就是 SystemServer 和 App Process . 其职责划分如下：

- **SystemServer 负责应用的启动流程调度、进程的创建和管理、窗口的创建和管理(StartingWindow 和 AppWindow) 等**
- **应用进程被 SystemServer 创建后，进行一系列的进程初始化、组件初始化(Activity、Service、ContentProvider、Broadcast)、主界面的构建、内容填充等**

## 冷启动和热启动[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E5%86%B7%E5%90%AF%E5%8A%A8%E5%92%8C%E7%83%AD%E5%90%AF%E5%8A%A8)

这里还需要引入冷启动和热启动的概念，这也是我们经常会碰到的两个概念

- **冷启动**：当启动应用时，后台没有该应用的进程，这时系统会重新创建一个新的进程分配给该应用，然后再根据启动的参数，启动对应的进程组件，这个启动方式就是冷启动
- **热启动**：当启动应用时，后台已有该应用的进程（例：按back键、home键，应用虽然会退出，但是该应用的进程是依然会保留在后台，可进入任务列表查看），所以在已有进程的情况下，这种启动会从已有的进程中来启动对应的进程组件，这个方式叫热启动

## 启动速度的测量[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E5%90%AF%E5%8A%A8%E9%80%9F%E5%BA%A6%E7%9A%84%E6%B5%8B%E9%87%8F)

各家应该都有自己的方案，关键在于如何定义启动结束的点，这个也是一直困扰我的一个地方，有的应用很好定义，有的应用则因为比较复杂，无法直接衡量启动速度。像 adb 这种方法自己玩玩可以，生产环境没啥用；录屏本身就有性能损耗..

这里我建议大家学习[历时1年，上百万行代码！首次揭秘手淘全链路性能优化（上）](https://mp.weixin.qq.com/s?__biz=MzAxNDEwNjk5OQ==&mid=2650403370&idx=1&sn=b4297b138eb7f73c95a6279c3458f025&chksm=83953a32b4e2b3247fc18cbee08a2682d8b09720a1c5fef0c36257ae92b1e201cb1ad3125455&mpshare=1&scene=1&srcid=#rd)中提到的测量方法：自动化、稳定、持续集成

> 通过OCR提取图片中的文字信息作为关键特征。该算法的优势：1. 在于应用页面上基本都是有文字的， OCR也可以识别到图片上的文字， 文字出现则图片加载完成， 和用户体感是一致的；2. 文字作为特征，过滤掉了很多图片特征可能带来的噪声， 减少了算法调试的工作量；另外阿里集团内有非常成熟和优秀的OCR服务——读光，文档识别率超过99.7%， 使用水滴平台封装的OCR服务，可以快速接入和使用。最终的识别方案就是基于OCR识别来进行的

# App 优化

## 启动窗口优化[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E5%90%AF%E5%8A%A8%E7%AA%97%E5%8F%A3%E4%BC%98%E5%8C%96)

启动窗口，也叫启动页、SplashWindow、StartingWindow 等，指的是应用启动时候的预览窗口。iOS App 强制有一个启动页，用户点击桌面 App 图标之后，系统会立即显示这个启动窗口，等 App 主页加载好之后再显示主页面。Android 也有类似的机制 (启动窗口这个是 Android 系统提供的)，但是也提供了一个接口，让应用开发者设置是否显示这个启动窗口(默认是显示)，部分开发者会把这个系统提供的启动窗口禁掉，启动自己的窗口。

但是启动自己的窗口需要的时间要比直接显示系统的启动窗口所花的时间要长，这就会导致用户在使用的时候，点击图标启动 App 的时候，有一定的延迟，表现在点击图标过了一段时间才进行窗口动画进入 App，我们要尽量避免这种情况

- **不要禁止系统默认的启动窗口：即不要在主题里面设置 android:windowDisablePreview 为 true**
- 自己定制启动窗口的内容，比如将启动页主题背景设置成闪屏页图片，或者尽量使闪屏页面的主题和主页一致。可以参考知乎、抖音的做法
- 合并闪屏和主页面的 Activity ：微信的做法，不过由于微信设置了 android:windowDisablePreview ， 且他在各个厂商的白名单里面，一般不会被杀，冷启动的机会比较少。不过也是一个可以思考的地方

## 线程优化[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E7%BA%BF%E7%A8%8B%E4%BC%98%E5%8C%96)

线程优化主要是减少 CPU 调度带来的波动，让启动时间更稳定。如果启动过程中有太多的线程一起启动，会给 CPU 带来非常大的压力，尤其是比较低端的机器。过多的线程同时跑会让主线程的 Sleep 和 Runnable 状态变多， 增加了应用的启动速度，优化的过程中要注意：

- 控制线程数量 – 线程池
- 检查线程间的锁 ，防止依赖等待
- 使用合理的启动架构
    - 微信内部使用的 mmkernel
    - 阿里 Alpha

## 系统调度优化[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E7%B3%BB%E7%BB%9F%E8%B0%83%E5%BA%A6%E4%BC%98%E5%8C%96)

应用启动的时候，如果主线程的工作过多，也会造成主线程过于繁忙，下面几个系统调度相关的点需要注意：

- **启动过程中减少系统调用**，避免与 AMS、WMS 竞争锁。启动过程中本身 AMS 和 WMS 的工作就很多，且 AMS 和 WMS 很多操作都是带锁的，如果此时 App 再有过多的 Binder 调用与 AMS、WMS 通信，SystemServer 就会出现大量的锁等待，阻塞关键操作
- **启动过程中不要启动子进程**，如果好几个进程同时启动，系统负担则会加倍，SystemServer 也会更繁忙
- **启动过程中除了 Activity 之外的组件启动要谨慎**，因为四大组件的启动都是在主线程的，如果组件启动慢，占用了 Message 通道，也会影响应用的启动速度
- Application 和主 Activity 的 onCreate 中异步初始化某些代码

启动过程中繁忙的 cpu  
[![](https://androidperformance.com/images/15740895432157.jpg)](https://androidperformance.com/images/15740895432157.jpg)

启动过程中繁忙的 SystemServer  
[![](https://androidperformance.com/images/15740895514226.jpg)](https://androidperformance.com/images/15740895514226.jpg)

## GC 优化[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#GC-%E4%BC%98%E5%8C%96)

启动过程中减少 GC 的次数

- 避免进行大量的字符串操作，特别是序列化和反序列化
- 频繁创建的对象需要考虑复用
- 转移到 Native 实现

可以参考下面这篇文章 [支付宝客户端架构解析：Android 客户端启动速度优化之「垃圾回收」](https://juejin.im/post/5be1077d518825171140dbfa))

## IO 优化[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#IO-%E4%BC%98%E5%8C%96)

启动过程中负载比较高，有许多系统 IO 都在此时发生，这时候 IO 的性能下降会比较快，此时 App 中的 IO 操作会比平时更慢一些，尤其是在性能比较差的机器上。

IO 分网络 IO 和磁盘 IO ，启动过程中不建议进行网络 IO ，对于磁盘 IO 则要细扣，邵文在高手课里面有讲到：

1. 我们要清楚启动过程中读了什么文件、多少个字节、 Buffer 是多大，使用了多长时间、在什么线程等一系列信息
2. 进行启动过程中的 IO 监控，微信在监控 IO 时发现有用户的 db 文件达到了 500MB

[![](https://androidperformance.com/images/15740895712642.jpg)](https://androidperformance.com/images/15740895712642.jpg)

下面图中可以看到低内存的时候，启动应用主线程有较多的 IO 等待（UI Thread 这一栏，橘红色代表 IO 等待 ）

[![](https://androidperformance.com/images/15740895799020.jpg)](https://androidperformance.com/images/15740895799020.jpg)

[![](https://androidperformance.com/images/15740895863900.jpg)](https://androidperformance.com/images/15740895863900.jpg)

## 资源重排[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E8%B5%84%E6%BA%90%E9%87%8D%E6%8E%92)

利用 Linux 的 IO 读取策略，PageCache 和 ReadAhead 机制，按照读取顺序重新排列，减少磁盘 IO 次数 。具体操作可以参考[支付宝 App 构建优化解析：通过安装包重排布优化 Android 端启动性能](https://juejin.im/post/5be400c3f265da61476fb63c) 这篇文章

Linux 底层文件系统中 VFS 上次 App 进程之间，存在一层 pagecache，pagecache 由内存中的物理 page 组成，其内容对应磁盘上的 block。Pagecache 的大小是动态变化的，可以扩大，也可以在内存不足时缩小。Cache 缓存的存储设备被称为后备存储（backing store），一个 page 通常包含多个 block，这些 block 不一定是连续的

[![](https://androidperformance.com/images/15740895943096.jpg)](https://androidperformance.com/images/15740895943096.jpg)

利用文件重布局结合Pagecache 机制可以减少启动过程中的真正 IO 的次数，简单的说，通过文件重布局的目的，就是将启动阶段需要用到的文件在 APK 文件中排布在一起，尽可能的利用 pagecache 机制，用最少的磁盘 IO 次数，读取尽可能多的启动阶段需要的文件，减少 IO 开销，从而达到提升启动性能的目的

## 类重排[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E7%B1%BB%E9%87%8D%E6%8E%92)

类重排的实现通过 ReDex 的 Interdex 调整类在 Dex 中的排列顺序。Interdex 优化不需要去分析类引用，它只需要调整 Dex 中类的顺序，把启动时需要加载的类按顺序放到主 dex 里，这个工作我们完全可以在编译过程中实现，而且这个优化可以提升启动速度，优化效果从 facebook 公布的数据来看也比较可观，性价比高。具体实现可以参考 [Redex 初探与 Interdex：Andorid 冷启动优化](https://mp.weixin.qq.com/s/Bf41Kez_OLZTyty4EondHA)

## 主页面布局优化[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E4%B8%BB%E9%A1%B5%E9%9D%A2%E5%B8%83%E5%B1%80%E4%BC%98%E5%8C%96)

应用主界面布局优化是老生常谈了，综合起来无非就是下面两点，这个需要结合具体的界面布局去做优化，网上也有比较多的资料可以查阅

- 通过减少冗余或者嵌套布局来降低视图层次结构
- 用 ViewStub 替代在启动过程中不需要显示的 UI 控件
- 使用自定义 View 替代复杂的 View 叠加

## 闲时调用[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E9%97%B2%E6%97%B6%E8%B0%83%E7%94%A8)

IdleHandler：当 Handler 空闲的时候才会被调用，如果返回 true, 则会一直执行，如果返回 false，执行完一次后就会被移除消息队列。比如，我们可以将从服务器获取推送 Token 的任务放在延迟 IdleHandler 中执行，或者把一些不重要的 View 的加载放到 IdleHandler 中执行

## 类加载优化[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E7%B1%BB%E5%8A%A0%E8%BD%BD%E4%BC%98%E5%8C%96)

可以在 systrace 生成的文件中看到 verifyClass 过程，因为需要校验方法的每一个指令，所以是一个比较耗时的操作。

[![](https://androidperformance.com/images/15740896151484.jpg)](https://androidperformance.com/images/15740896151484.jpg)

## App 瘦身[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#App-%E7%98%A6%E8%BA%AB)

App 瘦身包括代码瘦身和资源瘦身，通常的做法如下：

- Inspect  Code ：Android Studio 提供的代码审查工具，实际上是内嵌了 Lint
- 代码混淆
- 图片格式的选择：如果对图片的要求不高，可以换成 565
- 接入资源混淆
- 减少 Dex 数量

## 选择合适的启动框架[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E9%80%89%E6%8B%A9%E5%90%88%E9%80%82%E7%9A%84%E5%90%AF%E5%8A%A8%E6%A1%86%E6%9E%B6)

启动优化整个流程的梳理，流程的梳理，我们这里引入了一个有向无环图的概念，我们会把整个的概念梳理成有向无环图的结构，然后会去挨个加载。右边的部分，可以看到我们其实在启动的时候，首先会去加载一些必要的启动项，必要的启动项是左边流程，会用一个多进程的方式加载，以来有向无环图进行控制，比如说我是在非必须的时候启动加载我可以放在后面再去加载。当然在整个有向无环图的顺序加载，其实还是会做一些进程的判断，要判断某些项目是不是要在主进程里加载，某些要在初始进程里面加载

从 Spark 的 DAGScheduler 中领悟到它的核心思想，面向阶段调度（Stage-Oriented Scheduler）：把应用划分成一个个的阶段（Stage），再把任务（Task）安排到各个阶段中去，任务的编排则是通过构建 有向无环图（DAG），把任务依赖通过图的方式梳理得 井井有条。因为它分阶段执行，先集中资源把阶段一搞定，再齐心协力去执行阶段二，这样即能控制拥塞，又能保证时序，还能并发执行，让设备性能尽可能得到发挥

[![](https://androidperformance.com/images/15740896407148.jpg)](https://androidperformance.com/images/15740896407148.jpg)

[![](https://androidperformance.com/images/15740896485396.jpg)](https://androidperformance.com/images/15740896485396.jpg)

[![](https://androidperformance.com/images/15740896590892.jpg)](https://androidperformance.com/images/15740896590892.jpg)

大家可以参考淘宝的全链路优化的案例：[历时1年，上百万行代码！首次揭秘手淘全链路性能优化（上）](https://yq.aliyun.com/articles/710466)

## 启动网络链路优化[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E5%90%AF%E5%8A%A8%E7%BD%91%E7%BB%9C%E9%93%BE%E8%B7%AF%E4%BC%98%E5%8C%96)

### 问题和优化点[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E9%97%AE%E9%A2%98%E5%92%8C%E4%BC%98%E5%8C%96%E7%82%B9)

- 发送处理阶段：网络库bindService影响前x个请求，图片并发限制图片库线程排队
- 网络耗时：部分请求响应size大，包括 SO文件，Cache资源，图片原图大尺寸等
- 返回处理：个别数据网关请求json串复杂解析严重耗时（3s）,且历史线程排队设计不合适
- 上屏阻塞：回调UI线程被阻，反映主线程卡顿严重。高端机达1s，低端机恶化达3s以上
- 回调阻塞：部分业务回调执行耗时，阻塞主线程或回调线程

### 优化[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E4%BC%98%E5%8C%96)

- 多次重复的请求，业务方务必收敛请求次数，减少非必须请求。
- 数据大的请求如资源文件、so文件，非启动必须统一延后或取消。
- 业务方回调执行阻塞主线程耗时过长整改。我们知道，肉眼可见流畅运行，需要运行60帧/秒， 意味着每帧的处理时间不超过16ms。针对主线程执行回调超过16ms的业务方，推动主线程执行优化。
- 协议json串过于复杂导致解析耗时严重，网络并发线程数有限，解析耗时过长意味着请求长时间占用MTOP线程影响其他关键请求执行。推动业务方handler注入使用自己的线程解析或简化json串。

## 预加载[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E9%A2%84%E5%8A%A0%E8%BD%BD)

Activity 打开之前就预加载数据，在 Activity 的 UI 布局初始化完成后显示预加载的数据，大大缩短启动时间。 可以参考 ：[https://github.com/luckybilly/PreLoader/blob/master/README-zh-CN.md](https://github.com/luckybilly/PreLoader/blob/master/README-zh-CN.md)

## 保活[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E4%BF%9D%E6%B4%BB)

保活，是各个应用开发者的噩梦，也是 Android 厂商关注和打击的重点。不过从启动的角度来看，如果应用进程不被杀，那么启动自然就快了，所以保活对应用启动速度也是有极大的帮助。

当然这里说的保活，并不是建议大家用各种黑科技、相互唤醒、通知轰炸这种保活手段，而是提供真正的功能，能让用户觉得你在后台是合理的、可以接收的。比如在后台的时候，资源能释放的都释放掉，不要一直在后台做耗电操作，该停的服务停掉，该关的动画关掉。

当然对于应用开发者来说，上面说的都太多理想化了，而且目前的手机厂商也会很暴力，应用到了后台就会处理掉，不过这毕竟是一个方向，Google 也在规范应用后台行为和规范厂商处理应用这两方面都在做努力，Android 系统的生态，还是需要应用开发者和 Android 厂商一起取改善。

当然保活还有一条路就是走跟厂商的合作，优化后台内存、去掉重复拉起、去掉流氓逻辑、积极响应低内存警告，做好这些话后可以跟系统厂商联系，**谈放到查杀白名单和自启动白名单的可行性**

## 业务梳理[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E4%B8%9A%E5%8A%A1%E6%A2%B3%E7%90%86)

这里涉及到具体的业务，每个 App 都不一样，但是所要做的事情都是一样的，下面是邵文在高手课里面提到的：

- 梳理清楚启动过程中的每一个模块，哪些是一定需要的，那些是可以砍掉，那些是可以懒加载的
- 根据不同的业务场景决定不同的启动模式
- 懒加载防止集中化

可以把具体的业务分为下面四个维度（此处图文来自[https://juejin.im/post/5c21ea325188254eaa5c45b1#heading-5](https://juejin.im/post/5c21ea325188254eaa5c45b1#heading-5)）

- 必要且耗时：启动初始化，考虑用线程来初始化
- 必要不耗时：首页绘制
- 非必要但耗时：数据上报、插件初始化
- 非必要不耗时：不用想，这块直接去掉，在需要用的时再加载

然后按需进行加载优化

[![](https://androidperformance.com/images/15740896948509.jpg)](https://androidperformance.com/images/15740896948509.jpg)

## 业务优化[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E4%B8%9A%E5%8A%A1%E4%BC%98%E5%8C%96)

1. 优化业务中的代码效率，抓大放小，先从比较明显的瓶颈处下手，逐步进行优化
2. 历史债务要偿还，历史的代码要重构，不能一直拖着

具体的业务会有具体的优化场景，大家可以参考这篇文章中的优化流程和优化项[https://www.jianshu.com/p/f5514b1a826c](https://www.jianshu.com/p/f5514b1a826c)

> 1. 数据库及IO操作都移到工作线程，并且设置线程优先级为THREAD_PRIORITY_BACKGROUND，这样工作线程最多能获取到10%的时间片，优先保证主线程执行
> 2. 流程梳理，延后执行；实际上，这一步对项目启动加速最有效果。通过流程梳理发现部分流程调用时机偏失等， 例如
> 3. 更新等操作无需在首屏尚未展示就调用，造成资源竞争
> 4. 调用了IOS为了规避审核而做的开关，造成网络请求密集
> 5. 自有统计在Application的调用里创建数量固定为5的线程池，造成资源竞争
> 6. 修改广告闪屏逻辑为下次生效
> 7. 去掉用无但被执行的老代码
> 8. 去掉开发阶段使用但线上被执行的代码
> 9. 去掉重复逻辑执行代码
> 10. 去掉调用三方SDK里或者Demo里的多余代码
> 11. 信息缓存，常用信息只在第一次获取，之后从缓存中取
> 12. 项目是多进程架构，只在主进程执行Application的onCreate()

## 减少Activity的跳转层次[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E5%87%8F%E5%B0%91Activity%E7%9A%84%E8%B7%B3%E8%BD%AC%E5%B1%82%E6%AC%A1)

StartingWindow 会在用户点击 App 后立即创建并显示(前提是 App 没有禁止 StartingWindow)，在 AppWindow 创建好之后，StartingWindow 消失，AppWindow 显示

### 默认 App 的启动窗口流程[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E9%BB%98%E8%AE%A4-App-%E7%9A%84%E5%90%AF%E5%8A%A8%E7%AA%97%E5%8F%A3%E6%B5%81%E7%A8%8B)

|   |   |
|---|---|
|1  <br>2|StartingWindow(SystemWindow)   <br>  ->MainActivity(AppWindow)|

### 大部分三方 App 启动流程[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E5%A4%A7%E9%83%A8%E5%88%86%E4%B8%89%E6%96%B9-App-%E5%90%AF%E5%8A%A8%E6%B5%81%E7%A8%8B)

|   |   |
|---|---|
|1  <br>2  <br>3|StartingWindow(SystemWindow)   <br>  -> SplashActivity(AppWindow)  <br>    -> MainActivity(AppWindow)|

### 糟糕一点的启动流程是这样的[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E7%B3%9F%E7%B3%95%E4%B8%80%E7%82%B9%E7%9A%84%E5%90%AF%E5%8A%A8%E6%B5%81%E7%A8%8B%E6%98%AF%E8%BF%99%E6%A0%B7%E7%9A%84)

|   |   |
|---|---|
|1  <br>2  <br>3  <br>4|StartingWindow(SystemWindow)   <br>  -> MainActivity(AppWindow)   <br>    -> SplashActivity(AppWindow)  <br>      -> MainActivity(AppWindow)|

### 更糟糕一点的启动流程：去掉了 StartingWindow[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E6%9B%B4%E7%B3%9F%E7%B3%95%E4%B8%80%E7%82%B9%E7%9A%84%E5%90%AF%E5%8A%A8%E6%B5%81%E7%A8%8B%EF%BC%9A%E5%8E%BB%E6%8E%89%E4%BA%86-StartingWindow)

|   |   |
|---|---|
|1  <br>2|SplashActivity(AppWindow)  <br>   -> MainActivity(AppWindow)|

其实对用户来说，第一种启动流程是最好的，只涉及到一次窗口的切换；但是部分 App 由于广告页的需求，会使用第二种流程 ；但是尽量不要使用第三种和第四种启动流程，体验非常不好

# 厂商优化

除了 App 自身的优化之外，Android 框架对应用启动也是非常关注的，做了比较多的优化，下面简单说一下思路，各个厂商的实现也不太一样，但是基本上都会有，有些是硬核代码优化，有的是利用系统策略做优化。

厂商的策略各不相同，这里只是简单的提一下思路

## 启动加速[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E5%90%AF%E5%8A%A8%E5%8A%A0%E9%80%9F)

App 启动的时候，系统会对要启动的应用做绝对的资源倾斜，比如 CPU、IO、GPU 等，这一点大家抓个 Systrace 看一下即可，不管是频率还是调度算法，正在启动的 App 绝对是当时的系统 VIP 客户

[![](https://androidperformance.com/images/15740897496735.jpg)](https://androidperformance.com/images/15740897496735.jpg)

部分厂商也提供了资源调度的 SDK ，应用可以接入这些 SDK，在需要资源的时候直接调用 SDK 获取

## PreFork[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#PreFork)

Android Q 加入了 PreFork 机制，会先 fork 几个空进程，当 App 启动的时候，可以直接复用这几个空进程，而不用重新去 fork

|   |   |
|---|---|
|1  <br>2  <br>3  <br>4  <br>5  <br>6  <br>7  <br>8  <br>9  <br>10  <br>11  <br>12  <br>13  <br>14  <br>15  <br>16  <br>17  <br>18  <br>19  <br>20|2,348K: usap32 (pid 18731)  <br>2,346K: usap32 (pid 18702)  <br>2,343K: usap32 (pid 18707)  <br>2,342K: usap32 (pid 18729)  <br>2,341K: usap32 (pid 18711)  <br>2,335K: usap32 (pid 20322)  <br>2,335K: usap32 (pid 20325)  <br>2,333K: usap32 (pid 20319)  <br>2,333K: usap32 (pid 20320)  <br>2,333K: usap32 (pid 20321)  <br>  <br>  <br>1,509K: usap64 (pid 21169)  <br>1,509K: usap64 (pid 21180)  <br>1,507K: usap64 (pid 21171)  <br>1,452K: usap64 (pid 21513)  <br>1,450K: usap64 (pid 21506)  <br>1,449K: usap64 (pid 21512)  <br>1,447K: usap64 (pid 21511)  <br>1,445K: usap64 (pid 21514）|

## 启动消息重排[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E5%90%AF%E5%8A%A8%E6%B6%88%E6%81%AF%E9%87%8D%E6%8E%92)

启动的时候，对启动过程中的 Message 进行重新排列

## 主线程、渲染线程加速[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E4%B8%BB%E7%BA%BF%E7%A8%8B%E3%80%81%E6%B8%B2%E6%9F%93%E7%BA%BF%E7%A8%8B%E5%8A%A0%E9%80%9F)

部分厂家会对启动过程 App 的主线程和渲染线程做特殊对待，比如让他们直接跑到大核上，将其他不重要的线程移到小核

## 启动预测[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E5%90%AF%E5%8A%A8%E9%A2%84%E6%B5%8B)

部分场景会针对用户的使用习惯进行学习，比如在什么时间、什么场合、什么交通工具打开手机，系统会预测你要启动的 App，并在后台进行启动，这样你点击这个 App 的时候，就已经是热启动了

## 后台保活[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E5%90%8E%E5%8F%B0%E4%BF%9D%E6%B4%BB)

系统也会对一些应用进行特殊处理，以提升用户体验：包括但不限于 **进程\线程优先级调整、查杀白名单、用户常用应用记录**等，进行适当的后台保活，下次启动的时候就是热启动了

## 后台重启[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E5%90%8E%E5%8F%B0%E9%87%8D%E5%90%AF)

系统会对一些应用进行特殊处理，比如这个 App 比较重要但是不能杀掉，那么有的厂商会在这种应用退到后台之后，进行无感重启：比如说某个应用内存超标或者持续 Crash ，后台重启可以很好地解决这个问题，这样重启后的 App 是用户点击启动的时候就是热启动

## 内存优化[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E5%86%85%E5%AD%98%E4%BC%98%E5%8C%96)

部分应用启动的时候，需要大量的内存，比如现在的相机启动，这时候如果没有足够的内存，那么系统必须要通过杀掉很多应用、释放 Cache 等操作来给这个 App 让路，这个过程会使得这些大内存的 App 在启动的时候频繁进行内存操作，导致启动速度变慢

部分厂商会在监测到这种大内存 App 启动的时候，提前做内存的回收操作，这样在启动的时候，就有了足够的内存给这个 App 使用

## 优化启动逻辑[](https://androidperformance.com/2019/11/18/Android-App-Lunch-Optimize/?comefrom=https://blogread.cn/news/#%E4%BC%98%E5%8C%96%E5%90%AF%E5%8A%A8%E9%80%BB%E8%BE%91)

Android 系统更新也会对应用启动速度进行优化，比如上面提到的 Pre-Fork，又比如这里的简化 doFrame 个数

[![](https://androidperformance.com/images/15740897674007.jpg)](https://androidperformance.com/images/15740897674007.jpg)

# 参考文章

1. [都9102年了，Android 冷启动优化除了老三样还有哪些新招？](https://mp.weixin.qq.com/s?__biz=MzAxMTI4MTkwNQ==&mid=2650829097&idx=2&sn=e59841d4b1ed7e12a30e29ec51072d70&chksm=80b7a5b7b7c02ca184e0c06289d90823d589e738c55712318875f51e4aeb8646294b8d426299&mpshare=1&scene=1&srcid=&sharer_sharetime=1571275213308&sharer_shareid=60bd7acea7881a97fbf9a6126d3e88d3#rd)
2. [App startup time](https://developer.android.google.cn/topic/performance/vitals/launch-time)
3. [历时1年，上百万行代码！首次揭秘手淘全链路性能优化（上）](https://mp.weixin.qq.com/s?__biz=MzAxNDEwNjk5OQ==&mid=2650403370&idx=1&sn=b4297b138eb7f73c95a6279c3458f025&chksm=83953a32b4e2b3247fc18cbee08a2682d8b09720a1c5fef0c36257ae92b1e201cb1ad3125455&mpshare=1&scene=1&srcid=#rd)
4. [极客时间 ： Android 高手开发课](https://time.geekbang.org/column/intro/142)
5. [Facebook-Redex](https://github.com/facebook/redex)
6. [关于 Android 异步启动框架 alpha 的思考](http://yummylau.com/2019/03/15/%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90%E4%B9%8B%20alpha/)
7. [支付宝 App 构建优化解析：通过安装包重排布优化 Android 端启动性能](https://mp.weixin.qq.com/s/79tAFx6zi3JRG-ewoapIVQ)
8. [Redex 官网](https://github.com/facebook/redex)
9. [Redex 初探与 Interdex：Andorid 冷启动优化](https://mp.weixin.qq.com/s/Bf41Kez_OLZTyty4EondHA)
10. [Android性能优化笔记（一）——启动优化](https://juejin.im/post/5c21ea325188254eaa5c45b1#heading-5)

# 本文其他地址

由于博客留言交流不方便，点赞或者交流，可以移步本文的知乎或者掘金页面

[知乎 - Android App 启动优化全记录](https://zhuanlan.zhihu.com/p/92497570)