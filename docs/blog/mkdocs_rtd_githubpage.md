# 如何建立一个blog？

## 关于personal blog(eg. JUJU's Garage)

使用Hexo建立，主题为Next。具体的之后再说～

## 关于Doc类型的site(eg. this site)

**sphnix**对于python rst类型的文档应该是一个好选择，可惜我已经习惯markdown了。

对于使用markdown进行Doc书写，同样有类似的小东西：**mkdocs**。并且readthedoc同样兼容通过mkdocs编译出的site。

basic start of medic-readthedoc: [Getting started with MkDocs — Read the Docs user documentation 9.15.0 documentation](https://docs.readthedocs.io/en/stable/intro/getting-started-with-mkdocs.html)

some official examples: [readthedocs-examples/example-mkdocs-basic: A basic MkDocs project for Read the Docs (github.com)](https://github.com/readthedocs-examples/example-mkdocs-basic/)

[一个湾湾人的mkdcos教程](https://foreachsam.github.io/book-util-mkdocs/book/content/workflow/build-site/)，看起来还可以。

For now, this little notebook hosted on github page is enough, no need to host it on Readthedoc. 

### 如何让markdown也能写出类似rst的rendering block？

MkDocs uses the Python Markdown library to translate Markdown files into HTML. Python Markdown supports a variety of extensions that customize how pages are formatted. This setting lets you enable a list of extensions beyond the ones that MkDocs uses by default (meta, toc, tables, and fenced_code).

mkdocs repo中讨论这个问题的[issue](https://github.com/mkdocs/mkdocs/issues/1659)

!!! Note
    这是一个示范。具体是如何做到的，请参考上面的issue。

### 如何让code block识别语言并且highlight？

see this mkdocs [issue](https://github.com/mkdocs/mkdocs/issues/263)

also see the [official mkdocs Doc](https://www.mkdocs.org/user-guide/writing-your-docs/)

to be more exactly, [see here](https://mkdocs.readthedocs.io/en/latest/user-guide/choosing-your-theme/#readthedocs) for readthedocs theme setting.

By default, highlight.js only supports 23 common languages. List additional languages here to include support for them.

!!! tip
    下面是一个例子。但是我目前还没有找到能让rendering block与code block叠加在一起的方法。

``` python
import markdown
markdown.markdown(some_text, extensions=['admonition'])
```

### 如何给mkdocs生成的静态网页加密

这是比较现实的需求，因为某些内容我实际上不想让所有人都看见......

可以参考这个[mkdcos plugin]([mkdocs-encryptcontent-plugin · PyPI](https://pypi.org/project/mkdocs-encryptcontent-plugin/))

!!! info
    I want to be able to protect the content of the page with a password.

    Define a password to protect each page independently or a global password to protect them all.

    If a global password exists, all articles and pages are protected with this password.

    If a password is defined in an article or a page, it is always used even if there is a global password.

    If a password is defined as an empty character string, the content is not protected.

!!! tip
    see [here](/encrypt_template) for a demo. passwd is 'template'

## 让你的site能被google搜索到

register you site in [google search console](https://search.google.com/search-console)

need to upload the sitemap. So what is sitemap?

!!! info
    The concept of this format is extremely simple: you create an XML file that lists the pages on your site, as well as some information about them (update frequency, crawl priority, etc.):

    see more at [here](https://octoperf.com/blog/2015/06/17/mkdocs-sitemap/#sitemapxml)

site_url: https://jujuup.github.io/JUJUnotebook/ 需要设置site_url, 否则sitemap中的网址会是一堆None

## How to use bibliography file to do cross-reference in mkdocs?

see here: [mkdocs-bibtex](https://pypi.org/project/mkdocs-bibtex/)