# 本地运行

## 先决条件

安装运行时依赖项。此命令应安装所有开发依赖项。

```bash
yarn install
```

运行应用程序

```bash
yarn start
```

# 部署Electron应用

## 先决条件

安装ToDesktop CLI。完整文档可以在此处找到：https://www.npmjs.com/package/@todesktop/cli

```bash
yarn global add @todesktop/cli
```

配置`todesktop.json`文件。根据应用程序ID填写`id`。

## 构建

这将提示您登录。它会为所有平台触发构建。

```bash
todesktop build
```

如果您收到命令未找到的错误，请确保您的`yarn`全局bin目录在您的`PATH`环境变量中。您可以通过运行`yarn global bin`找到全局bin目录的位置。将此行添加到您的`.bashrc`或`.zshrc`文件中：`export PATH="$PATH:$(yarn global bin)"`。
