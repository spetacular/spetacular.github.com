---
layout: post
title: Editor.md开源在线 Markdown 编辑器增加本地自动保存功能
category : 微信
tagline: "Supporting tagline"
tags : [js,markdown]
---

[Editor.md](http://pandao.github.io/editor.md/) 是一款开源 Markdown 在线编辑器。在编辑较长文章时，为防止疏忽造成内容丢失，可以添加自动保存功能，

利用localStorage来暂存数据。当文本框有改动时，将内容存到localStorage里；当页面再次加载时，读取localStorage的值。

假设页面创建的编辑器命名为 mdEditor ,则只需将以下js加入页面即可：

```
<script>
    var key = 'default_md_key';
    mdEditor.on("load", function(){
        var content = localStorage.getItem(key);
        if(content){
            var f = confirm("您上次编辑的文章未提交，是否恢复？内容：\n"+content);
            if(f == true){
                testEditor.setValue(content)
            }
        }
    })

    mdEditor.on("change", function(){
        localStorage.setItem(key,this.getValue())
    })
</script>
```

用户向服务器提交数据并保存成功后，可以删掉key。

```
localStorage.removeItem(key);
```