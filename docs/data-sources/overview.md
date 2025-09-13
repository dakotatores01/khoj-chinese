# 数据源集成指南

Khoj 支持多种数据源，可以从您的个人文档、笔记和知识库中学习。本指南介绍如何连接和配置各种数据源。

## 目录

- [支持的数据源](#支持的数据源)
- [本地文件](#本地文件)
- [云端服务](#云端服务)
- [版本控制](#版本控制)
- [配置管理](#配置管理)

## 支持的数据源

Khoj 支持以下数据源类型：

### 📄 文档文件
- **Markdown** (.md) - 笔记和文档
- **PDF** (.pdf) - 扫描文档和报告
- **Word** (.docx) - 办公文档
- **Org 模式** (.org) - Emacs 笔记
- **纯文本** (.txt) - 简单文本文件

### 🖼️ 媒体文件
- **图像文件** - 支持 OCR 文字识别
- **音频文件** - 语音转文本（实验性）

### 🌐 云端服务
- **Notion** - 笔记和知识库
- **GitHub** - 代码仓库和文档
- **Google Drive** - 云端文档（计划中）
- **OneDrive** - Microsoft 云服务（计划中）

## 本地文件

### 添加本地文件

有多种方式添加本地文件到 Khoj：

#### 方法一：Web 界面上传

1. 打开 Khoj Web 界面
2. 点击 "Add Documents" 按钮
3. 拖拽文件到上传区域，或点击选择文件
4. 等待处理完成

#### 方法二：API 上传

```bash
curl -X POST "http://localhost:8000/api/content" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -F "files=@your-file.pdf"
```

#### 方法三：目录同步

```bash
# 将文件放入 Khoj 的数据目录
cp your-notes.md ~/khoj/data/
```

### 文件处理

Khoj 会自动：

1. **提取文本内容** - 从各种文件格式中提取文本
2. **生成嵌入向量** - 为语义搜索创建向量表示
3. **建立索引** - 创建高效的搜索索引
4. **增量更新** - 只处理变更的文件

### 支持的文件格式详情

#### Markdown 文件
- 支持标准 Markdown 语法
- 代码块和高亮
- 表格和列表
- 链接和图片引用

#### PDF 文档
- 文本提取（原生 PDF）
- OCR 处理（扫描 PDF）
- 多页文档支持
- 表格和图像识别

#### Word 文档
- 完整格式保留
- 表格和图片提取
- 样式和格式信息

#### Org 模式文件
- Emacs Org 模式语法
- 标题层次结构
- TODO 状态和标签
- 属性抽屉

## 云端服务

### Notion 集成

连接您的 Notion 工作区：

#### 配置步骤

1. **创建 Notion 集成**
   - 访问 [Notion Developers](https://developers.notion.com/)
   - 创建新的集成应用
   - 复制集成令牌

2. **授权访问**
   ```bash
   # 在 Khoj 设置中
   notion_token = "your_integration_token"
   ```

3. **选择页面**
   - 分享页面给您的集成
   - Khoj 会自动发现可访问的页面

#### 支持的内容类型
- 数据库和页面
- 文本块和富文本
- 表格和列表
- 嵌入内容

### GitHub 集成

连接 GitHub 仓库：

#### 配置步骤

1. **生成个人访问令牌**
   ```bash
   # GitHub Settings > Developer settings > Personal access tokens
   # 需要 repo 和 read:org 权限
   ```

2. **配置仓库**
   ```yaml
   github:
     pat_token: "your_github_token"
     repos:
       - name: "my-repo"
         owner: "myusername"
         branch: "main"
   ```

3. **文件类型支持**
   - Markdown 文档
   - README 文件
   - Wiki 页面
   - Issue 和 PR 描述

#### 高级配置

```yaml
# 支持多个仓库
repos:
  - name: "docs"
    owner: "myorg"
    branch: "main"
    # 可选：只索引特定路径
    filter_paths: ["docs/", "README.md"]
  - name: "knowledge-base"
    owner: "myorg"
    branch: "main"
```

### Google Drive 集成（计划中）

未来版本将支持：
- Google Docs 文档
- Google Sheets 表格
- Google Slides 演示文稿
- 自动同步更新

## 版本控制

### Git 仓库集成

Khoj 可以与 Git 仓库集成：

#### 自动同步
```yaml
# 定期检查更新
sync_interval: "1h"  # 每小时检查一次

# 忽略文件模式
ignore_patterns:
  - "*.tmp"
  - "node_modules/"
  - ".git/"
```

#### 分支和标签
- 支持多分支索引
- 标签特定的文档版本
- 分支间差异比较

### 变更检测

Khoj 自动检测文件变更：

- **文件修改时间** - 检测本地文件变更
- **Git 提交** - 跟踪仓库变更
- **API 通知** - 外部系统变更通知
- **定期扫描** - 定时检查所有数据源

## 配置管理

### 配置文件

Khoj 使用 YAML 配置文件管理数据源：

```yaml
# khoj.yml - 主配置文件
version: "1.0"

# 数据源配置
content-sources:
  local:
    - path: "~/Documents"
      include: ["*.md", "*.pdf", "*.org"]
      exclude: ["node_modules/"]

  notion:
    token: "${NOTION_TOKEN}"
    pages:
      - "Personal Notes"
      - "Work Projects"

  github:
    token: "${GITHUB_TOKEN}"
    repositories:
      - owner: "myorg"
        name: "docs"
        branch: "main"
```

### 环境变量

敏感信息使用环境变量：

```bash
# .env 文件
NOTION_TOKEN=your_notion_integration_token
GITHUB_TOKEN=your_github_personal_token
GOOGLE_API_KEY=your_google_api_key
```

### 命令行配置

运行时配置选项：

```bash
# 指定配置文件
khoj --config /path/to/khoj.yml

# 覆盖特定设置
khoj --notion-token "your_token"

# 调试模式
khoj --verbose
```

## 数据源最佳实践

### 组织结构

#### 文件命名约定
```
2024-01-15-项目会议.md
工作笔记/2024/Q1/计划.md
个人/日记/2024-01-15.md
```

#### 目录结构
```
~/Documents/
├── Work/
│   ├── Projects/
│   ├── Meetings/
│   └── Research/
├── Personal/
│   ├── Journal/
│   ├── Books/
│   └── Ideas/
└── Learning/
    ├── Courses/
    ├── Notes/
    └── Projects/
```

### 内容质量

#### Markdown 最佳实践
```markdown
# 会议纪要 - 2024-01-15

## 参会人员
- 张三
- 李四
- 王五

## 讨论内容
1. 项目进度
2. 技术方案
3. 时间安排

## 行动项
- [ ] 张三：完成需求文档
- [ ] 李四：技术调研
- [ ] 王五：原型设计
```

#### 标签和元数据
```markdown
---
title: "项目规划会议"
date: "2024-01-15"
tags: ["项目管理", "会议纪要", "Q1"]
participants: ["张三", "李四", "王五"]
---

会议内容...
```

### 性能优化

#### 大型文档库
- 使用 `.khojignore` 文件排除不需要的文件
- 分批处理大型目录
- 使用增量同步

#### 文件大小限制
- 单文件最大 50MB
- 建议文件大小 < 10MB 以获得最佳性能
- 对于大型文档，考虑拆分为多个小文件

### 安全考虑

#### 访问控制
- 定期轮换 API 令牌
- 使用最小权限原则
- 监控访问日志

#### 数据隐私
- 本地部署确保数据不离开您的网络
- 云部署使用端到端加密
- 定期备份重要数据

## 故障排除

### 常见问题

#### 文件无法索引
**问题**: 文件显示为"处理中"状态
**解决方案**:
1. 检查文件格式是否支持
2. 确认文件未损坏
3. 查看 Khoj 日志
4. 重启 Khoj 服务

#### Notion 连接失败
**问题**: 无法连接到 Notion
**解决方案**:
1. 验证集成令牌
2. 确认页面已分享给集成
3. 检查网络连接
4. 查看 Notion API 状态

#### GitHub 同步失败
**问题**: 仓库内容未更新
**解决方案**:
1. 验证个人访问令牌权限
2. 确认仓库存在且可访问
3. 检查分支名称
4. 查看 GitHub API 速率限制

### 监控和日志

#### 查看索引状态
```bash
# 检查索引进度
curl http://localhost:8000/api/content/size

# 查看最近的索引操作
tail -f ~/khoj/logs/khoj.log
```

#### 性能监控
- 使用 `/api/health` 检查服务状态
- 监控内存和 CPU 使用率
- 检查索引文件大小

## 下一步

配置好数据源后，您可以：

1. [开始聊天](../features/overview.md#聊天功能)
2. [设置自动化任务](../features/agents.md)
3. [连接客户端应用](../clients/overview.md)
4. [部署到生产环境](../deployment/overview.md)

如果遇到配置问题，请查看[故障排除指南](../troubleshooting/overview.md)或在社区寻求帮助。