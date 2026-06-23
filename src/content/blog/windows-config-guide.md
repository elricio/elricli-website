---
title: 'Windows 终端开发环境完整配置指南'
description: '介绍 Windows 终端开发环境的完整配置，包括安装必要的 CLI 工具和设置。'
pubDate: 2026-04-20
tags: ['Windows']
---

# Windows 终端开发环境完整配置指南

## 目标架构

Windows 原生推荐：

```txt
Windows Terminal
+ PowerShell 7
+ Scoop
+ Starship
+ zoxide
+ fzf / eza / bat / ripgrep / fd / lazygit
```

如果需要 Linux 开发环境，额外配置：

```txt
Windows Terminal
+ WSL2 Ubuntu
+ zsh
+ Starship
+ zoxide
+ fzf / eza / bat / ripgrep / fd / lazygit
```

---

# 一、Windows 原生方案：PowerShell 7

## 1. 安装 Windows Terminal 和 PowerShell 7

先确认是否有 winget：

```powershell
winget --version
```

安装 Windows Terminal：

```powershell
winget install Microsoft.WindowsTerminal
```

安装 PowerShell 7：

```powershell
winget install Microsoft.PowerShell
```

装完后打开 **Windows Terminal**，默认 Profile 建议设成：

```txt
PowerShell
```

不是旧版：

```txt
Windows PowerShell
```

验证 PowerShell 版本：

```powershell
$PSVersionTable.PSVersion
```

看到主版本是 `7` 即可。

---

## 2. 安装 Scoop

Scoop 是 Windows 上更适合安装开发 CLI 工具的包管理器，类似 macOS 的 Homebrew。

在 PowerShell 里执行：

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
Invoke-RestMethod -Uri https://get.scoop.sh | Invoke-Expression
```

验证：

```powershell
scoop --version
```

更新 Scoop：

```powershell
scoop update
```

---

## 3. 安装开发 CLI 工具

执行：

```powershell
scoop install starship zoxide fzf eza bat ripgrep fd lazygit
```

这些工具对应 macOS 上的：

```bash
brew install starship zoxide fzf eza bat ripgrep fd lazygit
```

工具作用：

| 工具             | 作用                           |
| ---------------- | ------------------------------ |
| `starship`       | 跨 shell prompt                |
| `zoxide`         | 智能跳目录，替代部分 `cd` 场景 |
| `fzf`            | 模糊搜索                       |
| `eza`            | 类似 `ls` 的现代替代           |
| `bat`            | 类似 `cat`，带语法高亮         |
| `ripgrep` / `rg` | 超快文本搜索                   |
| `fd`             | 更好用的文件查找               |
| `lazygit`        | 终端 Git UI                    |

验证：

```powershell
starship --version
zoxide --version
fzf --version
eza --version
bat --version
rg --version
fd --version
lazygit --version
```

---

## 4. 安装 Nerd Font

Starship / eza / Git 图标显示正常，通常需要 Nerd Font。

用 Scoop 安装字体：

```powershell
scoop bucket add nerd-fonts
scoop install JetBrainsMono-NF
```

然后打开 Windows Terminal 设置：

```txt
Settings
→ Profiles
→ PowerShell
→ Appearance
→ Font face
→ JetBrainsMono Nerd Font
```

如果没有显示这个字体名，重启 Windows Terminal。

---

## 5. 配置 PowerShell Profile

查看 PowerShell Profile 路径：

```powershell
$PROFILE
```

如果文件不存在，创建：

```powershell
New-Item -ItemType File -Path $PROFILE -Force
notepad $PROFILE
```

加入下面内容：

```powershell
# -----------------------------
# Prompt
# -----------------------------
Invoke-Expression (&starship init powershell)

# -----------------------------
# Smarter cd
# -----------------------------
Invoke-Expression (& { (zoxide init powershell | Out-String) })

# -----------------------------
# Aliases
# -----------------------------
Set-Alias lg lazygit
Set-Alias ll eza
Set-Alias b bat

# -----------------------------
# eza helpers
# -----------------------------
function la {
  eza -la --icons
}

function lt {
  eza --tree --level=2 --icons
}

