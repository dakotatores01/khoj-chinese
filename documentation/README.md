# 网站

本网站使用[Docusaurus](https://docusaurus.io/)构建，这是一个现代化的静态网站生成器。

### 安装

```
$ yarn
```

### 本地开发

```
$ yarn start
```

此命令启动本地开发服务器并打开浏览器窗口。大多数更改都会实时反映，无需重启服务器。

### 构建

```
$ yarn build
```

此命令将静态内容生成到`build`目录中，可以使用任何静态内容托管服务来提供服务。

### 部署

使用SSH：

```
$ USE_SSH=true yarn deploy
```

不使用SSH：

```
$ GIT_USER=<您的GitHub用户名> yarn deploy
```

如果您使用GitHub Pages进行托管，此命令是构建网站并推送到`gh-pages`分支的便捷方式。
