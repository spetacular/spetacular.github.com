---
layout: post
title: 在线编辑器增加本地自动保存功能[Editor.md,UEditor]
category : js
tagline: "Supporting tagline"
tags : [js,markdown]
---

UEditor 增加本地自动保存功能，更新于2017年11月17日。

# Editor.md

[Editor.md](http://pandao.github.io/editor.md/) 是一款开源 Markdown 在线编辑器。在编辑较长文章时，为防止疏忽造成内容丢失，可以添加自动保存功能。

预览地址：

[https://www.text.wiki/md/](https://www.text.wiki/md/)

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

# UEditor

[UEditor](http://ueditor.baidu.com/website/index.html) 本身有自动保存功能，但并不好用，这里也说明一下配置方法。

```
var key = 'default_ue_key';
var ue = UE.getEditor('editor');
    ue.addListener( 'ready', function( editor ) {
        var content = localStorage.getItem(key);
        if(content){
            var f = confirm("您上次编辑的文章未提交，是否恢复？内容：\n"+content);
            if(f == true){
                UE.getEditor('editor').execCommand('insertHtml', content);
            }
        }
    } );

    ue.addListener('contentchange',function(){
        var content = UE.getEditor('editor').getContent();
        localStorage.setItem(key,content);
    });
```
用户向服务器提交数据并保存成功后，可以删掉key。

```
localStorage.removeItem(key);
```
