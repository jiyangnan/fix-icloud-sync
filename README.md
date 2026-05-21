# fix-icloud-sync

解决 macOS iCloud Drive 文件访问死锁问题（Resource deadlock avoided）。

## 问题描述

当文件存储在 iCloud Drive（特别是 `~/Library/Mobile Documents/` 路径）时，macOS 经常只下载文件的元数据（stub），实际内容仍在云端。此时直接使用脚本、Python 或命令行工具读取文件时，会报错：

```
OSError: [Errno 11] Resource deadlock avoided
```

或

```
Resource deadlock avoided
```

常见于：
- 微信公众号文章发布脚本
- Obsidian + iCloud 同步场景
- 任何需要读取 iCloud 文件的自动化工具

## 解决方案

本工具通过 `TextEdit` 强制触发 iCloud 将文件真正下载到本地。

## 安装

```bash
# 下载脚本
curl -o /usr/local/bin/fix-icloud-sync https://raw.githubusercontent.com/你的用户名/fix-icloud-sync/main/fix-icloud-sync
chmod +x /usr/local/bin/fix-icloud-sync
```

## 使用方法

```bash
# 处理单个文件
fix-icloud-sync ~/Library/Mobile\ Documents/.../article.md

# 同时处理多个文件（推荐）
fix-icloud-sync article.md cover.jpg
```

## 原理

1. 使用 `caffeinate` 防止系统进入睡眠
2. 调用 `TextEdit` 打开文件，强制 iCloud daemon 下载真实内容
3. 验证文件是否可正常读取

## 注意事项

- 仅适用于 macOS
- 需要系统已安装 `TextEdit`（默认自带）
- 首次使用可能需要授予终端访问 iCloud 的权限

## 贡献

欢迎提交 Issue 或 Pull Request 改进此工具。

## License

MIT