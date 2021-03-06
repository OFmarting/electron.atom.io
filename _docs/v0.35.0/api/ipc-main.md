---
version: v0.35.0
category: API
title: 'Ipc Main'
source_url: 'https://github.com/atom/electron/blob/master/docs/api/ipc-main.md'
---

# ipcMain

The `ipcMain` module, when used in the main process, handles asynchronous and
synchronous messages sent from a renderer process (web page). Messages sent from
a renderer will be emitted to this module.

## Sending Messages

It is also possible to send messages from the main process to the renderer
process, see [webContents.send][webcontents-send] for more information.

* When sending a message, the event name is the `channel`.
* To reply a synchronous message, you need to set `event.returnValue`.
* To send an asynchronous back to the sender, you can use
  `event.sender.send(...)`.

An example of sending and handling messages between the render and main
processes:

```javascript
// In main process.
const ipcMain = require('electron').ipcMain;
ipcMain.on('asynchronous-message', function(event, arg) {
  console.log(arg);  // prints "ping"
  event.sender.send('asynchronous-reply', 'pong');
});

ipcMain.on('synchronous-message', function(event, arg) {
  console.log(arg);  // prints "ping"
  event.returnValue = 'pong';
});
```

```javascript
// In renderer process (web page).
const ipcRenderer = require('electron').ipcRenderer;
console.log(ipcRenderer.sendSync('synchronous-message', 'ping')); // prints "pong"

ipcRenderer.on('asynchronous-reply', function(event, arg) {
  console.log(arg); // prints "pong"
});
ipcRenderer.send('asynchronous-message', 'ping');
```

## Listening for Messages

The `ipcMain` module has the following method to listen for events:

### `ipcMain.on(channel, callback)`

* `channel` String - The event name.
* `callback` Function

When the event occurs the `callback` is called with an `event` object and a
message, `arg`.

## IPC Event

The `event` object passed to the `callback` has the following methods:

### `event.returnValue`

Set this to the value to be returned in a synchronous message.

### `event.sender`

Returns the `webContents` that sent the message, you can call
`event.sender.send` to reply to the asynchronous message, see
[webContents.send][webcontents-send] for more information.

[webcontents-send]: http://electron.atom.io/docs/v0.35.0/api/web-contents#webcontentssendchannel-args
