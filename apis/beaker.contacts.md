---
description: This API gives read access to the user's address book
---

# beaker.contacts

### beaker.contacts.requestProfile\(\)

Creates a dialog for selecting one of the user's profile drives.

* Returns **Promise&lt;Object&gt;**
  * **url** String. The profile drive's URL.
  * **title** String. The profile drive's display name.
  * **description** String. The profile drive's bio.

```javascript
var profile = await beaker.contacts.requestProfile()
```

### beaker.contacts.requestContact\(\)

Creates a dialog for selecting one of the user's contacts in their address book.

* Returns **Promise&lt;Object&gt;**
  * **url** String. The profile drive's URL.
  * **title** String. The profile drive's display name.
  * **description** String. The profile drive's bio.

```javascript
var contact = await beaker.contacts.requestContact()
```

### beaker.contacts.requestContacts\(\)

Creates a dialog for selecting multiple of the user's contacts in their address book.

* Returns **Promise&lt;Array&lt;Object&gt;&gt;**
  * **url** String. The profile drive's URL.
  * **title** String. The profile drive's display name.
  * **description** String. The profile drive's bio.

```javascript
var contacts = await beaker.contacts.requestContacts()
```

### beaker.contacts.list\(\)

Returns the full list of contacts in the address book. Requires user permission.

* Returns **Promise&lt;Array&lt;Object&gt;&gt;**
  * **url** String. The profile drive's URL.
  * **title** String. The profile drive's display name.
  * **description** String. The profile drive's bio.

```javascript
var contacts = await beaker.contacts.list()
```

