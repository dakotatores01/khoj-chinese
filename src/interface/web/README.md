这是一个[Next.js](https://nextjs.org/)项目。

## 开始使用

首先，安装依赖项：

```bash
bun install
```

如果您遇到任何依赖项链接问题，可以尝试运行：

```bash
bun add next
```

### 运行开发服务器：

```bash
bun dev
```

确保`next.config.mjs`中的`rewrites`为您的环境正确设置。重写用于将请求代理到API服务器。

```js
    rewrites: async () => {
        return [
            {
                source: '/api/:path*',
                destination: 'http://localhost:42110/api/:path*',
            },
        ];
    },
```

`destination`应该是API服务器的URL。

在浏览器中打开[http://localhost:3000](http://localhost:3000)查看结果。

您可以通过修改任何`.tsx`页面开始编辑页面。编辑文件时页面会自动更新。

### 测试构建文件

我们设置了一个用于构建和服务构建文件的实用程序命令。这对于在本地测试生产构建很有用。

1. 导出代码
   要构建文件一次并提供服务，请运行：

```bash
bun export
```

如果您使用Windows：

```bash
bun windowsexport
```

2. 持续构建代码

要持续构建文件并提供服务，请运行：

```bash
bun watch
```

如果您使用Windows：

```bash
bun windowswatch
```

现在您应该能够从Khoj应用在http://localhost:42110/加载您的自定义页面。要提供任何构建文件的服务，您应该像这样更新`web_client.py`中的路由，其中`new_file`是您在此仓库中添加的新页面：

```python
@web_client.post("/new_route", response_class=FileResponse)
@requires(["authenticated"], redirect="login_page")
def index_post(request: Request):

    return templates.TemplateResponse(
        "new_file/index.html",
        context={
            "request": request,
        },
    )
```

## 了解更多

要了解更多关于Next.js的信息，请查看以下资源：

- [Next.js文档](https://nextjs.org/docs) - 了解Next.js的特性和API。
- [Next.js App Router](https://nextjs.org/docs/app) - 了解Next.js路由器。
- [学习Next.js](https://nextjs.org/learn) - 一个交互式的Next.js教程。

您可以查看[Next.js GitHub仓库](https://github.com/vercel/next.js/) - 您的反馈和贡献是受欢迎的！
