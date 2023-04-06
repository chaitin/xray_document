# 编写环境

## VSCode

使用 VSCode，进行一些配置后可以提供一些智能提示，方便编写 POC。

首先安装 https://marketplace.visualstudio.com/items?itemName=redhat.vscode-yaml 插件，然后在 settings 中确认 Extensions - YAML 中相关的开关已经打开。然后点击 `Edit in settings.json`，将 json 内容修改为

```javascript
{
    "yaml.schemas": {
        "https://raw.githubusercontent.com/chaitin/gamma/master/static/schema/schema-v2.json": ["fingerprint-yaml-*.yml", "poc-yaml-*.yml"]
    }
}
```

这样创建 `poc-yaml-` 开头的 `yml` 为拓展名的文件的时候，就可以自动提示了。

注意，由于插件的 bug，除了第一行以外，其他的内容无法直接提示，需要使用快捷键让 VSCode 显示提示，一般是 `ctrl` + `Space`。

![poc](../assets/poc/poc.gif)

## jetbrains 系列 IDE

[下载文件](https://raw.githubusercontent.com/chaitin/gamma/master/static/schema/schema.json)