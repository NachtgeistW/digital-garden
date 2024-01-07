#Unity

  2023/11/3 12:54:42  
我草原来Unity内部的[[协程]]是[[状态机]]  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 12:57:18  
啊？  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 12:57:24  
协程全都是状态机  
  
 Is your type anti-allocator-aware? 2023/11/3 12:57:41  
协程全都是状态机  
  
 Is your type anti-allocator-aware? 2023/11/3 12:57:55  
[[async]]好像也是  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 12:58:02  
都是状态机  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 12:58:09  
你想想协程是个什么东西  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 12:58:24  
不就是状态机语法糖吗  
  
  2023/11/3 13:04:53  
我一直以为是个大for循环  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 13:05:19  
这跟unity没关系  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 13:05:33  
你写了generator（有[[yield]]的东西）就已经是协程了  
  
  2023/11/3 13:06:48  
以为Unity的协程有它自己的实现  
  
【OpenCV】Trarizon 2023/11/3 13:06:54  
你写了 yield return，那个方法生成的东西就是状态机了（

【Metal】 ((Func‹string›) (() =› "不會")).Invoke(); 2023/11/3 16:58:43  
> 我草原来 Unity 内部的协程是状态机  

...不是 [[IEnumerator]] 麼  
  
【Metal】 ((Func‹string›) (() =› "不會")).Invoke(); 2023/11/3 16:58:56  
那就是狀態機啊 (大叫  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 16:59:02  
对啊，跟Unity没关系  
  
【Metal】 ((Func‹string›) (() =› "不會")).Invoke(); 2023/11/3 16:59:12  
我甚至覺得是被濫用的feature(  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 16:59:27  
没有什么“Unity协程”这种东西  
  
【Metal】 ((Func‹string›) (() =› "不會")).Invoke(); 2023/11/3 16:59:30  
曾經有一段時間覺得這麼用很傻逼.jpg  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 16:59:42  
协程是C#的，Unity只是给了协程能用的API  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:00:06  
((Func‹string›) (() =› "不會")).Invoke(); 曾經有一段時間覺得這麼用很傻逼.jpg  
@((Func‹string›) (() =› "不會")).Invoke(); 本来就感觉很怪  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:00:32  
语义跟正常是反着的  
  
【Metal】 ((Func‹string›) (() =› "不會")).Invoke(); 2023/11/3 17:01:04  
只是unity內部用這個類去迭代調用回調....  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:01:05  
yield的语义应该是从协程里面往外带出数据  
  
【Metal】 ((Func‹string›) (() =› "不會")).Invoke(); 2023/11/3 17:01:07  
你自己處理yield...  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:01:37  
挂起协程之后从外部[[callback|回调]]往协程里面带入数据的不应该是 [[await]] 吗  
  
【Metal】 ((Func‹string›) (() =› "不會")).Invoke(); 2023/11/3 17:01:39  
應該屬於是單工多任務協程模型  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:01:57  
Unity这么用属于语义反着的.jpg  
  
 Is your type anti-allocator-aware? 2023/11/3 17:02:35  
unity咋用来着。。  
  
【Metal】 ((Func‹string›) (() =› "不會")).Invoke(); 2023/11/3 17:02:51  
那個協程只適合處理一些東西.. 可能不太適合互相調用..   
  
【Metal】 ((Func‹string›) (() =› "不會")).Invoke(); 2023/11/3 17:02:54  
我怎麼覺得會炸  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:03:11  
我觉得就应该老老实实用[[Task]]跟await那一套  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:03:26  
用yield return整个反过来真的那啥  
  
 Is your type anti-allocator-aware? 2023/11/3 17:04:57  
卧槽，用yield return来挂起啊  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:05:06  
是啊  

 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:05:30  
因为yield return是从协程里面往外传数据，没有返回值  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:05:50  
Unity就把你yield那玩意当成out param把结果给你塞回那个对象里面  
  
【Metal】 ((Func‹string›) (() =› "不會")).Invoke(); 2023/11/3 17:05:51  
Is your type anti-allocator-aware? 卧槽，用yield return来挂起啊  
是的...yield return等於把cpu時間交給調度器了，等下一次IEnumerator迭代進來  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:05:51  
何苦呢  

【Metal】 ((Func‹string›) (() =› "不會")).Invoke(); 2023/11/3 17:06:03  
傻逼死了  
  
 Is your type anti-allocator-aware? 2023/11/3 17:06:08  
我死了  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:06:24  
不是，本质都是一样的，底下都是个状态机，挂起协程把回调传给别人  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:06:48  
但是语法不一样明显术业有专攻  
  
【Metal】 ((Func‹string›) (() =› "不會")).Invoke(); 2023/11/3 17:06:59  
\[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 不是，本质都是一样的，底下都是个状态机，挂起协程把回调传给别人  
return了之後這個cpu就交給另一個回調了啊...  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:07:09  
await也是一样啊  
  
【Metal】 ((Func‹string›) (() =› "不會")).Invoke(); 2023/11/3 17:07:11  
這不就是調度器了..  
  
【Metal】 ((Func‹string›) (() =› "不會")).Invoke(); 2023/11/3 17:07:31  
await的話 c#的await/async有多種行爲...  
  
【Metal】 ((Func‹string›) (() =› "不會")).Invoke(); 2023/11/3 17:07:48  
可以跨線程 也可以當前線程....  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:08:02  
但是本质不还是awaiter决定的吗（  
  
【Metal】 ((Func‹string›) (() =› "不會")).Invoke(); 2023/11/3 17:08:03  
雖然沒有IEnumerator傻逼 但是也挺傻逼的  
  
【Metal】 ((Func‹string›) (() =› "不會")).Invoke(); 2023/11/3 17:08:16  
別的語言沒有這種奇怪的feature吧  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:08:33  
你看C++协程跟C#完全一个模子刻出来的，但是很底层  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:08:54  
（那个设计就是M$的人提出的  
  
【Metal】 ((Func‹string›) (() =› "不會")).Invoke(); 2023/11/3 17:09:25  
是的 本身就是一個awaiter 然後await方接管調度權限，一邊等awaiter結束一邊調度任務  
  
【Metal】 ((Func‹string›) (() =› "不會")).Invoke(); 2023/11/3 17:09:31  
這是語言無關的  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:09:38  
C++里面yield和await完全就一个东西，yield是await的语法糖  
  
【Metal】 ((Func‹string›) (() =› "不會")).Invoke(); 2023/11/3 17:09:47  
本質就是異步sleep加一個等awaiter的操作 類似  
  
【Metal】 ((Func‹string›) (() =› "不會")).Invoke(); 2023/11/3 17:10:06  
但 c#搞的是什麼飛機 多線程...啊 我看不懂.wav  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:10:26  
其实就是等于，把当前这个协程从await这个地方前后劈成两半  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:10:50  
把后面的部分搞成一个回调扔给awaiter让它结束任务之后callback回来  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:11:18  
但是Unity这样用很傻逼，这个大概没有异议.jpg  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:11:36  
而且最近版本的Unity不是已经公开说支持Task了吗  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:11:57  
用UniTask去，用什么Unity若智协程API.jpg  
  
 Is your type anti-allocator-aware? 2023/11/3 17:12:11  
用UniTask去，用什么Unity若智协程API.jpg  
  
【Metal】 ((Func‹string›) (() =› "不會")).Invoke(); 2023/11/3 17:12:56  
但是Unity这样用很傻逼，这个大概没有异议.jpg  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:13:52  
但是协程写状态机真的很爽.jpg  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:15:00  
((Func‹string›) (() =› "不會")).Invoke(); 但 c#搞的是什麼飛機 多線程...啊 我看不懂.wav  
@((Func‹string›) (() =› "不會")).Invoke(); C#给了你一个自带的event loop啊就是（  
  
 \[\[nodiscard, deprecated\]\] consteval Chlorie make_chlorie(); 2023/11/3 17:16:04  
你看我C++自己写的event loop效率吃屎