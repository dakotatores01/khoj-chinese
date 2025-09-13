# Django应用

Khoj主要使用Django作为后端框架，因为它强大的ORM和管理员界面。Django应用位于`src/app`目录。我们有一个已安装的应用，在`/database/`目录下。这个应用负责所有数据库相关的操作，并包含我们所有的模型。您可以在[此处](https://docs.djangoproject.com/en/4.2/)找到详细的Django文档🌈。

## 设置 (Docker)

### 先决条件
1. 确保已安装[Docker](https://docs.docker.com/get-docker/)。
2. 确保已安装[Docker Compose](https://docs.docker.com/compose/install/)。

### 运行

使用根目录中的`docker-compose.yml`文件，您可以使用以下命令运行Khoj应用：
```bash
docker-compose up
```

## 设置 (本地)

### 安装Postgres (带有PgVector)

#### MacOS
- 安装[Postgres.app](https://postgresapp.com/)。

#### Debian, Ubuntu
来自[官方说明](https://wiki.postgresql.org/wiki/Apt)

```bash
sudo apt install -y postgresql-common
sudo /usr/share/postgresql-common/pgdg/apt.postgresql.org.sh
sudo apt install postgres-16 postgresql-16-pgvector
```

#### Windows
- 使用[推荐的安装程序](https://www.postgresql.org/download/windows/)

#### 从源码
1. 按照说明[安装Postgres](https://www.postgresql.org/download/)
2. 如果需要手动安装，请按照说明[安装PgVector](https://github.com/pgvector/pgvector#installation)。为方便起见，下面复制了说明。

```bash
cd /tmp
git clone --branch v0.5.1 https://github.com/pgvector/pgvector.git
cd pgvector
make
make install # 可能需要sudo
```

### 创建Khoj数据库

#### MacOS
```bash
createdb khoj -U postgres
```

#### Debian, Ubuntu
```bash
sudo -u postgres createdb khoj
```

- [可选] 设置默认postgres用户密码
  - 使用`psql`执行`ALTER USER postgres PASSWORD 'my_secure_password';`
  - 在终端中运行`export $POSTGRES_PASSWORD=my_secure_password`，供Khoj稍后使用

### 安装Khoj

```bash
uv sync --all-extras
```

### 创建Khoj数据库迁移

此命令将为数据库应用创建迁移。每当向数据库应用添加新的数据库模型或修改现有的数据库模型（更新或删除）时，都应运行此命令。

```bash
python3 src/manage.py makemigrations
```

### 运行Khoj数据库迁移

此命令将在您的应用中运行任何待处理的迁移。
```bash
python3 src/manage.py migrate
```

### 启动Khoj服务器

虽然我们使用Django作为ORM，但我们仍在使用FastAPI服务器作为API。此命令会在后端自动搭建Django应用。

*注意：匿名模式绕过本地单用户使用的身份验证。*

```bash
python3 src/khoj/main.py --anonymous-mode
```
