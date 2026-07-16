# Ubuntu Desktop 开发环境安装指南

> 基于 Ubuntu 26.04 LTS (tim-pc, 9100 PRO SSD)，2026-07-14

## 前置条件

- Ubuntu 26.04 LTS 已安装
- 用户 `xing` 已创建，有 sudo 权限
- Tailscale 已配置（IP: 100.115.235.87）
- Homebrew (Linuxbrew) 已安装

## 安装清单总览

### L1 — Shell 基础

| 工具 | 用途 | 安装方式 | 版本 |
|------|------|----------|------|
| zsh | 替代 bash 的现代 shell | brew | 5.9.2 |
| oh-my-zsh | zsh 框架，主题/插件管理 | curl 脚本 | latest |
| starship | 跨 shell 提示符，替代 powerlevel10k | curl 脚本 → ~/.local/bin | latest |
| fzf | 模糊搜索（Ctrl+R 历史、Ctrl+T 文件） | brew | 0.74.0 |
| zoxide | 智能 cd（记录常用目录，`z <name>` 跳转） | curl 脚本 → ~/.local/bin | latest |

### L2 — 现代化 CLI 替代品

| 工具 | 替代 | 用途 | 安装方式 |
|------|------|------|----------|
| ripgrep (rg) | grep | 递归文本搜索，速度 10x+ | brew |
| fd-find (fd) | find | 更友好的文件查找 | brew |
| bat (batcat) | cat | 语法高亮 cat | brew |
| eza | ls | 彩色 + 图标 + git 感知 ls | brew |
| git-delta | git diff | 更好看的 git diff | brew |

### L3 — 开发运行时

| 工具 | 用途 | 安装方式 | 版本 |
|------|------|----------|------|
| nvm | Node.js 版本管理 | curl 脚本 | 0.40.1 |
| uv | Python 包/项目管理（替代 pip+venv） | curl 脚本 → ~/.local/bin | 0.11.28 |
| neovim | 现代 Vim 编辑器 | brew | 0.12.4 |

### L4 — Dotfiles 管理

| 工具 | 用途 | 安装方式 | 仓库 |
|------|------|----------|------|
| chezmoi | dotfiles 版本控制，多机同步 | curl 脚本 → ~/.local/bin | TIMHX/dotfiles.git |

### L5 — 会话管理

| 工具 | 用途 | 安装方式 | 版本 |
|------|------|----------|------|
| sesh | tmux session 管理 + fzf 切换 | GitHub release → ~/.local/bin | 2.27.0 |

## 安装步骤

### Step 1: L2 + zsh + neovim（brew）

```bash
brew install fzf ripgrep fd bat eza git-delta zsh neovim
```

### Step 2: oh-my-zsh

```bash
export PATH="/home/linuxbrew/.linuxbrew/bin:$PATH"
RUNZSH=no CHSH=no sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended
```

### Step 3: starship + zoxide + uv + nvm + chezmoi + sesh

```bash
mkdir -p ~/.local/bin

# starship
curl -sS https://starship.rs/install.sh | sh -s -- -y --bin-dir ~/.local/bin

# zoxide
curl -sSfL https://raw.githubusercontent.com/ajeetdsouza/zoxide/main/install.sh | sh

# nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash

# uv
curl -LsSf https://astral.sh/uv/install.sh | sh

# chezmoi
curl -fsSL get.chezmoi.io | sh -s -- -b ~/.local/bin

# sesh
curl -sSL https://github.com/joshmedeski/sesh/releases/download/v2.27.0/sesh_Linux_x86_64.tar.gz | tar xz -C ~/.local/bin/ sesh
```

### Step 4: chezmoi 初始化

```bash
export PATH="$HOME/.local/bin:$PATH"
chezmoi init --apply TIMHX/dotfiles.git
```

### Step 5: sudo 步骤（需手动执行）

```bash
bash ~/ubuntu-sudo-setup.sh
```

脚本内容：
```bash
# 添加 zsh 到系统 shell 列表
echo "/home/linuxbrew/.linuxbrew/bin/zsh" | sudo tee -a /etc/shells

# 设置 zsh 为默认 shell
sudo chsh -s /home/linuxbrew/.linuxbrew/bin/zsh xing

# 配置免密 sudo
echo "xing ALL=(ALL) NOPASSWD: ALL" | sudo tee /etc/sudoers.d/xing
sudo chmod 0440 /etc/sudoers.d/xing
```

