# 安装指南

桌面端基于 **Electron**，提供 macOS / Windows / Linux 安装包。当前版本暂未进行 Apple / Windows 代码签名，首次安装需要手动放行一次（不是文件损坏，按下方步骤操作即可）。

## 下载

前往 [GitHub Releases](https://github.com/NanmiCoder/cc-haha/releases) 下载对应平台的安装包：

| 平台 | 文件 |
|------|------|
| macOS (Apple Silicon / M 系列) | `Claude-Code-Haha-<版本>-mac-arm64.dmg` |
| macOS (Intel) | `Claude-Code-Haha-<版本>-mac-x64.dmg` |
| Windows (x64) | `Claude-Code-Haha-<版本>-win-x64.exe` |
| Linux (x64) | `Claude-Code-Haha-<版本>-linux-x86_64.AppImage` 或 `...-linux-amd64.deb` |
| Linux (ARM64) | `Claude-Code-Haha-<版本>-linux-arm64.AppImage` 或 `...-linux-arm64.deb` |

> 不确定 Mac 架构？点击左上角  → 关于本机，芯片为「Apple M…」选 arm64，「Intel」选 x64。

## macOS 安装

> 重要：当前 DMG 未签名，**直接双击会报"已损坏，无法打开"**（系统只给"移到废纸篓"按钮）。这不是文件损坏，按下面任一方式安装即可，不要点"移到废纸篓"。

### 方式一：脚本安装（推荐）

把 Release 资产里的 `install-macos-unsigned.sh` 和对应架构的 DMG 下载到同一目录（如 `~/Downloads`），然后运行：

```bash
bash ~/Downloads/install-macos-unsigned.sh
```

脚本会自动解除 DMG 和应用的隔离标记、挂载并装进「应用程序」后打开——全程不需要双击 DMG。

### 方式二：手动安装

1. 先解除 DMG 的隔离标记（把文件名换成你下载的版本），再双击它：

```bash
xattr -dr com.apple.quarantine ~/Downloads/Claude-Code-Haha-<版本>-mac-arm64.dmg
```

2. 把 **Claude Code Haha** 拖入 `Applications`，再解除应用的隔离标记：

```bash
xattr -dr com.apple.quarantine "/Applications/Claude Code Haha.app"
```

> - 若提示 `Permission denied`，在命令前加 `sudo`。
> - **macOS Sequoia (15) 起**，Apple 已移除「右键 → 打开」的绕过方式；对完全未签名的应用，系统设置里的"仍要打开"也可能不出现。因此 `xattr` 命令是当前最可靠的放行方式。
> - 该命令只移除"从网络下载"的隔离标记，请确认是从官方 GitHub Release 下载后再执行。在拿到 Apple 签名 + 公证之前，双击 DMG 的报错无法从构建侧消除。

## Windows 安装

1. 双击 `.exe` 安装程序，按向导完成安装。
2. 首次运行如果 SmartScreen 弹出 **"Windows 已保护你的电脑"**：点击 **「更多信息」** → **「仍要运行」**。

> 可选：若系统反复拦截，可在 PowerShell 中去掉文件的"网络来源"标记：
> ```powershell
> Unblock-File -Path "$env:USERPROFILE\Downloads\Claude-Code-Haha-<版本>-win-x64.exe"
> ```

## Linux 安装

### AppImage（免安装）

```bash
chmod +x Claude-Code-Haha-<版本>-linux-x86_64.AppImage
./Claude-Code-Haha-<版本>-linux-x86_64.AppImage
```

> - 提示缺少 FUSE：Ubuntu 22.04 及更早 `sudo apt install libfuse2`，Ubuntu 24.04+ `sudo apt install libfuse2t64`。
> - 提示 `SUID sandbox helper ... not configured correctly`：临时用 `--no-sandbox` 运行，或改用下面的 deb 安装。

### deb（推荐，自动解决依赖与沙箱权限）

```bash
sudo apt install ./Claude-Code-Haha-<版本>-linux-amd64.deb
```

## Web UI 模式

如果桌面端安装遇到问题，可以直接通过浏览器使用 Web UI。在项目根目录下分别启动服务端和前端：

```bash
# 1. 启动服务端（在项目根目录）
SERVER_PORT=3456 bun run src/server/index.ts

# 2. 启动前端（在 desktop 目录）
cd desktop
bun run dev --host 127.0.0.1 --port 2024
```

启动后浏览器访问 `http://127.0.0.1:2024` 即可。

## 常见问题

**Q: macOS 反复提示"已损坏"或"无法验证开发者"？**

执行 `xattr -dr com.apple.quarantine "/Applications/Claude Code Haha.app"`（必要时加 `sudo`），或使用上面的一键安装脚本。这是未签名应用被 Gatekeeper 拦截的正常现象，去掉隔离标记后即可正常使用。

**Q: 这个版本会自动更新吗？**

暂时不会。在拿到苹果签名前，请每次到 [GitHub Releases](https://github.com/NanmiCoder/cc-haha/releases) 手动下载新版本覆盖安装。覆盖安装不会删除本地配置和会话数据（`~/.claude`）。
