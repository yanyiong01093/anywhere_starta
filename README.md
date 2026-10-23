# Strata Desktop

[English](#strata-desktop) · [简体中文](#简体中文)

A Windows desktop client for [Strata](https://github.com/Niko1221/Strata). This repository is the **application-layer
distribution** channel: every Release carries the update package (`strata-app.zip` + `SHA256.txt`) that installed
clients download when they check for updates. The engine binary and the model weights are still fetched from the
upstream official sources by `setup.py`.

Upstream Strata is driven from a terminal — `START-HERE.bat` / `./setup.sh` asks questions in a console, and sharing
is enabled with command-line flags. This client keeps the same engine but puts those steps into a graphical window.

## What this desktop client adds

| | Upstream (command line) | This desktop client |
| --- | --- | --- |
| **Interface language** | English only | **Chinese / English.** Follows your system language on first launch and can be switched at any time with the `中 / EN` button. Both the first-run wizard and the main app are fully bilingual. |
| **Choosing a model** | Answer interactive console questions during `setup.py` | A list of official models is rendered **inside the window**, each row showing the download size, the RAM it needs, and whether it is experimental or supports vision. Pick one from the dropdown and click download. |
| **Where models are stored** | Fixed by the installer prompt | **You choose the folder.** Type a path or open a native folder picker; the default location is pre-filled. Handy for putting a 70 GB model on a second SSD. |
| **Download progress** | Console output | Progress is shown in the window. When the download finishes the app loads the model by itself and moves on to the main screen — no console window stays open. |
| **Share on your LAN** | `--host 0.0.0.0 --api-key <secret>` on the command line | A **Share model** card: flip one switch and the app listens on `0.0.0.0:8080`, generates a random API key, and lists every reachable LAN address. |
| **Share remotely (Tailscale)** | Manual network setup | The same card detects a running Tailscale client and shows the `100.64.0.0/10` peer address. Tailscale is a **point-to-point direct connection** (WireGuard), so a machine on another network talks to this PC directly through the OpenAI-compatible API — the model traffic does not pass through a third-party server. |
| **API key handling** | You invent and type it | Generated automatically, shown in a read-only field, with **Copy** and **Regenerate** buttons. Local loopback (`127.0.0.1` / `::1`) stays key-free; every other host must send the key, and the `Host` header is validated to block DNS-rebinding attacks. |
| **First run** | Console prompts | A graphical wizard. If no model is found on disk it waits in the window for you to pick and download one; if models are found it starts loading them straight away. |
| **Startup feel** | A console window while 35-55 GB loads | A loading page is shown immediately, then the main window, so the app never looks frozen while the model is being loaded. |
| **Switching models** | Re-run the installer | **Switch model** from the About page card or the tray-icon context menu. The old model is unloaded in the background and the new one is loaded — the window and the process stay alive. |
| **Vision (image input)** | A yes/no prompt during setup | Detected automatically: when an `mmproj` file is present it is hard-linked into the model folder, the `.done` marker is filled in, and the engine is started with `--vision yes` and a 700 MiB VRAM reserve. |
| **Updating** | `UPDATE.bat` | **Check update / Apply update / Restart service** in the ℹ panel. The GitHub Releases API is used first and the web page only as a fallback, so a blocked `github.com` does not break updating. |
| **Launching twice** | The second run reports port 8080 in use and stops | A single-instance mutex: a second double-click just brings the existing window to the front, with no duplicate windows, tray icons or killed services. |
| **Copying text** | Browser default | Text selection is enabled app-wide and a custom right-click menu offers **Copy** and **Select all**. |

### Model management in detail

The model list is not hard-coded in the page. On first launch the desktop shell calls `list_official_models()`,
which reads `setup.MODELS` and returns, for every entry:

- the model key and a short description,
- the download size in GB,
- the RAM it needs in GB,
- whether it is marked experimental,
- whether it supports vision.

Those fields are what you see in the dropdown, so you can compare before committing to a 70 GB download. The
storage folder defaults to the `data_dir` saved in your settings (falling back to `Strata-data` next to the
program); you can edit the path by hand or use the native folder picker.

### Sharing in detail

The **Share model** card is shown only when the desktop bridge is available, because it needs the local backend to
bind a port. Turning it on:

1. binds the OpenAI-compatible server to `0.0.0.0:8080`,
2. generates a random API key if none exists yet,
3. fills the address list with the loopback address, every LAN IPv4 address, virtual-machine adapters and, when
   Tailscale is running, the `100.64.0.0/10` peer address.

Other devices then point their OpenAI-compatible client at `http://<shown-address>:8080/v1`. Requests from
`127.0.0.1` and `::1` skip the key check so the local chat keeps working; requests from any other host must send
`Authorization: Bearer <key>`.

Remote sharing is **peer-to-peer**. Tailscale is not a proxy and not a relay service: it builds a point-to-point WireGuard tunnel between the two machines, so the model traffic goes straight from the client to this PC. If a direct path cannot be established, Tailscale falls back to its relay — that is the exception, not the normal path.

## Download

Three assets are attached to each Release. The version number in the file names changes with every Release.

| Asset | What it is | When to use it |
| --- | --- | --- |
| `Strata-Setup-<version>.exe` | Graphical wizard installer (Inno Setup). Installs into a folder of your choice and creates Start-menu and desktop shortcuts. | A normal first install. |
| `Strata-<version>-windows-x64-green.zip` | Portable build. Unzip it and run `Strata.exe` from the top-level folder; nothing is written outside that folder. | No installer, or a USB stick. |
| `strata-app.zip` + `SHA256.txt` | Application-layer update package (no `Strata.exe`, no `_internal/`). | Downloaded automatically by the built-in updater; you rarely need it by hand. |

You do **not** need Git or Python: a portable Python runtime is inside the package. The engine binary (CUDA and
non-CUDA variants) and the model weights are downloaded on first run from the official sources, so they are not
part of these archives.

## Requirements

| | |
| --- | --- |
| **System** | Windows 10 / 11, 64-bit. |
| **Graphics card** | NVIDIA GeForce RTX 20 / 30 / 40 / 50 series; an RTX 2080 Ti is confirmed working. The engine ships CUDA and non-CUDA variants and picks the right one. |
| **RAM** | 32 GB or more. 64 GB runs every model size. |
| **Disk** | About 80 GB free for the default model; the largest sizes need considerably more. An SSD makes the first start much faster. |

## Where things come from

- **This repository** only hosts the application-layer update packages in its Releases.
- **The engine** is downloaded from the upstream official source or mirror by `setup.py`.
- **The model weights** come from the same official sources. Nothing large is bundled in this repository.

## License

Upstream Strata is MIT-licensed. This distribution repository only repackages the desktop client layer; see the
[upstream repository](https://github.com/Niko1221/Strata) for the engine and model licenses.

---

## 简体中文

[English](#strata-desktop) · **简体中文**

[Strata](https://github.com/Niko1221/Strata) 的 Windows 桌面客户端。本仓库是**应用层分发**通道：每个 Release 里放的
是应用更新包（`strata-app.zip` + `SHA256.txt`），已安装的客户端检查更新时会下载覆盖。引擎二进制和模型权重仍由
`setup.py` 从上游官方源下载。

上游 Strata 是命令行驱动的 —— `START-HERE.bat` / `./setup.sh` 在控制台里问答，共享要靠命令行参数开启。本客户端
沿用同一个引擎，但把这些步骤搬到了图形窗口里。

## 桌面客户端独有的特点

| | 上游（命令行） | 本桌面客户端 |
| --- | --- | --- |
| **界面语言** | 仅英文 | **中文 / 英文**。首次启动跟随系统语言，之后随时可用 `中 / EN` 按钮切换。首启向导和主界面均为完整双语。 |
| **选择模型** | 在 `setup.py` 里逐个回答控制台问题 | 官方模型清单直接**渲染在窗口里**，每项显示下载体积、所需内存，以及是否为实验性、是否支持视觉。下拉选一个，点下载即可。 |
| **模型存放路径** | 由安装程序提问决定 | **可自行选择目录**。既能手动输入路径，也能调用系统原生目录选择器，默认位置已预填。适合把 70 GB 的模型放到第二块 SSD。 |
| **下载进度** | 控制台输出 | 窗口内显示进度。下载完成后程序自动加载模型并进入主界面，不会一直挂着控制台窗口。 |
| **局域网共享** | 命令行 `--host 0.0.0.0 --api-key <secret>` | **共享模型**卡片：打开一个开关，服务监听 `0.0.0.0:8080`，自动生成随机 API Key，并列出所有可访问的局域网地址。 |
| **远程共享（Tailscale）** | 需自行配置网络 | 同一张卡片会探测正在运行的 Tailscale 客户端，并显示 `100.64.0.0/10` 对等地址。Tailscale 是**点对点直连**（WireGuard），其他网络上的机器直接与本机通信，通过 OpenAI 兼容 API 使用该模型，模型流量不经过第三方服务器。 |
| **API Key 处理** | 自己编、自己输 | 自动生成，显示在只读输入框里，带**复制**和**重新生成**按钮。本机回环（`127.0.0.1` / `::1`）免鉴权；其他来源必须携带 Key，同时校验 `Host` 头以防 DNS 重绑定攻击。 |
| **首次启动** | 控制台问答 | 图形化向导。本机找不到模型时在窗口里等待你选择并下载；找到模型则直接开始加载。 |
| **启动观感** | 加载 35-55 GB 期间只有一个控制台窗口 | 先立即显示加载进度页，再进入主界面，加载期间程序不会看起来像卡死。 |
| **切换模型** | 重新运行安装程序 | 在 About 页的「切换模型」卡片或托盘右键菜单里切换。旧模型后台卸载、新模型后台加载，窗口和进程全程不关闭。 |
| **视觉（图片输入）** | 安装时询问是/否 | 自动识别：检测到 `mmproj` 文件就硬链接进模型目录、补全 `.done` 标记，并以 `--vision yes` 加 700 MiB 显存预留启动引擎。 |
| **更新** | `UPDATE.bat` | ℹ 面板里的**检查更新 / 应用更新 / 重启服务**。优先走 GitHub Releases API，网页访问仅作兜底，`github.com` 被阻断也不会导致更新失效。 |
| **重复启动** | 第二次运行提示 8080 端口被占用后退出 | 单实例互斥锁：再次双击只会把已有窗口带到前台，不会出现多窗口、多托盘图标，也不会杀掉已有服务。 |
| **复制文字** | 浏览器默认行为 | 全局启用文本选择，并提供自定义右键菜单，支持**复制**和**全选**。 |

### 模型管理细节

模型清单不是写死在页面里的。首启时桌面外壳调用 `list_official_models()`，它读取 `setup.MODELS` 并为每个条目返回：

- 模型 key 与简短说明，
- 下载体积（GB），
- 所需内存（GB），
- 是否标记为实验性，
- 是否支持视觉。

下拉框里展示的就是这些字段，方便你在下载 70 GB 之前先做比较。存放目录默认取设置里的 `data_dir`（缺省为程序旁的
`Strata-data`），可以手动改，也可以调用原生目录选择器。

### 共享细节

**共享模型**卡片只在桌面桥可用时显示，因为需要本地后端去绑定端口。打开后会：

1. 把 OpenAI 兼容服务绑定到 `0.0.0.0:8080`，
2. 若尚无 API Key 则现场随机生成一个，
3. 地址列表填入回环地址、所有局域网 IPv4 地址、虚拟机网卡，以及 Tailscale 运行时的 `100.64.0.0/10` 对等地址。

其他设备把 OpenAI 兼容客户端指向 `http://<显示的地址>:8080/v1` 即可。来自 `127.0.0.1` 和 `::1` 的请求免鉴权，
保证本机对话不受影响；其他来源必须携带 `Authorization: Bearer <key>`。

远程共享是**点对点直连**。Tailscale 既不是代理也不是中转服务：它在两台机器之间建立点对点 WireGuard 隧道，
模型流量从客户端直接到本机。只有两端确实无法建立直连时，才会回落到中继，那是例外而非常态。

## 下载

每个 Release 附带三个资产。文件名里的版本号随每次发布变化。

| 资产 | 是什么 | 什么时候用 |
| --- | --- | --- |
| `Strata-Setup-<版本>.exe` | 图形向导安装器（Inno Setup）。安装到你选择的目录，并创建开始菜单和桌面快捷方式。 | 常规首次安装。 |
| `Strata-<版本>-windows-x64-green.zip` | 绿色免安装包。解压后直接运行顶层目录里的 `Strata.exe`，不会向该目录之外写入任何东西。 | 不想安装，或放 U 盘里用。 |
| `strata-app.zip` + `SHA256.txt` | 应用层更新包（不含 `Strata.exe`，不含 `_internal/`）。 | 由内置更新器自动下载，一般不需要手动使用。 |

**不需要**安装 Git 或 Python：包内自带便携 Python 运行时。引擎二进制（CUDA 与非 CUDA 变体）和模型权重在首次
运行时从官方源下载，因此不包含在这些压缩包里。

## 环境要求

| | |
| --- | --- |
| **系统** | Windows 10 / 11，64 位。 |
| **显卡** | NVIDIA GeForce RTX 20 / 30 / 40 / 50 系列；RTX 2080 Ti 已验证可用。引擎提供 CUDA 与非 CUDA 两种变体并自动选择。 |
| **内存** | 32 GB 及以上。64 GB 可运行所有规格。 |
| **硬盘** | 默认模型约需 80 GB 可用空间，最大规格会更多。使用 SSD 会让首次启动快很多。 |

## 文件来源

- **本仓库**的 Release 里只放应用层更新包。
- **引擎**由 `setup.py` 从上游官方源或镜像下载。
- **模型权重**同样来自官方源，本仓库不打包任何大体积文件。

## 许可证

上游 Strata 采用 MIT 许可证。本分发仓库只重新打包桌面客户端这一层；引擎与模型各自的许可证请见
[上游仓库](https://github.com/Niko1221/Strata)。
