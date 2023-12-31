[「利用混音使得人声能够听起来更漂亮的辅助效果」～MitchieM技术博客 (weibo.com)](https://weibo.com/ttarticle/p/show?id=2309404711146048324105)

Mitchie M 技术博客      
原文链接：https://mitchie-m.com/blog/daw/vocal-aux-effect/  
已获得Mitchie M老师授权。  
  
技术文档翻译仍处于测试（BETA）阶段，如有描述问题请评论指出。（持续更新）  
如能帮上您的话，希望能收到您的反馈。

---

这一次，我想试着写写关于「若是想要利用混音使得人声轨能够听起来更漂亮，辅助效果该怎么设置比较好？」虽然也会有我自己风格的地方，如果能作为参考我会感到荣幸。

顺带一提，关于串联效果我以前也写过一篇文章，大家可以参考一下。虽然内容有点旧（汗）  
  
 

![链接：https://mitchie-m.com/blog/daw/vocal-mix-effect-plugin/](https://wx2.sinaimg.cn/large/006hP3xEgy1gw1z7hvewej30k305d3zi.jpg)

# 人声效果器辅助轨

说起人声轨的辅助效果，最常用的应该是延迟和混响吧。

然后，就我而言除此之外，还会加入Early reflection（早期反射声）和人声加倍效果器。关于这个将在以下逐个介绍。

## 延迟  
 

首先是延迟。这主要是，以突出人声并增加声音的深度和厚度为目的使用的。以及，通过将延迟音往左右延伸，也可以增加声音的广度。

![](https://wx4.sinaimg.cn/large/006hP3xEgy1gw3dqppss2j30ir0c9t9f.jpg)

​​

说到人声的延迟，由于存在Long delay，乒乓延时效果，氛围回响等各种选项，需要选择适合曲调的延迟。  
 

## ▼参考事项

   
 

https://m.weibo.cn/5760163050/4711022018169613  
  
 

最近也存在不加入人声延迟的歌曲，所以也不是说必须要加入。但是，类似rap这种具有冲击性的人声不加入会比较合适，所以根据使用场景也可试着考虑不添加这个选项。

## 混响  
 

混响和延迟一样，以增加人声的深度，厚度，广度为目的使用。

与延迟不同，混响会增加余韵，让空间充满自然的感觉。

![](https://wx2.sinaimg.cn/large/006hP3xEgy1gw3dqyi3urj30iq0c90tb.jpg)

​​

混响的种类主要有，大堂混响，房间混响，板式混响，但对于以vocaloid曲为主的我而言大堂混响系的使用率很高。就vocaloid的情况而言，由于音质与人类存在些许区别，若是不在混响里使用均衡器的话，声音变哑说不出话的情况也是很多的。

此外，音轨上的音符数量越多，就越难听到人声混响。这并不意味着高质量的混响​​插件会产生良好的效果，因此尝试使用自己的混响是个好主意。

**early reflection**

early reflection是指，是指在混响中最先反射并反弹回来的声音。若是混响插件的话也是有可以设定early reflection的，但是我的话是将这个early reflection单独作为人声的辅助效果来使用的。

![](https://wx4.sinaimg.cn/large/006hP3xEgy1gw3dwny73ij30ec0cx0u2.jpg)

​

使用early reflection的目的是，增加声音的广度的同时让声音变得清晰。

当然用延迟也能增加声音的广度，但early reflection很快就会有回音，使得其与原本的人声听起来几乎重叠。因此，听上去像是蔓延中的原音，声音很清晰。当然加入这个也使得混响的残响感也变得真实。

在加入early reflection的同时，不增加辅助效果器的发送量是一个诀窍。如果做到隐约能听到的话，就可以得到自然的广度。

以及，在同时使用混响和early reflection的情况下，请将那边关掉。在曲子中有时会改变混响的量，因此单独使用early reflection的话，可以在稳定的级别添加此声音。

**人声加倍效果器**

虽说人声加倍效果器不是必须添加，在音轨还有空余的情况下，将人声直接变广为目的使用。

插件的话我免费推荐iZotope的vocal doubler。

![](https://wx3.sinaimg.cn/large/006hP3xEgy1gw3dwz7w3tj30cv0aedgb.jpg)

​

以及，在管弦乐的数量很多，或者主唱的人数很多的情况下不用会比较好。

在独唱中使用人声加倍效果器的话，歌曲就会变得突出增加存在感。如果再加上淡淡的效果，就会变得自然。

  
 

如果以做到叠加的效果为目的去使用人声加倍效果器的话，把歌录两遍并使用它们不是更好吗？

## 最后

以上介绍的效果不需要全部都使用。在曲子中，根据各个部分改变发送量和广度，加上变化使用的话会有效果。特别是延迟和混响，当你在副歌部分的声音扩散到最广的时候，气氛会很热烈。

此外，还需要使用低切和均衡器来制作效果，我个人推荐在效果后面插入均衡器插件。

---

## mmp的评论：

辅助输出的自动化的写入工作是必要的。