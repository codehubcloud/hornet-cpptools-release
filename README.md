# Hornet C/C++

Hornet C/C++ is a C/C++ language extension for VS Code with four parsing modes, project indexing and interactive function call graphs.

## Demo

![Hornet C/C++ demo: function call graphs, animated connection highlighting, parsing modes and the status menu](https://github.com/codehubcloud/hornet-cpptools-release/releases/download/v0.0.1/hornet-demo.gif)

**Explore call relationships and navigate to the code.** The demo shows the call graph, animated connection tracing, four parsing modes, and shortcuts for CPU usage, logs and index maintenance. The graph also supports expanding and collapsing branches.

[Open the demo](https://github.com/codehubcloud/hornet-cpptools-release/releases/download/v0.0.1/hornet-demo.gif) · [Download the latest release](https://github.com/codehubcloud/hornet-cpptools-release/releases/latest)

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
code --install-extension ./hornet-cpp-0.0.4-win32-x64.vsix --force
```

The Marketplace extension ID is `codehubcloud.hornet-cpp`. VS Code 1.85 or later is required. Earlier GitHub 0.0.1/0.0.2 packages used `hornet.hornet-cpp`: uninstall that extension, install the Marketplace edition, and reload VS Code. A different publisher creates a different extension identity, so this migration requires a manual installation once. Do not keep both editions enabled.

For Remote SSH, WSL or Dev Containers, install the extension on the workspace host and choose an installer for that host's operating system. Browser-only VS Code cannot run the local processes required by this extension.

## Check for updates

### Changes in 0.0.4

- Increase the graph node limit from 250 to 1024, including the center node.
- Press **F6** on a selected variable or function, or the symbol at the cursor, to open its relationship graph. Reassign the shortcut in VS Code's Keyboard Shortcuts editor.

### Changes in 0.0.3

- `C_Cpp.intelliSenseEngine` defaults to `default`; Hornet no longer forces it to `disabled`.
- Right-click a structure member or global variable and choose **Hornet Show Graph**. Writers appear on the left, readers on the right; compound assignments and increments appear on both sides. Double-click an access node to open that reference. Semantic read/write highlights take priority; syntax fallback does not trace aliases or reference-parameter side effects.
- Each selected mode maintains its own `.vscode/hornet/index/<mode>/index.json` inventory of source/header symbols, including opened files using their saved disk contents. Hybrid and Compiler retain their own inventories in addition to clangd's native semantic index. Explicit exclusions still apply. Compiler requires clangd and starts with inferred browsing commands when no compilation database exists; accurate semantic results depend on actual build flags.
- Variable graphs distinguish declaration identities across function/block scopes, files, ordinary variables and structure owners. Center labels show category, owner/function, file and declaration line. Tag and Flyweight use syntax scopes and simple receiver types; unresolved member accesses are omitted. Complex C++ type resolution requires clangd.
- Graphs open directly for the cursor symbol without a candidate picker. Generated indexes, managed compile commands and default builds use `.vscode/hornet/`. Recognized legacy indexes migrate automatically; other extensions' files and VS Code settings stay in place. Explicit custom storage paths remain supported.

For the Marketplace edition, VS Code's **Auto Update** uses the extension marketplace. The built-in **Check for Updates** command described below checks GitHub Releases separately. The already-published GitHub 0.0.2 assets retain their original `hornet` publisher; they are not the Marketplace migration installer.

Run **Hornet C/C++: Check for Updates**, or select **Check for Updates** in the Hornet status menu. Hornet checks the public release repository and selects the VSIX for the workspace host, including SSH, WSL and containers. Choose **Download and Install** to verify its SHA-256 checksum and install through VS Code, then reload the window. **Open Release** opens the release page instead.

Startup checks run at most once per day and notify once per available version. Set `hornet-cpp.updates.checkOnStartup` to `false` for manual checks only. Checks and downloads respect the VS Code HTTP proxy and `HTTPS_PROXY` / `HTTP_PROXY`. Background failures appear in **Open Logs**; manual failures also offer the release page.

**Version correction:** The corrected **0.0.2** followed **0.0.1**; the current release is **0.0.4**. The incorrectly numbered **0.0.11** release is withdrawn. If you installed it, manually install the **0.0.2** VSIX and reload; that older package does not contain the update checker.

## User guide and walkthrough

Run **Hornet C/C++: Get Started** for the built-in five-step walkthrough, or **Hornet C/C++: Open User Guide** for the offline manual. Both are also available from the Hornet status menu. The walkthrough follows the VS Code display language and remembers progress; English and Chinese manuals are bundled. VS Code handles the walkthrough's initial display on installation.

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

You can also select a variable or function name, or place the cursor on it, and press **F6** in a C/C++ or CUDA editor. To customize or remove the shortcut, open **Preferences: Open Keyboard Shortcuts**, search for `@command:hornet-cpp.showCallGraph`, and edit its binding.

- Use the plus/minus buttons on either side of a function to expand or collapse relationships. The selected function keeps its screen position and zoom level while new nodes are arranged around it.
- Use the toolbar to expand or collapse branches together. Expansion respects the configured depth and the graph's node limit.
- Hover over or click a connection to highlight its calling direction.
- Click the background or press Esc to clear the selected connection.
- Drag the background to pan, scroll or use the toolbar to zoom, and double-click a function to navigate to its code.
- Use the center action to continue exploring from a different function.

Each graph loads up to 1024 functions. For larger call chains, change the center to explore another section. Branch groups receive their own space, sequential call chains remain horizontally aligned, and connection arrows attach to function ports.

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

Open extension settings with **Hornet C/C++ → gear → Extension Settings**, or search for `@ext:codehubcloud.hornet-cpp`. Setting descriptions and option labels follow the VS Code display language. JSON configuration keys and saved values remain unchanged.

To use your own formatting configuration, set `hornet-cpp.clangd.formatFilePath` to an absolute path or a workspace-relative path such as `${workspaceFolder}/config/.clang-format`. An explicit file takes priority over project configuration. If no file is found and `clangd.forceFormatStyle` is enabled, the bundled Hornet fallback uses LLVM style with four-space indentation and a 120-column limit.

## Parsing modes

Click the mode in the status bar or run **Hornet C/C++: Switch Parsing Mode**.

| Mode | Behavior | Requirements |
| --- | --- | --- |
| Hybrid (recommended) | Maintains a syntax index alongside the available Compiler service and routes requests according to compile-command coverage | Uses the syntax index without a database; semantic features need compile commands and clangd |
| Flyweight | Runs the Tree-sitter C/C++ WebAssembly parser in background workers, with incremental syntax trees for recent files | No compiler, clangd or compilation database required |
| Compiler | Uses clangd for semantic navigation, rename, refactoring and compilation diagnostics | clangd; inferred browsing commands when no database exists, actual build flags for accuracy |
| Tag | Builds lexical tags with syntax scopes and receiver types for variable binding in background workers | No additional tools required |

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

![Hornet C/C++ 功能演示：函数调用图、悬停高亮连线、切换解析模式和打开状态菜单](https://github.com/codehubcloud/hornet-cpptools-release/releases/download/v0.0.1/hornet-demo.gif)

**看清调用关系，快速定位代码。** 动图依次展示函数调用图、连线动态追踪、四种解析模式，以及 CPU、日志和索引维护入口。调用图还支持一键展开和折叠。

[单独打开动图](https://github.com/codehubcloud/hornet-cpptools-release/releases/download/v0.0.1/hornet-demo.gif) · [下载最新版本](https://github.com/codehubcloud/hornet-cpptools-release/releases/latest)

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
code --install-extension ./hornet-cpp-0.0.4-win32-x64.vsix --force
```

市场版扩展 ID 为 `codehubcloud.hornet-cpp`，要求 VS Code 1.85 或更新版本。之前 GitHub 的 0.0.1/0.0.2 安装包使用 `hornet.hornet-cpp`：请卸载旧扩展，安装市场版，再重新加载 VS Code。发布者不同会产生不同扩展 ID，因此首次迁移需要手动安装一次，请勿同时启用两个版本。

Remote SSH、WSL 和开发容器应在远程工作区环境安装扩展，并选择对应远程系统的包。浏览器版 VS Code 不支持本扩展需要的本地进程。

调用图会为展开的整组分支预留空间，连续调用链保持水平对齐，普通折线统一在相邻列的中线处转弯；点击展开时，所点击函数保持原位。

## 检查更新

### 0.0.4 更新

- 单张关系图的节点上限由 250 提高到 1024，包含中心节点。
- 选中变量或函数名称，或将光标放在名称上，按 **F6** 打开对应关系图；支持通过 VS Code 键盘快捷方式修改绑定。

### 0.0.3 更新

- `C_Cpp.intelliSenseEngine` 默认为 `default`，Hornet 不再强制设置为 `disabled`。
- 右键结构体成员变量或全局变量，选择 **Hornet Show Graph**：左侧显示写引用，右侧显示读引用；复合赋值、自增自减同时显示在两侧。双击节点跳转引用位置。优先使用语义后端的读写标记，语法后备分析不追踪别名和引用参数副作用。
- 当前模式独立保存 `.vscode/hornet/index/<mode>/index.json` 源码及头文件符号清单。已打开文件也会使用已保存的磁盘内容写入缓存。Hybrid、Compiler 同时保留自己的清单和 clangd 原生语义索引。显式排除设置仍然生效。Compiler 需要 clangd，没有数据库时使用推断的浏览命令启动；准确语义结果仍需真实构建参数。
- 变量关系按声明身份区分函数、嵌套块、文件、普通变量和不同结构体成员；中心节点标明类别、所属结构体或函数、文件和声明行号。Tag、Flyweight 使用语法作用域和简单接收对象类型，无法确认的成员访问会省略；复杂 C++ 类型解析需要 clangd。
- 关系图直接定位光标对应的符号，不再弹出候选选择框。自动生成的索引、托管编译数据库和默认构建目录统一放在 `.vscode/hornet/`，旧版 Hornet 索引自动迁移，其他扩展的文件和 VS Code 配置保留原位。显式自定义存储路径仍然有效。

市场版使用 VS Code 的 **Auto Update** 从扩展市场更新；下面的插件内“检查更新”独立检查 GitHub Releases。已经发布的 GitHub 0.0.2 附件保留原来的 `hornet` 发布者，不能用作市场版迁移安装包。

执行 **Hornet C/C++: 检查更新**，或在 Hornet 状态菜单中选择 **Check for Updates**。插件从公开发布仓检查正式版本，并按工作区主机选择安装包，包括 SSH、WSL 和容器。选择**下载并安装**后，插件会验证 SHA-256，再交给 VS Code 安装；完成后重新加载窗口。也可以选择**打开发布页**自行下载。

启动检查最多每天一次，同一可用版本仅提醒一次。将 `hornet-cpp.updates.checkOnStartup` 设置为 `false` 可仅保留手动检查。检查和下载支持 VS Code HTTP 代理及 `HTTPS_PROXY` / `HTTP_PROXY`。后台失败记入 **Open Logs**，手动检查失败时还可打开发布页。

**版本号纠正：** **0.0.2** 承接 **0.0.1**，当前版本为 **0.0.4**。误编号的 **0.0.11** 已撤回。如果已安装该旧版本，请手动安装当前市场版并重新加载窗口；旧安装包不包含检查更新功能。

## 使用指南与分步引导

在命令面板中执行 **Hornet C/C++: 开始使用（分步引导）**，按五个步骤完成上手；执行 **Hornet C/C++: 打开用户使用指南** 阅读离线手册。Hornet 状态菜单也提供 **Get Started** 和 **User Guide**。引导随 VS Code 显示语言切换并保存进度，手册附带中英文版本。安装时的引导展示由 VS Code 管理。

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

也可以在 C/C++ 或 CUDA 编辑器中选中变量、函数名称，或将光标放在名称上，按 **F6** 查看关系图。自定义或取消快捷键：执行 **首选项: 打开键盘快捷方式**，搜索 `@command:hornet-cpp.showCallGraph`，修改对应绑定。

- 函数框两侧的加减按钮展开或折叠调用关系。操作时该函数保持原来的屏幕位置和缩放比例，新增内容向四周排布；需要查看全图时点击适应窗口按钮。
- 工具栏支持一键展开全部、一键折叠全部；展开受当前图的节点上限约束。
- 将鼠标移到连线上或点击连线，可显示沿调用方向移动的高亮短线。
- 点击空白处或按 Esc 取消已选择的连线。
- 拖动空白区域平移，鼠标滚轮或工具栏缩放，双击函数跳转到对应代码。
- 使用“设为中心”从另一个函数继续浏览。

每张图最多加载 1024 个函数。大型调用链可切换中心分段浏览。布局为跨列调用预留连线路径，并将箭头连接到目标函数框的端口中心。

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
| Compiler | clangd 提供语义跳转、重命名、重构、实时编译诊断等 | 需要 clangd；无数据库时使用推断命令浏览，准确分析需要真实构建参数 |
| Tag | 后台线程建立词法标签，并通过语法作用域和接收对象类型绑定变量 | 无额外工具要求 |

Tag / Flyweight 支持符号搜索、大纲、补全、定义/声明跳转、语法引用和调用图。它们不会执行预处理、类型检查或重构，同名符号可能返回多个候选；精确重命名、重构和编译错误请使用 Compiler，或为 Hybrid 配置对应文件的编译命令。Flyweight 的速度取决于文件和编辑方式，不保证所有项目都快于 Tag。

内置轻量索引可以持久保存 JSON 缓存，并按项目文件更新。递归扫描支持大写扩展名，不受 `.gitignore`、`files.exclude` 或 `search.exclude` 影响；索引范围由 Hornet 全局及模式专属排除项决定，Tag 和 Flyweight 默认排除 build/output 等目录。读取或解析失败会在日志中列出。解析器和格式化器的 WebAssembly 文件随 VSIX 附带，无须联网下载。

在 **Hornet C/C++ → 齿轮 → 扩展设置** 中配置，或搜索 `@ext:codehubcloud.hornet-cpp`。设置说明、选项标签和顶部扩展简介随 VS Code 显示语言切换，JSON 配置键及保存值保持不变。原生详情正文固定读取一份 README，因此本页采用默认英文、中文说明可展开的形式。

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
