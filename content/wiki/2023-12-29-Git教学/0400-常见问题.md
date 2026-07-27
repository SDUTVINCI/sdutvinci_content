---
title: "常见问题"
---

### commit到本地的想直接取消
```Python

# 设置默认编辑器
git config --global core.editor vim

# 使用交互式rebase
git rebase -i HEAD~2
```

若我不想提交add bishe了

![](https://cdn.tungchiahui.cn/tungwebsite/assets/images/2023/12/29/image89.webp)

可以将上图修改为：

```Python
drop <hash1> add bishe
pick <hash2> update
```

然后退出编辑器后就成功了。
