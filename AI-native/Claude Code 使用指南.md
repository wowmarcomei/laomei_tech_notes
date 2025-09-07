#  Claude Code 使用教程



## 1 安装Node.js环境

Claude Code 需要 Node.js 环境才能运行。

### Windows 安装方法

方法一：官网下载（推荐）

1. 打开浏览器访问 `https://nodejs.org/`
2. 点击 "LTS" 版本进行下载（推荐长期支持版本）
3. 下载完成后双击 `.msi` 文件
4. 按照安装向导完成安装，保持默认设置即可

方法二：使用包管理器

如果你安装了 Chocolatey 或 Scoop，可以使用命令行安装：

```bash
# 使用 Chocolatey
choco install nodejs
# 或使用 Scoop
scoop install nodejs
```

###### Windows 注意事项

- 建议使用 PowerShell 而不是 CMD
- 如果遇到权限问题，尝试以管理员身份运行
- 某些杀毒软件可能会误报，需要添加白名单



###### 验证安装是否成功

安装完成后，打开 PowerShell 或 CMD，输入以下命令：

node --version

npm --version

如果显示版本号，说明安装成功了！



##  2. 安装 Claude Code

##### 安装 Claude Code

打开 PowerShell 或 CMD，运行以下命令：

```bash
# 全局安装 Claude Code

npm install -g @anthropic-ai/claude-code
```



这个命令会从 npm 官方仓库下载并安装最新版本的 Claude Code。

###### 提示

- 建议使用 PowerShell 而不是 CMD，功能更强大
- 如果遇到权限问题，以管理员身份运行 PowerShell

###### 验证 Claude Code 安装

安装完成后，输入以下命令检查是否安装成功：

```bash
claude --version
```

如果显示版本号，Claude Code 已经成功安装了。



## 3. 设置环境变量

#####  配置 Claude Code 环境变量

为了让 Claude Code 连接到你的中转服务，需要设置两个环境变量：

###### 方法一：PowerShell 临时设置（当前会话）

在 PowerShell 中运行以下命令：

```bash
$env:ANTHROPIC_BASE_URL = "http://xxxxx/api"
$env:ANTHROPIC_AUTH_TOKEN = "API密钥"
```



###### 方法二：PowerShell 永久设置（用户级）

在 PowerShell 中运行以下命令设置用户级环境变量：

```bash
# 设置用户级环境变量（永久生效）
[System.Environment]::SetEnvironmentVariable("ANTHROPIC_BASE_URL", "http://xxxxx:3000/api", [System.EnvironmentVariableTarget]::User)
[System.Environment]::SetEnvironmentVariable("ANTHROPIC_AUTH_TOKEN", "你的API密钥", [System.EnvironmentVariableTarget]::User)
```

查看已设置的环境变量：

```bash
# 查看用户级环境变量
[System.Environment]::GetEnvironmentVariable("ANTHROPIC_BASE_URL", [System.EnvironmentVariableTarget]::User)
[System.Environment]::GetEnvironmentVariable("ANTHROPIC_AUTH_TOKEN", [System.EnvironmentVariableTarget]::User)
```

设置后需要重新打开 PowerShell 窗口才能生效。



**验证环境变量设置**

设置完环境变量后，可以通过以下命令验证是否设置成功：

###### 在 PowerShell 中验证：

```bash
echo $env:ANTHROPIC_BASE_URL
echo $env:ANTHROPIC_AUTH_TOKEN
```

###### 在 CMD 中验证：

```bash
echo %ANTHROPIC_BASE_URL%
echo %ANTHROPIC_AUTH_TOKEN%
```

**预期输出示例：**

```bash
http://xxxxxxxx:3000/api

cr_xxxxxxxxxxxxxxxxxx
```

💡 如果输出为空或显示变量名本身，说明环境变量设置失败，请重新设置。

##  4 开始使用 Claude Code

现在你可以开始使用 Claude Code 了！

###### 启动 Claude Code

```bash
claude
```

在特定项目中使用

```bash
# 进入你的项目目录
cd C:\path\to\your\project
# 启动 Claude Code
claude
```

