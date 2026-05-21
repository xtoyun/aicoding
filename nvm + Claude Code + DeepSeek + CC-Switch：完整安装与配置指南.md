一、概述
本教程将带你从零开始，搭建一套完整的 AI 编程环境，包含 nvm（Node.js 版本管理器）、Claude Code（Anthropic 出品的 AI 编程命令行工具）、DeepSeek-V4-Pro（国产高性能大语言模型） 以及 CC-Switch（图形化 API 配置管理工具）。
这套组合的核心思路是：用 Claude Code 作为强大的 AI 编程 Agent 框架，通过 CC-Switch 图形化地将其底层模型切换为国产高性价比的 DeepSeek-V4-Pro，并用 nvm 保障 Node.js 环境的稳定与灵活管理。
为什么需要这套组合？

Claude Code 本身需要订阅 Claude，在国内使用有一定门槛
DeepSeek 提供了一个与 Anthropic API 完全兼容的接口，性能比肩 Claude Opus 4.6，但 API 价格便宜了两个数量级
CC-Switch 则让整个配置过程变得非常简单，有图形界面，不需要手动改 JSON 文件
nvm 用于管理 Node.js 多版本，确保不同项目对 Node.js 版本的不同需求得到满足

二、准备工作：申请 DeepSeek API Key
在开始安装任何软件之前，先去申请 DeepSeek 的 API Key。
步骤：

打开浏览器，访问 DeepSeek 开放平台：https://platform.deepseek.com/api_keys
注册/登录账号（支持微信扫码、手机号）
进入控制台后，点击左侧菜单的「API Keys」
点击「创建 API Key」，给它起个名字（比如 claude-code），然后点确认
⚠️ 立刻复制并保存好这个 Key！ 它只显示一次，关闭就看不到了！Key 的格式类似：sk-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

账号里要有余额才能使用。新用户一般有免费额度，不够可以充值（10 元可用很久）。

三、安装 nvm（Node.js 版本管理器）
3.1 为什么需要 nvm？
不同项目对 Node.js 版本有不同要求：老项目锁定 Node 16/18，新项目要求 Node 22+，全局工具（如 Claude Code）需要最新 LTS。直接安装/卸载 Node.js 很麻烦，而且容易残留环境变量和注册表项。nvm 的解决思路是：nvm install 18 下载并安装 Node 18，nvm install 22 下载并安装 Node 22，nvm use 18 把 PATH 指向 Node 18。
3.2 安装 nvm
Windows 用户：
Windows 下推荐使用 nvm-windows，不需要 WSL。

前往 nvm-windows Releases 下载最新 nvm-setup.exe（如 nvm-setup-1.1.12.exe）
双击运行安装程序，建议保持默认安装路径 C:\Users\用户名\AppData\Roaming\nvm
装完后 重新打开一个 PowerShell 窗口（让 PATH 生效），验证：
powershellnvm version

预期输出：1.1.12

注意：安装 nvm 前，建议先卸载电脑上现有的 Node.js，避免冗余引起冲突。

macOS/Linux 用户：
打开终端，运行以下命令安装 nvm：
bashcurl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash

如果 raw.githubusercontent.com 访问失败，可将域名替换为 cdn.gh-proxy.org 或 raw.gitmirror.com。
安装完成后，重载 shell 配置使其生效：
bashsource ~/.bashrc

验证 nvm 是否安装成功：
bashnvm --version

3.3 安装 Node.js LTS 版本
使用 nvm 安装 Node.js 长期支持版本：
bash# 查看可安装的 LTS 版本列表
nvm list available

# 安装 Node.js 22 LTS
nvm install 22

# 切换到 22
nvm use 22

# 验证
node --version
npm --version

预期输出：v22.14.0 和 10.9.2 类似的版本号。
3.4 配置 npm 国内镜像源
npm 默认从 https://registry.npmjs.org 拉包，国内直连速度慢且容易超时。切换到 npmmirror（原淘宝镜像）即可解决。
bash# 设置镜像源
npm config set registry https://registry.npmmirror.com

# 验证
npm config get registry

预期输出：https://registry.npmmirror.com

注意：https://registry.npm.taobao.org 已于 2024 年停止服务，请使用新域名 https://registry.npmmirror.com。

四、安装 Claude Code
4.1 通过 npm 全局安装
镜像源配好后，直接全局安装：
bashnpm install -g @anthropic-ai/claude-code

