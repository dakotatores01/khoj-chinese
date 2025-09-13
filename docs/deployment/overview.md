# 部署指南

本指南介绍如何在不同环境中部署 Khoj，包括本地安装、Docker 部署、云端部署和生产环境配置。

## 目录

- [快速开始](#快速开始)
- [本地部署](#本地部署)
- [Docker 部署](#docker-部署)
- [云端部署](#云端部署)
- [生产环境配置](#生产环境配置)
- [监控和维护](#监控和维护)

## 快速开始

### 一键部署脚本

对于 Linux/macOS 用户，可以使用快速部署脚本：

```bash
# 下载并运行部署脚本
curl -fsSL https://raw.githubusercontent.com/khoj-ai/khoj/main/scripts/install.sh | bash

# 或手动下载
wget https://raw.githubusercontent.com/khoj-ai/khoj/main/scripts/install.sh
chmod +x install.sh
./install.sh
```

### Docker Compose（推荐）

最简单的部署方式：

```yaml
# docker-compose.yml
version: '3.8'
services:
  khoj:
    image: khoj/khoj:latest
    ports:
      - "8000:8000"
    volumes:
      - ./data:/app/data
      - ./config:/app/config
    environment:
      - KHOJ_ADMIN_EMAIL=admin@example.com
    restart: unless-stopped
```

```bash
# 启动服务
docker-compose up -d

# 访问 http://localhost:8000
```

## 本地部署

### 系统要求

#### 最低配置
- **CPU**: 2 核心
- **内存**: 4GB RAM
- **存储**: 10GB 可用空间
- **网络**: 稳定互联网连接

#### 推荐配置
- **CPU**: 4+ 核心
- **内存**: 8GB+ RAM
- **存储**: 50GB+ SSD
- **GPU**: NVIDIA GPU（可选，用于加速）

### 安装步骤

#### 1. 安装 Python

```bash
# Ubuntu/Debian
sudo apt update
sudo apt install python3 python3-pip python3-venv

# macOS
brew install python3

# Windows
# 下载 Python 安装程序从 python.org
```

#### 2. 创建虚拟环境

```bash
# 创建虚拟环境
python3 -m venv khoj-env

# 激活虚拟环境
source khoj-env/bin/activate  # Linux/macOS
# khoj-env\Scripts\activate  # Windows
```

#### 3. 安装 Khoj

```bash
# 从 PyPI 安装
pip install khoj

# 或从源码安装（开发版）
git clone https://github.com/khoj-ai/khoj.git
cd khoj
pip install -e .
```

#### 4. 初始化数据库

```bash
# 运行数据库迁移
khoj migrate

# 创建超级用户（可选）
khoj createsuperuser
```

#### 5. 启动服务

```bash
# 启动 Khoj 服务
khoj runserver

# 或使用 gunicorn（生产环境）
gunicorn khoj.main:app -w 4 -k uvicorn.workers.UvicornWorker --bind 0.0.0.0:8000
```

## Docker 部署

### 单容器部署

```bash
# 拉取官方镜像
docker pull khoj/khoj:latest

# 运行容器
docker run -d \
  --name khoj \
  -p 8000:8000 \
  -v $(pwd)/data:/app/data \
  -e KHOJ_ADMIN_EMAIL=your@email.com \
  khoj/khoj:latest
```

### Docker Compose 部署

```yaml
# docker-compose.yml
version: '3.8'
services:
  khoj:
    image: khoj/khoj:latest
    ports:
      - "8000:8000"
    volumes:
      - ./data:/app/data
      - ./config:/app/config
      - ./logs:/app/logs
    environment:
      - KHOJ_ADMIN_EMAIL=admin@example.com
      - KHOJ_SECRET_KEY=your-secret-key
      - DJANGO_SETTINGS_MODULE=khoj.app.settings
    depends_on:
      - db
    restart: unless-stopped

  db:
    image: postgres:15
    environment:
      - POSTGRES_DB=khoj
      - POSTGRES_USER=khoj
      - POSTGRES_PASSWORD=khoj_password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    restart: unless-stopped

volumes:
  postgres_data:
```

### 自定义 Docker 镜像

```dockerfile
# Dockerfile
FROM khoj/khoj:latest

# 添加自定义配置
COPY config/khoj.yml /app/config/
COPY data/ /app/data/

# 设置环境变量
ENV KHOJ_CONFIG_PATH=/app/config/khoj.yml
ENV KHOJ_DATA_PATH=/app/data

# 暴露端口
EXPOSE 8000

# 启动命令
CMD ["khoj", "runserver", "0.0.0.0:8000"]
```

## 云端部署

### Heroku 部署

1. **创建 Heroku 应用**
```bash
heroku create your-khoj-app
```

2. **添加构建包**
```bash
heroku buildpacks:add heroku/python
heroku buildpacks:add https://github.com/heroku/heroku-buildpack-nginx
```

3. **设置环境变量**
```bash
heroku config:set KHOJ_SECRET_KEY=your-secret-key
heroku config:set KHOJ_ADMIN_EMAIL=your@email.com
```

4. **部署**
```bash
git push heroku main
```

### AWS EC2 部署

```bash
# 更新系统
sudo apt update && sudo apt upgrade -y

# 安装 Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# 克隆并运行
git clone https://github.com/khoj-ai/khoj.git
cd khoj
sudo docker-compose up -d

# 配置防火墙
sudo ufw allow 8000
```

### Google Cloud Run

```yaml
# cloud-run.yaml
apiVersion: serving.knative.dev/v1
kind: Service
metadata:
  name: khoj
spec:
  template:
    spec:
      containers:
      - image: khoj/khoj:latest
        env:
        - name: KHOJ_SECRET_KEY
          value: "your-secret-key"
        ports:
        - containerPort: 8000
```

### Vercel 部署（仅前端）

```json
// vercel.json
{
  "buildCommand": "npm run build",
  "outputDirectory": "out",
  "installCommand": "npm install",
  "framework": "nextjs",
  "env": {
    "KHOJ_API_URL": "https://your-khoj-api.com"
  }
}
```

## 生产环境配置

### 反向代理设置

#### Nginx 配置

```nginx
# /etc/nginx/sites-available/khoj
server {
    listen 80;
    server_name your-domain.com;

    # SSL 配置（强烈推荐）
    listen 443 ssl http2;
    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;

    # 安全头
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;

    # 代理设置
    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_For;
        proxy_set_header X-Forwarded-Proto $scheme;

        # WebSocket 支持
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }

    # 静态文件缓存
    location /static/ {
        alias /path/to/khoj/static/;
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

#### Caddy 配置

```caddyfile
# Caddyfile
your-domain.com {
    reverse_proxy localhost:8000

    # 自动 HTTPS
    tls your@email.com

    # 安全头
    header {
        X-Frame-Options "SAMEORIGIN"
        X-Content-Type-Options "nosniff"
        X-XSS-Protection "1; mode=block"
    }
}
```

### 数据库配置

#### PostgreSQL 设置

```bash
# 创建数据库
createdb khoj

# 创建用户
createuser khoj_user
psql -c "ALTER USER khoj_user PASSWORD 'secure_password';"

# 授予权限
psql -c "GRANT ALL PRIVILEGES ON DATABASE khoj TO khoj_user;"
```

#### 环境变量

```bash
# .env 文件
DATABASE_URL=postgresql://khoj_user:secure_password@localhost:5432/khoj
REDIS_URL=redis://localhost:6379/0
SECRET_KEY=your-very-secure-secret-key
KHOJ_ADMIN_EMAIL=admin@yourdomain.com
```

### 安全配置

#### 环境变量设置

```bash
# 生产环境必须设置
export SECRET_KEY="your-256-bit-secret"
export KHOJ_ADMIN_EMAIL="admin@yourdomain.com"
export ALLOWED_HOSTS="yourdomain.com,www.yourdomain.com"
export KHOJ_DOMAIN="yourdomain.com"

# 可选设置
export KHOJ_NO_HTTPS="false"  # 强制 HTTPS
export KHOJ_DEBUG="false"     # 禁用调试模式
```

#### 文件权限

```bash
# 设置正确的文件权限
sudo chown -R khoj:khoj /path/to/khoj/data
sudo chmod -R 750 /path/to/khoj/data

# 保护敏感文件
sudo chmod 600 /path/to/khoj/.env
sudo chmod 600 /path/to/khoj/ssl/*.key
```

### 性能优化

#### Gunicorn 配置

```python
# gunicorn.conf.py
bind = "0.0.0.0:8000"
workers = 4  # CPU 核心数的 2-4 倍
worker_class = "uvicorn.workers.UvicornWorker"
worker_connections = 1000
max_requests = 1000
max_requests_jitter = 50
timeout = 30
keepalive = 10
```

#### 系统调优

```bash
# 增加文件描述符限制
echo "fs.file-max = 100000" | sudo tee -a /etc/sysctl.conf
echo "* soft nofile 100000" | sudo tee -a /etc/security/limits.conf
echo "* hard nofile 100000" | sudo tee -a /etc/security/limits.conf

# 应用更改
sudo sysctl -p
```

## 监控和维护

### 健康检查

```bash
# API 健康检查
curl http://localhost:8000/api/health

# 数据库连接检查
curl http://localhost:8000/api/content/size

# 搜索功能检查
curl "http://localhost:8000/api/search?q=test"
```

### 日志管理

#### 日志轮转

```bash
# /etc/logrotate.d/khoj
/path/to/khoj/logs/*.log {
    daily
    missingok
    rotate 52
    compress
    delaycompress
    notifempty
    create 644 khoj khoj
    postrotate
        systemctl reload khoj
    endscript
}
```

#### 集中日志

```yaml
# 使用 ELK Stack 或类似工具
logging:
  version: 1
  disable_existing_loggers: false
  formatters:
    json:
      class: pythonjsonlogger.jsonlogger.JsonFormatter
      format: "%(asctime)s %(name)s %(levelname)s %(message)s"
  handlers:
    elasticsearch:
      class: cmreslogging.handlers.ElasticsearchHandler
      formatter: json
      hosts: ['localhost:9200']
      index_name: "khoj-logs"
```

### 备份策略

#### 数据库备份

```bash
# 每日备份脚本
#!/bin/bash
BACKUP_DIR="/path/to/backups"
DATE=$(date +%Y%m%d_%H%M%S)

# PostgreSQL 备份
pg_dump -U khoj_user -h localhost khoj > $BACKUP_DIR/khoj_$DATE.sql

# 压缩备份
gzip $BACKUP_DIR/khoj_$DATE.sql

# 删除 30 天前的备份
find $BACKUP_DIR -name "khoj_*.sql.gz" -mtime +30 -delete
```

#### 文件备份

```bash
# 数据目录备份
rsync -avz --delete /path/to/khoj/data/ /backup/khoj-data/

# 配置备份
cp /path/to/khoj/config/khoj.yml /backup/config/
```

### 监控工具

#### Prometheus + Grafana

```yaml
# prometheus.yml
scrape_configs:
  - job_name: 'khoj'
    static_configs:
      - targets: ['localhost:8000']
    metrics_path: '/metrics'
```

#### 应用监控

```python
# 在 Django settings 中添加
INSTALLED_APPS = [
    # ... 其他应用
    'django_prometheus',
    'health_check',
    'health_check.db',
    'health_check.cache',
    'health_check.storage',
]
```

### 更新和维护

#### 自动更新

```bash
# 更新脚本
#!/bin/bash
cd /path/to/khoj

# 备份当前版本
cp -r . .backup

# 拉取最新代码
git pull origin main

# 更新依赖
pip install -r requirements.txt

# 运行迁移
python manage.py migrate

# 重启服务
sudo systemctl restart khoj

# 验证服务
curl -f http://localhost:8000/api/health
```

#### 回滚策略

```bash
# 回滚脚本
cd /path/to/khoj

# 停止服务
sudo systemctl stop khoj

# 恢复备份
cp -r .backup/* .

# 重启服务
sudo systemctl start khoj
```

## 故障排除

### 常见部署问题

#### 服务启动失败

**问题**: Khoj 服务无法启动
**检查**:
1. 查看日志：`tail -f /path/to/khoj/logs/khoj.log`
2. 检查端口占用：`netstat -tlnp | grep 8000`
3. 验证配置文件：`khoj check`
4. 检查依赖：`pip list | grep khoj`

#### 数据库连接错误

**问题**: 无法连接到数据库
**检查**:
1. PostgreSQL 服务状态：`sudo systemctl status postgresql`
2. 连接配置：`psql -U khoj_user -d khoj -h localhost`
3. 环境变量：`echo $DATABASE_URL`

#### 内存不足错误

**问题**: 应用崩溃，内存不足
**解决方案**:
1. 增加系统内存
2. 配置 swap 文件
3. 优化 Gunicorn 工作进程数
4. 启用内存监控

### 性能问题

#### 响应时间慢

**诊断**:
```bash
# 检查系统资源
top
htop
free -h

# 检查网络连接
ping -c 4 google.com

# 检查磁盘 I/O
iostat -x 1

# 检查应用性能
curl -w "@curl-format.txt" -o /dev/null -s http://localhost:8000/api/health
```

**优化措施**:
1. 启用缓存（Redis）
2. 配置反向代理缓存
3. 优化数据库查询
4. 使用 CDN

## 下一步

部署完成后：

1. [配置数据源](../data-sources/overview.md) - 添加您的文档
2. [设置用户权限](../features/overview.md) - 配置多用户访问
3. [监控系统健康](#监控和维护) - 设置监控告警
4. [配置备份](#备份策略) - 确保数据安全

对于高级配置和定制部署，请查看[开发指南](../development/overview.md)。

如果遇到部署问题，请查看[故障排除指南](../troubleshooting/overview.md)或在社区寻求帮助。