### Step 6: SSH key（GitHub push）

```bash
ssh-keygen -t ed25519 -C "xing@tim-pc-ubuntu" -f ~/.ssh/id_ed25519 -N ""
# 将 ~/.ssh/id_ed25519.pub 添加到 GitHub → Settings → SSH Keys
```

### Step 7: GNOME Terminal 字体配置（Nerd Font）

**为什么需要**: starship 提示符使用 Nerd Font 图标，如果终端不用 Nerd Font 会显示希腊字母乱码（Ω、Φ）。

```bash
# 安装 Meslo Nerd Font（已在 ~/.local/share/fonts/ 则跳过）
# 如果没有，从 https://github.com/ryanoasis/nerd-fonts/releases 下载 Meslo.tar.xz
# 解压到 ~/.local/share/fonts/ 并 fc-cache -fv

# 获取默认 profile UUID
PROFILE=$(gsettings get org.gnome.Terminal.ProfilesList default | tr -d "'")

# 设置字体（关键：两个开关都要配）
gsettings set "org.gnome.Terminal.Legacy.Profile:/org/gnome/terminal/legacy/profiles:/:$PROFILE/" font 'MesloLGS Nerd Font Mono 12'
gsettings set "org.gnome.Terminal.Legacy.Profile:/org/gnome/terminal/legacy/profiles:/:$PROFILE/" use-system-font false

# 验证
gsettings get "org.gnome.Terminal.Legacy.Profile:/org/gnome/terminal/legacy/profiles:/:$PROFILE/" font
gsettings get "org.gnome.Terminal.Legacy.Profile:/org/gnome/terminal/legacy/profiles:/:$PROFILE/" use-system-font
# 应输出: 'MesloLGS Nerd Font Mono 12' / false
```

> **陷阱**: 仅设 `font` 不够，`use-system-font` 默认为 `true` 会覆盖自定义字体。两个 gsettings 都要执行，然后新开终端窗口生效。

## 验证清单

```bash
# PATH
echo $PATH | tr ':' '\n' | grep -E 'local/bin|linuxbrew'

# 已安装工具
which zsh starship fzf zoxide rg fd bat eza delta nvim chezmoi sesh uv nvm
# 应全部返回路径

# chezmoi 状态
chezmoi status    # 应无 diff

# zsh 测试
zsh -c 'echo "shell: $SHELL"; starship --version; zoxide --version'

# 字体验证
fc-list | grep Meslo   # 应列出 Nerd Font
starship prompt        # 应有彩色图标如 󰕈 ，不是希腊字母
```

## 目录结构

```
~/
├── .local/bin/          # CLI 工具（starship, zoxide, uv, chezmoi, sesh）
├── .oh-my-zsh/          # oh-my-zsh 框架
├── .nvm/                # Node.js 版本管理
├── .local/share/chezmoi/ # chezmoi source（git repo）
├── .zshrc               # zsh 配置（chezmoi 管理，TIMHX/dotfiles）
├── .ssh/id_ed25519      # GitHub SSH key
└── .bashrc              # bash 配置（含 brew PATH）
```

## 关键配置说明

### .zshrc 主要功能
- **PATH**: `~/.local/bin` 优先 → brew → 系统
- **提示符**: Starship（替代 oh-my-zsh 主题）
- **简写别名**: `ls→eza`, `grep→rg`, `cat→bat`, `find→fd`
- **chezmoi 快捷**: `cza <file>` 添加文件, `czp` 提交推送, `czu` 拉取更新
- **tmux 切换**: `t` 命令 → fzf 选择 session → sesh 连接

### 与 VPS 的差异
- VPS 使用 apt 装系统包，Ubuntu 桌面用 brew（免除 sudo）
- VPS 有 doppler/hermes 相关配置，Ubuntu 桌面版已精简
- dotfiles 共用同一 repo（TIMHX/dotfiles），配置文件通过 chezmoi 双向同步

## 下次装新机

1. 装好 Ubuntu + brew + Tailscale
2. 克隆本指南
3. 按 Step 1→7 执行
4. 验证清单确认
5. `chezmoi init --apply TIMHX/dotfiles.git` 拉取最新配置
