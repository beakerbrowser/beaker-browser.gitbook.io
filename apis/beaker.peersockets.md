---
description: This API sends and receives messages over hyperdrive connections
---

# beaker.peersockets

Peersockets enable you to send and receive messages to peers on a hyperdrive.

### Technical background

Hyperdrive establishes connections between users to send drive-data. Peersockets piggybacks on those connections by creating additional message-channels.

The peersocket channel is sepated into "topics" which are string IDs. You choose to handle to messages in a topic by "joining" the topic. If you don't join a topic, your device will still receive the messages, but they'll be discarded.

{% hint style="info" %}
Note: topics are specific to a hyperdrive. You can only send messages to peers connected to the same hyperdrive.
{% endhint %}

Every peer has a "peer id" assigned. You send messages to specific peers by using their assigned peer id. You can get the current peers by calling `watch()` and handling the "join" and "leave" events.

{% hint style="info" %}
Peer IDs are created for the network session and will not persist.
{% endhint %}

### Example usage

```javascript
// maintain a list of active peers
var peerIds = new Set()
var peerEvents = beaker.peersockets.watch()
peerEvents.addEventListener('join', e => {
  peerIds.add(e.peerId)
})
peerEvents.addEventListener('leave', e => {
  peerIds.delete(e.peerId)
})

// send and receive 'chat' messages
var topic = beaker.peersockets.join('chat')
function sendToAll (message) {
  message = new TextEncoder('utf-8').encode(input.value)
  for (let peerId of peerIds) {
    topic.send(peerId, message)
  }
})
topic.addEventListener('message', e => {
  console.log('peer', e.peerId, 'says', new TextDecoder().decode(e.message))
})
```

## API

### beaker.peersockets.join\(topic\)

Join a "topic" for sending and receiving messages.

* **topic** String. The topic identifier.
* Returns **Topic**

```javascript
var topic = beaker.peersockets.join('chat')
function sendToAll (message) {
  message = new TextEncoder('utf-8').encode(message)
  for (let peerId of peerIds) { // assume `peerIds` is being tracked using watch()
    topic.send(peerId, message)
  }
})
topic.addEventListener('message', e => {
  console.log('peer', e.peerId, 'says', new TextDecoder().decode(e.message))
})
```

### beaker.peersockets.watch\(\)

Watch for connection events. Emits "join" events for all connections that exist at time-of-call.

* Returns **PeerEvents**

```javascript
var peerIds = new Set()
var peerEvents = beaker.peersockets.watch()
peerEvents.addEventListener('join', e => {
  console.log('join', e)
  peerIds.add(e.peerId)
})
peerEvents.addEventListener('leave', e => {
  console.log('leave', e)
  peerIds.delete(e.peerId)
})
```

## `Topic` Instance

###  "message" event

Emitted when a peer sends you a message on the topic.

* **peerId** Number. The sending peer's identifier.
* **message** ArrayBuffer. The message content.

```javascript
topic.addEventListener('message', e => {
  console.log('peer', e.peerId, 'says', new TextDecoder().decode(e.message))
})
```

### topic.send\(peerId, message\)

Sends a message to the specified peer.

* **peerId** Number. The target peer's identifier.
* **message** ArrayBuffer. The message content.
* Returns **Void**.

```javascript
topic.send(1, new TextEncoder('utf-8').encode('Hello!'))
```

### topic.close\(\)

Closes the topic instance. This will not close the topic globally; other topic instances \(e.g. in other tabs\) remain active.

* Returns **Void**.

```javascript
topic.close()
```

## `PeerEvents` Instance

### "join" event

Emitted when a new peer connects. When `watch()` is called, any existing connections will be emitted as join events.

* **peerId** Number. The connecting peer's identifier.

```javascript
peerEvents.addEventListener('join', e => {
  console.log('new connection:', e.peerId)
})
```

### "leave" event

Emitted when a peer disconnects.

* **peerId** Number. The disconnecting peer's identifier.

```javascript
peerEvents.addEventListener('leave', e => {
  console.log('connection closed:', e.peerId)
})
```

### peerEvents.close\(\)

Stops listening for messages on the PeerEvents instance. Does not stop connections from being created.

