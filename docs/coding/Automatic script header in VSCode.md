---
title: Automatic script header in VSCode
date: 2023-07-15 15:28:49
categories:
- linux
tags: 
- vscode
- header
---

使用snippets: [vscode official support](https://code.visualstudio.com/docs/editor/userdefinedsnippets#_create-your-own-snippets) 这里同时也有pre-defined variable的介绍。

[snippets for markdown](https://code.visualstudio.com/docs/languages/markdown#:~:text=Snippets%20for%20Markdown,Snippets%20to%20find%20out%20how.)

snippets我认为是最好的选择。extension里当然也有一些auto-header的插件，但我觉得是脱裤子放屁的行为，VSCode 内置的snippets能简单完成的事不需要再复杂化了(可能也是因为我没有复杂的header需求)。

如无必要，勿增实体！

<!-- more -->

我在markdown.json里的设置，可以作为一个例子

```json
"Auto-generated Header": {
"prefix": "header",
"body": [
    "---",
    "title: ",
    "date: ${CURRENT_YEAR}-${CURRENT_MONTH}-${CURRENT_DATE} ${CURRENT_HOUR}:${CURRENT_MINUTE}:${CURRENT_SECOND}",
    "categories:",
    "tags: ",
    "---",
    "<!-- more -->"
],
"description": "header part for all of haoxing's blog"
}
```

设置完成后，在markdown中输入header，就可以自动生成下面这一串：

```
---
title: 
date: 2023-07-15 15:26:47
categories:
tags: 
---
<!-- more -->
```

如果没有反应，请参考[this issue in vscode github](https://github.com/Microsoft/vscode/issues/26108)
下面是我在user setting.json里面的code，可以作为一个例子

```json
"[markdown]": {
    "editor.unicodeHighlight.ambiguousCharacters": false,
    "editor.unicodeHighlight.invisibleCharacters": false,
    "diffEditor.ignoreTrimWhitespace": false,
    "editor.wordWrap": "on",
    "editor.quickSuggestions": true
}
```


