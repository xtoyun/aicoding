等。以下是根据您的要求，将“nvm + Claude Code + DeepSeek + CC-Switch：完整安装与配置指南”整理为 .md 格式的完整文档。文档中已综合搜索结果的关键信息，并按照引用规范标注来源。

nvm + Claude Code + DeepSeek + CC-Switch：完整安装与配置指南

适用环境：Windows 10/11、macOS、Linux
更新时间：2026-05-21
阅读时长：约 15 分钟

一、概述
本指南将带你从零开始搭建一套完整的 AI 编程环境，核心组合包括：

nvm：Node.js 版本管理器，用于灵活切换 Node.js 版本 。
Claude Code：Anthropic 推出的终端级 AI 编程助手，能直接理解项目并自动修改代码 。
DeepSeek V4 Pro：国产高性能大语言模型，提供与 Anthropic 兼容的 API，成本远低于官方 。
CC-Switch：图形化配置管理工具，一键切换模型供应商，无需手动编辑 JSON 文件 。

核心思路：用 Claude Code 作为 AI 编程 Agent 框架，通过 CC-Switch 图形化地将底层模型切换为 DeepSeek V4 Pro，并用 nvm 保障 Node.js 环境的灵活管理。

二、环境准备
2.1 依赖清单
组件最低版本说明nvm-windows (Windows) / nvm (macOS/Linux)1.1.12+ / 0.39.7+Node.js 版本管理器 Node.js18.0+（推荐 22 LTS）Claude Code 运行时 npm10.x+随 Node.js 自带CC-Switchv3.14.1+多模型切换桌面工具 Git2.38+Claude Code 需要 Git 进行版本控制 
2.2 系统要求

Windows：Windows 10 或 11（64 位），推荐使用 PowerShell 或 Windows Terminal 。
macOS：macOS 10.15 (Catalina) 及以上。
Linux：Ubuntu 22.04+ / Debian 11+ / Fedora 34+ 等主流发行版。

三、安装步骤
3.1 安装 nvm
Windows（推荐 nvm-windows）

前往 nvm-windows Releases 下载最新 nvm-setup.exe（如 nvm-setup-1.1.12.exe）。
双击安装，建议保持默认路径（C:\Users\<用户名>\AppData\Roaming\nvm）。
重要：安装 nvm 前请卸载电脑上已有的 Node.js，避免冲突 。
安装完成后重新打开一个 PowerShell 窗口，验证：
nvm version

预期输出：1.1.12。

macOS/Linux
使用安装脚本：
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

如果 raw.githubusercontent.com 访问失败，可使用镜像 raw.gitmirror.com。安装完成后重载 shell 配置：
source ~/.bashrc  # 或 source ~/.zshrc
nvm --version

预期输出：0.39.7 或更高 。
3.2 安装 Node.js 22 LTS
# 查看可安装的 LTS 版本
nvm list available

# 安装 Node.js 22 LTS
nvm install 22

# 切换到 22
nvm use 22

# 验证
node --version
npm --version

预期输出：v22.14.0 和 10.9.2 。

提示：如果 nvm list available 报错“Could not retrieve…”，说明 nvm 无法访问官方源。可手动下载 Node.js 安装包后通过 nvm install <本地路径> 安装，或配置镜像源（见下文）。

3.3 配置 npm 国内镜像源（国内用户必做）
npm config set registry https://registry.npmmirror.com
npm config get registry

预期输出：https://registry.npmmirror.com 。

⚠️ 旧地址 https://registry.npm.taobao.org 已于 2024 年停止服务，请使用新域名 。

可选：同步配置常用二进制镜像：
npm config set electron_mirror https://npmmirror.com/mirrors/electron/
npm config set sass_binary_site https://npmmirror.com/mirrors/node-sass/

3.4 安装 Claude Code
npm install -g @anthropic-ai/claude-code

验证：
claude --version

预期输出：v2.0.xx 或更高 。

Windows 常见错误：若提示 claude : 无法将"claude"项识别为 cmdlet...，说明 npm 全局 bin 目录不在 PATH 中。解决方案：手动将 %APPDATA%\npm 添加到系统环境变量 Path 中，或执行以下命令（以管理员身份）：
[Environment]::SetEnvironmentVariable("Path", $env:Path + ";C:\Users\<用户名>\AppData\Roaming\npm", "User")

然后重新打开终端 。

3.5 安装 CC-Switch
CC-Switch 是一个跨平台的桌面应用，用于图形化管理 Claude Code 等工具的模型供应商配置 。
Windows 用户：

前往 CC-Switch Releases 下载最新 .msi 安装包（如 CC-Switch-v3.14.1-Windows-x64.msi）。
双击安装，按提示完成。如遇 SmartScreen 警告，点击“更多信息”→“仍要运行” 。

macOS 用户（推荐 Homebrew）：
brew tap farion1231/ccswitch
brew install --cask cc-switch

Linux 用户：下载 .deb / .AppImage 包进行安装。
安装后启动 CC-Switch，系统托盘会出现图标，主界面可检测到已安装的 Claude Code 。
3.6 手动配置 Claude Code 免登录（必须）
Claude Code v2.0.65+ 强制要求完成初次引导（Onboarding），即便使用第三方 API 也会被卡住。需要手动修改两个 JSON 文件 。
文件一：~/.claude.json（Windows 路径：C:\Users\<用户名>\.claude.json）
{
  "hasCompletedOnboarding": true
}

