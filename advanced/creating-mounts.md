# Creating Mounts

"Mounting" is a tool which links one hyperdrive to another as a subfolder.

Mounts are useful for a variety of use-cases. They can be used to make collections, to attach code-module dependencies, and more.

A mounted drive will behave just like a subfolder. If you don't "own" the mounted drive, it will be read-only, even if the containing drive is yours.

## In the Editor

Visit your hyperdrive and [open the editor](../beginner/editor.md). Then expand the files listing by clicking the folder icon.

![Open the files listing by clicking the folder icon.](../.gitbook/assets/editor-list-files.png)

Right-click in the files listing \(but not on a file\) and select "New Mount."

![](../.gitbook/assets/editor-new-mount.png)

You will be prompted to select a hyperdrive to mount. If your target drive is not in the listing, enter the drive's URL in the input.

![Select the drive you want to mount or enter its URL.](../.gitbook/assets/select-drive-dialog.png)

You'll then be prompted to enter the mount's name. This is the name of the "folder" which the mount will represent. Choose a name and press "OK."

![](../.gitbook/assets/mount-name-dialog.png)

Your new mount will appear in the editor's files listing.

![](../.gitbook/assets/mount-in-editor.png)

## In the Files Explorer

Visit your hyperdrive and open the files explorer by clicking "Explore Files."

![](../.gitbook/assets/open-files-explorer.png)

Right-click in the files listing \(but not on a file\) and select "New Mount."

![](../.gitbook/assets/files-explorer-new-mount.png)

You will be prompted to select a hyperdrive to mount. If your target drive is not in the listing, enter the drive's URL in the input.

![Select the drive you want to mount or enter its URL.](../.gitbook/assets/select-drive-dialog.png)

You'll then be prompted to enter the mount's name. This is the name of the "folder" which the mount will represent. Choose a name and press "OK."

![](../.gitbook/assets/mount-name-dialog.png)

Your new mount will appear in the explorer's files listing.

![](../.gitbook/assets/mount-in-files-explorer.png)

## In the Terminal

Visit your hyperdrive and open the terminal by clicking "Terminal."

![](../.gitbook/assets/open-terminal.png)

You can create a directory by typing `mount` followed by the drive URL then the mount's name.

![](../.gitbook/assets/terminal-create-mount.png)

