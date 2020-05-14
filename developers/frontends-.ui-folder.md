# Frontends \(.ui folder\)

The standard behavior of `hyper://` is to serve whichever file is referenced by the URL. This works fine for simple use-cases, but struggles with two use-cases:

1. Sites which need a consistent theme and template applied across each page.
2. Applications which need to serve interfaces even where a file does not exist \(as in the [Single Page Application](https://en.wikipedia.org/wiki/Single-page_application) pattern\).

Two solve this, the `hyper://` protocol supports a behavior called "Frontends."

## How frontends work

A Frontend is simply an html file found at `/.ui/ui.html`. This file is used to provide a consistent interface for the site. It is served _rather_ _than_ the target file in the following cases:

* No file exists at the target URL.
* The target URL is a folder.
* The "Accept" header includes text/html \(which indicates the browser is asking for a "page"\).

Because the Frontend effectively overrides all page-serving, it can render whatever the site author wants. A common pattern is to use JavaScript to read whatever file is referenced by `window.location.pathname` and then place that in the UI, as in this example snippet:

{% code title="/.ui/ui.html" %}
```markup
<body>
  <main></main>
</body>
<script>
async function setup () {
  var main = document.querySelector('main')
  if (location.pathname.endsWith('.html')) {
    let self = new Hyperdrive(location.hostname)
    let html = await self.readFile(location.pathname).catch(e => `<h1>404 not found<h1>`)
    main.innerHTML = html
  } else if (location.pathname.endsWith('.jpg')) {
    main.innerHTML = `<img src="${location.pathname}">`
  } // etc...
}
setup()
</script>
```
{% endcode %}

## Mounted frontends

An advantage of Frontends is that they are stored in a subfolder. This makes it possible for frontends to be their own Hyperdrive sites which are mounted to `/.ui`.

```markup
|12345..af> mount $my_frontend_drive_url /.ui
```

Frontend hyperdrives can then be published and shared across multiple sites.
