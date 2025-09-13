# Khoj操作员 (实验性)

## 概述
为Khoj提供自己的计算机以透明、可控的方式操作。完成需要视觉浏览、文件编辑和终端访问的任务。带有研究模式的操作员可以工作30多分钟来完成更重要的任务，如功能开发、旅行规划、购物等。

## 设置

### 先决条件
- 已安装Docker和Docker Compose
- Anthropic API密钥（必需 - 目前仅启用Anthropic模型）

### 安装步骤
1. 下载Khoj docker-compose.yml文件
    ```shell
    mkdir ~/.khoj && cd ~/.khoj
    wget https://raw.githubusercontent.com/khoj-ai/khoj/master/docker-compose.yml
    ```

2. 更新`docker-compose.yml`以启用计算机操作员
    - 将`ANTHROPIC_API_KEY`设置为您的[Anthropic API密钥](https://console.anthropic.com/settings/keys)
    - 取消注释`KHOJ_OPERATOR_ENABLED=True`以启用操作员工具
    - 取消注释`- /var/run/docker.sock:/var/run/docker.sock`以挂载docker套接字，允许khoj操作其计算机容器。

3. 启动Khoj服务
    ```shell
    docker-compose up
    ```

4. 在http://localhost:42110访问Web应用
    确保在您的[设置页面](http://localhost:42110/settings)使用claude 3.7+模型

## 使用方法
使用`/operator`命令或在正常或研究模式下要求Khoj使用操作员工具来操作其计算机：

**示例：**
- `/operator Find flights from Bangkok to Mexico City with no US layover`
- `/research Clone the khoj repo and tell me how the operator tool is implemented`

## 支持的模型

目前仅启用**Anthropic模型**：
- Claude Sonnet 4
- Claude 3.7 Sonnet
- Claude Opus 4

*注意：OpenAI和其他操作员模型在开发中被禁用。*

## 功能

操作员可以：
- **计算机控制**：截图、点击、输入、导航桌面
- **文件操作**：创建、编辑和管理文件
- **终端访问**：执行bash命令和脚本
- **网页浏览**：导航网站、文档并提取信息

## 架构

- **环境**：操作员计算机和浏览器环境
- **模型**：启用视觉语言模型(VLM)来操作计算机
- **执行**：容器化计算机环境以实现安全和隔离
