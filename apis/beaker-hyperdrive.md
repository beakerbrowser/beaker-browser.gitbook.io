---
description: This API provides read and write access to hyperdrives.
---

# beaker.hyperdrive

The Hyperdrive API provides read and write access to hyperdrives.

You can use the API by instantiating Hyperdrive instances using [.drive\(\)](beaker-hyperdrive.md#beaker-hyperdrive-drive-url) or by using the global methods. The global methods can accept paths or URLs. If you pass a path into a global method, the current drive will be used as the target. For example, if you were on a drive `hyper://foobar/`, the following three would be equivalent:

```javascript
await beaker.hyperdrive.readdir('/')
await beaker.hyperdrive.readdir('hyper://foobar/')
await beaker.hyperdrive.drive('hyper://foobar/').readdir('/')
```

A hyperdrive is always allowed to read and write its own files. Applications must ask permission before writing to other hyperdrives.

## API

### beaker.hyperdrive.drive\(url\)

Create a Hyperdrive instance. The instance provides most methods in `beaker.hyperdrive` but scoped to the given drive's URL. Unlike the unscoped `beaker.hyperdrive` calls, only paths can be provided to the operations.

* **url** String. The URL of the drive to access.
* Returns **Hyperdrive**.
  * **url** String. The URL of the hyperdrive.
  * **version** String. The checked-out version of the instance. Will be undefined if using latest.
  * **checkout**\(version\): Hyperdrive. Returns a `Hyperdrive` instance at the given version.
  * **watch**\(path, onChanged\): EventTarget. Emits a 'changed' event when writes occur. Does not emit any change events for mounted drives within the parent drive.
  * ...most methods on `beaker.hyperdrive`.

```javascript
var drive = beaker.hyperdrive.drive('hyper://1234..ef')
await drive.readdir('/')
```

### beaker.hyperdrive.createDrive\(\[settings\]\)

Create a new Hyperdrive.

* **settings** Object.
  * **title** String. The title of the hyperdrive.
  * **description** String. The description of the hyperdrive.
  * **prompt** Boolean. Should the creation prompt show? If false, permission will still be requested.
* Returns **Promise&lt;Hyperdrive&gt;**.

```javascript
var drive = await beaker.hyperdrive.createDrive({
  title: 'My cool website',
  description: 'Demonstrating how to create Hyperdrives'
})
```

### beaker.hyperdrive.forkDrive\(url\)

Creates a "fork or clone drive" prompt. The user will use this to copy the files and settings of a hyperdrive into a new drive, optionally overriding some settings.

* **url** String. The URL of the drive to clone.
* Returns **Promise&lt;Hyperdrive&gt;**.

```javascript
var drive = await beaker.hyperdrive.forkDrive(existingDriveUrl)
```

### beaker.hyperdrive.getInfo\(url\[, opts\]\)

Fetch metadata and system information about the hyperdrive.

* **url** String. The URL of the hyperdrive to query. \(Not required on `Hyperdrive` objects returned by [beaker.hyperdrive.drive\(\)](beaker-hyperdrive.md#beaker-hyperdrive-drive-url).\)
* **opts** Object.
  * **timeout** Number \(ms\). How long to wait for the operation to complete before throwing a timeout error. Defaults to 60000.
* Returns **Promise&lt;Object&gt;**.
  * **key** String. The key of the hyperdrive.
  * **url** String. The URL of the hyperdrive.
  * **writable** Boolean. Can the local user modify the hyperdrive?
  * **version** Number. The latest revision number of the hyperdrive.
  * **title** String. The title of the hyperdrive.
  * **description** String. The description of the hyperdrive.

```javascript
var info = await beaker.hyperdrive.getInfo('hyper://1234..ef')
```

### beaker.hyperdrive.stat\(url\[, opts\]\)

Get metadata about a file.

* **url** String. The url to the file.
* **opts** Object.
  * **lstat** Boolean. If the file is a symlink, give information about the symlink instead of the link target.
  * **timeout** Number \(ms\). How long to wait for the operation to complete before throwing a timeout error. Defaults to 60000.
* Returns **Promise&lt;Object&gt;**.
  * **isFile\(\)** Function\(\): Boolean. Is the entry a file?
  * **isDirectory\(\)** Function\(\): Boolean. Is the entry a directory?
  * **size** Number. The size of the file in bytes.
  * **blocks** Number. The size of the file in blocks in the content hypercore.
  * **mtime** Number. The timestamp of the last modification of the file.
  * **ctime** Number. The timestamp of the creation of the file.
  * **mount** Object. Information about the target mount if the entry is a mount.
    * **key** String. The key of the mount target.
    * **version** Number. The version of the mount target, if specified.
  * **metadata** Object. User metadata attached to the entry.
  * **linkname** String. If a symlink, gives the target path.

```javascript
var stat = await beaker.hyperdrive.stat('hyper://1234..ef/index.json')
```

### beaker.hyperdrive.readFile\(url\[, opts\]\)

Read the contents of a file.

* **url** String. The url to the file.
* **opts** Object \| String. If a String, will act as the 'encoding' option.
  * **encoding** String. Desired output encoding. May be 'binary', 'utf8', 'hex', or 'base64'. Default 'utf8'.
  * **timeout** Number \(ms\). How long to wait for the operation to complete before throwing a timeout error. Defaults to 60000.
* Returns **Promise&lt;String \| ArrayBuffer&gt;**

```javascript
var fileStr = await beaker.hyperdrive.readFile('hyper://1234..ef/foo.txt')
var imgBuf = await beaker.hyperdrive.readFile('hyper://1234..ef/bar.png', 'binary')
var imgBase64 = await beaker.hyperdrive.readFile('hyper://1234..ef/bar.png', 'base64')
```

### beaker.hyperdrive.readdir\(url\[, opts\]\)

Read the contents of a directory.

* **url** String. The url to the directory.
* **opts** Object.
  * **includeStats** Boolean. Should the output include the 'stats' object for each entry?
  * **recursive** Boolean. Recurse into subfolders?
    * Note: does not recurse into child mounts.
  * **timeout** Number \(ms\). How long to wait for the operation to complete before throwing a timeout error. Defaults to 60000.
* Returns **Promise&lt;Array&lt;String \| Object&gt;&gt;**
  * If an object, the shape will be `{ name, stat }`

```javascript
var files = await beaker.hyperdrive.readdir('hyper://1234..ef/')
var allFiles = await beaker.hyperdrive.readdir('hyper://1234..ef/', {recursive: true})
var filesWithStats = await beaker.hyperdrive.readdir('hyper://1234..ef/', {includeStats: true})
```

### beaker.hyperdrive.query\(query\)

Read the contents of a drive or drives across multiple specified paths. This function can be used to read a wide range of files while filtering by multiple various attributes. If Hyperdrive were a SQL database, this would be the SELECT function.

* **query** Object.
  * **drive** String \| Array&lt;String&gt;. The drive\(s\) to query against.
  * **path** String \| Array&lt;String&gt;. The path\(s\) to query against. May use globbing patterns \('\*'\) to specify multiple files.
  * **type** String. Filters results to entries of this type. May be 'file', 'directory', or 'mount'.
  * **mount** String \(URL\). Filters results to mounts which point to the drive specified here.
  * **metadata** Object. Filters results by their metadata against the key-values specified here.
  * **sort** String. Specifies which attribute to sort the results by. May be 'name', 'ctime', or 'mtime'.
  * **reverse** Boolean. If true, the result order will be reversed.
  * **limit** Number. Specifies a maximum number of results to return.
  * **offset** Number. Specifies a starting offset within the results. Used for pagination.
  * **timeout** Number \(ms\). How long to wait for the operation to complete before throwing a timeout error. Defaults to 60000.
* Returns **Promise&lt;Array&lt;Object&gt;&gt;**
  * **type** String. The type of the entry. Must be 'file', 'directory', or 'mount'.
  * **path** String. The path of the entry relative to the queried drive.
  * **url** String. The URL of the entry relative to its _owning_ drive. Put another way, this is the _canonical_ URL of the entry. Entries which are in mounts will have a different origin than the querying drive's origin.
  * **stat** Object. The entry's Stat object, see stat\(\).
  * **drive** String. The URL of the drive which owns the entry.
  * **mount** String. The URL of the target drive if the entry is a mount.

```javascript
var rootFiles = await beaker.hyperdrive.query({
  drive: 'hyper://1234..ef',
  path: '/*',
  type: 'file'
})
var rootImgs = await beaker.hyperdrive.query({
  drive: 'hyper://1234..ef',
  path: ['/*.png', '/*.jpg', '/*.jpeg']
})
var postFiles = await beaker.hyperdrive.query({
  drive: 'hyper://1234..ef',
  path: '/microblog/*',
  sort: 'ctime'
})
var followMounts = await beaker.hyperdrive.query({
  drive: 'hyper://1234..ef',
  path: '/follows/*',
  type: 'mount'
})
var bobFollow = await beaker.hyperdrive.query({
  drive: 'hyper://1234..ef',
  path: '/follows/*',
  mount: bobsUrl
})
var commentsOnBeaker = await beaker.hyperdrive.query({
  drive: 'hyper://1234..ef',
  path: '/comments/*', 
  metadata: {href: 'https://beakerbrowser.com'}
})
```

### beaker.hyperdrive.diff\(url, other\[, prefix, opts\]\)

List the changes that have occurred between to versions of the drive.

Note: this method can only compare drives to other versions of itself. It cannot be used to compare two different drives.

* **url** String. The URL to diff.
* **other** Number\|String\|Hyperdrive. The version ID or hyperdrive instance to compare against.
* **opts** Object.
  * **timeout** Number \(ms\). How long to wait for the operation to complete before throwing a timeout error. Defaults to 60000.
* Returns **Promise&lt;Array&lt;Object&gt;&gt;**
  * type String. What operation occurred. One of: 'put', 'del', 'mount', 'unmount'.
  * name String. The path of the modified entry.
  * **value** Object. Information related to the change \(e.g. the 'stat' object of a put file\).

```javascript
var changes = await beaker.hyperdrive.diff('hyper://1234..ef', 5) // diff latest against revision 5
```

### beaker.hyperdrive.configure\(url, settings\[, opts\]\)

Update the settings and/or manifest \(index.json\) of the drive.

* **url** String. The URL of the drive to configure.
* **settings** Object.
  * **title** String. The title of the drive.
  * **description** String. The description of the drive.
* **opts** Object.
  * **timeout** Number \(ms\). How long to wait for the operation to complete before throwing a timeout error. Defaults to 60000.
* Returns **Promise&lt;Void&gt;**

```javascript
await beaker.hyperdrive.configure('hyper://1234..ef', {
  title: 'The drive title',
  description: 'The drive description'
})
```

### beaker.hyperdrive.writeFile\(url, data\[, opts\]\)

Write a file to the drive.

* **url** String. The URL of the file to write.
* **data** String\|ArrayBuffer. The content to write.
* **opts** String\|Object. If a string, acts as the encoding parameter.
  * **encoding** String. The encoding of the data parameter. Must be 'utf8', 'base64', 'hex', or 'binary'. Defaults to 'utf8' if data is a string and 'binary' if data is an ArrayBuffer.
  * **metadata** Object. The metadata to write to the file. Even if unspecified
  * **timeout** Number \(ms\). How long to wait for the operation to complete before throwing a timeout error. Defaults to 60000.
* Returns **Promise&lt;Void&gt;**

```javascript
await beaker.hyperdrive.writeFile('hyper://1234..ef/foo.txt', 'Foo')
await beaker.hyperdrive.writeFile('hyper://1234..ef/foo.png', imgPngBase64, 'base64')
await beaker.hyperdrive.writeFile('hyper://1234..ef/foo.jpg', imgJpgArrayBuffer, {encoding: 'binary'})
await beaker.hyperdrive.writeFile('hyper://1234..ef/foo.md', '# Markdown Doc', {
  metadata: {title: 'Markdown Doc'}
})
```

### beaker.hyperdrive.mkdir\(url\[, opts\]\)

Create a folder on the drive.

* **url** String. The URL of the folder to create.
* **opts** Object.
  * **timeout** Number \(ms\). How long to wait for the operation to complete before throwing a timeout error. Defaults to 60000.
* Returns **Promise&lt;Void&gt;**

```javascript
await beaker.hyperdrive.mkdir('hyper://1234..ef/sub')
```

### beaker.hyperdrive.symlink\(target, url\[, opts\]\)

Create a symlink on the drive. Good luck getting the argument order right the first time!

* **target** String. The URL which the symlink should point to.
* **url** String. Where to place the symlink.
* **opts** Object.
  * **timeout** Number \(ms\). How long to wait for the operation to complete before throwing a timeout error. Defaults to 60000.
* Returns **Promise&lt;Void&gt;**

```javascript
await beaker.hyperdrive.symlink('hyper://1234..ef/this-file-already-exists.txt', 'hyper://1234..ef/the-symlink.txt')
```

### beaker.hyperdrive.mount\(url, mount\[, opts\]\)

Create a mount on the drive to some other drive. \(Mounts are like symlinks that work across Hyperdrives.\) Note: we know, we know, the argument order is the opposite of symlink.

* **url** String. Where to place the mount.
* **mount** String\|Object\|Hyperdrive. The drive to mount. If a String or Hyperdrive, acts as the key attribute.
  * **key** String. The key of the drive to mount.
  * **version** Number\|String. The version to pin the mount to. If undefined, the mount will point to latest.
* **opts** Object.
  * **timeout** Number \(ms\). How long to wait for the operation to complete before throwing a timeout error. Defaults to 60000.
* Returns **Promise&lt;Void&gt;**

```javascript
await beaker.hyperdrive.mount('hyper://1234..ef/mount', 'hyper://fedcb..12')
await beaker.hyperdrive.mount('hyper://1234..ef/mount2', {key: 'fedcb..12', version: 10})
```

### beaker.hyperdrive.copy\(src, dst\[, opts\]\)

Copy a file or folder. Works across hyperdrives.

* **src** String. Where to copy the files from.
* **dst** String. Where to copy the files to.
* **opts** Object.
  * **timeout** Number \(ms\). How long to wait for the operation to complete before throwing a timeout error. Defaults to 60000.
* Returns **Promise&lt;Void&gt;**

```javascript
await beaker.hyperdrive.copy('hyper://1234..ef/foo.txt', 'hyper://1234..ef/bar.txt')
```

### beaker.hyperdrive.rename\(src, dst\[, opts\]\)

Move a file or folder. Works across hyperdrives.

* **src** String. Where to move the files from.
* **dst** String. Where to move the files to.
* **opts** Object.
  * **timeout** Number \(ms\). How long to wait for the operation to complete before throwing a timeout error. Defaults to 60000.
* Returns **Promise&lt;Void&gt;**

```javascript
await beaker.hyperdrive.rename('hyper://1234..af/foo.txt', 'hyper://1234..af/bar.txt')
```

### beaker.hyperdrive.updateMetadata\(url, metadata\[, opts\]\)

Modify the metadata on a file. Merges into the existing metadata of the file.

* **url** String. The URL of the file to modify.
* **metadata** Object. The metadata values to set.
* **opts** Object.
  * **timeout** Number \(ms\). How long to wait for the operation to complete before throwing a timeout error. Defaults to 60000.
* Returns **Promise&lt;Void&gt;**

```javascript
await beaker.hyperdrive.updateMetadata('hyper://1234..ef/foo.txt', {title: 'Foo'})
```

### beaker.hyperdrive.unlink\(url\[, opts\]\)

Deletes a file on the drive.

* **url** String. The URL of the file to delete.
* **opts** Object.
  * **timeout** Number \(ms\). How long to wait for the operation to complete before throwing a timeout error. Defaults to 60000.
* Returns **Promise&lt;Void&gt;**

```javascript
await beaker.hyperdrive.unlink('hyper://1234..ef/foo.txt')
```

### beaker.hyperdrive.rmdir\(url\[, opts\]\)

Deletes a folder on the drive.

* **url** String. The URL of the folder to delete.
* **opts** Object.
  * **recursive** Boolean. Delete the files within the folder if it's not empty. Default false.
  * **timeout** Number \(ms\). How long to wait for the operation to complete before throwing a timeout error. Defaults to 60000.
* Returns **Promise&lt;Void&gt;**

```javascript
await beaker.hyperdrive.rmdir('hyper://1234..ef/sub')
await beaker.hyperdrive.rmdir('hyper://1234..ef/sub2', {recursive: true})
```

### beaker.hyperdrive.unmount\(url\[, opts\]\)

Remove a mount on the drive.

* **url** String. The URL of the mount to delete.
* **opts** Object.
  * **timeout** Number \(ms\). How long to wait for the operation to complete before throwing a timeout error. Defaults to 60000.
* Returns **Promise&lt;Void&gt;**

```javascript
await beaker.hyperdrive.unmount('hyper://1234..ef/mount')
```

### beaker.hyperdrive.deleteMetadata\(url, keys\[, opts\]\)

Remove metadata keys from the file.

* **url** String. The URL of the file to modify.
* **keys** String\|Array&lt;String&gt;. The key or keys to delete.
* **opts** Object.
  * **timeout** Number \(ms\). How long to wait for the operation to complete before throwing a timeout error. Defaults to 60000.
* Returns **Promise&lt;Void&gt;**

```javascript
await beaker.hyperdrive.deleteMetadata('hyper://1234..ef/foo.txt', 'title')
await beaker.hyperdrive.deleteMetadata('hyper://1234..ef/bar.txt', ['title', 'href'])
```

