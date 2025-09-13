# 自动化任务系统

Khoj 的自动化任务系统允许用户创建定时任务和智能提醒，让 AI 助手自动执行重复性工作。本文档详细介绍自动化系统的各个方面。

## 目录

- [自动化概述](#自动化概述)
- [创建自动化任务](#创建自动化任务)
- [任务调度](#任务调度)
- [任务执行](#任务执行)
- [任务管理](#任务管理)
- [高级自动化](#高级自动化)
- [最佳实践](#最佳实践)

## 自动化概述

### 什么是自动化任务？

自动化任务是预先定义的指令，Khoj 会在指定的时间自动执行这些指令。它们可以是简单的提醒，也可以是复杂的多步骤操作。

### 自动化的优势

1. **节省时间**: 自动执行重复性任务
2. **提高效率**: 减少手动操作和干预
3. **智能提醒**: 基于上下文提供个性化提醒
4. **灵活调度**: 支持多种调度方式和频率

### 自动化应用场景

- **日常提醒**: 喝水、休息、服药提醒
- **工作流程**: 每日报告生成、数据备份
- **学习计划**: 学习进度跟踪、复习提醒
- **健康管理**: 运动记录、饮食建议
- **项目管理**: 任务截止提醒、进度更新

## 创建自动化任务

### 使用自然语言创建

最简单的创建方式是使用自然语言：

```
/automated_task 每天下午3点提醒我喝水
/automated_task 每个工作日早上9点发送今日待办事项
/automated_task 每周一上午10点生成上周工作报告
```

### Web 界面创建

通过 Web 界面创建自动化任务：

1. **访问自动化页面**
   - 打开 Khoj Web 界面
   - 点击左侧导航栏的 "Automation" 图标

2. **创建新任务**
   - 点击 "Create New Task" 按钮
   - 填写任务信息

3. **配置任务参数**
   - 设置任务查询
   - 定义调度时间
   - 配置通知方式

4. **保存任务**
   - 点击 "Save" 按钮完成创建

### API 创建

使用 API 创建自动化任务：

```bash
# 创建自动化任务
curl -X POST "http://localhost:8000/api/automation" \
     -H "Authorization: Bearer YOUR_API_KEY" \
     -H "Content-Type: application/json" \
     -d '{
           "query": "/automated_task 每天提醒我检查邮箱",
           "schedule": "0 9 * * *",  # 每天上午9点
           "subject": "每日邮箱检查提醒"
         }'
```

### 命令行创建

```bash
# 创建自动化任务
khoj automation create \
  --query "/automated_task 每周五生成周报" \
  --schedule "0 17 * * 5" \
  --subject "每周五报告生成"
```

## 任务调度

### Cron 表达式

Khoj 使用标准的 Cron 表达式来定义任务调度：

```
* * * * *
│ │ │ │ │
│ │ │ │ └── 星期几 (0-7, 0和7都表示周日)
│ │ │ └──── 月份 (1-12)
│ │ └────── 日期 (1-31)
│ └──────── 小时 (0-23)
└────────── 分钟 (0-59)
```

#### 常用 Cron 表达式

| 表达式 | 含义 |
|--------|------|
| `0 9 * * *` | 每天上午9点 |
| `0 0 * * 1` | 每周一午夜 |
| `0 */2 * * *` | 每2小时 |
| `0 0 1 * *` | 每月第一天 |
| `0 0 * * 0` | 每周日午夜 |
| `0 0 1 1 *` | 每年1月1日 |

### 自然语言调度

Khoj 支持自然语言描述调度时间：

```python
# 示例调度表达式
schedules = [
    "每天上午9点",
    "每工作日下午3点",
    "每周一上午10点",
    "每月第一天",
    "每季度第一个周一",
    "每年新年"
]
```

### 调度解析

Khoj 使用智能算法解析自然语言调度：

```python
# 调度解析示例
from khoj.utils.scheduler import parse_schedule

# 解析自然语言调度
cron_expression = parse_schedule("每天下午2点")
# 返回: "0 14 * * *"

# 解析复杂调度
cron_expression = parse_schedule("每工作日上午9点和下午3点")
# 返回: "0 9,15 * * 1-5"
```

### 调度验证

```python
# 验证 Cron 表达式
from khoj.utils.scheduler import validate_cron

is_valid = validate_cron("0 9 * * *")
# 返回: True

is_valid = validate_cron("invalid cron")
# 返回: False
```

## 任务执行

### 执行流程

自动化任务的执行流程如下：

1. **任务触发**: 调度器检测到任务执行时间
2. **上下文准备**: 准备任务执行所需的上下文
3. **查询处理**: 处理自动化查询
4. **AI 响应生成**: 生成 AI 响应
5. **通知发送**: 发送通知给用户
6. **结果记录**: 记录执行结果

### 执行上下文

任务执行时会考虑以下上下文：

#### 时间上下文

```python
# 时间上下文示例
context = {
    "current_time": "2024-01-15 15:00:00",
    "day_of_week": "Monday",
    "current_date": "2024-01-15",
    "current_month": "January"
}
```

#### 用户上下文

```python
# 用户上下文示例
context = {
    "username": "张三",
    "user_preferences": {
        "timezone": "Asia/Shanghai",
        "language": "zh-CN"
    }
}
```

#### 位置上下文

```python
# 位置上下文示例
context = {
    "location": {
        "city": "北京",
        "region": "北京市",
        "country": "中国"
    }
}
```

### 执行示例

#### 简单提醒任务

```python
# 任务配置
task = {
    "query": "/automated_task 每天提醒我喝水",
    "schedule": "0 */2 * * *",  # 每2小时
    "subject": "喝水提醒"
}

# 执行结果
response = """
记得喝水哦！保持身体水分充足对健康很重要。
建议每次饮用200-300毫升水。
"""
```

#### 复杂报告任务

```python
# 任务配置
task = {
    "query": "/automated_task 每周一生成上周工作报告",
    "schedule": "0 9 * * 1",  # 每周一上午9点
    "subject": "上周工作报告"
}

# 执行结果
response = """
# 上周工作报告 (2024-01-08 至 2024-01-14)

## 完成的任务
1. 完成了项目A的需求分析
2. 修复了项目B的3个关键bug
3. 参加了团队技术分享会

## 进行中的任务
1. 项目A的设计阶段 (70% 完成)
2. 项目C的开发初期 (30% 完成)

## 下周计划
1. 完成项目A的原型设计
2. 开始项目C的核心功能开发
3. 准备季度技术分享材料
"""
```

## 任务管理

### 查看任务列表

#### Web 界面

1. 访问自动化页面
2. 查看所有任务列表
3. 点击任务查看详细信息

#### 命令行

```bash
# 列出所有自动化任务
khoj automation list

# 查看特定任务详情
khoj automation info task-id-123
```

#### API

```bash
# 获取任务列表
curl "http://localhost:8000/api/automation" \
     -H "Authorization: Bearer YOUR_API_KEY"
```

### 编辑任务

#### Web 界面编辑

1. 进入任务详情页面
2. 点击 "Edit" 按钮
3. 修改任务配置
4. 点击 "Save" 保存更改

#### 命令行编辑

```bash
# 编辑任务
khoj automation update task-id-123 \
  --schedule "0 10 * * *" \
  --subject "更新后的提醒"
```

#### API 编辑

```bash
# 更新任务
curl -X PUT "http://localhost:8000/api/automation/task-id-123" \
     -H "Authorization: Bearer YOUR_API_KEY" \
     -H "Content-Type: application/json" \
     -d '{
           "schedule": "0 10 * * *",
           "subject": "更新后的提醒"
         }'
```

### 删除任务

#### Web 界面删除

1. 进入任务详情页面
2. 点击 "Delete" 按钮
3. 确认删除操作

#### 命令行删除

```bash
# 删除任务
khoj automation delete task-id-123
```

#### API 删除

```bash
# 删除任务
curl -X DELETE "http://localhost:8000/api/automation/task-id-123" \
     -H "Authorization: Bearer YOUR_API_KEY"
```

### 任务状态监控

#### 执行历史

```bash
# 查看任务执行历史
khoj automation history task-id-123

# API 获取执行历史
curl "http://localhost:8000/api/automation/task-id-123/history" \
     -H "Authorization: Bearer YOUR_API_KEY"
```

#### 执行统计

```python
# 任务执行统计
statistics = {
    "total_executions": 45,
    "successful_executions": 43,
    "failed_executions": 2,
    "average_execution_time": "2.3s",
    "last_execution": "2024-01-15 09:00:00"
}
```

## 高级自动化

### 条件触发任务

支持基于条件的任务触发：

```python
# 条件触发示例
conditional_task = {
    "query": "/automated_task 如果今天是周五且天气晴朗，提醒我去公园散步",
    "conditions": [
        {
            "type": "day_of_week",
            "value": "Friday"
        },
        {
            "type": "weather",
            "condition": "sunny"
        }
    ],
    "schedule": "0 18 * * 5"  # 每周五下午6点检查
}
```

### 循环任务

支持创建循环依赖的任务链：

```python
# 循环任务示例
loop_task = {
    "query": "/automated_task 每天检查待办事项，如果有紧急任务则发送提醒",
    "schedule": "0 */4 * * *",  # 每4小时
    "loop": {
        "max_iterations": 5,
        "break_condition": "no_urgent_tasks"
    }
}
```

### 外部触发任务

支持通过外部事件触发任务：

```python
# 外部触发任务示例
external_trigger_task = {
    "query": "/automated_task 当收到重要邮件时，生成摘要并发送通知",
    "trigger": {
        "type": "email",
        "criteria": {
            "sender": "boss@company.com",
            "keywords": ["urgent", "important"]
        }
    }
}
```

### 多步骤任务

支持创建复杂的多步骤自动化任务：

```python
# 多步骤任务示例
multi_step_task = {
    "name": "月度报告生成流程",
    "steps": [
        {
            "name": "数据收集",
            "query": "收集本月所有项目进度数据",
            "order": 1
        },
        {
            "name": "数据分析",
            "query": "分析收集到的数据，识别关键指标",
            "order": 2,
            "depends_on": "数据收集"
        },
        {
            "name": "报告生成",
            "query": "基于分析结果生成月度报告",
            "order": 3,
            "depends_on": "数据分析"
        },
        {
            "name": "报告发送",
            "query": "将生成的报告发送给相关人员",
            "order": 4,
            "depends_on": "报告生成"
        }
    ],
    "schedule": "0 0 1 * *"  # 每月第一天执行
}
```

## 最佳实践

### 设计优秀自动化任务

#### 1. 明确任务目标

```python
# 好的例子
good_task = {
    "query": "/automated_task 每天上午9点提醒我查看今日重点任务",
    "purpose": "提高工作效率，确保重要任务不被遗漏"
}

# 不好的例子
bad_task = {
    "query": "/automated_task 提醒我做事情",
    "purpose": "模糊不清"
}
```

#### 2. 合理设置调度频率

```python
# 避免过于频繁的调度
avoid_frequent = "*/5 * * * *"  # 每5分钟（可能过于频繁）

# 推荐的调度频率
recommended_schedules = [
    "0 9 * * *",     # 每天上午9点
    "0 0 * * 1",     # 每周一午夜
    "0 0 1 * *",     # 每月第一天
]
```

#### 3. 提供充足的上下文

```python
# 丰富的上下文信息
rich_context_task = {
    "query": "/automated_task 根据我的学习计划和当前进度，推荐今天的学习内容",
    "context": {
        "learning_plan": "存储在我的笔记中的学习计划",
        "current_progress": "通过API获取的当前学习进度",
        "available_time": "今天的空闲时间段"
    }
}
```

### 任务组织和管理

#### 1. 分类管理任务

```bash
# 按类别组织任务
khoj automation list --category work      # 工作相关任务
khoj automation list --category personal  # 个人相关任务
khoj automation list --category health    # 健康相关任务
```

#### 2. 使用有意义的命名

```python
# 好的命名
good_names = [
    "daily_water_reminder",      # 每日喝水提醒
    "weekly_report_generator",   # 周报生成器
    "monthly_learning_review"    # 月度学习回顾
]

# 不好的命名
bad_names = [
    "task1",    # 太模糊
    "reminder",  # 不具体
    "auto123"    # 无意义
]
```

#### 3. 定期审查和优化

```bash
# 定期审查任务执行情况
khoj automation review --period monthly

# 优化低效任务
khoj automation optimize task-id-123
```

### 性能优化

#### 1. 减少不必要的执行

```python
# 添加执行条件
conditional_execution = {
    "query": "/automated_task 只在工作日提醒我晨间冥想",
    "schedule": "0 8 * * *",
    "conditions": [
        {
            "type": "workday",
            "value": True
        }
    ]
}
```

#### 2. 优化查询性能

```python
# 简化查询以提高执行速度
optimized_query = "/automated_task 生成今日待办事项摘要"  # 简洁明了

# 避免过于复杂的查询
complex_query = "/automated_task 分析过去三个月的所有数据并生成详细的统计报告"  # 可能执行缓慢
```

#### 3. 合理使用资源

```python
# 限制资源使用
resource_limited_task = {
    "query": "/automated_task 生成简单的日报",
    "resource_limits": {
        "max_execution_time": 30,  # 最大执行时间30秒
        "max_memory_usage": "100MB"  # 最大内存使用100MB
    }
}
```

## 故障排除

### 常见问题

#### 任务未按预期执行

**问题**: 任务没有在预定时间执行
**检查清单**:
1. 验证 Cron 表达式是否正确
2. 检查 Khoj 服务是否正常运行
3. 确认系统时间和时区设置
4. 查看任务执行日志

```bash
# 检查任务状态
khoj automation status task-id-123

# 查看执行日志
khoj automation logs task-id-123
```

#### 任务执行失败

**问题**: 任务执行时报错
**解决方案**:
1. 查看错误日志获取详细信息
2. 检查网络连接和 API 密钥
3. 验证查询语法和参数
4. 确认依赖服务可用性

```bash
# 查看详细错误信息
khoj automation logs task-id-123 --level ERROR
```

#### 通知未送达

**问题**: 任务执行成功但未收到通知
**检查清单**:
1. 验证通知配置（邮箱、手机号等）
2. 检查垃圾邮件文件夹
3. 确认通知服务正常运行
4. 查看通知发送日志

### 调试技巧

#### 启用详细日志

```bash
# 启用自动化任务详细日志
export KHOJ_LOG_LEVEL=DEBUG
export KHOJ_AUTOMATION_DEBUG=true

# 重启 Khoj 服务
khoj restart
```

#### 手动触发任务

```bash
# 手动执行任务以测试
khoj automation run task-id-123 --now

# 强制执行任务（忽略调度）
khoj automation run task-id-123 --force
```

#### 模拟执行环境

```python
# 模拟任务执行环境进行测试
from khoj.automation.simulator import simulate_execution

test_result = simulate_execution(
    task_id="task-id-123",
    mock_context={
        "current_time": "2024-01-15 09:00:00",
        "user_data": "模拟用户数据"
    }
)
```

## 集成和扩展

### 与其他服务集成

#### 邮件集成

```python
# 邮件通知配置
email_notification = {
    "type": "email",
    "recipients": ["user@example.com"],
    "smtp_config": {
        "host": "smtp.gmail.com",
        "port": 587,
        "username": "your-email@gmail.com",
        "password": "your-app-password"
    }
}
```

#### 短信集成

```python
# 短信通知配置
sms_notification = {
    "type": "sms",
    "recipients": ["+8613800138000"],
    "provider": "twilio",
    "twilio_config": {
        "account_sid": "your-account-sid",
        "auth_token": "your-auth-token",
        "from_number": "+1234567890"
    }
}
```

#### Slack 集成

```python
# Slack 通知配置
slack_notification = {
    "type": "slack",
    "webhook_url": "https://hooks.slack.com/services/YOUR/SLACK/WEBHOOK",
    "channel": "#general",
    "username": "Khoj Bot"
}
```

### 自定义通知渠道

```python
# 自定义通知渠道示例
class CustomNotificationChannel:
    def __init__(self, config):
        self.config = config
        
    def send(self, message, subject=None):
        """发送自定义通知"""
        # 实现通知发送逻辑
        pass
        
    def validate_config(self):
        """验证配置"""
        # 实现配置验证逻辑
        return True

# 注册自定义通知渠道
from khoj.automation.notification import register_channel
register_channel("custom", CustomNotificationChannel)
```

## 安全考虑

### 任务安全

#### 1. 权限控制

```python
# 任务权限配置
task_permissions = {
    "owners": ["user1@example.com"],
    "viewers": ["team@example.com"],
    "editors": ["admin@example.com"]
}
```

#### 2. 敏感信息保护

```python
# 安全地存储敏感信息
secure_task_config = {
    "query": "/automated_task 使用加密的API密钥获取数据",
    "secrets": {
        "api_key": "encrypted_api_key_value",
        "database_password": "encrypted_db_password"
    }
}
```

#### 3. 执行环境隔离

```python
# 任务执行环境配置
execution_environment = {
    "sandboxed": True,
    "allowed_domains": ["api.example.com", "data.company.com"],
    "blocked_functions": ["os.system", "subprocess.call"],
    "timeout": 60  # 最大执行时间60秒
}
```

### 数据安全

#### 1. 数据加密

```python
# 敏感数据加密配置
encryption_config = {
    "at_rest": True,
    "in_transit": True,
    "algorithm": "AES-256-GCM",
    "key_management": "hardware_security_module"
}
```

#### 2. 访问审计

```python
# 访问审计配置
audit_config = {
    "log_all_executions": True,
    "log_failed_attempts": True,
    "retention_period": "90d",
    "alert_on_suspicious_activity": True
}
```

## 监控和告警

### 执行监控

#### 1. 实时监控

```python
# 实时监控配置
monitoring_config = {
    "dashboard": True,
    "metrics_collection": True,
    "performance_tracking": True,
    "error_reporting": True
}
```

#### 2. 告警设置

```python
# 告警配置
alert_config = {
    "failure_threshold": 3,
    "notification_channels": ["email", "slack"],
    "escalation_policy": {
        "level_1": "30m",  # 30分钟后通知管理员
        "level_2": "1h",   # 1小时后通知高级管理员
        "level_3": "4h"    # 4小时后通知CTO
    }
}
```

### 性能监控

#### 1. 执行时间监控

```python
# 性能监控配置
performance_monitoring = {
    "track_execution_time": True,
    "track_resource_usage": True,
    "baseline_performance": "30s",
    "alert_on_degradation": "2x"  # 性能下降2倍时告警
}
```

#### 2. 资源使用监控

```python
# 资源使用监控
resource_monitoring = {
    "cpu_usage_limit": "80%",
    "memory_usage_limit": "500MB",
    "disk_space_limit": "1GB",
    "network_bandwidth_limit": "10MB/s"
}
```

## 下一步

深入了解自动化系统的其他方面：

1. [探索内置自动化任务](#内置自动化任务) - 了解预设任务模板
2. [创建自定义任务](#自定义自动化任务) - 开发满足特定需求的任务
3. [管理任务集合](#任务管理) - 组织和维护任务库
4. [集成外部服务](#集成和扩展) - 扩展自动化能力

如需更多帮助，请查看[开发指南](../development/overview.md)或在社区寻求支持。