验证安装是否成功：
bashclaude --version

预期输出：v2.0.xx 类似的版本号。
4.2 Windows 用户常见问题
如果提示 claude : 无法将"claude"项识别为 cmdlet...，说明 npm 全局 bin 目录不在 PATH 中。
解决方法：以管理员身份打开 PowerShell，执行：
powershellSet-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser

然后重新安装。
4.3 跳过首次登录验证
编辑或新建 ~/.claude.json（Windows 路径：C:\Users\<用户名>\.claude.json），将 hasCompletedOnboarding 设为 true，跳过 Anthropic 官方登录验证。
json{
  "hasCompletedOnboarding": true
}

这个设置可以避免启动时的登录提示，让你直接进入工作状态。

五、安装 CC-Switch
5.1 什么是 CC-Switch？
CC-Switch 是一个跨平台的开源桌面应用，用于图形化管理 Claude Code、Codex、Gemini CLI 等多个 AI CLI 工具的模型供应商配置，内建 50+ Provider 预设。 它将原本需要 3-5 分钟的手动配置过程缩短到 5 秒，效率提升 60 倍，而且配置出错率降为 0%。
5.2 下载安装
Windows 用户：

访问 CC-Switch Releases 页面
找到最新版本，下载 CC-Switch-vx.x.x-Windows-x64.msi 安装包
双击 .msi → 下一步 → 同意协议 → 保持默认路径 → 安装
如遇缺少 WebView2 Runtime 的提示，需先安装 Microsoft Edge WebView2 Runtime：

访问 https://developer.microsoft.com/en-us/microsoft-edge/webview2/
下载后双击安装，装完重启即可

macOS 用户：
推荐使用 Homebrew 安装：
bashbrew tap farion1231/ccswitch
brew install --cask cc-switch

也可以直接从 Releases 下载 .dmg 安装包，将 CC-Switch.app 拖入「应用程序」文件夹。首次打开时 Mac 可能提示"无法验证开发者"，解决方法：打开「系统设置」→「隐私与安全性」→ 找到 CC-Switch，点击「仍要打开」。
Linux 用户：
从 Releases 下载 .deb / .rpm / .AppImage 格式的安装包进行安装。
5.3 安装后验证
安装成功后，打开 CC-Switch，系统托盘（任务栏右下角/菜单栏）会出现 CC-Switch 的图标。

六、在 CC-Switch 中配置 DeepSeek
这是最关键的步骤！
6.1 添加 DeepSeek Provider

启动 CC-Switch，主界面顶部有应用切换栏（Claude Code / Codex / Gemini CLI 等），确保当前选中的是 Claude Code。
点击右上角的「+」按钮，进入添加供应商页面。
你会看到一些内置预设（DeepSeek、Kimi、智谱等）。如果列表里有 DeepSeek 预设，直接选择即可；如果没有，选择「自定义」或「Custom」。
填写以下信息：

表格字段名填写内容名称（Name）DeepSeek V4 Pro（随意起名，方便识别）Base URLhttps://api.deepseek.com/anthropicAPI Key你在第二步申请的 DeepSeek API Key认证类型（Auth Type）ANTHROPIC_AUTH_TOKENAPI 格式Anthropic
6.2 配置模型名称（⚠️ 极其重要！）
关于 [1m] 的重要说明：在配置 DeepSeek 模型名称时，必须写成 deepseek-v4-pro[1m]，而不是 deepseek-v4-pro。
原因：加上 [1m] 才能开启 100 万 Token 的超长上下文。不加的话，只有默认的较短上下文，对于大型项目分析会有明显限制。这是一个非常容易漏掉的关键细节！
表格模型配置项填写内容说明主模型（Model）deepseek-v4-pro[1m]必须加 [1m]，开启 100 万 Token 上下文！Opus 模型deepseek-v4-pro[1m]复杂任务使用Sonnet 模型deepseek-v4-pro[1m]默认任务使用Haiku 模型deepseek-v4-flash快速/简单任务，更省钱子代理模型deepseek-v4-flash后台子任务
6.3 其他可选配置（建议开启）

将 CLAUDE_CODE_EFFORT_LEVEL 设置为 max——让模型以满血状态运行，充分调用思考能力
将 API_TIMEOUT_MS 设置为 3000000——防止长任务超时，DeepSeek 处理复杂请求有时需要较长时间

