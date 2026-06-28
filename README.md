# 百度网盘协作技能包 (Baidu Netdisk Workflow)

> 让 AI 智能体帮你操作百度网盘 — 上传、下载、搜索、分享、管理文件，全部命令行完成，不需要打开网页或客户端。

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

## 📦 这是什么

这是一个 **AI 智能体 Skills** — 你把文件夹路径告诉 AI，AI 读完 README 就能上手操作你的百度网盘。开箱即用。

**典型对话：**
> 用户："帮我把桌面的 report.pdf 上传到网盘"  
> AI：（自动完成）

> 用户："在网盘搜一下关于合同的文件"  
> AI：（搜索并列出结果）

## ✨ 功能

| 功能 | 说明 |
|------|------|
| 📂 文件列表 | 查看网盘目录结构 |
| ⬆️ 上传文件 | 本地文件上传到网盘 |
| ⬇️ 下载文件 | 网盘文件下载到本地 |
| 🔍 搜索文件 | 按关键词搜索网盘内容 |
| 🔗 分享链接 | 生成分享链接（需付费接口） |
| 📁 目录管理 | 创建/重命名/移动/复制/删除 |
| 📎 转存文件 | 从分享链接转存到网盘 |

## 🛠 底层工具

本技能包基于 **[bdpan-cli](https://github.com/wxnacy/bdpan-cli)**（作者：wxnacy），一个开源的百度网盘命令行客户端。

- **原项目地址**：https://github.com/wxnacy/bdpan-cli
- **最新版本下载**：https://github.com/wxnacy/bdpan-cli/releases
- **原项目许可证**：MIT License

> ⚠️ **版本说明**：本技能包内置了 bdpan-cli 的离线安装包（v0.0.7 macOS/Linux，v0.0.4 Windows），覆盖了其核心功能（上传/下载/搜索/分享/管理等），但不一定包含最新版本的所有功能。如需最新版本，建议直接从原项目 Releases 页面下载。
>
> 本技能包的主要价值在于：
> 1. 提供了 AI 智能体可直接读取的 **SKILL.md** 学习文件
> 2. 内置了 **跨平台离线安装包**（GitHub 不通时也能装）
> 3. 封装了三級安装逻辑（GitHub → 镜像代理 → 离线包）
> 4. 提供了 AI 自动化的 **完整对话流程**

## 🚀 快速开始

### 前置条件

- 百度网盘账号（免费注册）
- 手机百度网盘 App
- 当前机器有网络连接

### 1. 安装 bdpan

**方法一：从 GitHub 直接下载（推荐）**
```bash
# macOS ARM
curl -L -o /tmp/bdpan.tar.gz https://github.com/wxnacy/bdpan-cli/releases/download/v0.0.7/bdpan_0.0.7_darwin_arm64.tar.gz
cd /tmp && tar xzf bdpan.tar.gz && sudo mv bdpan /usr/local/bin/

# macOS Intel
curl -L -o /tmp/bdpan.tar.gz https://github.com/wxnacy/bdpan-cli/releases/download/v0.0.7/bdpan_0.0.7_darwin_amd64.tar.gz

# Linux AMD64
curl -L -o /tmp/bdpan.tar.gz https://github.com/wxnacy/bdpan-cli/releases/download/v0.0.7/bdpan_0.0.7_linux_amd64.tar.gz

# Windows AMD64 (v0.0.4)
# 下载 https://github.com/wxnacy/bdpan-cli/releases/download/v0.0.4/bdpan_0.0.4_windows_amd64.tar.gz
```

**方法二：GitHub 不通 → 用镜像代理**
```bash
curl -L -o /tmp/bdpan.tar.gz https://mirror.ghproxy.com/https://github.com/wxnacy/bdpan-cli/releases/download/v0.0.7/bdpan_0.0.7_darwin_arm64.tar.gz
```

**方法三：断网环境 → 用离线包**
```bash
# 从本技能包的 offline/ 目录取对应平台的二进制
cp offline/mac_arm64/bdpan /usr/local/bin/bdpan
chmod +x /usr/local/bin/bdpan
```

### 2. 登录

```bash
bdpan login --qrcode --accept-disclaimer
```

手机百度网盘 App 扫码即可。Token 有效期约 30 天。

### 3. 使用

```bash
bdpan ls                                # 查看文件列表
bdpan upload 文件.pdf /apps/bdpan/      # 上传文件
bdpan download /apps/bdpan/文件.pdf ./  # 下载文件
bdpan search 关键词                      # 搜索文件
bdpan share /apps/bdpan/文件.pdf        # 生成分享链接
```

> 注意：bdpan 操作的是百度网盘「我的应用数据/bdpan/」目录下的文件。

## 📁 文件结构

```
baidu-netdisk-workflow/
├── SKILL.md              ← AI 学习用（核心）
├── README.md             ← 本文件
├── LICENSE               ← MIT License
├── 用户手册.md/docx       ← 买家使用说明
├── offline/              ← 离线安装包（6 平台）
│   ├── mac_amd64/        (macOS Intel, ~33MB)
│   ├── mac_arm64/        (macOS M芯片, ~31MB)
│   ├── linux_amd64/      (Linux x64, ~32MB)
│   ├── linux_arm64/      (Linux ARM, ~30MB)
│   ├── win_amd64/        (Windows x64, ~39MB)
│   └── win_arm64/        (Windows ARM, ~36MB)
└── scripts/              （AI 直接调用 bdpan 命令）
```

## 🤖 For AI Agents

本仓库包含 `SKILL.md` 文件，AI 智能体读取后即可理解完整工作流程：

1. 检测用户平台和网络状况
2. 三级安装策略（GitHub → 镜像代理 → 离线包）
3. 扫码登录引导
4. 核心命令参考
5. 对话示例

**用法：** 用户将仓库路径告诉 AI，AI 读取 `SKILL.md` 后即可操作。

## 📝 License

MIT License. See [LICENSE](LICENSE).

本技能包底层工具 [bdpan-cli](https://github.com/wxnacy/bdpan-cli) 同样采用 MIT License。
