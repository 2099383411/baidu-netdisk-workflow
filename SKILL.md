---
name: baidu-netdisk-workflow
description: 百度网盘协作技能包 - 上传、下载、搜索、分享、管理文件。AI 读完即可操作。需要百度网盘账号 + 手机 App 扫码登录。触发词：百度网盘、网盘、bdpan、云盘、百度云、网盘上传、网盘下载、网盘分享、文件管理。
---

# 百度网盘协作技能包

## 概述

让 AI 智能体帮你操作百度网盘。上传、下载、搜索、分享、管理文件，全部用命令行完成，不需要打开网页或客户端。

**核心流程：** 安装 bdpan → 扫码登录 → 干活。登录成功后 Token 有效期约 30 天，到期重新扫码。

---

## AI 使用步骤

### 1. 检测运行环境和网络

先检测当前系统平台和网络环境：

```bash
# 检测平台
UNAME=$(uname -s)
ARCH=$(uname -m)
case "$UNAME" in
  Darwin) PLATFORM="macos" ;;
  Linux)  PLATFORM="linux" ;;
  MINGW*|MSYS*|CYGWIN*) PLATFORM="windows" ;;
  *) PLATFORM="unknown" ;;
esac
case "$ARCH" in
  x86_64|amd64) ARCH_TYPE="amd64" ;;
  arm64|aarch64) ARCH_TYPE="arm64" ;;
  *) ARCH_TYPE="unknown" ;;
esac
echo "平台: $PLATFORM 架构: $ARCH_TYPE"

# 检测网络（能否访问 GitHub）
curl -s --connect-timeout 5 https://github.com > /dev/null 2>&1 && echo "github_ok" || echo "github_blocked"
```

### 2. 安装 bdpan

先检查本地是否已有：

```bash
command -v bdpan && bdpan version && exit 0
```

根据平台和网络状况选择安装方式：

#### 方式 A：能访问 GitHub（推荐）

```bash
# 根据平台选择下载 URL
case "$PLATFORM-$ARCH_TYPE" in
  macos-amd64) URL="https://github.com/wxnacy/bdpan-cli/releases/download/v0.0.7/bdpan_0.0.7_darwin_amd64.tar.gz" ;;
  macos-arm64) URL="https://github.com/wxnacy/bdpan-cli/releases/download/v0.0.7/bdpan_0.0.7_darwin_arm64.tar.gz" ;;
  linux-amd64) URL="https://github.com/wxnacy/bdpan-cli/releases/download/v0.0.7/bdpan_0.0.7_linux_amd64.tar.gz" ;;
  linux-arm64) URL="https://github.com/wxnacy/bdpan-cli/releases/download/v0.0.7/bdpan_0.0.7_linux_arm64.tar.gz" ;;
  windows-amd64) URL="https://github.com/wxnacy/bdpan-cli/releases/download/v0.0.4/bdpan_0.0.4_windows_amd64.tar.gz" ;;
  windows-arm64) URL="https://github.com/wxnacy/bdpan-cli/releases/download/v0.0.4/bdpan_0.0.4_windows_arm64.tar.gz" ;;
esac

curl -L -o /tmp/bdpan.tar.gz "$URL"
cd /tmp && tar xzf bdpan.tar.gz && chmod +x bdpan

# 移到 PATH
# macOS/Linux
sudo mv bdpan /usr/local/bin/
# Windows
# move bdpan.exe C:\Windows\System32\
```

#### 方式 B：GitHub 不通 → 用技能包自带的离线包

技能包 `offline/` 目录下已预置各平台二进制，直接取用：

```bash
# 技能包路径（用户告诉你的那个文件夹路径）
SKILL_DIR="<用户告诉你的技能包路径>"

case "$PLATFORM-$ARCH_TYPE" in
  macos-amd64) BIN="$SKILL_DIR/offline/mac_amd64/bdpan" ;;
  macos-arm64) BIN="$SKILL_DIR/offline/mac_arm64/bdpan" ;;
  linux-amd64) BIN="$SKILL_DIR/offline/linux_amd64/bdpan" ;;
  linux-arm64) BIN="$SKILL_DIR/offline/linux_arm64/bdpan" ;;
  windows-amd64) BIN="$SKILL_DIR/offline/win_amd64/bdpan.exe" ;;
  windows-arm64) BIN="$SKILL_DIR/offline/win_arm64/bdpan.exe" ;;
esac

if [ -f "$BIN" ]; then
  cp "$BIN" /usr/local/bin/bdpan
  chmod +x /usr/local/bin/bdpan
  echo "离线安装完成"
else
  echo "错误：当前平台 ($PLATFORM-$ARCH_TYPE) 暂不支持"
fi
```

#### 方式 C：国内环境，先用 GitHub 镜像代理试试

```bash
GH_MIRROR="https://mirror.ghproxy.com/"
URL="$GH_MIRROR/https://github.com/wxnacy/bdpan-cli/releases/download/v0.0.7/bdpan_0.0.7_darwin_arm64.tar.gz"
curl -L -o /tmp/bdpan.tar.gz "$URL"

# 如果镜像也失败 → fallback 到离线包（方式 B）
```

### 3. 验证安装

```bash
bdpan version
bdpan --help
```

### 4. 登录（需要百度网盘 App）

> **前置条件：** 用户需要有百度网盘账号 + 手机上安装百度网盘 App

```bash
bdpan login --qrcode --accept-disclaimer
```

