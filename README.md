
# Aeolira's Scoop Buckets 🚀

这是一个个人专属的 **Scoop 聚合软件源**。

本仓库通过 GitHub Actions 实现了全自动的双轨制运行：
1. **外部仓库同步（`bucket/`）**：每日定时自动搬运全网各大主流/第三方源（Extras, Versions, Dorado 等）
2. **专属自建应用（`apps/`）**：提供完全解耦的个人自建绿色软件存放区，只需将手写或 AI 生成的 JSON 放入 `apps/`，后台机器人会自动将其合并至生产线并挂机维护。

---

## 🛠️ 第一部分：Scoop 的极致纯净安装指南

默认情况下，Scoop 会将软件安装在 `C:\Users\用户名\scoop`。为了防止 C 盘爆炸，推荐将 Scoop 核心和软件库**全局安装在特定的自定义文件夹**（例如 `D:\Software\Scoop`）。

### 1. 自定义路径环境准备
以管理员身份打开 **PowerShell**，运行以下命令来规划你的专属安装路径：

```powershell
# 1. 设置用户软件的安装路径（根据你的盘符喜好修改）
[environment]::SetEnvironmentVariable('SCOOP', 'D:\Software\Scoop', 'User')
$env:SCOOP='D:\Software\Scoop'

# 2. 设置全局/需要管理员权限软件的安装路径
[environment]::SetEnvironmentVariable('SCOOP_GLOBAL', 'D:\Software\Scoop\Global', 'User')
$env:SCOOP_GLOBAL='D:\Software\Scoop\Global'

```

### 2. 一键下载与安装 Scoop

确保网络畅通（推荐开启代理加速），在终端中执行：

```powershell
# 允许本地脚本运行
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser

# 使用官方/国内镜像一键安装核心
irm [https://get.scoop.sh](https://get.scoop.sh) | iex

```

*验证安装：输入 `scoop -v`，若看到版本号，说明成功扎根在你的自定义目录！*

---

## 🔗 第二部分：绑定仓库


```powershell
# 1. 彻底移除本地可能残留的旧同名源
scoop bucket rm aeolira 2>$null
Remove-Item -Recurse -Force "$env:USERPROFILE\scoop\buckets\aeolira" -ErrorAction SilentlyContinue

# 2. 正式接入你专属于云端的真实硬核仓库
scoop bucket add aeolira [https://github.com/aeolira/aeolira-buckets.git](https://github.com/aeolira/aeolira-buckets.git)

# 3. 强行刷新索引
scoop update
scoop bucket list

```

---

## 🧪 第三部分：自建软件专属加工厂（核心工作流）


### 📐 1. 自建 App 应该放在哪里？

* **绝对路径**：根目录下的 **`apps/`** 文件夹（若本地没有，自行新建即可）。
* **例子**：`apps/voov-meeting.json`

### 📋 2. 完美的下载链接（URL）需要什么条件？

当你遇到想要收录的软件时，去官网或 GitHub Release 观察其下载链接，需满足以下条件：

1. **后缀格式**：必须是 **`.zip` / `.7z` / `.tar.gz`（解压即用）** 或标准的 **`.msi`（自带静默安装）**。普通的图形化安装包（如 `setup.exe`）容易导致后台锁死。
2. **生命周期**：链接中必须**带有具体的版本号数字**（如 `v1.8.19`），不能是 `latest` 这种模糊字眼，否则云端机器人无法帮你自动算 Hash 并挂机升级。

### 📝 3. 编写专属 JSON 的两种标准流派

#### 流派 A：标准的绿色解压版（以独立压缩包为例）

```json
{
    "version": "1.0.0",
    "description": "这是你的自建软件描述",
    "homepage": "[https://github.com/example/repo](https://github.com/example/repo)",
    "license": "MIT",
    "architecture": {
        "64bit": {
            "url": "[https://github.com/example/repo/releases/download/v1.0.0/tool-x64.zip](https://github.com/example/repo/releases/download/v1.0.0/tool-x64.zip)",
            "hash": "计算出的SHA-256数字指纹"
        }
    },
    "bin": "tool.exe",
    "shortcuts": [
        [ "tool.exe", "开始菜单快捷方式名称" ]
    ]
}

```

#### 流派 B：大厂静默安装包（以腾讯会议国际版为例，后缀为 .exe 但底层支持静默解压）

```json
{
    "version": "3.32.21.510",
    "description": "VooV Meeting is the international version of Tencent Meeting.",
    "homepage": "[https://voovmeeting.com/](https://voovmeeting.com/)",
    "license": "Proprietary",
    "architecture": {
        "64bit": {
            "url": "[https://updatecdn.meeting.qq.com/cos/.../VooVMeeting_3.32.21.510_x86_64.publish.exe](https://updatecdn.meeting.qq.com/cos/.../VooVMeeting_3.32.21.510_x86_64.publish.exe)",
            "hash": "134ffa8eeb2301e74833be7feffa2058"
        }
    },
    "installer": { "args": [ "/S", "/D=$dir" ] },
    "uninstaller": { "args": [ "/S" ] },
    "bin": "VooVMeeting.exe",
    "shortcuts": [ [ "VooVMeeting.exe", "VooV Meeting" ] ]
}

```

### 🚀 4. 一键交棒给机器人

当你在本地的 `apps/` 目录下丢进写好的 JSON 后，只需要用 Git 推送上云，即可触发解耦更新流：

```powershell
git add apps/
git commit -m "feat: add my new app"
git push origin main

```
---
made by gemini （the ai）
