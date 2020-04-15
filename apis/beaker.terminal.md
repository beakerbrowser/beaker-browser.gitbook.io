# beaker.terminal

### beaker.terminal.registerCommand\(command\)

Register a Webterm Page Command. See the [Webterm documentation on Page Commands](../advanced/webterm.md#page-commands).

* **command** Object
  * **handle** Function. The command's handling function. May be async. Accepts a single `opts` object as the first argument and then any number of positional arguments after.
  * **name** String. The name of the command.
  * **help** String. An optional help description of the command.
  * **usage** String. An optional usage description of the command.
* Returns **Void**

```javascript
beaker.terminal.registerCommand({
  name: 'alert',
  help: 'Display an alert box',
  usage: 'alert {message}',
  async handle (opts = {}, message = '') {
    alert(message)
  }
})
// @alert "Hello World"
```

### beaker.terminal.unregisterCommand\(commandName\)

Unregisters a Webterm Page Command. See the [Webterm documentation on Page Commands](../advanced/webterm.md#page-commands).

* **commandName** String. The name of the command to unregister.
* Returns **Void**.

```javascript
beaker.terminal.unregisterCommand('alert')
```



