# Content-Type Negotiation

Some files will be served correctly if you do not include an extension. Rather than typing:

```markup
<img src="/thumb.png">
```

You can just type:

```markup
<img src="/thumb">
```

This is helpful when you don't know which extension the site may use. In the case of images, it might be `.png`, `.jpg`, `.jpeg`, or `.gif`. By not including the extension, you will fetch whichever format they use.

The behaviors are:

* **text/html**. Will match `.html` and `.md`.
* **text/css**. Will match `.css`.
* **image/\***. Will match `.png`, `.jpg`, `.jpeg`, and `.gif`.
