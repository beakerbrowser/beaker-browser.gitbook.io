# Index.json Manifest

Every Hyperdrive has a manifest file at /index.json. This file is used by the browser to store various kinds of metadata, including the type and title of the drive.

{% hint style="info" %}
Note: the manifest schema is still under development and may change.
{% endhint %}

An example manifest:

```javascript
{
  "title": "My Hyper Website",
  "description": "An example hyperdrive"
}
```

## Manifest schema

### .title

A string identifying the title of the drive.

### .description

A string providing a description of the drive's contents.

### .forkOf

A string identifying the URL of a drive which this drive has forked.

.content

