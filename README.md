# Hornet C/C++

Hornet C/C++ is a C/C++ language extension for VS Code with four parsing modes, project indexing and interactive function call graphs.

## Demo

![Hornet C/C++ demo: function call graphs, animated connection highlighting, parsing modes and the status menu](https://github.com/codehubcloud/hornet-cpptools-release/releases/download/v0.0.10/hornet-demo.gif)

**Explore call relationships and navigate to the code.** The demo shows the call graph, animated connection tracing, four parsing modes, and shortcuts for CPU usage, logs and index maintenance. The graph also supports expanding and collapsing branches.

[Open the demo](https://github.com/codehubcloud/hornet-cpptools-release/releases/download/v0.0.10/hornet-demo.gif) · [Download the latest release](https://github.com/codehubcloud/hornet-cpptools-release/releases/latest)

This repository distributes extension installers and user documentation. This release channel has its own version sequence, starting at **0.0.1**.

## Download and install

Open the [latest release](https://github.com/codehubcloud/hornet-cpptools-release/releases/latest) and download the appropriate `.vsix` file from Assets.

| Environment | Installer suffix |
| --- | --- |
| Windows x64 | `win32-x64.vsix` |
| Windows ARM64 | `win32-arm64.vsix` |
| Linux x64 | `linux-x64.vsix` |
| Linux ARM64 | `linux-arm64.vsix` |
| Linux ARMHF | `linux-armhf.vsix` |
| macOS Intel | `darwin-x64.vsix` |
| macOS Apple Silicon | `darwin-arm64.vsix` |
| Alpine Linux x64 | `alpine-x64.vsix` |
| Alpine Linux ARM64 | `alpine-arm64.vsix` |
| Universal | `universal.vsix` |

In VS Code, open Extensions, choose **… → Install from VSIX…**, select the installer, then run **Developer: Reload Window**.

You can also install from the command line:

```powershell
code --install-extension ./hornet-cpp-0.0.10-win32-x64.vsix --force
```

The extension ID is `hornet.hornet-cpp`. VS Code 1.85 or later is required. If you previously installed a 0.1.x version from another release channel, install this channel's VSIX manually and check the version on the extension details page. This channel does not currently provide Marketplace updates; follow GitHub Releases.

For Remote SSH, WSL or Dev Containers, install the extension on the workspace host and choose an installer for that host's operating system. Browser-only VS Code cannot run the local processes required by this extension.

## Open your first project

1. Open and trust a C/C++ project folder.
2. The default Hybrid mode discovers compilation databases automatically. Files with compile commands use clangd; without a database, the Tag index provides basic browsing. Automatic clangd download and verification are available on Windows x64, glibc Linux x64, and macOS x64/ARM64. On other targets, install clangd or choose Tag / Flyweight.
3. Hornet builds the project index. A spinning status icon and a percentage with file counts show progress; hover for details. When indexing completes, the status changes to **Hornet C/C++: idle**.
4. Right-click a function and choose **Hornet Show Graph**.

Platform-specific installers do not include a native clangd or compiler. Cross-compilation projects still need the actual toolchain, header paths and preprocessor definitions.

## Features

- Completion, hover information, signature help, definition/declaration navigation and references.
- Rename, code actions, formatting, semantic highlighting, inlay hints, outlines and symbol search.
- Call hierarchy, type hierarchy and interactive function call graphs.
- Compilation database import, merge, validation, discovery and project index refresh.
- Separate configuration and language services for each workspace folder.

Available semantic features depend on the selected server and the completeness of the project's compile commands. The bundled formatter is also available in Tag and Flyweight modes.

## Function call graph

Right-click a C/C++ function and run **Hornet Show Graph** to open the **Hornet Graph** panel.

- Use the plus/minus buttons on either side of a function to expand or collapse relationships. The selected function keeps its screen position and zoom level while new nodes are arranged around it.
- Use the toolbar to expand or collapse branches together. Expansion respects the configured depth and the graph's node limit.
- Hover over or click a connection to highlight its calling direction.
- Click the background or press Esc to clear the selected connection.
- Drag the background to pan, scroll or use the toolbar to zoom, and double-click a function to navigate to its code.
- Use the center action to continue exploring from a different function.

Each graph loads up to 250 functions. For larger call chains, change the center to explore another section. Branch groups receive their own space, sequential call chains remain horizontally aligned, and connection arrows attach to function ports.

## Compilation settings and indexing

Hornet stores processed compile commands in `.vscode/hornet/compile-db/compile_commands.json`. Run **Hornet C/C++: Import Compilation Database** to import an existing database, or use a configured CMake build directory.

Without complete compile commands, macros, conditional compilation, header resolution and call relationships may be incomplete. Hybrid limits rename, code actions and diagnostics for files without compile commands. Compiler mode uses clangd's capabilities.

Run **Hornet Build Index**, or click the indexing status item, to refresh the index. Indexing does not compile or link your program.

Example settings:

```json
{
  "hornet-cpp.languageProvider": "Hybrid",
  "hornet-cpp.clangd.path": "clangd",
  "hornet-cpp.cpuUsage": "Medium",
  "hornet-cpp.clangd.ignoreDiagnostics": "not_indexed",
  "hornet-cpp.clangd.enableInlayHints": true,
  "hornet-cpp.syntaxColor.enable": true
}
```

Open extension settings with **Hornet C/C++ → gear → Extension Settings**, or search for `@ext:hornet.hornet-cpp`. Setting descriptions and option labels follow the VS Code display language. JSON configuration keys and saved values remain unchanged.

To use your own formatting configuration, set `hornet-cpp.clangd.formatFilePath` to an absolute path or a workspace-relative path such as `${workspaceFolder}/config/.clang-format`. An explicit file takes priority over project configuration. If no file is found and `clangd.forceFormatStyle` is enabled, the bundled Hornet fallback uses LLVM style with four-space indentation and a 120-column limit.

## Parsing modes

Click the mode in the status bar or run **Hornet C/C++: Switch Parsing Mode**.

| Mode | Behavior | Requirements |
| --- | --- | --- |
| Hybrid (recommended) | Maintains a syntax index alongside the available Compiler service and routes requests according to compile-command coverage | Uses the syntax index without a database; semantic features need compile commands and clangd |
| Flyweight | Runs the Tree-sitter C/C++ WebAssembly parser in background workers, with incremental syntax trees for recent files | No compiler, clangd or compilation database required |
| Compiler | Uses clangd for semantic navigation, rename, refactoring and compilation diagnostics | Valid compile_commands.json and clangd |
| Tag | Builds a fast lexical symbol index in background workers | No additional tools required |

Tag / Flyweight provide symbol search, outlines, completion, definition/declaration navigation, syntax references and call graphs. They do not run preprocessing, type checking or refactoring; symbols with identical names may produce multiple candidates. Use Compiler, or supply compile commands for Hybrid, when precise semantic results are needed. Flyweight performance depends on the project and editing patterns.

The built-in syntax index can persist a JSON cache and refresh it from project files. Source and header discovery includes uppercase file extensions and is independent of `.gitignore`, `files.exclude` and `search.exclude`. Hornet's global and mode-specific exclusions determine what is indexed; Tag and Flyweight exclude build/output directories by default. Read or parse failures are recorded in the logs. Parser and formatter WebAssembly assets are bundled in the VSIX.

Custom native server executables must support the expected protocol. The built-in cache, symbol search and Hornet formatting fallback are this project's implementations; related settings describe differences from the original custom servers.

## Status and maintenance

Hover over **Hornet C/C++: idle** to see activation state, installed version, mode and CPU usage. Click the status item to open the maintenance menu.

- **Open Extension Logs / Open Clangd Logs / Open Hornet-DB Logs** open extension, compiler and syntax-index logs.
- **Open Settings** opens Hornet settings. The mode and CPU controls offer direct changes. CPU usage defaults to Maximum; High, Medium and Low are also available.
- **Update Index** refreshes the current index. **Rebuild Index** restarts parsing, clears the workspace's standard clangd index cache and scans the project again.
- **Reload window** reloads VS Code.
- **Clear Errors** clears current Hornet diagnostics. Later analysis may report them again.
- **FAQ** opens troubleshooting information.

CPU settings control clangd's thread budget and the background parser's duty cycle; they are approximate targets rather than operating-system resource limits. Hornet-DB is the log name for the lightweight syntax index.

## FAQ

**clangd download failed:** Run **Hornet C/C++: Auto Setup clangd** to retry, or **Hornet C/C++: Configure clangd** to select an installed executable. Downloads support VS Code's HTTP proxy and the `HTTPS_PROXY` / `HTTP_PROXY` environment variables. Hybrid can continue with its syntax index; Flyweight is another available mode.

**Index or call relationships are incomplete:** Check the compilation database, toolchain paths, include paths and preprocessor definitions. Status details distinguish project-file scanning from clangd compilation-database tasks. Check the logs and Hornet exclusion settings for failures or excluded files.

**Duplicate completion or diagnostics:** Check whether another enabled C/C++ extension is providing the same language features.

**Inspect logs:** Run **Hornet C/C++: Open Logs**, then check the extension version and clangd startup messages.

## Feedback

Report problems in [Issues](https://github.com/codehubcloud/hornet-cpptools-release/issues), including the operating system, CPU architecture, VS Code/extension/clangd versions and reproduction steps. Review logs before sharing them because they may contain local project paths.

## Third-party notices

The installer includes `LICENSE.txt` and `ThirdPartyNotices.txt`. clangd is installed separately under its own license. Hornet C/C++ is an independent tool, not Microsoft's official C/C++ extension.

---

<details>
<summary>中文说明 / Chinese documentation</summary>

Hornet C/C++ 是面向 VS Code 的 C/C++ 语言扩展，提供四种解析模式、项目索引和交互式函数调用图。

## 功能演示

![Hornet C/C++ 功能演示：函数调用图、悬停高亮连线、切换解析模式和打开状态菜单](https://github.com/codehubcloud/hornet-cpptools-release/releases/download/v0.0.10/hornet-demo.gif)

**看清调用关系，快速定位代码。** 动图依次展示函数调用图、连线动态追踪、四种解析模式，以及 CPU、日志和索引维护入口。调用图还支持一键展开和折叠。

[单独打开动图](https://github.com/codehubcloud/hornet-cpptools-release/releases/download/v0.0.10/hornet-demo.gif) · [下载最新版本](https://github.com/codehubcloud/hornet-cpptools-release/releases/latest)

本仓库发布插件安装包和使用说明。安装包从 **0.0.1** 开始独立编号。

## 下载与安装

打开 [最新版本](https://github.com/codehubcloud/hornet-cpptools-release/releases/latest)，在 Assets 中下载适合当前环境的 `.vsix` 文件。

| 使用环境 | 安装包后缀 |
| --- | --- |
| Windows x64 | `win32-x64.vsix` |
| Windows ARM64 | `win32-arm64.vsix` |
| Linux x64 | `linux-x64.vsix` |
| Linux ARM64 | `linux-arm64.vsix` |
| Linux ARMHF | `linux-armhf.vsix` |
| macOS Intel | `darwin-x64.vsix` |
| macOS Apple Silicon | `darwin-arm64.vsix` |
| Alpine Linux x64 | `alpine-x64.vsix` |
| Alpine Linux ARM64 | `alpine-arm64.vsix` |
| 通用包 | `universal.vsix` |

在 VS Code 中打开扩展视图，点击右上角 `…` → **Install from VSIX… / 从 VSIX 安装…**，选择下载的安装包，再执行 **Developer: Reload Window / 开发人员: 重新加载窗口**。

也可以使用命令行，例如：

```powershell
code --install-extension ./hornet-cpp-0.0.10-win32-x64.vsix --force
```

扩展 ID 为 `hornet.hornet-cpp`，要求 VS Code 1.85 或更新版本。如果已经安装旧发布渠道的 0.1.x 版本，本渠道的 0.0.1 编号更小，需要手动从 VSIX 安装；确认扩展详情显示目标版本。此渠道暂不通过 VS Code Marketplace 自动更新，请关注 Releases。

Remote SSH、WSL 和开发容器应在远程工作区环境安装扩展，并选择对应远程系统的包。浏览器版 VS Code 不支持本扩展需要的本地进程。

调用图会为展开的整组分支预留空间，连续调用链保持水平对齐，普通折线统一在相邻列的中线处转弯；点击展开时，所点击函数保持原位。

## 首次打开项目

1. 打开并信任 C/C++ 项目文件夹。
2. 默认 Hybrid 模式自动发现编译数据库；未配置数据库时直接启用 Tag 索引。有编译命令的文件使用 clangd。Windows x64、glibc Linux x64、macOS x64/ARM64 支持缺失时自动下载 clangd 并校验；其他目标请安装 clangd 或选择 Tag / Flyweight。
3. 扩展建立项目索引。状态栏以持续旋转图标显示正在索引，旁边显示当前扫描阶段的百分比和文件数量；悬停查看详细进度；完成后显示 **Hornet C/C++: idle**。
4. 在函数上右键选择 **Hornet Show Graph** 查看调用图。

安装包按平台生成，不代表该平台会附带 clangd 或编译器。交叉编译项目仍需配置实际工具链、头文件路径和宏定义。

## 主要功能

- 补全、悬停说明、签名提示、跳转定义/声明、查找引用。
- 重命名、代码操作、格式化、语义高亮、内联提示、大纲和符号搜索。
- 调用层次、类型层次和交互式函数调用图。
- 编译数据库导入、合并、校验、配置发现和项目索引刷新。
- 每个工作区文件夹独立配置和独立语言服务。

具体语言功能取决于 clangd 支持能力以及项目编译配置是否完整。

## 函数调用图

在 C/C++ 函数上右键执行 **Hornet Show Graph**，图形显示在底部 **Hornet Graph** 面板。

- 函数框两侧的加减按钮展开或折叠调用关系。操作时该函数保持原来的屏幕位置和缩放比例，新增内容向四周排布；需要查看全图时点击适应窗口按钮。
- 工具栏支持一键展开全部、一键折叠全部；展开受当前图的节点上限约束。
- 将鼠标移到连线上或点击连线，可显示沿调用方向移动的高亮短线。
- 点击空白处或按 Esc 取消已选择的连线。
- 拖动空白区域平移，鼠标滚轮或工具栏缩放，双击函数跳转到对应代码。
- 使用“设为中心”从另一个函数继续浏览。

每张图最多加载 250 个函数。大型调用链可切换中心分段浏览。布局为跨列调用预留连线路径，并将箭头连接到目标函数框的端口中心。

## 编译配置与索引

扩展读取 `.vscode/hornet/compile-db/compile_commands.json` 中的编译命令。可以执行 **Hornet C/C++: Import Compilation Database** 导入现有 `compile_commands.json`，或使用已配置的 CMake 构建目录。

没有完整编译数据库时可以进行基础浏览，但宏、条件编译、头文件和调用关系可能不完整。Hybrid 模式会限制未配置文件的重命名、代码操作和诊断；Compiler 模式按 clangd 提供的能力处理。

手动刷新索引：命令面板执行 **Hornet Build Index**，或点击状态栏索引项。索引不等同于编译或链接程序。

设置示例：

```json
{
  "hornet-cpp.languageProvider": "Hybrid",
  "hornet-cpp.clangd.path": "clangd",
  "hornet-cpp.cpuUsage": "Medium",
  "hornet-cpp.clangd.ignoreDiagnostics": "not_indexed",
  "hornet-cpp.clangd.enableInlayHints": true,
  "hornet-cpp.syntaxColor.enable": true
}
```

## 解析模式

点击底部模式名称或执行 **Hornet C/C++: Switch Parsing Mode**：

| 模式 | 工作方式 | 所需配置 |
| --- | --- | --- |
| Hybrid（推荐） | 同时维护 Tag 索引和可用的 Compiler 服务，按文件的编译命令覆盖情况自动选择 | 没有编译数据库时使用 Tag；语义功能需要数据库和 clangd |
| Flyweight | 后台线程运行 Tree-sitter C/C++ WebAssembly 解析器，近期文件使用增量语法树 | 无须安装编译器、clangd 或生成数据库 |
| Compiler | clangd 提供语义跳转、重命名、重构、实时编译诊断等 | 有效的 compile_commands.json 和 clangd |
| Tag | 后台线程快速建立词法标签索引，开箱即用 | 无额外工具要求 |

Tag / Flyweight 支持符号搜索、大纲、补全、定义/声明跳转、语法引用和调用图。它们不会执行预处理、类型检查或重构，同名符号可能返回多个候选；精确重命名、重构和编译错误请使用 Compiler，或为 Hybrid 配置对应文件的编译命令。Flyweight 的速度取决于文件和编辑方式，不保证所有项目都快于 Tag。

内置轻量索引可以持久保存 JSON 缓存，并按项目文件更新。递归扫描支持大写扩展名，不受 `.gitignore`、`files.exclude` 或 `search.exclude` 影响；索引范围由 Hornet 全局及模式专属排除项决定，Tag 和 Flyweight 默认排除 build/output 等目录。读取或解析失败会在日志中列出。解析器和格式化器的 WebAssembly 文件随 VSIX 附带，无须联网下载。

在 **Hornet C/C++ → 齿轮 → 扩展设置** 中配置，或搜索 `@ext:hornet.hornet-cpp`。设置说明、选项标签和顶部扩展简介随 VS Code 显示语言切换，JSON 配置键及保存值保持不变。原生详情正文固定读取一份 README，因此本页采用默认英文、中文说明可展开的形式。

使用自己的格式文件时，将 `hornet-cpp.clangd.formatFilePath` 设置为绝对路径或 `${workspaceFolder}/config/.clang-format` 这样的工作区相对路径。指定文件优先；找不到项目格式文件且启用 `clangd.forceFormatStyle` 时，使用本项目定义的 Hornet 样式：LLVM 基础、4 空格缩进、120 列。内置数据库和符号搜索也属于本项目实现，与原版定制服务器的区别已在相关设置中说明。

## 状态与维护

悬停 **Hornet C/C++: idle** 显示 **Hornet C/C++ Status**、Activated、实际安装版本、模式、CPU Usage；点击状态栏可打开相同操作的菜单。VS Code 的状态栏右键菜单由编辑器管理，因此 Hornet 使用悬停链接和左键菜单提供这些操作。

- **Open Extension Logs / Open Clangd Logs / Open Hornet-DB Logs**：分别查看扩展、编译服务和 Tag/Flyweight 索引日志。
- **Open Settings**：打开 Hornet 设置。点击模式或 CPU Usage 可直接修改；CPU 默认 Maximum，可改为 High、Medium、Low。
- **Update Index**：更新现有索引；**Rebuild Index**：重启解析器、清除工作区标准 clangd 索引缓存并重新扫描。
- **Reload window**：重新加载 VS Code 窗口。
- **Clear Errors**：清除 Hornet 当前诊断；下一次解析产生的错误会再次显示。
- **FAQ**：打开下方常见问题。

CPU 设置控制 clangd 线程数量和轻量后台解析器的工作占比，并非操作系统级 CPU 使用率硬上限。Hornet-DB 是 Hornet 轻量索引日志的名称。

## FAQ

**clangd 下载失败：** 执行 **Hornet C/C++: Auto Setup clangd** 重试，或通过 **Hornet C/C++: Configure clangd** 指定已安装的可执行文件。下载支持 VS Code HTTP 代理及 `HTTPS_PROXY` / `HTTP_PROXY` 环境变量。Hybrid 此时仍可使用 Tag；也可以切换到 Flyweight。

**索引或调用关系不完整：** 检查编译数据库、工具链路径、头文件搜索路径和预处理宏。状态详情分别显示工程源文件/头文件扫描数量和 clangd 编译数据库任务数量。检查 Hornet-DB / Clangd 日志及 `hornet-cpp.excludePaths` 是否存在读取失败或主动排除；语义解析仍依赖真实编译参数。

**补全或诊断重复：** 检查是否同时启用了其他提供相同功能的 C/C++ 语言扩展。

**查看日志：** 执行 **Hornet C/C++: Open Logs**，确认当前扩展版本和 clangd 启动状态。

## 反馈

请在 [Issues](https://github.com/codehubcloud/hornet-cpptools-release/issues) 提交问题，附上操作系统、CPU 架构、VS Code/插件/clangd 版本和复现步骤。日志中可能包含本地项目路径，提交前请自行检查。

## 第三方声明

插件安装包内保留 `LICENSE.txt` 和 `ThirdPartyNotices.txt`。clangd 单独安装并遵循其自身许可证。Hornet C/C++ 是独立工具，不是微软官方 C/C++ 扩展。

</details>
