# Communicating with the webview

When creating a UI, chances are that you will need to communicate between your "frontend" (the webview) and you "backend" (the plugin running in Sketch).

## Sending a message to the WebView from your plugin command

If you want to update the UI when something changes in Sketch (when the selection changes for example), you will need to send a message to the WebView.

To do so, you need to define a global function in the WebView that you will call from the plugin.

On the WebView:

```js
window.someGlobalFunctionDefinedInTheWebview = function(arg) {
  console.log(arg)
}
```

On the plugin:

```js
const res = browserWindow.webContents.executeJavaScript(
  'someGlobalFunctionDefinedInTheWebview("hello")'
)
```

## Sending a message to the WebView from another plugin or command

If you do not have access to the WebView instance (because it was created in another command for example), you can still send a message by using the `id` used to create the WebView.

```js
import { isWebviewPresent, sendToWebview } from 'sketch-module-web-view/remote'

if (isWebviewPresent('unique.id')) {
  sendToWebview('unique.id', 'someGlobalFunctionDefinedInTheWebview("hello")')
}
```

## Sending a message to the plugin from the WebView

When the user interacts with the WebView, you will probably need to communicate with your plugin. You can do so by listening to the event that the WebView will dispatch.

For example, if we wanted to log something in the plugin, we could define the `nativeLog` event.

On the plugin:

```js
var sketch = require('sketch')

browserWindow.webContents.on('nativeLog', function(s) {
  sketch.UI.message(s)
})
```

On the webview:

```js
import pluginCall from 'sketch-module-web-view/client'

pluginCall('nativeLog', 'Called from the webview')

// you can pass any argument that can be stringified
pluginCall('nativeLog', {
  a: b,
})

// you can also pass multiple arguments
pluginCall('nativeLog', 1, 2, 3)
```
