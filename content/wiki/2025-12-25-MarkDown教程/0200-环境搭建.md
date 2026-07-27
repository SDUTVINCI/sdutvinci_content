---
title: "环境搭建"
---

### 安装
首先可以安装一个VScode插件叫做`Markdown All in One`,然后还有`Markdown Preview Enhanced`.

https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one

https://marketplace.visualstudio.com/items?itemName=shd101wyy.markdown-preview-enhanced




### 配置图片直接粘贴
#### 规划文件夹与图片等命名
图片名字格式为当前图片创建的日期:时间戳.扩展名,例如20251225153422.webp

在工程根目录创建`.vscode/settings.json`

```json
{
  "markdown.editor.filePaste.enabled": "always",
  "markdown.copyFiles.destination": {
    "**/content/posts/*.md": "/public/images/${documentBaseName}/${unixTime}.${fileExtName}",
    "**/content/pages/*.md": "/public/images/pages/${unixTime}.${fileExtName}"
  },
  "markdown.copyFiles.overwriteBehavior": "nameIncrementally"
}
```