# -----------------------------
# search helpers
# -----------------------------
function grep {
  rg @args
}

function find {
  fd @args
}

# -----------------------------
# Git helpers
# -----------------------------
function gs {
  git status
}

function ga {
  git add @args
}

function gcmsg {
  git commit -m $args
}

function gp {
  git push
}

function gl {
  git pull
}

# -----------------------------
# Node helpers
# -----------------------------
function ni {
  npm install
}

function nr {
  npm run @args
}

function pi {
  pnpm install
}

function pr {
  pnpm run @args
}
```

保存后重新打开 Windows Terminal，或者执行：

```powershell
. $PROFILE
```

验证 Starship：

```powershell
$env:STARSHIP_SHELL
```

验证 zoxide：

```powershell
Get-Command z
```

让 zoxide 先学习几个目录：

```powershell
cd ~
cd Downloads
cd Documents
```

之后测试：

```powershell
z doc
```

---

# 二、Starship 配置

Starship 的配置文件跨平台基本可以复用。

Windows 路径：

```powershell
notepad "$HOME\.config\starship.toml"
```

如果目录不存在：

```powershell
New-Item -ItemType Directory -Path "$HOME\.config" -Force
New-Item -ItemType File -Path "$HOME\.config\starship.toml" -Force
```

可以先用这份轻量配置：

```toml
add_newline = true

format = """
$directory$git_branch$git_status$nodejs$package$cmd_duration
$character
"""

[directory]
truncation_length = 3
truncate_to_repo = true

[git_branch]
symbol = "git:"

[nodejs]
symbol = "node:"

[cmd_duration]
min_time = 1000
format = "took [$duration]($style) "

