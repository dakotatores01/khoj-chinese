# API 参考文档

Khoj 提供了完整的 REST API，允许开发者集成 Khoj 的功能到自己的应用程序中。本文档详细介绍了所有可用的 API 端点、参数和响应格式。

## 目录

- [认证](#认证)
- [搜索 API](#搜索-api)
- [聊天 API](#聊天-api)
- [内容管理 API](#内容管理-api)
- [代理 API](#代理-api)
- [自动化 API](#自动化-api)
- [模型 API](#模型-api)
- [错误处理](#错误处理)
- [速率限制](#速率限制)

## 认证

### API 密钥

大多数 API 端点都需要认证。您可以通过以下方式提供 API 密钥：

#### 请求头认证
```http
Authorization: Bearer YOUR_API_KEY
```

#### 查询参数认证
```http
GET /api/search?q=test&token=YOUR_API_KEY
```

### 获取 API 密钥

在 Web 界面中：

1. 登录到 Khoj
2. 进入设置页面
3. 在 "API Keys" 部分生成新的密钥

### 认证示例

```bash
# 使用 curl
curl -H "Authorization: Bearer YOUR_API_KEY" \
     http://localhost:8000/api/search?q=hello

# 使用 Python requests
import requests
headers = {"Authorization": "Bearer YOUR_API_KEY"}
response = requests.get("http://localhost:8000/api/search?q=hello", headers=headers)
```

## 搜索 API

### 搜索端点

#### `GET /api/search`

执行语义搜索查询。

##### 参数

| 参数 | 类型 | 必需 | 描述 |
|------|------|
| `q` | string | 是 | 搜索查询 |
| `n` | integer | 否 | 返回结果数量（默认 5） |
| `t` | string | 否 | 搜索类型（默认 All） |
| `r` | boolean | 否 | 是否启用递归搜索 |
| `max_distance` | float | 否 | 最大距离阈值 |
| `dedupe` | boolean | 否 | 是否启用去重 |

##### 响应格式

```json
[
  {
    "entry": "搜索结果的文本内容",
    "score": 0.85,
    "corpus_id": "文档ID",
    "additional": {
      "source": "数据源类型",
      "file": "文件路径",
      "uri": "文档URI",
      "compiled": "编译后的内容",
      "heading": "标题"
    }
  }
]
```

##### 示例

```bash
# 基本搜索
curl "http://localhost:8000/api/search?q=项目会议记录" \
     -H "Authorization: Bearer YOUR_API_KEY"

# 限制结果数量
curl "http://localhost:8000/api/search?q=项目会议记录&n=10" \
     -H "Authorization: Bearer YOUR_API_KEY"

# 指定搜索类型
curl "http://localhost:8000/api/search?q=项目会议记录&t=markdown" \
     -H "Authorization: Bearer YOUR_API_KEY"
```

### 更新索引

#### `GET /api/update`

更新内容索引。

##### 参数

| 参数 | 类型 | 必需 | 描述 |
|------|------|
| `t` | string | 否 | 内容类型 |
| `force` | boolean | 否 | 强制重新索引 |

##### 响应

```json
{
  "status": "ok",
  "message": "khoj reloaded"
}
```

##### 示例

```bash
# 更新所有内容
curl "http://localhost:8000/api/update" \
     -H "Authorization: Bearer YOUR_API_KEY"

# 强制重新索引
curl "http://localhost:8000/api/update?force=true" \
     -H "Authorization: Bearer YOUR_API_KEY"
```

## 聊天 API

### 聊天端点

#### `POST /api/chat`

与 Khoj 进行聊天对话。

##### 请求体

```json
{
  "q": "你的问题",
  "stream": true,
  "n": 5,
  "d": 0.18,
  "t": "all",
  "r": false,
  "conversation_id": "会话ID（可选）"
}
```

##### 参数说明

| 参数 | 类型 | 描述 |
|------|------|------|
| `q` | string | 聊天查询 |
| `stream` | boolean | 是否流式响应 |
| `n` | integer | 搜索结果数量 |
| `d` | float | 搜索距离阈值 |
| `t` | string | 搜索类型 |
| `r` | boolean | 递归搜索 |
| `conversation_id` | string | 会话ID |

##### 响应格式

###### 非流式响应
```json
{
  "response": "AI的回答内容",
  "context": [
    {
      "entry": "相关的上下文内容",
      "file": "文件路径"
    }
  ]
}
```

###### 流式响应
流式响应返回一系列事件，每个事件以特殊分隔符结束：
```
{"type": "message", "data": "部分响应内容"}␃🔚␗
{"type": "references", "data": [...]}␃🔚␗
{"type": "end_response"}␃🔚␗
```

##### 示例

```bash
# 基本聊天
curl -X POST "http://localhost:8000/api/chat" \
     -H "Authorization: Bearer YOUR_API_KEY" \
     -H "Content-Type: application/json" \
     -d '{"q": "你好，今天天气怎么样？"}'

# 流式聊天
curl -X POST "http://localhost:8000/api/chat" \
     -H "Authorization: Bearer YOUR_API_KEY" \
     -H "Content-Type: application/json" \
     -d '{"q": "解释量子计算", "stream": true}'
```

### 聊天历史

#### `GET /api/chat/history`

获取聊天历史记录。

##### 参数

| 参数 | 类型 | 描述 |
|------|------|------|
| `conversation_id` | string | 会话ID |

##### 响应

```json
{
  "status": "ok",
  "response": {
    "chat": [
      {
        "message": "用户消息",
        "by": "you",
        "created": "时间戳"
      },
      {
        "message": "AI回复",
        "by": "khoj",
        "created": "时间戳"
      }
    ]
  }
}
```

### 聊天选项

#### `GET /api/chat/options`

获取聊天配置选项。

##### 响应

```json
{
  "models": {
    "default": "gpt-4o",
    "options": ["gpt-4o", "claude-3-5-sonnet", "gemini-1.5-pro"]
  }
}
```

## 内容管理 API

### 上传内容

#### `PUT /api/content`

上传文件内容。

##### 表单数据

| 字段 | 类型 | 描述 |
|------|------|------|
| `files` | file | 要上传的文件 |

##### 响应

```json
{
  "status": "ok",
  "filenames": ["file1.md", "file2.pdf"]
}
```

##### 示例

```bash
# 上传单个文件
curl -X PUT "http://localhost:8000/api/content" \
     -H "Authorization: Bearer YOUR_API_KEY" \
     -F "files=@notes.md"

# 上传多个文件
curl -X PUT "http://localhost:8000/api/content" \
     -H "Authorization: Bearer YOUR_API_KEY" \
     -F "files=@notes1.md" \
     -F "files=@notes2.pdf"
```

### 获取内容大小

#### `GET /api/content/size`

获取索引内容的大小信息。

##### 响应

```json
{
  "indexed_data_size_in_mb": 150
}
```

### 获取文件列表

#### `GET /api/content/files`

获取已索引的文件列表。

##### 参数

| 参数 | 类型 | 描述 |
|------|------|------|
| `page` | integer | 页码 |

##### 响应

```json
{
  "files": [
    {
      "file_name": "notes.md",
      "raw_text": "文件内容预览...",
      "updated_at": "2024-01-15T10:30:00Z"
    }
  ],
  "num_pages": 5
}
```

### 删除文件

#### `DELETE /api/content/file`

删除指定文件。

##### 参数

| 参数 | 类型 | 描述 |
|------|------|------|
| `filename` | string | 要删除的文件名 |

##### 响应

```json
{
  "status": "ok"
}
```

## 代理 API

### 获取代理列表

#### `GET /api/agents`

获取所有可用代理。

##### 响应

```json
[
  {
    "name": "研究助理",
    "slug": "research-assistant",
    "personality": "专业的研究助理，擅长学术写作和文献综述",
    "input_tools": ["notes", "online"],
    "output_modes": ["text", "diagram"]
  }
]
```

### 创建代理

#### `POST /api/agents`

创建新的代理。

##### 请求体

```json
{
  "name": "代理名称",
  "personality": "代理个性描述",
  "input_tools": ["notes"],
  "output_modes": ["text"]
}
```

##### 响应

```json
{
  "status": "ok",
  "agent": {
    "id": 123,
    "name": "新代理",
    "slug": "new-agent"
  }
}
```

## 自动化 API

### 创建自动化任务

#### `POST /api/automation`

创建新的自动化任务。

##### 请求体

```json
{
  "query": "/automated_task 每天提醒我喝水",
  "schedule": "0 9 * * *",
  "subject": "每日喝水提醒"
}
```

##### 响应

```json
{
  "status": "ok",
  "job_id": "automation_1234567890"
}
```

### 获取自动化任务列表

#### `GET /api/automation`

获取所有自动化任务。

##### 响应

```json
[
  {
    "id": "automation_1234567890",
    "query": "/automated_task 每天提醒我喝水",
    "schedule": "0 9 * * *",
    "subject": "每日喝水提醒",
    "next_run": "2024-01-16T09:00:00Z"
  }
]
```

### 删除自动化任务

#### `DELETE /api/automation/{job_id}`

删除指定的自动化任务。

##### 响应

```json
{
  "status": "ok"
}
```

## 模型 API

### 获取模型配置

#### `GET /api/model/options`

获取可用的模型选项。

##### 响应

```json
{
  "chat": {
    "default": "gpt-4o",
    "options": [
      {
        "name": "GPT-4o",
        "id": "gpt-4o",
        "provider": "openai",
        "capabilities": ["text", "vision"]
      }
    ]
  },
  "text_to_image": {
    "default": "dall-e-3",
    "options": [
      {
        "name": "DALL-E 3",
        "id": "dall-e-3",
        "provider": "openai"
      }
    ]
  }
}
```

### 设置默认模型

#### `POST /api/model/set`

设置默认模型。

##### 请求体

```json
{
  "model_type": "chat",
  "model_name": "gpt-4o"
}
```

##### 响应

```json
{
  "status": "ok"
}
```

## 错误处理

### 错误响应格式

所有错误响应都遵循统一格式：

```json
{
  "detail": "错误描述信息"
}
```

### 常见错误码

| 状态码 | 描述 | 解决方案 |
|--------|------|----------|
| 400 | 请求参数错误 | 检查请求参数 |
| 401 | 未认证 | 提供有效的API密钥 |
| 403 | 权限不足 | 检查用户权限 |
| 404 | 资源未找到 | 检查请求路径 |
| 429 | 请求过于频繁 | 降低请求频率 |
| 500 | 服务器内部错误 | 联系技术支持 |
| 503 | 服务不可用 | 稍后重试 |

### 错误处理示例

```python
import requests

try:
    response = requests.get("http://localhost:8000/api/search?q=test")
    response.raise_for_status()  # 抛出HTTP错误
    data = response.json()
except requests.exceptions.HTTPError as e:
    if response.status_code == 401:
        print("API密钥无效，请检查认证信息")
    elif response.status_code == 429:
        print("请求过于频繁，请稍后再试")
    else:
        print(f"HTTP错误: {e}")
except requests.exceptions.RequestException as e:
    print(f"请求失败: {e}")
```

## 速率限制

### 限制规则

Khoj API 对请求频率有限制：

| 用户类型 | 限制 | 说明 |
|----------|------|------|
| 免费用户 | 100次/天 | 基本使用限制 |
| 订阅用户 | 1000次/天 | 更高的使用限额 |
| 企业用户 | 自定义 | 根据套餐定制 |

### 限制头部信息

响应中包含速率限制信息：

```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1640995200
```

### 处理速率限制

```python
import time
import requests

def make_request_with_retry(url, headers):
    while True:
        response = requests.get(url, headers=headers)
        
        if response.status_code == 429:
            # 获取重置时间
            reset_time = int(response.headers.get('X-RateLimit-Reset', 0))
            sleep_time = max(reset_time - int(time.time()), 0) + 1
            print(f"速率限制，等待 {sleep_time} 秒后重试...")
            time.sleep(sleep_time)
            continue
            
        return response
```

## 客户端 SDK

### Python SDK

```python
from khoj import KhojClient

# 初始化客户端
client = KhojClient(
    server_url="http://localhost:8000",
    api_key="your-api-key"
)

# 搜索
results = client.search("项目会议记录")

# 聊天
response = client.chat("总结这份报告")

# 上传文件
client.upload_files(["notes.md", "report.pdf"])
```

### JavaScript SDK

```javascript
import { KhojClient } from 'khoj-sdk';

const client = new KhojClient({
    serverUrl: 'http://localhost:8000',
    apiKey: 'your-api-key'
});

// 搜索
const results = await client.search('项目会议记录');

// 流式聊天
const stream = client.chatStream('解释量子计算');
for await (const chunk of stream) {
    console.log(chunk);
}
```

## Webhook 集成

### 配置 Webhook

Khoj 支持 Webhook 回调，当特定事件发生时通知外部系统。

#### 支持的事件

- `chat_message`: 新的聊天消息
- `content_update`: 内容更新完成
- `automation_trigger`: 自动化任务触发

#### Webhook 配置示例

```json
{
  "webhooks": [
    {
      "event": "chat_message",
      "url": "https://your-service.com/webhook",
      "secret": "your-webhook-secret"
    }
  ]
}
```

#### Webhook 请求格式

```http
POST /webhook HTTP/1.1
Content-Type: application/json
X-Khoj-Signature: sha256=签名

{
  "event": "chat_message",
  "timestamp": "2024-01-15T10:30:00Z",
  "data": {
    "message": "用户消息内容",
    "response": "AI回复内容",
    "user_id": "用户ID"
  }
}
```

## 最佳实践

### 安全建议

1. **保护API密钥**
   - 不要在客户端代码中暴露密钥
   - 定期轮换密钥
   - 使用环境变量存储密钥

2. **验证响应**
   - 始终验证API响应格式
   - 处理可能的错误情况
   - 实现超时机制

3. **实施重试逻辑**
   - 对临时错误实施指数退避重试
   - 避免对速率限制错误立即重试

### 性能优化

1. **批量操作**
   - 尽可能使用批量上传接口
   - 合并相似的API请求

2. **缓存策略**
   - 缓存频繁访问的数据
   - 实现适当的缓存失效机制

3. **连接管理**
   - 复用HTTP连接
   - 合理设置超时时间

## 下一步

详细了解各个API端点：

1. [搜索功能详解](../features/search.md)
2. [聊天功能详解](../features/chat.md)
3. [内容管理详解](../data-sources/overview.md)
4. [代理系统详解](../features/agents.md)

如需更深入的技术支持，请查看[开发指南](../development/overview.md)或在社区寻求帮助。