---
layout: post
title: "Markdown Test"
date: 2016-12-20 09:33:38 +0800
comments: true
tags: 
     Markdown
   
---

虽然博客搭好还是蛮快的，但是添加一些plugin，却花费了一些时间。对于我一个不会前端的人（只接触过html，js, etc.） 要我fix网页的bug还是挺为难我的。

作为一个以后的ML的从业者，那我的博文里就必须少不了各种公式，所以就必须借助`MathJax.js`帮助渲染公式。按照一系列的tutorial，安装好了`MathJax.js`，但是发现并不能work，花了两天解决了这个问题。其问题在于这个博客是通过https安全访问的，而`MathJax.js`的加载是通过http。所以在localhost访问时，`Markdown.js`能够顺利渲染`LaTeX`数学公式，而当真正deploy后，却无法访问。后面在script中将`http`改成了`https`才能正常加载o(╯□╰)o

<!--more-->

好了，下面来测试下Markdown语法：

*东川路吴彦祖*

**东川路吴彦祖**

东川路吴彦祖
===

东川路吴彦祖
---

### 东川路吴彦祖

[这是一个外链接](https://xijunlee.github.io/)

- [ ] 东川路吴彦祖
- [x] 东川路吴彦祖

$$E=mc^2$$

```python
@requires_authorization
class SomeClass:
        pass
    
    if __name__ == '__main__':
            # A comment
                print 'hello world'
```

| 项目        | 价格   |  数量  |
| --------   | -----:  | :----:  |
| 计算机     | \$1600 |   5     |
| 手机        |   \$12   |   12   |
| 管线        |    \$1    |  234  |
