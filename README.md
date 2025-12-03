# Kimi CLI

> [!WARNING] 
> **本项目说明**：此版本是基于 [官方 Kimi CLI](https://github.com/MoonshotAI/kimi-cli) 改造的定制版本，**非官方主线版本**。
>
> 包含额外的功能扩展（CTF Agent、Daemon 模式、自定义模型等），如需使用官方版本，请访问 [MoonshotAI/kimi-cli](https://github.com/MoonshotAI/kimi-cli)。

## 主要功能
基于 kimi-cli 二次开发的针对 CTF 竞赛的专用 Agent

> [!IMPORTANT]  
> 建议与 [ez-xbow-platform-mcp](https://github.com/m-sec-org/ez-xbow-platform-mcp) 配合使用。

**🎯 本版本新增功能：**

-   ✨ **自定义 OpenAI 模型 API 接口支持** - 支持 DeepSeek、通义千问等第三方模型
-   🛡️ **Xbow 专用 Agent** - 针对竞赛优化的 Agent
-   🤖 **Daemon 模式自动解题** - 配合 xbow MCP 自动获取题目并解题
-   ⏰ **命令执行防沉迷保护** - 防止无限循环和过度命令执行
-   📁 **Kimi Session 隔离** - 按工作目录维护独立的对话上下文

**原有功能：**

-   类 Shell 的 UI 和 Shell 命令执行
-   Zsh 集成
-   支持
-   MCP 支持
-   更多功能持续开发中...

## 安装

Kimi CLI 以 Python 包的形式发布在 PyPI 上。我们强烈推荐使用 [uv](https://docs.astral.sh/uv/) 进行安装。如果你还没有安装 uv，请先按照[这里](https://docs.astral.sh/uv/getting-started/installation/)的说明进行安装。

安装好 uv 后，你可以使用以下命令安装 Kimi CLI：

```sh
uv tool install --python 3.13 kimi-cli
```

运行 `kimi --help` 检查 Kimi CLI 是否安装成功。

> [!IMPORTANT]  
> 由于 macOS 的安全检查机制，首次运行 `kimi` 命令可能需要 10 秒或更长时间，具体取决于你的系统环境。

使用以下命令将 Kimi CLI 升级到最新版本：

## 使用

在你想要工作的目录中运行 `kimi` 命令，然后发送 `/setup` 来配置 Kimi CLI：

![](./docs/images/setup.png)

配置完成后，Kimi CLI 就可以使用了。你可以发送 `/help` 获取更多信息。

## 🏆 比赛环境快速启动

> [!IMPORTANT]  
> 以下是完整的 CTF 自动解题环境配置流程，适用于需要使用 Daemon 模式进行自动化解题的场景。

### 第一步：配置 xbow MCP 服务

在当前目录创建 `mcp.json` 配置文件：

```json
{
    "mcpServers": {
        "xbow": {
            "url": "http://127.0.0.1:8080"
        }
    }
}
```

### 第二步：启动 xbow MCP 服务

xbow MCP 项目地址：[ez-xbow-platform-mcp](https://github.com/m-sec-org/ez-xbow-platform-mcp)
根据使用场景选择启动方式：

**使用模拟平台运行（测试）**：

```bash
./xbow-mcp --mock -listen 127.0.0.1:8080
```

**使用真实平台运行**：

```bash
./xbow-mcp \
  -xbow-url https://your-xbow-platform.com \
  -xbow-token YOUR_AUTH_TOKEN \
  -mode streamable \
  -listen 127.0.0.1:8080
```

### 第三步：配置 Kimi CLI

1. 安装依赖：

```bash
uv sync
```

2. 进入交互式命令行配置模型：

```bash
uv run kimi
```

3. 在 kimi 命令行中发送 `/setup` 配置模型（参考[自定义 OpenAI API 接口](#%E8%87%AA%E5%AE%9A%E4%B9%89-openai-api-%E6%8E%A5%E5%8F%A3)部分）
4. 配置完成后退出保存

### 第四步：启动自动解题

1. 根据实际情况修改启动脚本 `start.sh` 中的模型参数（`-m`）
2. 运行启动脚本：

```bash
sh start.sh
```

### 构建二进制（可选，用于多开）

如果需要同时运行多个 CLI 实例进行并行解题，可以构建独立的二进制文件：

```bash
make build
```

构建完成后，你可以：

-   在不同目录中同时运行多个 `kimi` 实例
-   每个实例独立配置不同的 agent 和模型
-   实现多 agent 并行解题，提高效率

**示例多开配置**：

```bash
# 终端1：使用 DeepSeek 模型
cd /path/to/project1
./kimi -a security -m deepseek-chat --daemon --verbose -c "优先做 web 题"

# 终端2：使用其他模型
cd /path/to/project2
./kimi -a security_beta -m qwen-plus --daemon --verbose -c "优先做 pwn 题"
```

## 功能

### Shell 模式

Kimi CLI 不仅是一个编码 Agent，同时也是一个 Shell。你可以通过按 `Ctrl-X` 来切换模式。在 Shell 模式下，你可以直接运行 Shell 命令而无需离开 Kimi CLI。

> [!NOTE]  
> 像 `cd` 这样的内置 Shell 命令暂不支持。

### Zsh 集成

你可以将 Kimi CLI 与 Zsh 一起使用，为你的 Shell 体验赋予 AI Agent 能力。

通过以下命令安装 [zsh-kimi-cli](https://github.com/MoonshotAI/zsh-kimi-cli) 插件：

```sh
git clone https://github.com/MoonshotAI/zsh-kimi-cli.git \
  ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/kimi-cli
```

> [!NOTE]  
> 如果你使用的不是 Oh My Zsh 的插件管理器，可能需要参考插件的 README 了解安装说明。

然后在 `~/.zshrc` 中将 `kimi-cli` 添加到 Zsh 插件列表：

```sh
plugins=(... kimi-cli)
```

重启 Zsh 后，你可以通过按 `Ctrl-X` 切换到 Agent 模式。

### ACP 支持

Kimi CLI 开箱即支持 。你可以将它与任何兼容 ACP 的编辑器或 IDE 一起使用。

例如，要在 [Zed](https://zed.dev/) 中使用 Kimi CLI，请在 `~/.config/zed/settings.json` 中添加以下配置：

```json
{
    "agent_servers": {
        "Kimi CLI": {
            "command": "kimi",
            "args": ["--acp"],
            "env": {}
        }
    }
}
```

然后你就可以在 Zed 的 Agent 面板中创建 Kimi CLI 线程了。

### 使用 MCP 工具

Kimi CLI 支持成熟的 MCP 配置约定。例如：

```json
{
    "mcpServers": {
        "context7": {
            "url": "https://mcp.context7.com/mcp",
            "headers": {
                "CONTEXT7_API_KEY": "YOUR_API_KEY"
            }
        },
        "chrome-devtools": {
            "command": "npx",
            "args": ["-y", "chrome-devtools-mcp@latest"]
        }
    }
}
```

使用 `--mcp-config-file` 选项运行 `kimi` 以连接到指定的 MCP 服务器：

```sh
kimi --mcp-config-file /path/to/mcp.json
```

> cli 目录下放置`mcp.json`无需指定配置文件会自动读取

### 自定义 OpenAI API 接口

Kimi CLI 支持使用自定义的 OpenAI 兼容 API 端点，可以使用 DeepSeek、通义千问等第三方模型服务。

#### 方式一：通过 `/setup` 配置（推荐）

运行 `kimi` 后，发送 `/setup` 命令进行交互式配置：

1. 选择 API 平台：

    ```
    Select the API platform
       1. Kimi For Coding
       2. Moonshot AI 开放平台 (moonshot.cn)
       3. Moonshot AI Open Platform (moonshot.ai)
    >  4. Custom API
    ```

2. 选择 Provider 类型：

    ```
    Select provider type
    >  1. OpenAI Legacy
       2. OpenAI Responses
       3. Kimi
       4. Anthropic
    ```

3. 根据提示输入 API Base URL 和 API Key

**支持的第三方服务示例**：

-   **DeepSeek**

    -   API Base: `https://api.deepseek.com/v1`
    -   Provider: OpenAI Legacy
    -   Model: `deepseek-chat`, `deepseek-coder` 等

-   **通义千问（Qwen）**

    -   API Base: `https://dashscope.aliyuncs.com/compatible-mode/v1`
    -   Provider: OpenAI Legacy
    -   API Key: 从阿里云获取

-   **其他 OpenAI 兼容服务**

    -   只要提供 OpenAI 兼容接口的服务都可以使用

#### 方式二：通过命令行参数

直接在启动时指定（注：此功能需要相应的命令行参数支持）：

```sh
kimi --openai-api-base https://api.deepseek.com/v1 --openai-api-key YOUR_API_KEY -m deepseek-chat
```

此功能允许你使用各种 OpenAI 兼容的第三方模型服务或自托管模型。

### Xbow 专用 Agent

Kimi CLI 包含专为竞赛设计的特殊 Agent(ctfer,security,security_beta) 模式。此模式针对安全分析、漏洞评估以及 CTF 挑战中常见的问题解决任务进行了优化。

> [https://github.com/swisskyrepo/PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)
>
> `security_beta`agent ，需要先 clone 上面这个仓库，借助 cli 的文件工具进行技巧筛选

### Daemon 模式自动解题

Kimi CLI 可以在 daemon 模式下运行，并在与 xbow MCP 服务器集成时自动获取和解决 CTF 题目。此功能支持持续的自动化解题工作流。

#### 配置 Daemon 模式

1. **在当前目录创建 MCP 配置文件** `mcp.json`（Kimi CLI 会自动加载当前目录的 mcp.json）：

```json
{
    "mcpServers": {
        "xbow": {
            "url": "http://127.0.0.1:8080"
        }
    }
}
```

2. **启动 Daemon 模式进行自动解题**：

```sh
kimi -a security -m deepseek-chat --daemon --verbose -c "优先尝试没有做过的题目,解决的题禁止尝试做和验证,如果list_challenges没有题目就说明完成任务了,不需要进行任何操作"
```

**参数说明**：

-   `-a security`：指定使用 security（CTF）专用 agent
-   `-m deepseek-chat`：指定使用的模型（可根据需要选择其他模型）
-   `--daemon`：启用 daemon 模式
-   `--verbose`：显示详细日志
-   `-c "..."` 或 `--command "..."`：初始任务指令，指导 agent 的解题策略

3. **工作原理**：

    - Daemon 在后台持续运行
    - 自动从 xbow MCP 获取新题目（通过 `list_challenges`）
    - 使用指定的 agent 和模型分析并尝试解决问题
    - 自动提交解决方案
    - 记录所有活动以供审查
    - 当没有新题目时自动完成任务

4. **监控 Daemon 模式**：

    - 查看实时日志：使用 `--verbose` 参数查看详细的执行日志
    - 停止 Daemon：使用 `Ctrl-C` 或发送终止信号

> [!TIP]  
> 你可以根据实际需求自定义任务指令，例如：
>
> -   指定题目类型：`-c "只做 web 类型的题目"`
> -   调整解题策略：`-c "优先做简单题目，从低分开始"`
> -   设置完成条件：`-c "完成 10 道题目后停止"`

> [!NOTE]
>
> -   MCP 配置文件默认从当前目录的 `mcp.json` 加载，无需指定 `--mcp-config-file` 参数
> -   建议使用 `--verbose` 参数以便实时监控 daemon 的执行状态
> -   Agent 类型 `-a` 和模型 `-m` 参数请根据实际项目配置

> [!TIP]  
> 📚 **完整环境配置指南**：如需配置 CTF 比赛环境进行自动解题，请参考 [比赛环境快速启动](#-ctf-%E6%AF%94%E8%B5%9B%E7%8E%AF%E5%A2%83%E5%BF%AB%E9%80%9F%E5%90%AF%E5%8A%A8) 章节，包含 xbow MCP 配置、服务启动、模型配置和多开等完整说明。

### 命令执行防沉迷

为了防止意外的无限循环或过度的命令执行，Kimi CLI 包含了防沉迷保护功能。此功能会监控命令执行频率，并在检测到异常模式时暂停或发出警告。

该保护功能会自动：

-   跟踪命令执行频率
-   检测潜在的无限循环
-   在达到限制前提供警告
-   必要时临时暂停执行

此功能默认启用，防止 agent 降智

### Kimi Session 隔离

Kimi CLI 支持 session 隔离，允许你维护独立的对话上下文。每个工作目录会有独立的 session，拥有自己的：

-   对话历史
-   上下文和状态
-   配置设置

**使用方式**：

每次运行 `kimi` 时，会为当前工作目录创建一个新的 session：

```sh
kimi
```

如果要继续上一次的 session，使用 `--continue` 或 `-C` 参数：

```sh
kimi --continue
# 或者
kimi -C
```

也可以通过 `--work-dir` 或 `-w` 指定工作目录：

```sh
kimi -w /path/to/project --continue
```

**Session 数据存储**：

-   Session 数据会存储在**当前目录**的 `.kimi` 文件夹中
-   每个工作目录会创建独立的 session
-   不同目录的项目有各自独立的 `.kimi` 文件夹和 session 历史

这在以下场景特别有用：

-   同时处理多个项目
-   为不同任务维护独立的上下文
-   将 CTF 解题会话与开发工作隔离
-   在不同项目目录中保持各自的对话历史

## Development

To develop Kimi CLI, run:

```sh
git clone https://github.com/MoonshotAI/kimi-cli.git
cd kimi-cli

make prepare  # prepare the development environment
```

Then you can start working on Kimi CLI.

Refer to the following commands after you make changes:

```sh
uv run kimi  # run Kimi CLI

make format  # format code
make check  # run linting and type checking
make test  # run tests
make help  # show all make targets
```

## Contributing

We welcome contributions to Kimi CLI! Please refer to [CONTRIBUTING.md](./CONTRIBUTING.md) for more information.
