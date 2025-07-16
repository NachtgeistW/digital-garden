---
title: Unity 协程的历史遗留问题
aliases: 
tags:
  - CSharp
  - Unity
type: concepts
draft: true
---

今天终于想明白了一件事：Unity 的“协程函数”在 C# 里其实是“迭代器函数”。而这么做的原因是 C# 现在的这套协程 async/await 体系是 5.0 才引入的，12年；StartCoroutime 能追溯到 08 年以前。所以 Unity 才用迭代器实现自己的协程；才会有 UniTask 这个用 async/await 重新实现的真正的 Unity 多线程库

但是问题是 Unity API 不是线程安全的，所以不推荐直接在 Unity 里写 async/await。要用就用 UniTask

## See also

- [Unity协程的原理与应用 - 知乎](https://zhuanlan.zhihu.com/p/279383752)