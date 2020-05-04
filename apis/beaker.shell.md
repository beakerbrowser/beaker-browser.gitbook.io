# beaker.shell

### beaker.shell.drivePropertiesDialog\(url\)

Create a dialog for editing the properties of the given hyperdrive.

* **url** String. The URL of the drive to view.
* Returns **Promise&lt;Void&gt;**

```javascript
await beaker.shell.drivePropertiesDialog(drive.url)
```

### beaker.shell.selectFileDialog\(\[opts\]\)

Create a dialog for selecting files or folders.

* **opts** Object
  * **title** String. The title of the selection dialog.
  * **buttonLabel** String. The label on the "OK" button.
  * **drive** String. The initial drive to explore.
  * **defaultPath** String. The initial path to explore.
  * **select** Array&lt;String&gt;. What kind of entries to accept. Must contain 'file', 'folder', 'mount'.
  * **filters** Object
    * **extensions** Array&lt;String&gt;. The extensions of the files to accept.
    * **writable** Boolean. If true, only accept writable files. If false, only accept unwritable files.
  * **allowMultiple** Boolean. If true, allow multiple files to be selected.
  * **disallowCreate** Boolean. If true, require the selected file to exist.
* Returns **Promise&lt;Array&lt;Object&gt;&gt;**
  * **path** String. The path of the selected file or folder.
  * **origin** String. The URL of the hyperdrive that contains the file or folder.
  * **url** String. The URL of the file or folder.

```javascript
var files = await beaker.shell.selectFileDialog({
  title: 'Select an Image',
  buttonLabel: 'Select Image',
  select: ['file'],
  filters: {
    extensions: ['png', 'jpg', 'jpeg', 'gif']
  },
  allowMultiple: true,
  disallowCreate: true
})
files[0].path   // => '/imgs/foo.png'
files[0].origin // => 'hyper://1234..56/'
files[0].url    // => 'hyper://1234..56/imgs/foo.png'
```

### beaker.shell.saveFileDialog\(\[opts\]\)

Create a dialog for selecting where to save a file.

* **opts** Object
  * **title** String. The title of the selection dialog.
  * **buttonLabel** String. The label on the "OK" button.
  * **drive** String. The initial drive to explore.
  * **defaultPath** String. The initial path to explore.
  * **defaultFilename** String. The suggested default filename.
  * **extension** String. The extension of the saved file.
  * **filters** Object
    * **extensions** Array&lt;String&gt;. The extensions of the files to accept.
    * **writable** Boolean. If true, only accept writable files. If false, only accept unwritable files.
* Returns **Promise&lt;Array&lt;Object&gt;&gt;**
  * **path** String. The path of the selected file or folder.
  * **origin** String. The URL of the hyperdrive that contains the file or folder.
  * **url** String. The URL of the file or folder.

```javascript
var file = await beaker.shell.saveFileDialog({
  title: 'Save Image To...',
  buttonLabel: 'Save Image',
  defaultFilename: 'image.png',
  extension: 'png',
  disallowCreate: true
})
file.path   // => '/imgs/foo.png'
file.origin // => 'hyper://1234..56/'
file.url    // => 'hyper://1234..56/imgs/foo.png'
```

### beaker.shell.selectDriveDialog\(\[opts\]\)

Create a dialog for selecting a hyperdrive from the user's library.

* **opts** Object
  * **title** String. The title of the selection dialog.
  * **buttonLabel** String. The label on the "OK" button.
  * **writable** Boolean. If true, only accept writable drives. If false, only accept unwritable drives.
  * **type** String. The type of the drive to select.
* Returns **Promise&lt;String&gt;**

```javascript
var driveUrl = await beaker.shell.selectDriveDialog({
  title: 'Select Your Website',
  buttonLabel: 'Select Site',
  writable: true,
  type: 'website'
})
```



