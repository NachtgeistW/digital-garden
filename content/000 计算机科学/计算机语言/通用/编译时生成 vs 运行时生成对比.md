---
aliases: []
type: concepts
tags:
- 
---

两种代码生成方式在性能、开发体验和适用场景上有显著差异。

**编译时生成示例（RelayCommand）：**

```csharp
// 你写的代码
[RelayCommand]
private async Task SaveAsync() { }

// 编译器自动生成
public IAsyncRelayCommand SaveCommand { get; }
```

**运行时生成示例（反射调用）：**

```csharp
// 每次都要反射查找
var method = typeof(MyClass).GetMethod("SaveAsync");
var command = new AsyncRelayCommand(() => (Task)method.Invoke(this, null));
```

**对比表格：**

|特性|编译时生成|运行时生成|
|---|---|---|
|性能|✅ 零开销，直接调用|❌ 反射开销大|
|IDE支持|✅ 完整智能感知|❌ 无法识别动态内容|
|错误发现|✅ 编译时报错|❌ 运行时才发现|
|灵活性|❌ 编译时确定|✅ 可动态调整|
|AOT兼容|✅ 完全支持|❌ 可能失效|

**选择建议：**

- 性能敏感 + 确定需求 → 编译时生成
- 需要动态行为 + 插件系统 → 运行时生成

## Reference Links

- 

## See also

- 