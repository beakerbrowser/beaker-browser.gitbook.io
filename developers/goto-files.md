# .Goto Files

A `.goto` file is a shortcut file used by Beaker in many different contexts. All bookmarks, for instance, are saved as `.goto` files in the user's system drive.

A `.goto` file is not expected to have any content. Instead, it has two [metadata attributes](introduction-to-hyperdrive.md#file-k-v-metadata):

* **`title`**. The title of the resource being pointed to by the `.goto` file.
* **`href`**. The URL of the resource being pointed to by the `.goto` file.

When a `.goto` file is visited on the `hyper://` protocol, Beaker will automatically redirect to its `href` value.

{% hint style="info" %}
You can create `.goto` files using [Webterm](../advanced/webterm.md) with the **mkgoto** command.
{% endhint %}

