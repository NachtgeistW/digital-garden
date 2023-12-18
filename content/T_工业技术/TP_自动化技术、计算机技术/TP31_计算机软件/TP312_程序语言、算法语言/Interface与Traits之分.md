Trarizon 2023-11-23 16:05:32
好消息是，他们最近在搞 trait 了

Trarizon 2023-11-23 16:06:47
https://github.com/dotnet/csharplang/issues/5497

Trarizon 2023-11-23 16:09:03
不知道要搞多久. Jpg

Trarizon 2023-11-23 16:09:58
我感觉像是 rust 那样

Galium maximowiczii 2023-11-23 16:17:32
Trait

Galium maximowiczii 2023-11-23 16:17:37
是干嘛的来着

Galium maximowiczii 2023-11-23 16:18:36
（开始复习 C++

Trarizon 2023-11-23 16:19:05
c#这个trait比较偏向extension method pro max plus

盐酸 2023-11-23 16:19:07
Trait 不是 C++的东西啊

Trarizon 2023-11-23 16:19:21
起码真的可以这么用（

盐酸 2023-11-23 16:19:32
本来就是 extension method pro max plus 吧

Galium maximowiczii 2023-11-23 16:19:34
那是哪个语言的

盐酸 2023-11-23 16:19:37
Rust

残像之形 2023-11-23 16:19:58
只是 C++的语法正好可以写 traits

盐酸 2023-11-23 16:20:09
不是，这个 trait 不是指 type trait

盐酸 2023-11-23 16:20:19
Rust trait 更像是 C++的 concept

Galium maximowiczii 2023-11-23 16:20:27
能给我讲讲吗

Galium maximowiczii 2023-11-23 16:20:41
完全没学过 Rust

Trarizon 2023-11-23 16:21:00
从 c#上简单说就是你可以为已有class实现你自己定义的interface

残像之形 2023-11-23 16:21:36
https://doc.rust-lang.org/rust-by-example/trait.html

残像之形 2023-11-23 16:21:56
Rust 的 traits 能做到类似 C++的 cpo

盐酸 2023-11-23 16:22:58
C++ CPO 真不行

残像之形 2023-11-23 16:23:05
那确实不行

残像之形 2023-11-23 16:23:17
就应该尽早整语言支持

盐酸 2023-11-23 16:23:21
这种东西没有语言支持纯用 library 写能写，但是用户体验拉到爆

盐酸 2023-11-23 16:24:00
而且 Rust trait 可以语言层面支持类型擦除

Galium maximowiczii 2023-11-23 16:27:48
我看完了那个实例

Galium maximowiczii 2023-11-23 16:28:20
换成 C# 的话感觉确实像 Sheep : IAnimal

Trarizon 2023-11-23 16:28:47
我看很多文章解释 trait：像是接口

残像之形 2023-11-23 16:28:55
这个和接口继承有本质的不同

Trarizon 2023-11-23 16:28:59
然后又讲不清楚哪里不一样

盐酸 2023-11-23 16:30:28
Rust：我们不搞 OOP，我知道这个东西能用来搞 OOP，但是我们这个不是 OOP

Galium maximowiczii 2023-11-23 16:31:32
可是我看了一下用法，真的很像 interface

残像之形 2023-11-23 16:32:02
这种显然比 interface 高级，没有侵入性

Galium maximowiczii 2023-11-23 16:32:41
为什么 interface 有侵入性？

残像之形 2023-11-23 16:33:05
因为用 interface 是一定要写到类定义@ 

残像之形 2023-11-23 16:33:27
使用者用 traits 可以不改源码下加实现

盐酸 2023-11-23 16:33:39
有侵入性就是，你如果不是类作者你不能给那个类实现某个 trait

盐酸 2023-11-23 16:34:27
问题在于 Rust 的模块设定好像是 trait 或者 struct 必须有一个是你所有的你才能写 impl

盐酸 2023-11-23 16:34:54
就是你作为 C 库的作者不能给 A 库的类实现 B 库的 trait

盐酸 2023-11-23 16:35:12
合理，但是还是得写 wrapper

Galium maximowiczii 2023-11-23 16:36:02
我还是不太懂 interface 的侵入性

残像之形 2023-11-23 16:36:21
就是你得改源码才能实现你想要的 interface

残像之形 2023-11-23 16:36:31
库作者不加就没得用

残像之形 2023-11-23 16:36:45
要么就 ``@[[nodiscard, deprecated]] consteval Chlorie make_chlorie ()``; 

盐酸 2023-11-23 16:37:01
你想给别人的类实现你自己的 interface 你就得给那个类写 wrapper

Galium maximowiczii 2023-11-23 16:37:46
我试着理一下

Galium maximowiczii 2023-11-23 16:39:27
比如说雷亚给 Deemo 整了个 note 的类，现在我想给 note 类实现我自己的 IPianoSoundPlayable 接口，这个是做不到的

盐酸 2023-11-23 16:39:50
Weirdly specific example but ok. Jpg

Galium maximowiczii 2023-11-23 16:40:50
但是如果 Deemo 是 Rust 写的，我就能给 note 类写自己的 PianoSoundPlayable traits 实现

Galium maximowiczii 2023-11-23 16:42:02
Traits 不是拿来 define shared behavior in an abstract way 的吗

盐酸 2023-11-23 16:42:40
@  基本还是 interface

盐酸 2023-11-23 16:43:04
Interface 不也是 define shared behavior in an abstract way(

盐酸 2023-11-23 16:43:30
属于话术，把一个很浅显的东西说得很高级

Galium maximowiczii 2023-11-23 16:46:38
所以总结：traits 就是个没侵入性的 interface

Trarizon 2023-11-23 16:47:46
Trait 就是 [[Extension Methods|extension method]] promaxplus+interface promaxplus

Trarizon 2023-11-23 16:47:48
（嗯

残像之形 2023-11-23 17:01:49
Define user customizable behavior in a non-intrusive way
这样应该好点