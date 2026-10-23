# Strata Desktop

**[English](#strata-desktop)** · [简体中文](#简体中文)

A Windows desktop client for **[Strata](https://github.com/Niko1221/Strata)** — the open-source engine that
runs a 125-billion-parameter model on a normal gaming PC. The engine stays upstream; this repository ships the
desktop layer, the Windows installers, and the update feed.

## What this build adds

| | Upstream Strata | This desktop build |
| --- | --- | --- |
| First start | Double-click `START-HERE.bat` and answer the questions in a console window | Double-click `Strata.exe`: a graphical wizard checks the machine, downloads the engine and dependencies, and picks up models already on disk |
| Everyday start | Run `START-HERE.bat` again, then open `127.0.0.1:8080` in a browser | One double-click: a native window (WebView2) plus a tray icon |
| While the model loads | Nothing to look at until the browser page is ready | The window opens at once with a loading page: stage, elapsed time, and a Retry button |
| Launching twice | A second server starts and fights over port 8080 | Single instance: the running window is raised to the front and the second launch exits |
| Switching models | Run setup again | Tray menu or the About card; the model is swapped in the background, chat history stays |
| Sharing to phones and other PCs | Set up the port and a key yourself | One switch: binds `0.0.0.0:8080`, generates an API key, lists LAN / VM / Tailscale addresses; local access needs no key |
| Updates | `UPDATE.bat`, built on git | In-app check → download → SHA-256 verify → overwrite; no git required; engine and Python dependencies update in the same pass |
| Vision | Asked as a question during setup | Detected from the `mmproj` file and enabled automatically, with VRAM reserved for the encoder |
| Copying text | Whatever the browser allows | Text selection and a right-click menu (copy, select all) work on every page |
| Distribution | Source tree and batch files | Installer, portable ZIP and an app-only update package; a portable Python runtime is bundled, so the target PC needs nothing preinstalled |

## Download

Windows 10 / 11, x64 → [Latest release](https://github.com/yanyiong01093/anywhere_starta/releases/latest)

- **`Strata-Setup-<version>.exe`** — recommended. Installs to `%LOCALAPPDATA%\Programs\Strata` and adds a
  Start-menu shortcut. The engine and the model are downloaded on first start.
- **`Strata-<version>-windows-x64-green.zip`** — portable. Unzip anywhere and run `Strata.exe`.
- **`strata-app.zip` + `SHA256.txt`** — application-layer update package. Installed clients fetch it themselves
  when you press *Check for updates*; there is no need to download it by hand.

Large files (engine, model) come from official sources at first start; they are not bundled in the download.

## Requirements

| | |
| --- | --- |
| GPU | NVIDIA GeForce RTX 20 / 30 / 40 / 50 series, **12 GB VRAM or more** |
| RAM | 32 GB or more; 64 GB runs every model size |
| Disk | About 80 GB free, SSD preferred |
| OS | Windows 10 / 11, x64 |

Linux and macOS use the upstream project's `setup.sh`; this desktop build is Windows-only.

## Where things come from

- Engine binaries and model files: official upstream releases (`Niko1221/Strata`), downloaded on first start.
- Application layer (`desktop/`, `serve/web/`): built in this project and delivered as `strata-app.zip`.
- Update feed: this repository's GitHub Releases, read through the GitHub API with a web fallback.

> The app-only update package never replaces `Strata.exe`. A new launcher requires reinstalling from the latest
> installer.

## License

The engine and its source follow the upstream project's license; the desktop layer is distributed under the same
terms.

---

## 简体中文

**[English](#strata-desktop)** · 简体中文

这是 **[Strata](https://github.com/Niko1221/Strata)** 的 Windows 桌面客户端。Strata 是一个开源引擎，能在普通游戏电脑上
运行 1250 亿参数的大模型。引擎本身来自上游项目，本仓库提供桌面外壳、Windows 安装包和更新通道。

## 桌面客户端新增了什么

| | 上游原版 | 本桌面客户端 |
| --- | --- | --- |
| 首次启动 | 双击 `START-HERE.bat`，在黑色命令行窗口里逐项回答问题 | 双击 `Strata.exe`：图形向导自动检测环境、下载引擎与依赖、扫描本机已有模型 |
| 日常启动 | 再运行一次 `START-HERE.bat`，再用浏览器打开 `127.0.0.1:8080` | 双击一次即可：独立应用窗口（WebView2）+ 系统托盘图标 |
| 模型加载期间 | 浏览器页面出来之前没有任何反馈 | 窗口立即显示加载页：当前阶段、已用时间，失败可一键重试 |
| 重复启动 | 会再起一个 server，抢 8080 端口，两个实例互相干扰 | 单实例：把已在运行的窗口唤到前台，第二次启动直接退出 |
| 切换模型 | 重新跑一遍安装程序 | 托盘菜单或「关于」页卡片，后台完成卸载与加载，聊天记录保留 |
| 共享给手机 / 其他电脑 | 自己改配置、开端口、配密钥 | 一个开关：绑定 `0.0.0.0:8080`，自动生成 API Key，列出局域网 / 虚拟机 / Tailscale 地址；本机访问免密钥 |
| 更新 | `UPDATE.bat`，依赖 git | 应用内检查 → 下载 → SHA-256 校验 → 覆盖安装；不需要 git；引擎与 Python 依赖同步更新 |
| 视觉（看图） | 安装时作为一个问题询问 | 检测到 `mmproj` 文件后自动启用，并为编码器预留显存 |
| 复制文字 | 取决于浏览器行为 | 所有页面都能选中文字，并提供右键菜单（复制 / 全选） |
| 分发方式 | 源码目录 + 批处理脚本 | 安装器、绿色免安装包、应用增量包三种形态；内置便携 Python，目标机无需预装任何环境 |

## 下载

Windows 10 / 11 x64 → [最新版本](https://github.com/yanyiong01093/anywhere_starta/releases/latest)

- **`Strata-Setup-<版本>.exe`** —— 推荐。安装到 `%LOCALAPPDATA%\Programs\Strata`，并创建开始菜单快捷方式；
  引擎与模型在首次启动时下载。
- **`Strata-<版本>-windows-x64-green.zip`** —— 绿色免安装。解压到任意目录，运行 `Strata.exe` 即可。
- **`strata-app.zip` + `SHA256.txt`** —— 应用层增量更新包。已安装的客户端在点击「检查更新」后自行下载，
  不需要手动获取。

引擎和模型文件体积较大，均在首次启动时从官方源下载，不随安装包分发。

## 系统要求

| | |
| --- | --- |
| 显卡 | NVIDIA GeForce RTX 20 / 30 / 40 / 50 系列，**显存 12 GB 以上** |
| 内存 | 32 GB 以上；64 GB 可运行所有模型规格 |
| 磁盘 | 约 80 GB 可用空间，建议 SSD |
| 系统 | Windows 10 / 11，x64 |

Linux 与 macOS 请使用上游项目的 `setup.sh`；本桌面客户端仅支持 Windows。

## 各部分来源

- 引擎二进制与模型文件：上游官方发布（`Niko1221/Strata`），首次启动时下载。
- 应用层（`desktop/`、`serve/web/`）：本项目开发，通过 `strata-app.zip` 分发。
- 更新通道：本仓库的 GitHub Releases，优先走 GitHub API，网页作为兜底。

> 应用增量包不会替换 `Strata.exe`。启动器本身有更新时，需要重新下载最新安装包覆盖安装。

## 许可证

引擎及其源码遵循上游项目的许可证；桌面层以相同条款分发。