6.4 启用 DeepSeek Provider
确认所有信息填写无误后，点击「保存」或「添加」。配置保存后，在供应商列表中找到刚才添加的 DeepSeek V4 Pro，点击「启用」。CC-Switch 会自动将配置写入 Claude Code 的配置文件（~/.claude/settings.json），无需手动操作。
6.5 健康检查（验证配置是否正确）
启用后，点击 Provider 旁的「健康检查」按钮，CC-Switch 会发送一个测试请求验证 API Key 和网络连通性。

✅ 显示"成功"或绿色状态：配置正确，可以使用！
❌ 显示错误：请检查 API Key 是否正确、是否有余额

七、启动 Claude Code 并验证
7.1 启动 Claude Code
打开终端，进入你的项目目录，然后输入：
bashcd 你的项目文件夹路径
claude

如果是第一次运行，Claude Code 会有一个简短的初始化流程，直接按提示操作即可（无需登录 Anthropic 账号！ 使用 DeepSeek API 不需要 Claude 账号）。
7.2 确认模型是否正确
进入 Claude Code 界面后，输入以下命令查看当前状态：
/status

你会看到类似这样的输出：
Model: deepseek-v4-pro[1m]
API Base URL: https://api.deepseek.com/anthropic
...

如果 Model 一行显示的是 deepseek-v4-pro[1m]，说明接入成功！🎉
7.3 测试一下
试着让 Claude Code 做一件简单的事：
你好！请介绍一下你自己，你现在使用的是什么模型？

或者让它帮你写一段代码：
帮我写一个 Python 函数，输入一个列表，返回列表中所有偶数的平方和

如果 Claude Code 正常响应并给出答案，恭喜你，整个配置已经完成！

八、手动配置备用方案（可选）
如果 CC-Switch 图形界面遇到问题，也可以直接手动配置环境变量，效果完全一样。
8.1 编辑 settings.json
新建 ~/.claude/settings.json（Windows 路径：C:\Users\<用户名>\.claude\settings.json），写入以下内容：
json{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "sk-你的DeepSeek-API-Key",
    "ANTHROPIC_BASE_URL": "https://api.deepseek.com/anthropic",
    "ANTHROPIC_MODEL": "deepseek-v4-pro[1m]",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "deepseek-v4-pro[1m]",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "deepseek-v4-pro[1m]",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "deepseek-v4-flash",
    "CLAUDE_CODE_SUBAGENT_MODEL": "deepseek-v4-pro[1m]",
    "API_TIMEOUT_MS": "300000"
  },
  "model": "deepseek-v4-pro[1m]"
}

8.2 跳过首次引导
编辑 ~/.claude.json（Windows 路径：C:\Users\<用户名>\.claude.json）：
json{
  "hasCompletedOnboarding": true
}

九、Claude Code 核心会话管理命令
在 Claude Code 会话中，以下几个命令是管理状态、成本和会话生命周期的核心工具：
表格命令功能简述典型使用场景/status查看当前会话的版本、模型、记忆文件路径、项目目录等运行状态开始工作前或遇到异常时，快速确认环境配置是否正确/cost显示本次会话已消耗的 Token 数量及预估费用按量计费时，每次任务后或下班前查看开销，控制成本/clear完全清空当前对话历史，开始一个全新会话需要切换到完全不相干的任务，或旧上下文已不再有用时/compact压缩过长的对话历史，保留关键信息以释放上下文窗口对话很长导致响应变慢、AI 开始"遗忘"时使用/rename <名称>给当前会话重新命名，方便日后查找和恢复重要的调试或开发任务中途，打上有意义的标签/resume <名称>通过会话名称恢复到之前命名的会话第二天继续同一条任务，或复用排查过的问题场景/export将当前完整对话导出为 Markdown 文件归档复盘、分享给同事或作为项目文档保存

小贴士：善用 /compact 和 /rename 能极大提升长任务的连续性，避免反复向 AI 解释背景信息。

