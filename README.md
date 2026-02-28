# -Agent-
小红书自动发布Agent部署

# 小红书自动发布Agent部署教程

> 本教程将指导您如何使用 OpenCode + xiaohongshu-mcp 搭建小红书内容自动发布系统，实现本地可控的内容管理流程。

---

## 目录

- [简介](#简介)
- [前置准备](#前置准备)
- [安装步骤](#安装步骤)
- [配置说明](#配置说明)
- [使用方法](#使用方法)
- [故障排查](#故障排查)
- [常见问题](#常见问题)

---

## 简介

### 什么是小红书自动发布Agent？

小红书自动发布Agent是一个基于 OpenCode 平台和 xiaohongshu-mcp 工具构建的自动化内容发布系统。它允许您：

- 本地管理小红书账号
- 自动化内容发布流程
- 集成多种内容格式（图文、视频）
- 与现有的 OpenCode 工作流无缝集成

### 核心组件

- **OpenCode**: 开源的 AI 编程工具，提供本地化、模型自由的工作环境
- **xiaohongshu-mcp**: 小红书相关的 MCP (Model Context Protocol) 工具集
- **AGENT.md**: OpenCode 执行任务前的特性加载配置

---

## 前置准备

### 1. 检查 Node.js 环境

打开终端或命令提示符，运行：

```
node --version
npm --version
```

确保版本满足要求。

### 2. 克隆仓库（如果使用 Git）

```
git clone <repository-url>
cd <repository-directory>
```

### 3. 创建工作目录

```
mkdir xiaohongshu-agent
cd xiaohongshu-agent
```

---

## 安装步骤

### 步骤 1: 安装 OpenCode

**方式一：使用 npm 全局安装（推荐）**

```powershell中输入
npm install -g @opencode/cli
```

**方式二：从 GitHub 下载安装包**

1. 访问 [OpenCode GitHub Releases](https://github.com/opencode/opencode/releases)
2. 下载对应平台的安装包
3. 运行安装程序并按照提示完成安装

**验证安装**

```bash
opencode --version
```

### 步骤 2: 安装 xiaohongshu-mcp

参考项目地址:https://github.com/xpzouying/xiaohongshu-mcp/tree/main下载软件包

### 步骤 3: 初始化 OpenCode 工作空间

```bash
opencode init
```

按照提示配置您的偏好设置。

### 步骤 4: 配置 Agent.md

在项目根目录创建 `AGENT.md` 文件，定义 Agent 的特性和行为：

```markdown
# OpenCode Agent 配置

## 工作流定义

### 内容创作流程
1. 根据主题生成创意内容
2. 整理成小红书格式（图文/视频）
3. 自动发布到小红书平台

## 能力清单

- 文本创作
- 图片处理
- 视频编辑
- 自动发布
- 数据统计

## 约束条件

- 本地执行，不上传敏感数据
- 自动发布前需人工审核
- 遵守小红书社区规范
```

---

## 配置说明

### 1. OpenCode 配置

编辑 OpenCode 配置文件（通常在 `~/.opencode/config.json` 或项目级 `.opencode.json`）：

配置opencode要连接的本地mcp服务器

模板如下:
```
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "xiaohongshu": {
      "type": "remote",
      "url": "http://localhost:18060/mcp",
      "enabled": true
    }
  },
  "plugin": [
    "open-orchestra@0.2.4"
  ]
}
```

### 2. 连接xiaohongshu-mcp 配置
```
1、xiaohongshu-mcp软件包下载后会有 

.\xiaohongshu-login-windows-amd64.exe与.\xiaohongshu-mcp-windows-amd64.exe两个文件

2、先打开第一个登录你的小红书账号,然后再打开第二个开启mcp服务器。

3、接着再命令行输入npx @modelcontextprotocol/inspector,会自动打开浏览器

4、将TransportType设为HTTP,URL设置为http://localhost:18060/mcp，Connection Type使用Via Proxy模式，点击connect,显示连接成功即可

5、再右侧面板中点击List Tools使用check_login_status工具检查小红书登录状态,若状态正确则可以回到opencode了
```
### 3. opencode自动发贴
```
1、首先要给Agent设定人设,opencode执行命令默认加载根目录下的AGENT.md文件,因此我们需要在AGENT.md文件中告知opencode的Agent,你是谁,要做什么。

2、还要编写Agent人物设定.md与执行流程规范.md，你可以将这两样文件放置在同一个文件夹并在AGENT.md中写入信息,让opencode工作时必须阅读该文件夹下的所有手册

3、配置图片模型,在执行流程规范中告知Agent若要发帖使用图片,应该使用哪个工具,我是写了一个python脚本供调用的。

4、一切都配置好之后在opencode中说:发一篇xxx的帖子,Agent便自动执行
```

### 例如以下是我的目录树

## 📁 项目目录结构

```
D:\Xhs/
├── 📄 主要脚本
│   ├── auto_reply.py           # 自动回复监控脚本（主程序）
│   ├── comment_monitor.py      # 评论监控增强版（可选）
│   └── test_monitor.py         # 测试脚本
│
├── 🖼️ 图像生成模块
│   └── image_generator/
│       ├── create_note.py      # 笔记创建工具
│       ├── generate_image.py   # 通用图像生成器
│       ├── .env.example        # 环境配置模板
│       └── README.md           # 图像工具说明
│
├── 🚀 启动脚本
│   ├── start_monitor.bat       # Windows 启动脚本（前台）
│   ├── start_monitor.ps1       # PowerShell 启动脚本
│   └── start_monitor_background.bat  # Windows 后台启动脚本
│
├── 📊 数据存储
│   └── save/
│       ├── monitored_posts.json    # 监控帖子列表
│       ├── processed_comments.json # 已处理评论记录
│       ├── reply_log.txt            # 回复日志
│       ├── comment_monitor.log     # 监控日志
│       ├── image/                   # 生成的图片（按日期分类）
│       └── text/                    # 保存的文章（按日期分类）
│
├── 📖 文档
│   ├── AGENTS.md                 # 主项目文档
│   ├── COMMENT_MONITOR_README.md # 评论监控详细文档
│   └── Agent_setting/            # 人物设定与规范
│       ├── 人物设定.md
│       └── 执行流程规范.md
│
├── 🔧 配置文件
│   ├── opencode.json             # OpenCode 配置
│   ├── skills-lock.json          # 技能锁定配置
│   └── cookies.txt               # Cookie 文件（未启用）
│
├── 🎯 工具
│   ├── templates/index.html      # 评论监控前端页面
│   ├── 素材/                     # 专用素材库
│   └── xiaohongshu-mcp-windows-amd64/  # 小红书 MCP 工具
│       └── xiaohongshu-login-windows-amd64.exe  # 登录程序
│
└── 📂 缓存目录
    ├── __pycache__/              # Python 缓存
    ├── .agents/                  # OpenCode agents
    ├── .claude/                  # Claude 配置
    ├── .opencode/                # OpenCode 配置
    ├── .sisyphus/                # Sisyphus 配置
    └── Agent_setting/            # Agent 设定

---

## 使用方法

### 基本使用流程

#### 1. 启动 OpenCode

Agent 将自动加载 `AGENT.md` 中的配置，并连接到小红书 MCP 服务。

#### 2. 创建内容

在 OpenCode 中输入指令：

```
创建一篇关于旅游攻略的小红书文章
```

Agent 将自动：
- 生成文章标题和内容
- 添加适当的标签
- 准备发布内容


**祝您使用愉快！**