AI 执行后会显示一个二维码，此时需要**让用户用手机百度网盘 App 扫码**。

扫码成功后会显示「✅ 已登录」。Token 有效期约 30 天，到期后重复此步骤。

**如果二维码模式不可用（如 SSH 无 GUI 环境），备用方案：**

```bash
bdpan login --device-code --accept-disclaimer
```

会显示一个设备码和链接，用户需要在浏览器打开链接并输入设备码完成授权。

### 5. 开始使用

详细命令见下方。

---

## 核心命令

### 查看登录状态

```bash
bdpan whoami
```

### 查看文件列表

```bash
bdpan ls                         # 根目录
bdpan ls /apps/bdpan/书籍/       # 指定目录
bdpan ls --json                  # JSON 格式输出
bdpan ls --order time --desc     # 按时间降序
```

bdpan 操作的是百度网盘「我的应用数据/bdpan/」目录下的文件。

### 上传文件

```bash
bdpan upload 本地文件.pdf /apps/bdpan/文档/目标文件名.pdf
bdpan upload ./项目文件夹/ /apps/bdpan/项目文件夹/
```

> 单文件上传时，远端路径不能以 `/` 结尾，必须包含文件名。

### 下载文件

```bash
bdpan download /apps/bdpan/文档/文件.pdf ./下载/
```

大文件下载策略（> 50MB 用后台下载）：

```bash
# 获取文件大小
bdpan ls --json /apps/bdpan/文件.pdf

# 大文件后台下载
nohup bdpan download /apps/bdpan/文件.pdf ./下载/ > /tmp/bdpan-dl.log 2>&1 &
```

### 搜索文件

```bash
bdpan search 关键词
bdpan search 关键词 --category 4     # 筛选文件类型（4=文档）
bdpan search 关键词 --page-size 10   # 分页
```

### 创建分享链接

```bash
bdpan share /apps/bdpan/文档/文件.pdf --period 7    # 7天有效
bdpan share /apps/bdpan/文档/文件.pdf --period 0    # 永久有效
```

> 注意：分享功能是百度网盘付费接口，需在百度网盘开放平台购买服务后可用。

### 管理操作

```bash
bdpan mkdir /apps/bdpan/新文件夹/         # 创建文件夹
bdpan rename /apps/bdpan/文件.pdf 新名称.pdf  # 重命名
bdpan mv /apps/bdpan/源路径/ /apps/bdpan/目标/  # 移动
bdpan cp /apps/bdpan/源.pdf /apps/bdpan/目标/   # 复制
bdpan rm /apps/bdpan/文件.pdf            # 删除（需确认）
```

### 从分享链接下载

```bash
bdpan download "https://pan.baidu.com/s/1xxxxx?pwd=提取码" ./下载目录/
bdpan download "https://pan.baidu.com/s/1xxxxx" ./下载目录/ -p 提取码
```

### 转存（仅存到网盘，不下载到本地）

```bash
bdpan transfer "https://pan.baidu.com/s/1xxxxx" -p 提取码 -d /apps/bdpan/目标目录/
```

---

## 登录说明（用户须知）

1. 需要有百度网盘账号（免费注册）
2. 手机上安装百度网盘 App
3. AI 会引导你扫码，打开 App 扫一下即可
4. 登录成功后 30 天内无需重新登录
5. 到期后 AI 会提示重新扫码

---

## 使用示例（AI 与用户对话）

```
用户：帮我把桌面的 report.pdf 上传到网盘
AI：好的，先检查登录状态... 已登录。
    正在上传 report.pdf 到 /apps/bdpan/文档/report.pdf ...
    上传完成！

用户：帮我在网盘搜一下关于 "合同" 的文件
AI：正在搜索... 找到以下文件：
  1. 2026年合同.pdf（2026-06-20）
  2. 采购合同模板.docx（2026-05-15）

用户：帮我生成分享链接，7天有效
AI：正在创建分享链接...
    分享链接：https://pan.baidu.com/s/1xxxxx
    提取码：abcd
    有效期：7天
```

## 文件结构

```
baidu-netdisk-workflow/
├── SKILL.md              ← AI 学习用（本文件）
├── README.md             ← 完整文档（中英双语）
├── LICENSE               ← MIT License
├── 用户手册.md/docx       ← 买家看
├── offline/              ← 离线安装包（GitHub 不通时使用）
│   ├── mac_amd64/bdpan         (macOS Intel, ~32MB)
│   ├── mac_arm64/bdpan         (macOS M芯片, ~30MB)
│   ├── linux_amd64/bdpan       (Linux x64, ~31MB)
│   ├── linux_arm64/bdpan       (Linux ARM, ~29MB)
│   ├── win_amd64/bdpan.exe     (Windows x64, ~38MB, v0.0.4)
│   └── win_arm64/bdpan.exe     (Windows ARM, ~35MB, v0.0.4)
└── scripts/
    └── （空，AI 直接调用 bdpan 命令）
```

## 底层工具

本技能基于 [bdpan-cli](https://github.com/wxnacy/bdpan-cli)（MIT License），作者 wxnacy。

- 原项目：https://github.com/wxnacy/bdpan-cli
- 最新版下载：https://github.com/wxnacy/bdpan-cli/releases

本技能包包含离线版，但不一定是最新版。

## GitHub 开源

开源地址：https://github.com/2099383411/baidu-netdisk-workflow
