---
title: 
aliases:
  - 异步编程（C#）
type: concepts
tags:
  - CSharp
draft: 
---

C# 异步编程使用 [[async (csharp)|async]] / [[await (csharp)|await]] 关键字实现非阻塞操作，特别适用于 I/O 密集型任务。

**命名约定：** 异步方法必须以 `Async` 后缀命名，这是微软的强制性约定。

**返回类型：**

- 无返回值：`async Task`
- 有返回值：`async Task<T>`
- 高性能场景：`async ValueTask`

**UI 应用的重要性：**

csharp

```csharp
// ❌ 阻塞 UI
void LoadData() { 
    var data = httpClient.GetString(url).Result; 
}

// ✅ 保持响应
async Task LoadDataAsync() { 
    var data = await httpClient.GetStringAsync(url); 
}
```

异步编程让应用在等待 I/O 操作时能够保持响应性，提供更好的用户体验。

## Reference Links

- 

## See also

- [async/await 在 C# 语言中是如何工作的？（上）](https://mp.weixin.qq.com/s?__biz=MjM5NTE3NDgyMg==&mid=2650424696&idx=1&sn=93e178ef48741f520479564c65079d4e&chksm=bef265b18985eca77308009ba59763f4daf0fe1ffd8e67183e7573c8763af4e05df7f949d743&token=1730347698&lang=zh_CN&poc_token=HMm4p2Wj1UwepFCySAkahaqeqgrgxzXXqP2tAGeH)
- [async/await 在 C# 语言中是如何工作的？（中）](https://mp.weixin.qq.com/s?__biz=MjM5NTE3NDgyMg==&mid=2650425004&idx=1&sn=0278c668667952efbe0d55b479390a3e&chksm=bef266658985ef73cbce2f201243e2ff19b08da72edf776c9bec71e1b6cdecee25a3127114dd&cur_album_id=2345718230113140736&scene=189#wechat_redirect)
- [async/await 在 C# 语言中是如何工作的？（下）](https://mp.weixin.qq.com/s?__biz=MjM5NTE3NDgyMg==&mid=2650425708&idx=1&sn=74a4e8b919d068491cd6671da93fb4a5&chksm=bef269a58985e0b307747707ee410ddc1e22f809dd2f87bb85522a8874301cdccdfec26e9121&cur_album_id=2345718230113140736&scene=189#wechat_redirect)