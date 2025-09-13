# 故障排除指南

本指南帮助您诊断和解决使用 Khoj 时遇到的常见问题。包含故障排除步骤、调试技巧和最佳实践。

## 目录

- [快速诊断](#快速诊断)
- [安装问题](#安装问题)
- [启动问题](#启动问题)
- [功能问题](#功能问题)
- [性能问题](#性能问题)
- [网络问题](#网络问题)
- [数据问题](#数据问题)
- [高级调试](#高级调试)

## 快速诊断

### 系统状态检查

运行以下命令检查 Khoj 的整体健康状态：

```bash
# 检查服务状态
curl http://localhost:8000/api/health

# 检查数据库连接
python -c "import khoj; khoj.configure.initialize_server()"

# 检查磁盘空间
df -h

# 检查内存使用
free -h

# 检查进程状态
ps aux | grep khoj
```

### 日志查看

查看 Khoj 的日志文件：

```bash
# 查看最近的错误日志
tail -f ~/.khoj/logs/khoj.log

# 查看详细调试日志
tail -f ~/.khoj/logs/khoj.log | grep -i error

# 查看系统日志（Linux）
journalctl -u khoj -f
```

### 配置验证

验证配置文件：

```bash
# 检查配置文件语法
python -c "import yaml; yaml.safe_load(open('khoj.yml'))"

# 验证数据库配置
python manage.py check --database

# 测试 API 连接
curl -I http://localhost:8000/api/search?q=test
```

## 安装问题

### pip 安装失败

**问题**: `pip install khoj` 失败

**解决方案**:

1. **升级 pip**
```bash
pip install --upgrade pip
```

2. **安装系统依赖**
```bash
# Ubuntu/Debian
sudo apt install build-essential python3-dev

# macOS
xcode-select --install

# Windows
# 安装 Visual Studio Build Tools
```

3. **使用虚拟环境**
```bash
python -m venv khoj-env
source khoj-env/bin/activate  # Linux/macOS
pip install khoj
```

4. **从源码安装**
```bash
git clone https://github.com/khoj-ai/khoj.git
cd khoj
pip install -e .
```

### Docker 安装失败

**问题**: Docker 容器无法启动

**解决方案**:

1. **检查 Docker 版本**
```bash
docker --version
# 需要 Docker 20.10+ 或 Docker Compose 2.0+
```

2. **检查端口占用**
```bash
netstat -tlnp | grep 8000
# 如果端口被占用，使用不同端口
docker run -p 8001:8000 khoj/khoj
```

3. **检查权限**
```bash
# 添加用户到 docker 组
sudo usermod -aG docker $USER
# 重新登录或运行 newgrp docker
```

4. **清理 Docker 缓存**
```bash
docker system prune -a
docker volume prune
```

## 启动问题

### 服务无法启动

**问题**: Khoj 服务启动失败

**诊断步骤**:

1. **检查错误日志**
```bash
tail -n 50 ~/.khoj/logs/khoj.log
```

2. **验证 Python 环境**
```bash
python --version
which python
pip list | grep khoj
```

3. **检查配置文件**
```bash
# 测试配置文件加载
python -c "from khoj.utils.config import SearchConfig; SearchConfig()"
```

4. **检查数据库**
```bash
# 测试数据库连接
python manage.py dbshell
```

**常见解决方案**:

- **端口被占用**: 使用不同端口 `khoj --port 8001`
- **权限问题**: 以管理员身份运行或检查文件权限
- **依赖缺失**: 重新安装依赖 `pip install -r requirements.txt`
- **配置文件错误**: 检查 YAML 语法

### 数据库迁移失败

**问题**: Django 数据库迁移失败

**解决方案**:

```bash
# 重置数据库迁移
python manage.py migrate --fake khoj zero
python manage.py migrate

# 或完全重置数据库
python manage.py flush
python manage.py migrate
```

## 功能问题

### 搜索功能不工作

**问题**: 搜索返回空结果或错误

**诊断步骤**:

1. **检查索引状态**
```bash
curl http://localhost:8000/api/content/size
# 应该返回非零的文档数量
```

2. **验证文件索引**
```bash
curl http://localhost:8000/api/content/files?page=0
# 检查文件是否被正确索引
```

3. **测试搜索 API**
```bash
curl "http://localhost:8000/api/search?q=test"
```

**解决方案**:

- **重新索引内容**
```bash
curl -X POST http://localhost:8000/api/update -H "Authorization: Bearer YOUR_TOKEN"
```

- **检查文件格式支持**
```
支持: .md, .pdf, .docx, .org, .txt
不支持: .doc, .rtf, .odt (转换为支持的格式)
```

- **验证文件权限**
```bash
ls -la /path/to/your/files
# 确保 Khoj 有读取权限
```

### 聊天功能异常

**问题**: 聊天响应慢或错误

**诊断步骤**:

1. **检查 AI 模型配置**
```bash
curl http://localhost:8000/api/chat/options
```

2. **验证 API 密钥**
```bash
# 检查环境变量
echo $OPENAI_API_KEY
echo $ANTHROPIC_API_KEY
```

3. **测试模型连接**
```bash
curl -X POST http://localhost:8000/api/chat \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{"q": "Hello", "stream": false}'
```

**解决方案**:

- **配置正确的 API 密钥**
```bash
export OPENAI_API_KEY="your-key-here"
# 重新启动 Khoj
```

- **检查模型可用性**
```bash
# OpenAI 模型状态
curl https://status.openai.com/api/v2/status.json

# Anthropic 模型状态
curl https://status.anthropic.com/
```

- **调整超时设置**
```yaml
# khoj.yml
chat:
  timeout: 60  # 增加超时时间
  max_tokens: 1000
```

### 文件上传失败

**问题**: 无法上传文档

**诊断步骤**:

1. **检查文件大小**
```bash
ls -lh your-file.pdf
# 单文件最大 50MB
```

2. **验证文件类型**
```bash
file your-file.pdf
# 确保是真实的文件类型，不是伪装的
```

3. **检查上传 API**
```bash
curl -X PUT http://localhost:8000/api/content \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -F "files=@your-file.pdf"
```

**解决方案**:

- **减小文件大小**
```bash
# 压缩 PDF
gs -sDEVICE=pdfwrite -dCompatibilityLevel=1.4 -dPDFSETTINGS=/ebook -dNOPAUSE -dQUIET -dBATCH -sOutputFile=output.pdf input.pdf
```

- **转换文件格式**
```bash
# PDF 转文本
pdftotext input.pdf output.txt

# Word 转 Markdown
pandoc input.docx -o output.md
```

- **检查文件编码**
```bash
# 确保 UTF-8 编码
file -i your-file.txt
# 转换编码
iconv -f latin1 -t utf8 input.txt > output.txt
```

## 性能问题

### 响应缓慢

**问题**: 搜索和聊天响应很慢

**诊断步骤**:

1. **检查系统资源**
```bash
# CPU 使用率
top -p $(pgrep khoj)

# 内存使用
free -h

# 磁盘 I/O
iostat -x 1
```

2. **分析查询性能**
```bash
# 启用慢查询日志
export KHOJ_DEBUG=true
# 查看日志中的查询时间
```

3. **检查缓存状态**
```bash
# Redis 缓存（如果启用）
redis-cli info
```

**优化措施**:

- **启用缓存**
```yaml
# khoj.yml
cache:
  enabled: true
  backend: redis
  ttl: 3600
```

- **优化索引**
```bash
# 重新构建索引
curl -X POST http://localhost:8000/api/update?force=true
```

- **增加系统资源**
```bash
# 增加内存或 CPU 核心
# 或使用更快的存储设备
```

### 内存不足

**问题**: 应用崩溃，内存错误

**解决方案**:

1. **增加系统内存**
```bash
# 检查当前内存
free -h

# 如果是 Docker，增加容器内存限制
docker run --memory=4g khoj/khoj
```

2. **优化配置**
```yaml
# khoj.yml
embedding:
  batch_size: 10  # 减小批处理大小

search:
  max_results: 50  # 限制搜索结果数量
```

3. **启用内存监控**
```bash
# 使用内存分析工具
pip install memory-profiler
python -m memory_profiler khoj/main.py
```

### 高 CPU 使用率

**问题**: CPU 使用率持续很高

**诊断步骤**:

```bash
# 监控 CPU 使用
top -p $(pgrep khoj)

# 检查活跃进程
ps aux --sort=-%cpu | head -10
```

**解决方案**:

- **调整工作进程数**
```bash
# Gunicorn 配置
workers = 2  # 减少工作进程数
```

- **启用连接池**
```yaml
database:
  pool_size: 10
  max_overflow: 20
```

- **优化查询**
```python
# 在代码中使用 select_related 和 prefetch_related
entries = Entry.objects.select_related('user').prefetch_related('embeddings')
```

## 网络问题

### 连接超时

**问题**: API 请求超时

**诊断步骤**:

```bash
# 测试网络连接
ping -c 4 google.com

# 检查 DNS 解析
nslookup api.openai.com

# 测试 API 端点
curl -v --connect-timeout 10 https://api.openai.com/v1/models
```

**解决方案**:

- **配置代理**
```bash
export HTTP_PROXY="http://proxy.company.com:8080"
export HTTPS_PROXY="http://proxy.company.com:8080"
```

- **增加超时时间**
```yaml
api:
  timeout: 60
  retry_attempts: 3
```

- **使用本地模型**
```yaml
chat:
  model: "local-model"
  provider: "ollama"
```

### CORS 错误

**问题**: 浏览器显示 CORS 错误

**解决方案**:

1. **配置允许的域名**
```python
# settings.py
ALLOWED_HOSTS = ['localhost', '127.0.0.1', 'yourdomain.com']

CORS_ALLOWED_ORIGINS = [
    "http://localhost:3000",
    "https://yourdomain.com",
]
```

2. **检查请求头**
```javascript
// 前端请求添加正确头
headers: {
    'Content-Type': 'application/json',
    'Authorization': `Bearer ${token}`
}
```

### SSL/HTTPS 问题

**问题**: HTTPS 连接失败

**诊断步骤**:

```bash
# 检查 SSL 证书
openssl s_client -connect yourdomain.com:443

# 验证证书链
openssl verify -CAfile /etc/ssl/certs/ca-certificates.crt your-cert.pem
```

**解决方案**:

- **获取有效证书**
```bash
# 使用 Let's Encrypt
certbot certonly --webroot -w /var/www/html -d yourdomain.com

# 或使用自签名证书（仅开发环境）
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365
```

- **配置 HTTPS 重定向**
```nginx
server {
    listen 80;
    server_name yourdomain.com;
    return 301 https://$server_name$request_uri;
}
```

## 数据问题

### 索引损坏

**问题**: 搜索结果不准确或缺失

**解决方案**:

1. **重建索引**
```bash
# 强制重新索引所有内容
curl -X POST "http://localhost:8000/api/update?force=true" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

2. **清理损坏数据**
```bash
# 删除孤立记录
python manage.py delete_orphaned_entries

# 重置向量索引
python manage.py reset_embeddings
```

3. **验证数据完整性**
```python
from khoj.database.models import Entry
# 检查嵌入向量维度一致性
entries = Entry.objects.all()
for entry in entries:
    if len(entry.embeddings) != 768:  # 假设维度为 768
        print(f"Invalid embedding for entry {entry.id}")
```

### 数据同步失败

**问题**: Notion/GitHub 等数据源不同步

**诊断步骤**:

```bash
# 检查数据源状态
curl http://localhost:8000/api/content/github
curl http://localhost:8000/api/content/notion
```

**解决方案**:

- **验证 API 令牌**
```bash
# GitHub 令牌检查
curl -H "Authorization: token YOUR_TOKEN" https://api.github.com/user

# Notion 令牌检查
curl -H "Authorization: Bearer YOUR_TOKEN" https://api.notion.com/v1/users/me
```

- **检查权限**
```yaml
# GitHub 需要 repo 和 read:org 权限
# Notion 需要读取页面权限
```

- **手动触发同步**
```bash
curl -X POST "http://localhost:8000/api/update?t=github" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

## 高级调试

### 启用调试模式

```bash
# 环境变量
export KHOJ_DEBUG=true
export KHOJ_LOG_LEVEL=DEBUG

# 或配置文件
debug: true
log_level: DEBUG
```

### 性能分析

```python
# 添加性能分析
import cProfile
import pstats

def profile_function(func):
    def wrapper(*args, **kwargs):
        profiler = cProfile.Profile()
        profiler.enable()
        result = func(*args, **kwargs)
        profiler.disable()
        stats = pstats.Stats(profiler)
        stats.sort_stats('cumulative').print_stats(20)
        return result
    return wrapper
```

### 数据库查询分析

```python
# Django 查询分析
from django.db import connection
from django.conf import settings

if settings.DEBUG:
    def show_queries():
        for query in connection.queries:
            print(f"{query['time']}s: {query['sql']}")

    # 在视图函数中使用
    show_queries()
```

### 内存泄漏检测

```python
import tracemalloc

# 启用内存跟踪
tracemalloc.start()

# 获取当前内存使用
current, peak = tracemalloc.get_traced_memory()
print(f"Current memory usage: {current / 1024 / 1024:.1f} MB")
print(f"Peak memory usage: {peak / 1024 / 1024:.1f} MB")

# 获取内存分配统计
stats = tracemalloc.take_snapshot()
top_stats = stats.statistics('lineno')
for stat in top_stats[:10]:
    print(stat)
```

### 网络调试

```bash
# 使用 tcpdump 捕获网络流量
sudo tcpdump -i any -s 0 -w khoj_network.pcap host api.openai.com

# 使用 Wireshark 分析捕获的文件
# 或使用 mitmproxy 拦截 HTTPS 流量
mitmproxy --mode transparent
```

### 日志分析工具

```bash
# 使用 grep 搜索特定错误
grep -r "ERROR" ~/.khoj/logs/

# 使用 awk 分析响应时间
awk '/response_time/ {sum += $2; count++} END {print "Average:", sum/count}' khoj.log

# 使用 jq 处理 JSON 日志（如果适用）
cat khoj.log | jq '. | select(.level == "ERROR")'
```

## 获取帮助

如果以上方法都无法解决问题：

1. **收集诊断信息**
```bash
# 系统信息
uname -a
python --version
pip list | grep khoj

# Khoj 版本和配置
khoj --version
cat khoj.yml
```

2. **提交问题报告**
- GitHub Issues: [https://github.com/khoj-ai/khoj/issues](https://github.com/khoj-ai/khoj/issues)
- 提供完整的错误日志和配置信息

3. **社区支持**
- Discord: [https://discord.gg/BDgyabRM6e](https://discord.gg/BDgyabRM6e)
- 邮件: team@khoj.dev

4. **商业支持**
- 企业版用户可联系专属技术支持

## 预防措施

### 定期维护

1. **监控系统健康**
```bash
# 设置监控脚本
#!/bin/bash
# 检查服务状态
if ! curl -f http://localhost:8000/api/health; then
    echo "Khoj service is down" | mail -s "Khoj Alert" admin@example.com
fi
```

2. **定期备份**
```bash
# 自动化备份脚本
0 2 * * * /path/to/backup-script.sh
```

3. **更新依赖**
```bash
# 每月更新一次
pip install --upgrade khoj
```

### 最佳实践

- **使用版本控制**: 跟踪配置文件更改
- **监控日志**: 设置日志轮转和告警
- **定期测试**: 创建自动化测试脚本
- **文档记录**: 记录自定义配置和更改

通过遵循这个故障排除指南，您应该能够诊断和解决大部分 Khoj 使用过程中遇到的问题。如有疑问，请随时寻求社区帮助！