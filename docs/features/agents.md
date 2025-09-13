# 代理系统详解

Khoj 的代理系统是其核心功能之一，允许用户创建具有特定个性和能力的 AI 助手。本文档详细介绍代理系统的各个方面。

## 目录

- [代理概述](#代理概述)
- [创建代理](#创建代理)
- [代理配置](#代理配置)
- [内置代理](#内置代理)
- [代理交互](#代理交互)
- [代理管理](#代理管理)
- [自定义代理](#自定义代理)

## 代理概述

### 什么是代理？

代理是具有特定个性、能力和用途的 AI 助手。它们可以根据不同的场景和需求进行定制，为用户提供专门的服务。

### 代理的优势

1. **个性化**: 每个代理都有独特的个性和专长
2. **专业化**: 针对特定领域进行优化
3. **灵活性**: 可以随时创建、修改和删除
4. **可扩展性**: 支持自定义工具和功能

### 代理的应用场景

- **学术研究**: 专门处理学术论文和研究资料
- **项目管理**: 跟踪项目进度和任务分配
- **创意写作**: 提供写作灵感和内容创作
- **技术支持**: 解答技术问题和提供指导
- **生活助手**: 管理日常事务和提醒

## 创建代理

### Web 界面创建

通过 Web 界面创建代理是最直观的方式：

1. **访问代理页面**
   - 打开 Khoj Web 界面
   - 点击左侧导航栏的 "Agents" 图标

2. **创建新代理**
   - 点击 "Create New Agent" 按钮
   - 填写代理基本信息

3. **配置代理参数**
   - 设置代理名称和描述
   - 定义代理个性
   - 选择输入工具和输出模式

4. **保存代理**
   - 点击 "Save" 按钮完成创建

### API 创建

使用 API 创建代理：

```bash
# 创建新代理
curl -X POST "http://localhost:8000/api/agents" \
     -H "Authorization: Bearer YOUR_API_KEY" \
     -H "Content-Type: application/json" \
     -d '{
           "name": "研究助理",
           "personality": "专业的研究助理，擅长学术写作和文献综述",
           "input_tools": ["notes", "online"],
           "output_modes": ["text", "diagram"]
         }'
```

### 配置文件创建

通过配置文件批量创建代理：

```yaml
# agents.yml
- name: "研究助理"
  slug: "research-assistant"
  personality: "专业的研究助理，擅长学术写作和文献综述"
  input_tools: ["notes", "online"]
  output_modes: ["text", "diagram"]

- name: "项目管理器"
  slug: "project-manager"
  personality: "严格的项目管理器，专注于任务跟踪和进度管理"
  input_tools: ["notes", "calendar"]
  output_modes: ["text", "task_list"]
```

导入配置文件：

```bash
khoj agents import --file agents.yml
```

## 代理配置

### 基本配置

#### 名称和标识

```yaml
name: "代理名称"        # 代理的显示名称
slug: "agent-slug"      # 代理的唯一标识符（URL 友好）
```

#### 个性设置

```yaml
personality: |
  代理的个性描述，可以是多行文本。
  这里可以详细描述代理的性格特点、专业知识和交流风格。
```

### 输入工具

代理可以使用多种输入工具来获取信息：

#### 笔记工具 (notes)

```yaml
input_tools:
  - notes  # 访问用户的个人笔记和文档
```

功能：
- 搜索和引用用户文档
- 理解用户的知识体系
- 基于个人数据提供建议

#### 在线工具 (online)

```yaml
input_tools:
  - online  # 访问互联网信息
```

功能：
- 实时搜索网络信息
- 获取最新资讯和数据
- 验证信息准确性

#### 网页工具 (webpage)

```yaml
input_tools:
  - webpage  # 读取特定网页内容
```

功能：
- 深度阅读指定网页
- 提取网页结构化信息
- 分析网页内容

#### 代码工具 (code)

```yaml
input_tools:
  - code  # 执行代码和分析数据
```

功能：
- 执行 Python 代码
- 分析数据和生成图表
- 自动化计算任务

#### 操作工具 (operator)

```yaml
input_tools:
  - operator  # 执行浏览器操作
```

功能：
- 控制浏览器执行任务
- 自动化网页交互
- 执行复杂的在线操作

### 输出模式

代理可以以多种方式输出结果：

#### 文本模式 (text)

```yaml
output_modes:
  - text  # 标准文本输出
```

特点：
- 适用于大多数场景
- 支持 Markdown 格式
- 可以包含链接和引用

#### 图像模式 (image)

```yaml
output_modes:
  - image  # 生成图像
```

特点：
- 创建视觉化内容
- 支持多种图像格式
- 可以包含文字描述

#### 图表模式 (diagram)

```yaml
output_modes:
  - diagram  # 生成图表和流程图
```

特点：
- 创建结构化图表
- 支持多种图表类型
- 可以导出为图像或代码

#### 文件模式 (file)

```yaml
output_modes:
  - file  # 生成文件
```

特点：
- 创建文档文件
- 支持多种文件格式
- 可以包含复杂内容

### 高级配置

#### 模型配置

```yaml
chat_model:
  name: "gpt-4o"  # 使用的聊天模型
  temperature: 0.7  # 创造性温度
  max_tokens: 1000  # 最大输出长度
```

#### 工具配置

```yaml
tools:
  - name: "web_search"
    enabled: true
    config:
      max_results: 5
  - name: "code_execution"
    enabled: true
    config:
      timeout: 30
```

## 内置代理

### 默认代理 (Khoj)

这是 Khoj 的默认代理，具有通用的 AI 能力：

```yaml
name: "Khoj"
slug: "khoj"
personality: |
  我是 Khoj，你的智能个人助手。
  我可以帮助你处理各种任务，从回答问题到创作内容。
  我可以访问你的笔记和个人文档，为你提供个性化的帮助。
input_tools: ["notes", "online", "webpage"]
output_modes: ["text", "image", "diagram"]
```

### 研究助理 (Research Assistant)

专门用于学术研究和文献分析：

```yaml
name: "研究助理"
slug: "research-assistant"
personality: |
  我是你的研究助理，专门帮助你进行学术研究。
  我熟悉各种研究方法和学术写作规范。
  我可以帮助你整理文献、分析数据和撰写论文。
input_tools: ["notes", "online", "webpage"]
output_modes: ["text", "diagram"]
```

### 创意写作师 (Creative Writer)

专注于创意写作和内容创作：

```yaml
name: "创意写作师"
slug: "creative-writer"
personality: |
  我是你的创意写作伙伴，热爱故事和文字游戏。
  我可以帮助你激发创作灵感、完善故事情节和润色文字。
  让我们一起创造令人惊叹的作品吧！
input_tools: ["notes", "online"]
output_modes: ["text", "image"]
```

### 项目经理 (Project Manager)

专注于项目管理和任务跟踪：

```yaml
name: "项目经理"
slug: "project-manager"
personality: |
  我是你的项目经理，专注于帮助你高效完成项目。
  我擅长制定计划、跟踪进度和协调资源。
  让我们一起确保项目按时高质量完成。
input_tools: ["notes", "calendar"]
output_modes: ["text", "task_list"]
```

### 技术专家 (Tech Expert)

专注于技术问题解答和指导：

```yaml
name: "技术专家"
slug: "tech-expert"
personality: |
  我是你的技术专家，精通各种编程语言和开发工具。
  我可以帮助你解决技术难题、优化代码和学习新技术。
  无论是前端还是后端，我都能提供专业建议。
input_tools: ["notes", "code", "online"]
output_modes: ["text", "code"]
```

## 代理交互

### 与代理对话

#### Web 界面交互

1. **选择代理**
   - 在聊天界面顶部选择代理
   - 或在代理页面点击代理卡片

2. **开始对话**
   - 在聊天输入框中输入问题
   - 代理会根据其个性和能力回应

3. **上下文管理**
   - 代理会记住对话历史
   - 可以随时切换代理而不丢失上下文

#### 命令行交互

```bash
# 与特定代理对话
khoj chat --agent research-assistant "帮我总结这份研究报告"

# 流式对话
khoj chat --agent creative-writer --stream "写一个关于太空旅行的故事"
```

#### API 交互

```bash
# 与代理对话
curl -X POST "http://localhost:8000/api/chat" \
     -H "Authorization: Bearer YOUR_API_KEY" \
     -H "Content-Type: application/json" \
     -d '{
           "q": "帮我分析这个项目的可行性",
           "agent": "project-manager"
         }'
```

### 代理指令

代理支持特殊的指令来控制行为：

#### 切换代理

```
/switch research-assistant  # 切换到研究助理
/switch creative-writer     # 切换到创意写作师
```

#### 查询代理信息

```
/info                       # 查看当前代理信息
/help                       # 查看可用代理列表
```

#### 自定义代理行为

```
/mode creative              # 设置创造性模式
/mode precise               # 设置精确模式
```

## 代理管理

### 查看代理列表

#### Web 界面

1. 点击左侧导航栏的 "Agents" 图标
2. 查看所有可用代理的列表
3. 点击代理卡片查看详细信息

#### 命令行

```bash
# 列出所有代理
khoj agents list

# 查看特定代理信息
khoj agents info research-assistant
```

#### API

```bash
# 获取代理列表
curl "http://localhost:8000/api/agents" \
     -H "Authorization: Bearer YOUR_API_KEY"
```

### 编辑代理

#### Web 界面编辑

1. 进入代理详情页面
2. 点击 "Edit" 按钮
3. 修改代理配置
4. 点击 "Save" 保存更改

#### 命令行编辑

```bash
# 编辑代理
khoj agents edit research-assistant --personality "新的个性描述"
```

#### API 编辑

```bash
# 更新代理信息
curl -X PUT "http://localhost:8000/api/agents/research-assistant" \
     -H "Authorization: Bearer YOUR_API_KEY" \
     -H "Content-Type: application/json" \
     -d '{
           "personality": "更新后的个性描述"
         }'
```

### 删除代理

#### Web 界面删除

1. 进入代理详情页面
2. 点击 "Delete" 按钮
3. 确认删除操作

#### 命令行删除

```bash
# 删除代理
khoj agents delete research-assistant
```

#### API 删除

```bash
# 删除代理
curl -X DELETE "http://localhost:8000/api/agents/research-assistant" \
     -H "Authorization: Bearer YOUR_API_KEY"
```

### 导出和导入

#### 导出代理配置

```bash
# 导出所有代理
khoj agents export --file agents_backup.yml

# 导出特定代理
khoj agents export research-assistant --file research_assistant.yml
```

#### 导入代理配置

```bash
# 导入代理配置
khoj agents import --file agents_backup.yml
```

## 自定义代理

### 创建专业代理

#### 学术导师代理

```yaml
# academic_mentor.yml
name: "学术导师"
slug: "academic-mentor"
personality: |
  我是你的学术导师，拥有丰富的教学和研究经验。
  我可以帮助你理解复杂的学术概念、改进研究方法和完善学术写作。
  我熟悉各种学科领域的知识，能够提供跨学科的指导。
input_tools: ["notes", "online", "webpage"]
output_modes: ["text", "diagram"]
chat_model:
  name: "gpt-4o"
  temperature: 0.3  # 保持严谨性
```

#### 健康顾问代理

```yaml
# health_advisor.yml
name: "健康顾问"
slug: "health-advisor"
personality: |
  我是你的健康顾问，专注于提供科学的健康建议。
  我了解营养学、运动科学和心理健康知识。
  我可以帮助你制定健康计划、改善生活习惯和预防疾病。
input_tools: ["notes", "online"]
output_modes: ["text", "image"]
chat_model:
  name: "claude-3-5-sonnet"
  temperature: 0.5  # 平衡专业性和亲和力
```

### 集成外部工具

#### 自定义工具开发

```python
# custom_tool.py
from khoj.processor.tools.base_tool import BaseTool

class CustomAnalysisTool(BaseTool):
    def __init__(self, config):
        super().__init__(config)
        self.name = "custom_analysis"
        
    async def run(self, query, context=None):
        """执行自定义分析"""
        # 实现分析逻辑
        result = {
            "analysis": "分析结果",
            "recommendations": ["建议1", "建议2"]
        }
        return result
```

注册工具：

```yaml
# 在代理配置中注册工具
tools:
  - name: "custom_analysis"
    enabled: true
    module: "custom_tool.CustomAnalysisTool"
```

### 代理模板

#### 教育领域模板

```yaml
# education_template.yml
template:
  name: "{subject}导师"
  slug: "{subject}-tutor"
  personality: |
    我是{subject}导师，专门帮助学生学习{subject}。
    我熟悉{subject}的核心概念和学习方法。
    我可以根据你的水平提供个性化的指导。
  input_tools: ["notes", "online"]
  output_modes: ["text", "diagram"]
```

使用模板：

```bash
# 基于模板创建代理
khoj agents create-from-template education_template.yml \
  --vars "subject=数学"
```

#### 商业领域模板

```yaml
# business_template.yml
template:
  name: "{industry}顾问"
  slug: "{industry}-advisor"
  personality: |
    我是{industry}行业的资深顾问，拥有丰富的实战经验。
    我可以帮助你分析市场趋势、制定商业策略和解决经营问题。
  input_tools: ["notes", "online", "webpage"]
  output_modes: ["text", "diagram", "task_list"]
```

### 代理协作

多个代理可以协同工作：

#### 代理链

```yaml
# agent_chain.yml
agents:
  - name: "数据分析师"
    slug: "data-analyst"
    next: "report-writer"
    
  - name: "报告撰写员"
    slug: "report-writer"
    next: "presentation-designer"
    
  - name: "演示设计师"
    slug: "presentation-designer"
```

执行代理链：

```bash
# 执行代理链
khoj agents chain --config agent_chain.yml "分析销售数据"
```

#### 代理团队

```yaml
# agent_team.yml
team:
  name: "项目团队"
  members:
    - research-assistant
    - project-manager
    - tech-expert
  coordinator: "project-manager"
```

使用代理团队：

```bash
# 与代理团队协作
khoj agents team --config agent_team.yml "启动新产品开发项目"
```

## 最佳实践

### 设计优秀代理

1. **明确定位**
   - 确定代理的专业领域
   - 定义清晰的目标用户
   - 设计独特的个性特征

2. **合理配置**
   - 选择合适的输入工具
   - 设置恰当的输出模式
   - 调整模型参数以匹配需求

3. **持续优化**
   - 根据使用反馈调整配置
   - 定期更新个性描述
   - 扩展工具和功能

### 使用代理技巧

1. **选择合适代理**
   - 根据任务性质选择代理
   - 考虑代理的专业能力和个性
   - 必要时切换代理以获得更好效果

2. **提供充分上下文**
   - 提供相关的背景信息
   - 明确表达需求和期望
   - 给出具体的示例和约束

3. **有效交互**
   - 使用清晰简洁的语言
   - 分步骤提出复杂问题
   - 及时提供反馈和澄清

### 管理代理集合

1. **分类组织**
   - 按领域或用途分类代理
   - 使用有意义的命名规范
   - 定期清理不再需要的代理

2. **版本控制**
   - 使用版本控制系统管理配置
   - 记录重要变更和更新
   - 备份关键代理配置

3. **共享协作**
   - 与团队成员共享代理配置
   - 建立代理开发和维护流程
   - 收集团队反馈持续改进

## 故障排除

### 常见问题

#### 代理无响应

**问题**: 代理长时间没有回应
**解决方案**:
1. 检查网络连接
2. 验证 API 密钥有效性
3. 查看服务器日志
4. 重启 Khoj 服务

#### 代理回答不准确

**问题**: 代理提供的信息不准确或不相关
**解决方案**:
1. 检查代理配置是否正确
2. 验证输入工具权限
3. 调整模型温度参数
4. 更新代理个性描述

#### 代理创建失败

**问题**: 无法创建新代理
**解决方案**:
1. 检查配置文件语法
2. 验证必需字段是否完整
3. 确认代理标识符唯一性
4. 查看系统日志获取详细错误信息

### 性能优化

#### 响应速度优化

```yaml
# 优化代理配置以提高响应速度
chat_model:
  name: "gpt-4o-mini"  # 使用更快的模型
  max_tokens: 500      # 限制输出长度
  temperature: 0.3      # 降低创造性以提高一致性
```

#### 资源使用优化

```yaml
# 优化资源使用
tools:
  - name: "web_search"
    config:
      max_results: 3   # 减少搜索结果数量
  - name: "code_execution"
    config:
      timeout: 15      # 缩短代码执行超时时间
```

## 下一步

深入了解代理系统的其他方面：

1. [探索内置代理](#内置代理) - 了解预设代理的功能
2. [创建自定义代理](#自定义代理) - 开发满足特定需求的代理
3. [管理代理集合](#代理管理) - 组织和维护代理库
4. [集成外部工具](#集成外部工具) - 扩展代理能力

如需更多帮助，请查看[开发指南](../development/overview.md)或在社区寻求支持。