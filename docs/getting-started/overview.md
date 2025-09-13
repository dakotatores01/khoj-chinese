# 快速开始指南

欢迎使用 Khoj！本指南将帮助您快速开始使用这个强大的 AI 助手。

## 目录

- [在线试用](#在线试用)
- [安装要求](#安装要求)
- [安装步骤](#安装步骤)
- [第一次运行](#第一次运行)
- [基本使用](#基本使用)

## 在线试用

最简单的开始方式是在我们的云服务上试用 Khoj：

1. 访问 [https://app.khoj.dev](https://app.khoj.dev)
2. 创建账户
3. 开始使用！

这种方式无需任何安装，您可以立即体验 Khoj 的所有功能。

## 安装要求

如果您选择自托管 Khoj，需要满足以下要求：

### 硬件要求

- **CPU**: 现代多核处理器（推荐 4+ 核心）
- **内存**: 至少 8GB RAM（推荐 16GB+ 用于大型模型）
- **存储**: 至少 10GB 可用空间
- **网络**: 稳定互联网连接（用于在线功能）

### 软件要求

- **操作系统**: Linux, macOS, Windows
- **Python**: 3.10 - 3.12
- **Docker**: 可选，用于容器化部署

### 可选依赖

- **PostgreSQL**: 用于生产环境数据库
- **Redis**: 用于缓存（可选）
- **Nginx**: 用于反向代理（生产环境推荐）

## 安装步骤

### 方式一：使用 pip 安装（推荐）

```bash
# 安装 Khoj
pip install khoj

# 或者从源码安装
git clone https://github.com/khoj-ai/khoj.git
cd khoj
pip install -e .
```

### 方式二：使用 Docker

```bash
# 使用预构建镜像
docker run -p 8000:8000 khoj/khoj:latest

# 或者构建自己的镜像
git clone https://github.com/khoj-ai/khoj.git
cd khoj
docker build -t khoj .
docker run -p 8000:8000 khoj
```

### 方式三：开发环境安装

```bash
git clone https://github.com/khoj-ai/khoj.git
cd khoj
pip install -e ".[dev]"
```

## 第一次运行

安装完成后，运行以下命令启动 Khoj：

```bash
khoj
```

或者如果是从源码安装：

```bash
python -m khoj.main
```

首次运行时，Khoj 会：

1. 创建数据库和配置文件
2. 下载必要的模型文件
3. 启动 Web 服务器
4. 在浏览器中打开应用

服务器将在 `http://localhost:8000` 上运行。

## 基本使用

### 1. 添加您的文档

Khoj 的强大之处在于它可以从您的个人文档中学习。要开始使用：

1. 在 Web 界面中点击 "Add Documents"
2. 拖拽或选择要添加的文件
3. 支持的文件类型：
   - Markdown (.md)
   - PDF (.pdf)
   - Word 文档 (.docx)
   - 纯文本 (.txt)
   - Org 模式 (.org)

### 2. 开始聊天

添加文档后，您可以：

- **自然语言搜索**: 输入问题，Khoj 会从您的文档中找到相关信息
- **智能聊天**: 与 Khoj 进行对话，它会记住上下文
- **任务自动化**: 使用 `/automated_task` 创建定时任务

### 3. 示例查询

```
"昨天我记了什么笔记？"
"帮我总结上个月的项目进展"
"创建每日提醒，提醒我喝水"
"解释这个代码片段的作用"
```

## 下一步

现在您已经成功安装并运行了 Khoj！建议您接下来：

1. [探索功能特性](../features/overview.md) - 了解 Khoj 的所有功能
2. [配置数据源](../data-sources/overview.md) - 连接更多数据源
3. [设置客户端](../clients/overview.md) - 在不同设备上使用 Khoj
4. [自定义代理](../features/agents.md) - 创建个性化的 AI 助手

## 获取帮助

如果遇到问题：

- 查看[故障排除指南](../troubleshooting/overview.md)
- 在 [Discord](https://discord.gg/BDgyabRM6e) 社区寻求帮助
- 查看 [GitHub Issues](https://github.com/khoj-ai/khoj/issues)

祝您使用愉快！🎉