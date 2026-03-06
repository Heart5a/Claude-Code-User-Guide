# Claude Code 配置与使用教程

> 从零开始：在 Windows 上通过 WSL 安装并使用 Claude Code 的完整教程。

## 📋 目录

- [1. 安装 WSL 与 Node.js](#1-安装-wsl-与-nodejs)
  - [1.1 安装 WSL](#11-安装-wsl)
  - [1.2 安装 Node.js](#12-安装-nodejs)
- [2. 安装 Claude Code](#2-安装-claude-code)
- [3. 适配 WSL 网络环境（代理配置）](#3-适配-wsl-网络环境代理配置)
- [4. Claude Code 使用教程](#4-claude-code-使用教程)

---

## 1. 安装 WSL 与 Node.js

### 1.1 安装 WSL

**（1）在 CMD 中安装 WSL：**
```bash
wsl --install
```

**（2）重启电脑。**

**（3）设置默认发行版：**
```bash
wsl --set-default Ubuntu
```

**（4）在 WSL 中安装基础依赖：**
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install git ripgrep -y
```

✅ WSL 安装完成！你的电脑现在可以运行 Linux 环境了。

---

### 1.2 安装 Node.js

#### 安装 nvm（Node 版本管理器）
```bash
# 回到主目录
cd ~

# 创建 nvm 目录并克隆 Gitee 镜像
mkdir -p ~/.nvm
git clone https://gitee.com/mirrors/nvm.git ~/.nvm

# 切换到稳定版本
cd ~/.nvm && git checkout v0.39.7
```

#### 配置环境变量
```bash
nano ~/.bashrc
```

在文件末尾添加：
```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"
```

保存退出（`Ctrl+O` → 回车 → `Ctrl+X`），然后使配置生效：
```bash
source ~/.bashrc
```

验证安装：
```bash
nvm --version
# 应输出 0.39.7
```

#### 配置 Node 国内镜像
```bash
echo 'export NVM_NODEJS_ORG_MIRROR=https://npmmirror.com/mirrors/node' >> ~/.bashrc
source ~/.bashrc
```

#### 安装 Node.js
```bash
nvm install 18
```

✅ Node.js 安装完成！

---

## 2. 安装 Claude Code
```bash
npm install -g @anthropic-ai/claude-code
```

> ⚠️ 如果直接输入 `claude` 报错，别慌！这通常是网络问题，请继续看第 3 节的代理配置。

---

## 3. 适配 WSL 网络环境（代理配置）

由于 Claude Code 需要访问 Anthropic 的 API，在国内环境下需要配置代理。

### 3.1 准备工作

1. **打开代理工具**，选择美国节点
2. **记下代理端口**（例如 `10809`）
3. **开启"允许来自局域网的连接"**（在代理软件的设置中）

### 3.2 获取 Windows 主机 IP

在 CMD 中执行：
```cmd
ipconfig
```

找到 **"以太网适配器 vEthernet (WSL)"** 下的 IPv4 地址，例如 `172.27.224.1`。

### 3.3 配置 WSL 代理
```bash
nano ~/.bashrc
```

在文件末尾添加（**请替换为你自己的 IP 和端口**）：
```bash
export windows_ip=172.27.224.1       # ← 替换为你的主机 IP
export http_proxy=http://$windows_ip:10809   # ← 替换为你的代理端口
export https_proxy=http://$windows_ip:10809
export no_proxy=localhost,127.0.0.1,10.0.0.0/8,192.168.0.0/16,172.16.0.0/12
```

保存退出并生效：
```bash
source ~/.bashrc
```

### 3.4 验证配置
```bash
# 检查代理变量
echo $http_proxy
echo $https_proxy
# 应输出类似 http://172.27.224.1:10809

# 测试与 Anthropic API 的连通性
curl https://api.anthropic.com
```

如果返回了内容（一只小爬虫 🐛），说明配置成功！

---

## 4. Claude Code 使用教程

### 4.1 启动 Claude Code
```bash
claude
```

### 4.2 选择外观模式

首次启动会让你选择界面主题，按个人喜好选择即可。

### 4.3 登录 Claude 账户

- 按提示在浏览器中打开登录链接（推荐使用 Google Chrome）
- 使用绑定的 Google 账号登录
- 确认授权后回到终端

### 4.4 切换模型

如果你有 Claude Pro 订阅，可以切换到更强大的模型（如 Opus）以获得最佳体验。

---

## 🔧 常见问题

| 问题 | 解决方案 |
|------|---------|
| `claude` 命令报错 | 检查代理配置是否正确，确认能 `curl https://api.anthropic.com` |
| nvm 安装失败 | 确认 git 已安装，尝试用 Gitee 镜像地址 |
| WSL 无法联网 | 确认代理软件已开启"允许局域网连接"，IP 地址正确 |

---

## 📝 License

MIT License - 详见 [LICENSE](LICENSE) 文件。