若文件已存在，确保 JSON 中包含该字段 。
文件二：~/.claude/settings.json（Windows 路径：C:\Users\<用户名>\.claude\settings.json）
{
  "env": {
    "ANTHROPIC_BASE_URL": "https://api.deepseek.com/anthropic",
    "ANTHROPIC_AUTH_TOKEN": "sk-你的DeepSeek-API-Key",
    "ANTHROPIC_MODEL": "deepseek-v4-pro",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "deepseek-v4-pro",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "deepseek-v4-pro",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "deepseek-v4-flash",
    "ANTHROPIC_SMALL_FAST_MODEL": "deepseek-v4-flash",
    "CLAUDE_CODE_SUBAGENT_MODEL": "deepseek-v4-pro",
    "CLAUDE_CODE_EFFORT_LEVEL": "max",
    "API_TIMEOUT_MS": "3000000"
  },
  "model": "deepseek-v4-pro"
}

⚠️ 字段名必须是 ANTHROPIC_AUTH_TOKEN 而非 ANTHROPIC_API_KEY，否则返回 401 错误 。
⚠️ 旧模型名 deepseek-chat / deepseek-reasoner 将于 2026-07-24 停用，务必使用 deepseek-v4-pro / deepseek-v4-flash 。

3.7 在 CC-Switch 中配置 DeepSeek Provider（推荐方式）

启动 CC-Switch，顶部切换至 Claude Code 标签。
点击右上角“+”，在预设列表中选择 DeepSeek，Base URL 自动填充为 https://api.deepseek.com/anthropic。
填入你在 DeepSeek 平台申请的 API Key（申请方式见下文）。
在高级选项中设置模型映射：

主模型：deepseek-v4-pro
Haiku：deepseek-v4-flash
Sonnet/Opus：deepseek-v4-pro

可选：启用“最大强度思考”（Max）和“1M 上下文”（[1m] 后缀）。
点击保存，然后在主界面点击 Enable 启用。

关于 [1m] 后缀：社区实测发现，若不加 [1m]，DeepSeek V4 Pro 默认上下文较短；加上 [1m] 才能开启 100 万 Token 超长上下文。配置时模型名称应写为 deepseek-v4-pro[1m] 。

3.8 启动与验证
在任意项目目录下执行：
claude

若终端出现 Claude Code 交互界面，输入 /status 查看模型信息：
Current model: deepseek-v4-pro

输入一条简单指令测试全链路：
你好，请简单介绍一下你自己

正常返回回复即表示配置成功 。

四、DeepSeek API Key 申请（必读）

访问 DeepSeek 开放平台，注册/登录。
在控制台左侧点击 API Keys → 创建 API Key，命名后确认。
重要：复制并安全保存 API Key（仅显示一次）。
账户需要充值至少 ¥1.00 才能调用（DeepSeek V4 Pro 费用：输入 1 元 / 百万 Tokens，输出 2 元 / 百万 Tokens）。

五、高效工作流建议

项目级记忆：在每个项目中运行 /init，生成 CLAUDE.md 文件，记录项目规范，并提交到 Git 仓库 。
上下文管理：完成一个功能后使用 /clear 清空对话历史，节省 Token。
模型切换：通过 CC-Switch 在 deepseek-v4-pro（复杂任务）和 deepseek-v4-flash（快速任务）之间切换 。
Plan 模式工作流：按 Shift + Tab 切换模式，先规划后编码，提升效率 。
并行会话：利用 Git worktrees 开启多个 Claude Code 实例，互不干扰 。

六、常见问题与 Debug
❌ claude 命令不可用
原因：npm 全局 bin 目录未加入 PATH。
解决：将 %APPDATA%\npm 添加到系统环境变量 Path 中 。
❌ npm install 超时
原因：默认 npm 官方源国内访问慢。
解决：切换到 https://registry.npmmirror.com 。
❌ 提示 “Login required”
原因：hasCompletedOnboarding 未设为 true。
解决：编辑 ~/.claude.json 添加该字段 。
❌ API 调用 401 错误
原因：字段名写为 ANTHROPIC_API_KEY 而非 ANTHROPIC_AUTH_TOKEN，或 API Key 无效。
解决：检查 settings.json 中字段名及 Key 的正确性 。
❌ 模型名称未显示 1M 上下文
原因：未加 [1m] 后缀。
解决：在 CC-Switch 中将模型名称改为 deepseek-v4-pro[1m] 。

七、参考资源

nvm-windows GitHub：https://github.com/coreybutler/nvm-windows/releases
Node.js 中文镜像：https://nodejs.cn/download/
CC-Switch Releases：https://github.com/farion1231/cc-switch/releases
DeepSeek 开放平台：https://platform.deepseek.com/
Claude Code 官方文档：https://docs.anthropic.com/en/docs/claude-code/overview

八、总结
通过以上步骤，你已经成功搭建了 nvm + Claude Code + DeepSeek + CC-Switch 这套完整的 AI 编程环境。本组合充分利用了各工具的优势：nvm 管理 Node.js 多版本，Claude Code 提供强大的 Agent 能力，DeepSeek 提供高性价比的大模型，CC-Switch 让配置管理变得可视化、零错误。祝你编码愉快！

