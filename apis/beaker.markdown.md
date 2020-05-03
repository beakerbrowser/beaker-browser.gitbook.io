---
description: This API renders Markdown into HTML
---

# beaker.markdown

## beaker.markdown.toHTML\(md\)

Renders Markdown into HTML.

* **md** String. The Markdown to render.
* Returns **String**.

```javascript
var html = beaker.markdown.toHTML('# hello!')
console.log(html) // => '<h1>Hello!</h1>'
```

