# 客户端应用指南

Khoj 支持多种客户端应用，让您可以在不同设备和平台上使用 AI 助手。本指南介绍所有可用的客户端及其使用方法。

## 目录

- [Web 客户端](#web-客户端)
- [桌面客户端](#桌面客户端)
- [移动客户端](#移动客户端)
- [编辑器集成](#编辑器集成)
- [API 客户端](#api-客户端)

## Web 客户端

### 主要特性

Khoj 的 Web 客户端提供了完整的功能集：

- **现代界面** - 响应式设计，支持桌面和移动设备
- **实时聊天** - 流式响应，无需等待
- **文件管理** - 拖拽上传，批量处理
- **设置面板** - 完整的配置选项
- **多用户支持** - 账户管理和权限控制

### 访问方式

#### 云端访问
访问 [https://app.khoj.dev](https://app.khoj.dev) 直接使用，无需安装。

#### 本地部署
```bash
# 启动本地服务器
khoj

# 访问 http://localhost:8000
```

### 界面功能

#### 主聊天界面
- **消息输入框** - 支持多行文本
- **文件上传** - 拖拽或点击上传
- **语音输入** - 录音并转换为文本
- **格式化输出** - Markdown 渲染

#### 侧边栏
- **会话管理** - 创建、切换、删除会话
- **设置面板** - 模型配置、数据源设置
- **帮助文档** - 内置使用指南

#### 代理选择
- **预设代理** - Khoj 默认代理
- **自定义代理** - 用户创建的个性化代理
- **快速切换** - 一键切换不同角色

## 桌面客户端

### 系统要求

- **操作系统**: Windows 10+, macOS 10.15+, Linux
- **内存**: 至少 4GB RAM
- **存储**: 500MB 可用空间

### 安装步骤

#### Windows
```bash
# 下载并运行安装程序
# 访问 https://khoj.dev/downloads#desktop
```

#### macOS
```bash
# 使用 Homebrew
brew install khoj-ai/tap/khoj

# 或下载 .dmg 文件
```

#### Linux
```bash
# Ubuntu/Debian
sudo apt install khoj

# 或使用 snap
sudo snap install khoj
```

### 桌面客户端特性

#### 系统集成
- **系统托盘** - 后台运行，不占用任务栏
- **全局快捷键** - `Ctrl+Space` 快速唤起
- **通知集成** - 系统原生通知

#### 离线功能
- **本地模型** - 支持本地 LLM 推理
- **缓存管理** - 智能缓存，减少网络请求
- **同步机制** - 数据同步到云端

#### 隐私保护
- **本地存储** - 数据存储在本地设备
- **加密传输** - 与服务器通信加密
- **访问控制** - 本地认证和权限管理

## 移动客户端

### WhatsApp 集成

Khoj 支持通过 WhatsApp 使用：

#### 设置步骤

1. **访问设置页面**
   ```
   http://localhost:8000/settings
   ```

2. **启用 WhatsApp**
   - 选择 "WhatsApp" 作为客户端
   - 生成唯一的访问令牌

3. **连接 WhatsApp**
   - 扫描 QR 码或输入电话号码
   - 验证身份并授权

#### WhatsApp 功能

- **文本聊天** - 完整的 Khoj 对话功能
- **语音消息** - 发送语音，自动转换为文本
- **文件共享** - 发送图片、文档进行分析
- **群聊支持** - 在群聊中使用 Khoj

#### 使用示例

```
用户: 帮我总结这份报告
Khoj: 我需要先查看报告内容。请发送 PDF 文件。

用户: [发送文件]
Khoj: 基于您发送的报告，我来为您总结主要内容...

用户: 好的，谢谢！
```

### 移动 App（计划中）

未来版本将支持原生移动应用：

- **iOS App** - App Store 下载
- **Android App** - Google Play 下载
- **功能对等** - 与 Web 客户端相同的功能

## 编辑器集成

### Emacs 集成

Khoj 提供了完整的 Emacs 包：

#### 安装方法

```emacs-lisp
;; 在 init.el 中添加
(use-package khoj
  :ensure t
  :config
  (setq khoj-server-url "http://localhost:8000")
  (setq khoj-api-key "your-api-key"))
```

#### Emacs 功能

- **Org 模式集成** - 直接从 Org 文件查询
- **缓冲区搜索** - 在当前缓冲区中搜索
- **笔记同步** - 自动同步 Org 文件
- **快捷命令** - `M-x khoj-search`

#### 使用示例

```org
* 我的笔记

这是一些重要的笔记内容...

* 会议记录
会议纪要...
```

```emacs-lisp
;; 在 Emacs 中搜索
M-x khoj-search
Query: "会议记录的内容"
```

### Obsidian 插件

Khoj 官方 Obsidian 插件：

#### 安装步骤

1. **打开 Obsidian 设置**
2. **进入社区插件**
3. **搜索 "Khoj"**
4. **安装并启用**

#### 配置选项

```json
{
  "serverUrl": "http://localhost:8000",
  "apiKey": "your-api-key",
  "autoSync": true,
  "syncInterval": 30
}
```

#### Obsidian 功能

- **笔记搜索** - 在所有笔记中语义搜索
- **智能建议** - 基于上下文的建议
- **模板集成** - 自动生成笔记模板
- **链接管理** - 智能双链建议

#### 插件命令

- `Khoj: Search Notes` - 搜索所有笔记
- `Khoj: Chat` - 启动聊天会话
- `Khoj: Sync` - 手动同步笔记库

### VS Code 扩展（计划中）

未来将支持 VS Code 扩展：

- **代码搜索** - 在代码库中搜索
- **文档生成** - 自动生成代码文档
- **重构建议** - AI 驱动的重构建议

## API 客户端

### REST API

Khoj 提供完整的 REST API：

#### 基础 URL
```
https://api.khoj.dev/v1
# 或本地部署
http://localhost:8000/api
```

#### 认证方式

```bash
# Bearer Token 认证
curl -H "Authorization: Bearer YOUR_TOKEN" \
     https://api.khoj.dev/v1/search

# API Key 认证
curl -H "X-API-Key: YOUR_API_KEY" \
     https://api.khoj.dev/v1/search
```

### Python 客户端

官方 Python SDK：

```python
from khoj import Khoj

# 初始化客户端
client = Khoj(
    server_url="http://localhost:8000",
    api_key="your-api-key"
)

# 搜索文档
results = client.search("项目会议记录")

# 聊天
response = client.chat("总结这份报告", files=["report.pdf"])

# 添加文档
client.add_documents(["notes.md", "report.pdf"])
```

### JavaScript/TypeScript 客户端

```javascript
import { Khoj } from 'khoj-sdk';

const client = new Khoj({
    serverUrl: 'http://localhost:8000',
    apiKey: 'your-api-key'
});

// 异步搜索
const results = await client.search('会议纪要');

// 流式聊天
const stream = client.chatStream('解释这个概念');
for await (const chunk of stream) {
    console.log(chunk);
}
```

### cURL 示例

```bash
# 搜索
curl -X GET "http://localhost:8000/api/search?q=会议记录" \
     -H "Authorization: Bearer YOUR_TOKEN"

# 聊天
curl -X POST "http://localhost:8000/api/chat" \
     -H "Authorization: Bearer YOUR_TOKEN" \
     -H "Content-Type: application/json" \
     -d '{"q": "总结这份报告", "stream": true}'

# 上传文件
curl -X PUT "http://localhost:8000/api/content" \
     -H "Authorization: Bearer YOUR_TOKEN" \
     -F "files=@notes.md"
```

## 客户端比较

| 客户端 | 平台 | 安装复杂度 | 离线支持 | 实时同步 |
|--------|------|------------|----------|----------|
| Web | 所有 | 无需安装 | 否 | 是 |
| 桌面 | Win/macOS/Linux | 中等 | 是 | 是 |
| WhatsApp | 移动 | 低 | 否 | 是 |
| Emacs | 所有 | 中等 | 是 | 手动 |
| Obsidian | 所有 | 低 | 是 | 手动 |
| API | 所有 | 高 | 否 | 是 |

## 高级配置

### 代理设置

为客户端配置代理服务器：

```yaml
# khoj.yml
client:
  proxy:
    http: "http://proxy.company.com:8080"
    https: "http://proxy.company.com:8080"
  timeout: 30
```

### 缓存配置

优化客户端性能：

```yaml
# 启用本地缓存
cache:
  enabled: true
  max_size: "1GB"
  ttl: "24h"
```

### 安全设置

```yaml
# 启用 HTTPS
security:
  https_only: true
  certificate: "/path/to/cert.pem"
  key: "/path/to/key.pem"
```

## 故障排除

### 连接问题

#### Web 客户端无法连接
1. 检查服务器是否运行
2. 验证 URL 和端口配置
3. 检查防火墙设置
4. 查看浏览器控制台错误

#### 桌面客户端启动失败
1. 检查系统要求
2. 查看日志文件
3. 重新安装客户端
4. 检查权限设置

#### WhatsApp 连接失败
1. 验证电话号码格式
2. 检查网络连接
3. 重新生成访问令牌
4. 确认 WhatsApp 账户状态

### 性能问题

#### 响应缓慢
1. 检查网络连接
2. 清理缓存文件
3. 重启客户端
4. 检查服务器负载

#### 内存使用过高
1. 减少并发请求
2. 调整缓存大小
3. 关闭不必要的后台进程
4. 更新到最新版本

### 数据同步问题

#### 文件未同步
1. 检查文件权限
2. 验证文件格式
3. 手动触发同步
4. 查看同步日志

## 最佳实践

### 生产环境部署

1. **使用反向代理** (Nginx/Caddy)
2. **启用 HTTPS**
3. **配置监控**
4. **定期备份**
5. **更新安全补丁**

### 开发环境设置

1. **启用调试模式**
2. **配置日志级别**
3. **使用本地模型**
4. **设置开发数据库**

### 用户管理

1. **角色和权限**
2. **访问控制列表**
3. **审计日志**
4. **会话管理**

## 下一步

选择适合您的客户端后：

1. [配置数据源](../data-sources/overview.md) - 添加您的文档
2. [探索功能](../features/overview.md) - 学习使用技巧
3. [设置自动化](../features/agents.md) - 创建智能代理
4. [部署扩展](../deployment/overview.md) - 扩展您的设置

如果遇到问题，请查看[故障排除指南](../troubleshooting/overview.md)或加入社区讨论。