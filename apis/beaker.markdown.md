---
description: This API renders markdown into HTML
---

# beaker.markdown

### beaker.markdown.toHTML\(md\)

Renders markdown into HTML.

* **md** String. The markdown to render.
* Returns **String**.

```javascript
var html = beaker.markdown.toHTML('# hello!')
console.log(html) // => '<h1>Hello!</h1>'
```