十、常见问题与解决方案
❓ Q1：启动 claude 后提示 "Not logged in, please run /login"
原因：认证类型选择了 ANTHROPIC_API_KEY 而不是 ANTHROPIC_AUTH_TOKEN。
解决方案：回到 CC-Switch，编辑 DeepSeek Provider 的配置，将认证类型改为 ANTHROPIC_AUTH_TOKEN，保存后重新启用。
❓ Q2：提示 400 错误 / Bad Request
可能原因 1：模型名称填错了。检查是否正确填写了 deepseek-v4-pro[1m]（注意中括号和 1m）。
可能原因 2：API 格式选择有误，确保选择的是 Anthropic 兼容格式，而不是 OpenAI chat completions。
可能原因 3：Base URL 末尾多了或少了斜杠，正确格式为 https://api.deepseek.com/anthropic（结尾无斜杠）。
❓ Q3：模型名称不显示 1M 上下文
原因：模型名称没有加 [1m] 后缀。
解决方案：在 CC-Switch 中，将所有需要长上下文的模型字段（Opus、Sonnet、主模型）都改为 deepseek-v4-pro[1m]，保存并重新启用。
❓ Q4：Windows 下 CC-Switch 安装失败，提示缺少 WebView2
解决方案：先安装 Microsoft Edge WebView2 Runtime，然后重新安装 CC-Switch。
❓ Q5：npm install 报错，提示网络超时
解决方案：尝试切换 npm 镜像源到国内镜像：
bashnpm config set registry https://registry.npmmirror.com
npm install -g @anthropic-ai/claude-code

❓ Q6：健康检查失败，提示余额不足
解决方案：登录 https://platform.deepseek.com/ 充值账户余额。DeepSeek V4-Pro 的价格非常实惠，10 元可以使用很长时间。
❓ Q7：响应速度慢，感觉在等待
原因：DeepSeek-V4-Pro 使用了思维链推理，复杂任务确实需要思考时间，这是正常现象。
解决方案：确保 API_TIMEOUT_MS 设置为足够大的值（如 3000000），防止超时中断。
❓ Q8：claude 命令找不到
解决方案：如果提示 claude : 无法将"claude"项识别为 cmdlet...，说明 npm 全局 bin 目录不在 PATH 中。

Windows：将 C:\Users\<用户名>\AppData\Roaming\npm 添加到系统 PATH 环境变量中。
macOS/Linux：将 ~/.npm-global/bin 添加到 PATH 中。

十一、高效工作流建议
11.1 项目级记忆与规范
在每个项目中运行 /init 命令，Claude Code 会分析你的项目并生成 CLAUDE.md 文件。这个文件会成为 AI 的"项目记忆"，记录项目架构、编码规范等。务必将其提交到 Git 仓库，以便团队共享。
11.2 上下文管理
完成一个功能开发或切换任务后，使用 /clear 命令清空对话历史，可以节省 Token，也让新任务更专注。
11.3 使用不同模型
通过 CC-Switch，你可以随时在 deepseek-v4-pro[1m]（处理复杂任务）和 deepseek-v4-flash（处理快速简单任务）之间切换，平衡效果和成本。
11.4 Plan 模式工作流（推荐）
在 Claude Code 中，按 Shift + Tab 可以切换权限确认模式：

Plan Mode（计划模式）：反复迭代直到计划满意
Auto-accept Mode（自动接受编辑模式）：Claude 一次性完成
Review & Verify：审查并验证结果

实践要点："如果我的目标是写一个 PR，我会使用 Plan 模式，与 Claude 反复讨论直到我满意它的计划。然后切换到自动接受编辑模式，Claude 通常能一次完成。"

十二、总结
通过以上步骤，你成功搭建了一套强大、灵活且高性价比的 AI 编程助手组合：

nvm 管理 Node.js 版本，确保环境兼容
Claude Code 提供强大的编程 Agent 框架
DeepSeek-V4-Pro 提供高性能且成本极低的模型能力
CC-Switch 提供图形化的配置管理，一键切换模型

这套工具组合将极大提升你的开发效率，让你在终端里用 Claude Code 的强大工具链，调用 DeepSeek-V4-Pro 的顶级能力，低成本享受 AI 编程助手！

最终检查清单：

✅ DeepSeek API Key 已申请并保存
✅ nvm 已安装，Node.js 22 LTS 已就绪
✅ npm 镜像源已配置为 npmmirror.com
✅ Claude Code 已通过 npm 全局安装
✅ ~/.claude.json 中已设置 "hasCompletedOnboarding": true
✅ CC-Switch 已安装并配置 DeepSeek Provider
✅ 模型名称使用了 deepseek-v4-pro[1m]（带 [1m] 后缀）
✅ 认证类型为 ANTHROPIC_AUTH_TOKEN
✅ 健康检查通过，Claude Code 启动后 /status 显示正确模型
