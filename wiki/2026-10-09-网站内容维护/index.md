---
title: 网站内容维护
description: 
authors:
  - dongjiahui
tags:
  - 通用资料
---

### 维护网站内容方式

Vinci 机器人队网站的 Wiki 有三种编辑方式：CMS 富文本编辑、CMS 在线 Markdown 编辑，以及通过 GitHub Pull Request（下文简称 PR）提交修改。

如果只是修改少量文字，前两种方式更直接；如果需要新增章节、批量修改、使用本地编辑器或保留完整的代码审查记录，推荐使用第三种方式。

#### 方式一：使用 CMS 富文本编辑器

1. 打开 [CMS 登录页](/cms/login)，登录已通过审核的成员账号。
2. 在“文章”中找到需要修改的 Wiki，创建编辑草稿；也可以在“草稿”页面新建文章草稿。
3. 在编辑页选择“富文本”，像使用普通文档编辑器一样修改标题、段落、列表、图片和代码块。
4. 保存前查看“发布效果”，确认排版正确。

富文本适合不熟悉 Markdown 的成员。如果页面含有 HTML、Vue/MDC 组件或其他扩展语法，编辑器可能显示兼容性警告；此时应使用 Markdown 源码模式并仔细核对预览。

![](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/09/1791527959749-79ef9c18.webp)

#### 方式二：在 CMS 中直接编辑 Markdown

1. 按照方式一进入文章草稿。
2. 选择“Markdown 源码与预览”。
3. 在左侧直接修改 Markdown，在右侧对照网站最终的发布效果。
4. 需要图片时，可使用草稿页面的图片上传功能，系统会上传图片并插入对应的 Markdown 链接。

这种方式无需在本地安装 Git，适合单篇文章的精确修改。

![](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/09/1791527981896-12740293.webp)

#### 方法三（推荐推荐推荐！！！github PR提交md）

##### 编辑前先让内容仓库跟上数据库

建议在开始编辑前，先请管理员在 CMS 工作台找到“最近全量对账”卡片，点击“手动全量导出”，并等待页面显示成功。这一步会把 PostgreSQL 中的最新正式内容对账并导出到内容仓库，避免从过时的 Markdown 开始修改。普通成员看不到该按钮时，联系管理员执行即可。

全量导出成功后，再从 Clone 仓库开始。下面每一步只有一条命令，请按顺序执行，不要把几条命令粘成一行。

![](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/09/1791527942240-d53bf6c4.webp)

##### 克隆

网站的 Markdown 位于 [`SDUTVINCI/sdutvinci_content`](https://github.com/SDUTVINCI/sdutvinci_content) 内容仓库。

```bash
git clone https://github.com/SDUTVINCI/sdutvinci_content.git
```

在`sdutvinci_content`文件夹打开VScode

```bash
cd ./sdutvinci_content/
code .
```

![](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/09/1791528613821-416adf15.webp)

##### 配置插件

打开VScode扩展，搜索`tungchiahui.md-image-uploader`：

![](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/09/1791527382047-0db725d6.webp)

然后下载完毕后

创建`.vscode/settings.json`：

```json
{
  //请放到个人设置,不要放到settings.json的部分
  // "mdImageUploader.s3.accessKeyId": "xxxxxxxxxxxxxxxxxxxx",
  // "mdImageUploader.s3.secretAccessKey": "xxxxxxxxxxxxxxxxxxxx",

  //可以放到settings.json的部分,以下是例子
  "mdImageUploader.enabled": true,
  "mdImageUploader.s3.endpoint": "https://s3.sdutvinci.cn",
  "mdImageUploader.s3.region": "us-east-1",
  "mdImageUploader.s3.bucket": "vos",
  "mdImageUploader.s3.forcePathStyle": true,
  "mdImageUploader.cdnUrl": "https://cdn.sdutvinci.cn",
  "mdImageUploader.datedUploadPath": "/site-assets/images/wiki",
  "mdImageUploader.undatedUploadPath": "/site-assets/images/undate"
}
```

然后`ctrl shift P`：`open user settings`:

![](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/09/1791527587136-b7efe39f.webp)

在最底下粘贴上：

```json
    "mdImageUploader.s3.accessKeyId": "78h3+34LPHp9mRBFHJ5x",
    "mdImageUploader.s3.secretAccessKey": "ZwoNOJXBiOTW8VWmz/cXC+wpbWMmc2FCzp8Y6dSZ",
```

![](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/09/1791527625771-70398b88.webp)

##### 建立分支

基本的git不会该杀，第二天主动找学长领十小时枪子！[Git教程](/wiki/2023-12-29-git-jiao-xue)

![](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/09/1791528206069-5ce2f138.webp)

![](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/09/1791528218539-deccd422.webp)

![](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/09/1791528222755-5ea0b8da.webp)

随便给分支起个名字，比如`pr_test`

![](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/09/1791528227587-4f67bbaf.webp)

##### 编辑内容

> 详细编辑规则请看[维护网站内容规则](#维护网站内容规则)

比如我新建一个`2026-10-09-网站内容维护`和`2026-10-09-网站内容维护/index.md`：

![](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/09/1791528449388-8feee480.webp)

在frontmatter里写上：

```yaml
---
title: 网站内容维护
description: 
authors:
  - dongjiahui
tags:
  - 通用资料
---
```

用md编辑若干内容，但注意标题要从2级开始。

详细编辑规则请看[维护网站内容规则](#维护网站内容规则)

然后我再把`2026-04-17-STM32CubeIDE-VScode环境搭建`彻底重构，里面有删除文章，新建文章等等。

![](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/09/1791528688703-22036e5a.webp)

改成：

![](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/09/1791528728767-d257b125.webp)

##### 把分支push到github

基本的git不会该杀，第二天主动找学长领十小时枪子！[Git教程](/wiki/2023-12-29-git-jiao-xue)

先commit

![](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/09/1791528772469-20ccf3dd.webp)

然后push

![](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/09/1791528813021-fa9954c5.webp)

##### 创建PR

打开内容github：https://github.com/SDUTVINCI/sdutvinci_content

点击这个`pull request`：

![](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/09/1791528917759-2e9cd47d.webp)

然后点击`create pull request`：

![](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/09/1791528999657-a77e18c7.webp)

然后出现下方界面就别管了，千万不要点`Merge Pull Request`，有冲突也不要管。

![](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/09/1791529035986-fdf5e12d.webp)

记住这个`#10`

![](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/09/1791529130995-b8e1880d.webp)

##### 导入PR

https://vinci.sdut.edu.cn/cms/content-imports

输入PR编号：

![](https://cdn.sdutvinci.cn/site-assets/images/wiki/2026/10/09/1791529150866-2ce19363.webp)



### 维护网站内容规则