[character]
success_symbol = "[❯](bold green)"
error_symbol = "[❯](bold red)"
```

如果图标乱码，优先检查 Windows Terminal 的字体，而不是改 Starship。

---

# 三、Windows Terminal 推荐设置

打开 Windows Terminal 设置 JSON：

```txt
Settings
→ Open JSON file
```

重点检查 PowerShell profile 里的字体设置：

```json
{
  "font": {
    "face": "JetBrainsMono Nerd Font",
    "size": 12
  }
}
```

建议配置：

| 项                 | 推荐                                      |
| ------------------ | ----------------------------------------- |
| Default profile    | PowerShell                                |
| Font               | JetBrainsMono Nerd Font                   |
| Starting directory | `%USERPROFILE%`                           |
| Theme              | One Half Dark / Catppuccin / Dracula 均可 |

不要为了像 macOS 一样在 Windows 原生环境里强行装 zsh。Windows 原生环境里 PowerShell 更自然。

---

# 四、Node / pnpm / Git 配置

## 1. Node.js

Windows 原生简单方案：

```powershell
winget install OpenJS.NodeJS.LTS
```

验证：

```powershell
node -v
npm -v
```

启用 pnpm：

```powershell
corepack enable
corepack prepare pnpm@latest --activate
pnpm -v
```

---

## 2. Git

安装 Git：

```powershell
winget install Git.Git
```

验证：

```powershell
git --version
```

设置基础身份：

```powershell
git config --global user.name "你的名字"
git config --global user.email "你的邮箱"
```

设置默认分支：

```powershell
git config --global init.defaultBranch main
```

---

# 五、VS Code / Cursor 配置

如果你在 Windows 上用 VS Code 或 Cursor，建议设置：

```json
{
  "terminal.integrated.defaultProfile.windows": "PowerShell",
  "terminal.integrated.fontFamily": "JetBrainsMono Nerd Font"
}
```

如果你同时用 WSL，VS Code 进入 WSL 项目时会自动使用 WSL 的 shell，不要强行让它用 Windows PowerShell。

---

# 六、WSL 方案：接近 macOS 的 zsh 环境

如果你希望 Windows 上也有接近 macOS 的 zsh 体验，使用 WSL2。

## 1. 安装 WSL Ubuntu

在 PowerShell 中执行：

```powershell
wsl --install -d Ubuntu
```

重启后打开 Ubuntu。

更新系统：

```bash
sudo apt update && sudo apt upgrade -y
```

安装基础工具：

```bash
sudo apt install -y zsh git curl unzip fzf ripgrep fd-find bat
```

注意 Ubuntu/Debian 里命令名通常是：

```bash
fdfind
batcat
```

可以加 alias：

```bash
alias fd='fdfind'
alias bat='batcat'
```

---

## 2. WSL 中安装并启用 zsh

安装 zsh：

```bash
sudo apt install -y zsh
```

查看 zsh 路径：

```bash
which zsh
```

设为默认 shell：

```bash
chsh -s $(which zsh)
```

重新打开 WSL 后验证：

```bash
echo $SHELL
```

如果输出类似下面这样，说明生效：

```txt
/usr/bin/zsh
```

---

## 3. WSL 中启用 Starship 和 zoxide

zsh 中启用方式：

```zsh
eval "$(starship init zsh)"
eval "$(zoxide init zsh)"
```

这和 macOS 上一样。

---

## 4. WSL 的 `.zshrc` 不要直接复制 macOS

macOS 里的这些配置不能直接搬到 WSL：

```zsh
export JAVA_HOME=$(/usr/libexec/java_home -v 17)
export ANDROID_HOME=$HOME/Library/Android/sdk
source $(brew --prefix nvm)/nvm.sh
```

原因：

| macOS 配置               | WSL 问题                                  |
| ------------------------ | ----------------------------------------- |
| `/usr/libexec/java_home` | WSL 没有                                  |
| `~/Library/Android/sdk`  | Windows/WSL 路径不同                      |
| Homebrew nvm 路径        | WSL 里不同                                |
| macOS `open`             | WSL 里通常用 `explorer.exe` 或 `xdg-open` |

WSL 的 `.zshrc` 应该单独维护。

---

# 七、Windows 原生 vs WSL 怎么选

## 默认选 Windows 原生 PowerShell，如果主要做：

```txt
React / Vue / Next.js
Node / pnpm
VS Code / Cursor
Git
Docker Desktop
普通前端开发
```

## 选 WSL，如果主要做：

```txt
Linux shell 脚本
需要贴近线上 Linux 环境
Docker/Linux 服务
tmux / neovim 深度工作流
公司项目明确要求 Linux
```

推荐长期组合：

```txt
日常前端：Windows PowerShell
Linux/后端/脚本：WSL Ubuntu
两边都装 Starship / zoxide / fzf / rg / fd / bat / lazygit
```

---

# 八、最终安装清单

## Windows 原生完整命令

```powershell
winget install Microsoft.WindowsTerminal
winget install Microsoft.PowerShell
winget install Git.Git
winget install OpenJS.NodeJS.LTS

Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
Invoke-RestMethod -Uri https://get.scoop.sh | Invoke-Expression

scoop bucket add nerd-fonts
scoop install JetBrainsMono-NF
scoop install starship zoxide fzf eza bat ripgrep fd lazygit
```

## PowerShell Profile

创建并打开：

```powershell
New-Item -ItemType File -Path $PROFILE -Force
notepad $PROFILE
```

加入：

```powershell
Invoke-Expression (&starship init powershell)
Invoke-Expression (&zoxide init powershell)

Set-Alias lg lazygit
Set-Alias ll eza
Set-Alias b bat

function la {
  eza -la --icons
}

function lt {
  eza --tree --level=2 --icons
}

function grep {
  rg @args
}

function find {
  fd @args
}
```

---

# 九、验证清单

重开 Windows Terminal 后执行：

```powershell
$PSVersionTable.PSVersion
starship --version
zoxide --version
fzf --version
eza --version
bat --version
rg --version
fd --version
lazygit --version
node -v
npm -v
git --version
```

再测试：

```powershell
la
lt
rg "useEffect"
fd package.json
lazygit
z downloads
```

如果都正常，Windows 原生开发环境就基本配好了。

---

# 十、最终建议

Windows 配置不要追求“和 macOS 完全一样”，而是追求：

```txt
工具一致：starship / zoxide / fzf / rg / fd / bat / lazygit
体验接近：prompt、跳目录、搜索、Git UI 类似
Shell 本地化：macOS 用 zsh，Windows 原生用 PowerShell，WSL 用 zsh
```

这样长期最稳，也最容易维护。
