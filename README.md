# Hornet C/C++

Hornet C/C++ 是面向 VS Code 的 C/C++ 语言扩展，提供基于 clangd 的代码浏览、项目索引和交互式函数调用图。

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
code --install-extension ./hornet-cpp-0.0.1-win32-x64.vsix --force
```

扩展 ID 为 `hornet.hornet-cpp`，要求 VS Code 1.85 或更新版本。如果已经安装旧发布渠道的 0.1.x 版本，本渠道的 0.0.1 编号更小，需要手动从 VSIX 安装；确认扩展详情显示目标版本。此渠道暂不通过 VS Code Marketplace 自动更新，请关注 Releases。

Remote SSH、WSL 和开发容器应在远程工作区环境安装扩展，并选择对应远程系统的包。浏览器版 VS Code 不支持本扩展需要的本地进程。

## 首次打开项目

1. 打开并信任 C/C++ 项目文件夹。
2. 扩展会搜索可用的 clangd。Windows x64、glibc Linux x64、macOS x64/ARM64 支持缺失时自动下载并校验；其他目标请先在工作区主机上安装 clangd。
3. 扩展自动发现编译数据库或 CMake 构建配置，并建立项目索引。状态栏显示索引进度和 **Index ready**。
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

- 函数框两侧的加减按钮展开或折叠调用关系。
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
  "hornet-cpp.mode": "hybrid",
  "hornet-cpp.clangd.path": "clangd",
  "hornet-cpp.cpuUsage": "Medium",
  "hornet-cpp.clangd.ignoreDiagnostics": "not_indexed",
  "hornet-cpp.clangd.enableInlayHints": true,
  "hornet-cpp.syntaxColor.enable": true
}
```

目前 Hybrid 使用 clangd；Tag/Flyweight 尚未实现，选择它们不会切换到新的后端。

## 常见问题

**clangd 下载失败：** 点击状态栏 **Hornet: Retry clangd** 重试；可以通过 **Hornet C/C++: Configure clangd** 指定已安装的可执行文件。下载支持 VS Code HTTP 代理及 `HTTPS_PROXY` / `HTTP_PROXY` 环境变量。

**索引或调用关系不完整：** 检查编译数据库、工具链路径、头文件搜索路径和预处理宏。基础扫描有目录深度和文件数量限制，大型项目应提供真实编译数据库。

**补全或诊断重复：** 检查是否同时启用了其他提供相同功能的 C/C++ 语言扩展。

**查看日志：** 执行 **Hornet C/C++: Open Logs**，确认当前扩展版本和 clangd 启动状态。

## 反馈

请在 [Issues](https://github.com/codehubcloud/hornet-cpptools-release/issues) 提交问题，附上操作系统、CPU 架构、VS Code/插件/clangd 版本和复现步骤。日志中可能包含本地项目路径，提交前请自行检查。

## 第三方声明

插件安装包内保留 `LICENSE.txt` 和 `ThirdPartyNotices.txt`。clangd 单独安装并遵循其自身许可证。Hornet C/C++ 是独立工具，不是微软官方 C/C++ 扩展。
