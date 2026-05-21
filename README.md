# fix-icloud-sync

解决 macOS iCloud Drive 文件访问死锁问题（"Resource deadlock avoided" / EDEADLK）。

## 问题描述

当文件存储在 iCloud Drive（尤其是 `~/Library/Mobile Documents/` 路径）时，macOS 经常只保留文件元数据（stub），实际内容仍在云端。直接读取时会报错：

```
OSError: [Errno 11] Resource deadlock avoided
```

常见场景：
- Hermes Agent / OpenClaw Agent 执行文件操作
- 微信公众号书评发布脚本
- Obsidian + iCloud 同步
- 任何自动化读取 iCloud 文件的流程

---

## 官方 Skill 版本

我们已为以下 Agent 提供正式 Skill 支持：

- **Hermes Agent**：`~/.hermes/skills/fix-icloud-sync/`
- **OpenClaw Agent**：`~/openclaw/skills/fix-icloud-sync/`

---

## 不同 Agent 的安装方式

### 1. Hermes Agent 适配

**推荐方式**（直接放入 Hermes 脚本目录）：

```bash
# 克隆仓库
git clone https://github.com/jiyangnan/fix-icloud-sync.git ~/tools/fix-icloud-sync

# 创建软链接到 Hermes scripts 目录
ln -s ~/tools/fix-icloud-sync/fix-icloud-sync ~/.hermes/scripts/fix-icloud-sync

# 赋予执行权限
chmod +x ~/.hermes/scripts/fix-icloud-sync
```

之后可以在 Hermes 中直接调用：
```bash
fix-icloud-sync /path/to/icloud/file.md
```

---

### 2. OpenClaw Agent 适配

**推荐方式**：

```bash
# 克隆到 OpenClaw 工具目录
git clone https://github.com/jiyangnan/fix-icloud-sync.git ~/openclaw/tools/fix-icloud-sync

# 创建软链接（根据你的 OpenClaw 配置调整路径）
ln -s ~/openclaw/tools/fix-icloud-sync/fix-icloud-sync ~/.openclaw/bin/fix-icloud-sync

chmod +x ~/.openclaw/bin/fix-icloud-sync
```

或直接加入 OpenClaw 的技能目录作为独立工具使用。

---

### 3. Claude Code / Cursor / 通用 CLI 方式

**最简单安装方式**：

```bash
# 一键安装到系统 PATH
curl -fsSL https://raw.githubusercontent.com/jiyangnan/fix-icloud-sync/main/fix-icloud-sync \
  -o /usr/local/bin/fix-icloud-sync && \
chmod +x /usr/local/bin/fix-icloud-sync
```

安装后可直接在任何终端或 Agent 中使用。

---

## 使用方法

```bash
# 处理单个文件
fix-icloud-sync ~/Library/Mobile\ Documents/.../article.md

# 同时处理多个文件（推荐用于书评发布前）
fix-icloud-sync article.md cover.jpg
```

---

## 原理

1. 使用 `caffeinate` 防止系统睡眠
2. 调用 `TextEdit` 强制触发 iCloud daemon 下载真实文件内容
3. 验证文件是否可正常读取

---

## 注意事项

- 仅支持 macOS
- 需要系统自带的 `TextEdit`
- 首次使用可能需要授权终端访问 iCloud 文件

---

## 贡献

欢迎提交 Issue 或 PR，适配更多 Agent。

## License

MIT