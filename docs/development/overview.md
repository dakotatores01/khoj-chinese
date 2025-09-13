# 开发指南

本指南面向希望为 Khoj 贡献代码或自定义功能的开发者。涵盖了从环境搭建到贡献流程的所有必要信息。

## 目录

- [环境搭建](#环境搭建)
- [项目结构](#项目结构)
- [核心组件](#核心组件)
- [开发流程](#开发流程)
- [测试指南](#测试指南)
- [贡献指南](#贡献指南)
- [发布流程](#发布流程)

## 环境搭建

### 系统要求

#### 开发环境
- **操作系统**: Linux, macOS, Windows
- **Python**: 3.10 - 3.12
- **Node.js**: 18.x 或更高版本（用于前端开发）
- **Docker**: 可选（用于容器化开发）
- **PostgreSQL**: 13+（推荐用于开发）
- **Redis**: 6+（可选，用于缓存）

#### 硬件要求
- **CPU**: 至少 4 核心
- **内存**: 至少 8GB RAM（推荐 16GB+）
- **存储**: 至少 20GB 可用空间

### 开发环境安装

#### 1. 克隆仓库

```bash
git clone https://github.com/khoj-ai/khoj.git
cd khoj
```

#### 2. 创建虚拟环境

```bash
# 使用 venv
python -m venv khoj-dev
source khoj-dev/bin/activate  # Linux/macOS
# khoj-dev\Scripts\activate  # Windows

# 或使用 conda
conda create -n khoj-dev python=3.11
conda activate khoj-dev
```

#### 3. 安装依赖

```bash
# 安装开发依赖
pip install -e ".[dev]"

# 安装前端依赖（如果需要开发前端）
cd src/interface/web
npm install
cd ../../..
```

#### 4. 配置数据库

```bash
# 安装 PostgreSQL（Ubuntu/Debian）
sudo apt install postgresql postgresql-contrib

# 创建数据库和用户
sudo -u postgres psql
CREATE DATABASE khoj_dev;
CREATE USER khoj_dev WITH PASSWORD 'khoj_dev';
GRANT ALL PRIVILEGES ON DATABASE khoj_dev TO khoj_dev;
\q

# 运行数据库迁移
python manage.py migrate
```

#### 5. 环境变量配置

创建 `.env` 文件：

```bash
# .env
DEBUG=True
SECRET_KEY=your-development-secret-key
DATABASE_URL=postgresql://khoj_dev:khoj_dev@localhost:5432/khoj_dev
REDIS_URL=redis://localhost:6379/0
OPENAI_API_KEY=your-openai-api-key
ANTHROPIC_API_KEY=your-anthropic-api-key
```

#### 6. 启动开发服务器

```bash
# 启动后端
python manage.py runserver

# 启动前端（新终端）
cd src/interface/web
npm run dev
```

## 项目结构

### 根目录结构

```
khoj/
├── src/                    # 源代码目录
│   ├── khoj/              # 主应用
│   │   ├── app/           # Django 应用配置
│   │   ├── database/      # 数据库模型和适配器
│   │   ├── interface/      # 用户界面
│   │   ├── processor/     # 数据处理器
│   │   ├── routers/       # API 路由
│   │   ├── search_filter/ # 搜索过滤器
│   │   ├── search_type/   # 搜索类型
│   │   ├── utils/         # 工具函数
│   │   └── configure.py   # 应用配置
│   ├── interface/         # 客户端界面
│   │   ├── android/       # Android 应用
│   │   ├── desktop/       # 桌面应用
│   │   ├── emacs/         # Emacs 插件
│   │   ├── obsidian/     # Obsidian 插件
│   │   └── web/           # Web 界面
│   └── telemetry/         # 遥测服务
├── tests/                 # 测试代码
├── documentation/         # 英文文档
├── docs/                 # 中文文档
├── scripts/               # 脚本文件
├── .github/               # GitHub 工作流
└── requirements/         # 依赖文件
```

### 核心模块说明

#### `src/khoj/app/`

Django 应用配置和设置：

- `settings.py`: Django 设置
- `urls.py`: URL 路由配置
- `wsgi.py`: WSGI 应用入口

#### `src/khoj/database/`

数据库相关代码：

- `models/`: Django 模型定义
- `migrations/`: 数据库迁移文件
- `adapters/`: 数据库适配器

#### `src/khoj/processor/`

数据处理模块：

- `content/`: 内容处理器（Markdown, PDF, DOCX 等）
- `conversation/`: 对话处理器（各种 LLM 集成）
- `embeddings/`: 嵌入模型处理器
- `image/`: 图像生成处理器
- `speech/`: 语音处理处理器
- `tools/`: 工具处理器（代码执行、在线搜索等）

#### `src/khoj/routers/`

FastAPI 路由：

- `api.py`: 主 API 路由
- `api_chat.py`: 聊天 API
- `api_content.py`: 内容管理 API
- `api_model.py`: 模型管理 API
- 其他特定功能 API

#### `src/khoj/search_filter/`

搜索过滤器：

- `base_filter.py`: 过滤器基类
- `date_filter.py`: 日期过滤器
- `file_filter.py`: 文件过滤器
- `word_filter.py`: 词汇过滤器

#### `src/khoj/search_type/`

搜索类型实现：

- `text_search.py`: 文本搜索实现

#### `src/khoj/utils/`

工具函数：

- `config.py`: 配置管理
- `helpers.py`: 辅助函数
- `initialization.py`: 初始化函数
- `state.py`: 应用状态管理

## 核心组件

### 数据处理管道

Khoj 使用模块化的数据处理管道：

```python
# 数据处理流程示例
from khoj.processor.content.markdown.markdown_to_entries import MarkdownToEntries
from khoj.processor.embeddings import EmbeddingsModel

# 1. 将 Markdown 文件转换为条目
entries = MarkdownToEntries.from_file("notes.md")

# 2. 生成嵌入向量
embeddings_model = EmbeddingsModel()
embeddings = embeddings_model.embed_entries(entries)

# 3. 存储到数据库
from khoj.database.models import Entry
for entry, embedding in zip(entries, embeddings):
    Entry.objects.create(
        raw=entry.raw,
        compiled=entry.compiled,
        embeddings=embedding
    )
```

### 搜索架构

Khoj 的搜索架构基于语义搜索：

```python
# 搜索流程示例
from khoj.search_type.text_search import query
from khoj.database.models import Entry

# 1. 编码查询
query_embedding = embeddings_model.embed_query("项目会议记录")

# 2. 执行搜索
results = Entry.objects.annotate(
    similarity=1 - CosineDistance('embeddings', query_embedding)
).filter(similarity__gt=0.5).order_by('-similarity')

# 3. 返回结果
for result in results[:5]:
    print(f"相似度: {result.similarity}, 内容: {result.compiled[:100]}...")
```

### 对话系统

Khoj 支持多种对话模型：

```python
# 对话系统示例
from khoj.processor.conversation.openai.gpt import converse_openai
from khoj.processor.conversation.anthropic.anthropic_chat import converse_anthropic

# OpenAI 对话
async def chat_with_gpt(messages):
    response = await converse_openai(
        messages=messages,
        model="gpt-4o",
        api_key="your-api-key"
    )
    return response

# Anthropic 对话
async def chat_with_claude(messages):
    response = await converse_anthropic(
        messages=messages,
        model="claude-3-5-sonnet",
        api_key="your-api-key"
    )
    return response
```

## 开发流程

### 创建新功能

#### 1. 创建功能分支

```bash
git checkout -b feature/new-feature-name
```

#### 2. 实现功能

根据功能类型，在相应目录中创建或修改文件：

```python
# 示例：创建新的内容处理器
# src/khoj/processor/content/new_format/new_format_to_entries.py

from khoj.processor.content.text_to_entries import TextToEntries
from khoj.utils.rawconfig import Entry

class NewFormatToEntries(TextToEntries):
    def __init__(self, config=None):
        super().__init__(config)

    @staticmethod
    def from_file(file_path: str) -> list[Entry]:
        """从新格式文件创建条目"""
        entries = []
        # 解析文件并创建条目
        with open(file_path, 'r', encoding='utf-8') as f:
            content = f.read()
            # 处理内容
            entry = Entry(
                raw=content,
                compiled=content,
                heading="文件标题",
                file=file_path
            )
            entries.append(entry)
        return entries
```

#### 3. 添加测试

```python
# tests/test_new_format_to_entries.py

import pytest
from khoj.processor.content.new_format.new_format_to_entries import NewFormatToEntries

def test_new_format_parsing():
    """测试新格式解析"""
    entries = NewFormatToEntries.from_file("tests/data/new_format/sample.new")
    assert len(entries) > 0
    assert entries[0].heading is not None
```

#### 4. 更新文档

```markdown
<!-- docs/data-sources/overview.md -->
### 新格式支持

Khoj 现在支持 .new 格式的文件：

```python
from khoj.processor.content.new_format.new_format_to_entries import NewFormatToEntries

entries = NewFormatToEntries.from_file("sample.new")
```
```

### 数据库迁移

当修改数据库模型时，需要创建迁移：

```bash
# 创建迁移文件
python manage.py makemigrations

# 应用迁移
python manage.py migrate
```

### API 开发

添加新的 API 端点：

```python
# src/khoj/routers/api_new_feature.py

from fastapi import APIRouter, Depends
from khoj.database.adapters import UserAdapters

api_new_feature = APIRouter()

@api_new_feature.get("/new-feature")
async def new_feature_endpoint(user: KhojUser = Depends(UserAdapters.user)):
    """新功能端点"""
    # 实现功能逻辑
    return {"status": "ok", "message": "新功能工作正常"}
```

注册路由：

```python
# src/khoj/configure.py

def configure_routes(app):
    from khoj.routers.api_new_feature import api_new_feature
    app.include_router(api_new_feature, prefix="/api/new-feature")
```

## 测试指南

### 测试框架

Khoj 使用 pytest 作为测试框架：

```bash
# 运行所有测试
pytest

# 运行特定测试文件
pytest tests/test_markdown_to_entries.py

# 运行特定测试函数
pytest tests/test_markdown_to_entries.py::test_markdown_file_parsing

# 并行运行测试
pytest -n auto
```

### 测试类型

#### 单元测试

```python
# tests/test_utils.py

import pytest
from khoj.utils.helpers import is_none_or_empty

def test_is_none_or_empty():
    """测试空值检查函数"""
    assert is_none_or_empty(None) == True
    assert is_none_or_empty("") == True
    assert is_none_or_empty(" ") == True
    assert is_none_or_empty("test") == False
```

#### 集成测试

```python
# tests/test_search_integration.py

import pytest
from khoj.search_type.text_search import query
from khoj.database.models import Entry

@pytest.mark.django_db
def test_search_integration():
    """测试搜索集成"""
    # 创建测试数据
    Entry.objects.create(
        raw="测试文档内容",
        compiled="测试文档内容",
        embeddings=[0.1, 0.2, 0.3]  # 简化的嵌入向量
    )
    
    # 执行搜索
    results = query("测试")
    
    # 验证结果
    assert len(results) > 0
    assert "测试" in results[0]["entry"]
```

#### 端到端测试

```python
# tests/test_api_end_to_end.py

import pytest
import requests

def test_chat_api_end_to_end(khoj_api_server):
    """测试聊天 API 端到端"""
    response = requests.post(
        f"{khoj_api_server}/api/chat",
        json={"q": "你好"},
        headers={"Authorization": "Bearer test-token"}
    )
    
    assert response.status_code == 200
    assert "response" in response.json()
```

### 测试数据

测试数据位于 `tests/data/` 目录：

```
tests/data/
├── config.yml          # 测试配置
├── docx/               # Word 文档测试数据
├── images/             # 图像测试数据
├── markdown/           # Markdown 测试数据
├── org/                # Org 模式测试数据
├── pdf/                # PDF 测试数据
└── plaintext/          # 纯文本测试数据
```

### 测试覆盖率

```bash
# 运行测试并生成覆盖率报告
pytest --cov=khoj --cov-report=html

# 查看覆盖率
open htmlcov/index.html
```

## 贡献指南

### 贡献流程

1. **Fork 仓库**
   - 访问 [Khoj GitHub](https://github.com/khoj-ai/khoj)
   - 点击 "Fork" 按钮

2. **克隆 Fork**
```bash
git clone https://github.com/your-username/khoj.git
cd khoj
git remote add upstream https://github.com/khoj-ai/khoj.git
```

3. **创建功能分支**
```bash
git checkout -b feature/your-feature-name
```

4. **实现功能**
   - 编写代码
   - 添加测试
   - 更新文档

5. **提交更改**
```bash
git add .
git commit -m "feat: 添加新功能描述"
git push origin feature/your-feature-name
```

6. **创建 Pull Request**
   - 在 GitHub 上创建 PR
   - 填写详细的 PR 描述

### 代码规范

#### Python 代码风格

使用 Black 和 Ruff 进行代码格式化：

```bash
# 格式化代码
black src/khoj/

# 检查代码风格
ruff check src/khoj/
```

#### 提交信息规范

遵循 Conventional Commits 规范：

```
feat: 添加新功能
fix: 修复错误
docs: 更新文档
style: 代码样式调整
refactor: 代码重构
test: 添加测试
chore: 构建过程或辅助工具的变动
```

#### 代码审查清单

- [ ] 代码符合 Python 风格指南
- [ ] 添加了适当的测试
- [ ] 更新了相关文档
- [ ] 通过了所有测试
- [ ] 没有引入新的警告或错误
- [ ] 代码易于理解和维护

### 文档贡献

#### 更新文档

所有文档更改都应该：

1. 使用清晰简洁的语言
2. 提供实际的代码示例
3. 包含相关的配置选项说明
4. 更新目录和导航链接

#### 翻译文档

中文文档翻译指南：

1. 保持技术术语的一致性
2. 注意中英文标点符号的使用
3. 保留代码块和配置示例的原文
4. 确保链接和引用的有效性

## 发布流程

### 版本管理

Khoj 使用语义化版本控制（SemVer）：

- **主版本号**: 不兼容的 API 修改
- **次版本号**: 向后兼容的功能性新增
- **修订号**: 向后兼容的问题修正

### 发布步骤

#### 1. 准备发布

```bash
# 更新版本号
# 在 pyproject.toml 中更新版本

# 更新 CHANGELOG.md
# 添加发布说明

# 提交更改
git add pyproject.toml CHANGELOG.md
git commit -m "chore: 准备发布 v1.2.3"
```

#### 2. 创建发布标签

```bash
# 创建并推送标签
git tag -a v1.2.3 -m "Release version 1.2.3"
git push origin v1.2.3
```

#### 3. 构建和发布

```bash
# 构建分发包
python -m build

# 发布到 PyPI
python -m twine upload dist/*
```

#### 4. Docker 镜像发布

```bash
# 构建 Docker 镜像
docker build -t khoj/khoj:v1.2.3 .
docker build -t khoj/khoj:latest .

# 推送到 Docker Hub
docker push khoj/khoj:v1.2.3
docker push khoj/khoj:latest
```

### 发布检查清单

- [ ] 更新版本号
- [ ] 更新 CHANGELOG.md
- [ ] 运行所有测试
- [ ] 构建分发包
- [ ] 发布到 PyPI
- [ ] 构建 Docker 镜像
- [ ] 推送 Docker 镜像
- [ ] 创建 GitHub Release
- [ ] 通知社区

## 高级主题

### 自定义模型集成

添加新的 LLM 集成：

```python
# src/khoj/processor/conversation/new_provider/new_provider_chat.py

from khoj.processor.conversation.utils import Conversation

class NewProviderChat(Conversation):
    def __init__(self, model_name: str, api_key: str):
        self.model_name = model_name
        self.api_key = api_key
        
    async def send_message(self, messages: list, **kwargs):
        """发送消息到新提供商"""
        # 实现 API 调用逻辑
        pass
        
    def validate_auth(self) -> bool:
        """验证认证信息"""
        # 实现认证验证逻辑
        pass
```

### 插件系统

Khoj 支持插件扩展：

```python
# 创建插件目录结构
my_plugin/
├── __init__.py
├── plugin.py
└── config.yml

# plugin.py
from khoj.processor.content.text_to_entries import TextToEntries

class MyPlugin(TextToEntries):
    def process(self, files: dict[str, str]) -> list[Entry]:
        """处理自定义格式文件"""
        # 实现处理逻辑
        pass
```

### 性能优化

#### 数据库优化

```python
# 使用数据库索引
class Entry(models.Model):
    class Meta:
        indexes = [
            models.Index(fields=['file_type']),
            models.Index(fields=['created_at']),
        ]

# 使用 select_related 和 prefetch_related
entries = Entry.objects.select_related('user').prefetch_related('embeddings')
```

#### 缓存策略

```python
# 使用 Redis 缓存
from django.core.cache import cache

def get_cached_result(query):
    cache_key = f"search_result_{hash(query)}"
    result = cache.get(cache_key)
    if result is None:
        result = perform_search(query)
        cache.set(cache_key, result, timeout=3600)  # 缓存1小时
    return result
```

## 故障排除

### 常见开发问题

#### 依赖安装失败

```bash
# 清理 pip 缓存
pip cache purge

# 重新安装依赖
pip install -e ".[dev]"

# 或使用国内镜像源
pip install -e ".[dev]" -i https://pypi.tuna.tsinghua.edu.cn/simple
```

#### 数据库迁移问题

```bash
# 重置数据库
python manage.py flush
python manage.py migrate

# 或完全重置
rm db.sqlite3
python manage.py migrate
```

#### 前端开发问题

```bash
# 清理 Node.js 模块缓存
cd src/interface/web
rm -rf node_modules package-lock.json
npm install

# 重新构建
npm run build
```

### 调试技巧

#### 日志调试

```python
# 启用详细日志
import logging
logging.basicConfig(level=logging.DEBUG)

# 在代码中添加日志
logger = logging.getLogger(__name__)
logger.debug(f"调试信息: {variable}")
```

#### 性能分析

```python
# 使用 cProfile 进行性能分析
import cProfile
import pstats

def profile_function():
    profiler = cProfile.Profile()
    profiler.enable()
    
    # 执行要分析的代码
    your_function()
    
    profiler.disable()
    stats = pstats.Stats(profiler)
    stats.sort_stats('cumulative')
    stats.print_stats(20)
```

## 社区和支持

### 获取帮助

1. **GitHub Issues**: [https://github.com/khoj-ai/khoj/issues](https://github.com/khoj-ai/khoj/issues)
2. **Discord 社区**: [https://discord.gg/BDgyabRM6e](https://discord.gg/BDgyabRM6e)
3. **邮件支持**: team@khoj.dev

### 贡献资源

1. **贡献文档**: [CONTRIBUTING.md](https://github.com/khoj-ai/khoj/blob/main/CONTRIBUTING.md)
2. **行为准则**: [CODE_OF_CONDUCT.md](https://github.com/khoj-ai/khoj/blob/main/CODE_OF_CONDUCT.md)
3. **路线图**: [ROADMAP.md](https://github.com/khoj-ai/khoj/blob/main/ROADMAP.md)

通过遵循本开发指南，您将能够有效地为 Khoj 项目做出贡献或构建自定义功能。感谢您的参与和支持！