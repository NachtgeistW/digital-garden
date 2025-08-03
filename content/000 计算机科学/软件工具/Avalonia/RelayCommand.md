---
aliases: []
type: concepts
tags: []
---

`[RelayCommand]` 是 CommunityToolkit.Mvvm 提供的源生成器属性，用于简化 [[MVVM]] 模式中的命令创建。通过在方法上添加此属性，编译器会自动生成对应的 `ICommand` 属性。

**命名规则：**

- 方法名以 `Async` 结尾：去掉 `Async` + `Command`
- 普通方法：直接添加 `Command` 后缀
- 示例：`AddAlbumAsync()` → `AddAlbumCommand`

**生成类型：**

- 返回 `Task` → 生成 `IAsyncRelayCommand`
- 返回 `void` → 生成 `IRelayCommand`

这种方式消除了手动创建命令的样板代码，让 MVVM 开发更加简洁高效。

## Reference Links

- [Button Command | Avalonia Docs](https://docs.avaloniaui.net/docs/tutorials/music-store-app/button-command)

## See also

- 