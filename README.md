# SwiftWebViewBridge

Swift version of [WebViewJavascriptBridge](https://github.com/marcuswestin/WebViewJavascriptBridge) with more simplified, friendly methods to send messages between Swift and JS in UIWebViews.

---
1. [Preview](#1)
2. [Requirements](#2)
3. [Installation](#3)
4. [How to use it](#4)
5. [Dig it up](#5)

<h2 id="1">Preview</h2>

![preview](http://ww3.sinaimg.cn/mw690/9161297cgw1f1kkmao1elj20b90k076h.jpg)

<h2 id="2">Requirements</h2>

1. Xcode7.0+
2. iOS8.0+
3. [SwiftyJSON](https://github.com/SwiftyJSON/SwiftyJSON)(The communication between Swift and JS depends on JSON messages.)

<h2 id="3">Installation</h2>

####Cocoapods

1. Add these lines below to your Podfile 

	```
	platform :ios, '8.0'
	use_frameworks!	
	pod 'SwiftWebViewBridge', '~> 0.1.1'
	```
2. Install the pod by running `pod install`
3. import SwiftWebViewBridge

####Manually

Drag SwiftWebViewBridge document or files in it to your project. If If you have imported SwiftyJSON by adding SwiftyJSON.swift file or Cocoapods etc, you could remove SwiftyJSON.swift in SwiftWebViewBridge.


<h2 id="4">How to use it:</h2>

###General

1. initialize a bridge with defaultHandler
2. register handlers to handle different events
3. send data / call handler on both sides

###For Swift

#####func bridge(webView: UIWebView, defaultHandler handler: SWVBHandler?) -> SwiftJavaScriptBridge?
Generate a bridge with associated webView and default handler to deal with messages from js without specifying designated handler

```
guard let bg = SwiftJavaScriptBridge.bridge(webView, defaultHandler: { data, responseCallback in
	print("Swift received message from JS: \(data)")
	responseCallback("Swift already got your msg, thanks")
}) else {        
  	print("Error: initlizing bridge failed")
  	return
}
```
#####func registerHandlerForJS(handlerName name: String, handler:SWVBHandler)
Register a handler for JavaScript calling

```
// take care of retain cycle!
bridge.registerHandlerForJS(handlerName: "getSesionId", handler: { [unowned self] data, responseCallback in
	let sid = self.session            
	responseCallback(["msg": "Swift has already finished its handler", "returnValue": [1, 2, 3]])
})
```
#####func sendDataToJS(data: AnyObject)
Simply Sent data to JS 

```
bridge.sendDataToJS(["msg": "Hello JavaScript", "gift": ["100CNY", "1000CNY", "10000CNY"]])
```
#####func sendDataToJS(data: AnyObject, responseCallback: SWVBResponseCallBack?)
Send data to JS with callback closure

```
bridge.sendDataToJS("Did you received my gift, JS?", responseCallback: { data in
	print("Receiving JS return gift: \(data)")
})
```
#####func callJSHandler(handlerName: String?, params: AnyObject?, responseCallback: SWVBResponseCallBack?)
Call JavaScript registered handler

```
bridge.callJSHandler("alertReceivedParmas", params: ["msg": "JS, are you there?"], responseCallback: nil)
```

#####two custom closures mentioned above 

```
/// 1st param: resonseData to JS
public typealias SWVBResponseCallBack = AnyObject -> Void
/// 1st param: data sent from JS; 2nd param: responseCallback for sending data back to JS
public typealias SWVBHandler = (AnyObject, SWVBResponseCallBack) -> Void
```

###For JavaScript

#####function init(defaultHandler)

```
bridge.init(function(message, responseCallback) {
	log('JS got a message', message)
	var data = { 'JS Responds' : 'Message received = )' }
	responseCallback(data)
})
```
#####function registerHandlerForSwift(handlerName, handler)

```
bridge.registerHandlerForSwift('alertReceivedParmas', function(data, responseCallback) {
	log('ObjC called alertPassinParmas with', JSON.stringify(data))
	alert(JSON.stringify(data))
	var responseData = { 'JS Responds' : 'alert triggered' }
	responseCallback(responseData)
})
```

#####function sendDataToSwift(data, responseCallback)

```
bridge.sendDataToSwift('Say Hello Swiftly to Swift')
bridge.sendDataToSwift('Hi, anybody there?', function(responseData){
	alert("got your response: " + JSON.stringify(responseData))
})
```

#####function callSwiftHandler(handlerName, data, responseCallback)

```
SwiftWebViewBridge.callSwiftHandler("printReceivedParmas", {"name": "小明", "age": "6", "school": "GDUT"}, function(responseData){
	log('JS got responds from Swift: ', responseData)
})
```
<h2 id="5">Dig it up</h2>

If you are interesting in how swift and javascript communicate with each other, the source code have very detailed comments on it. Also, you can find the unminified javascript file in UnminifiedJavascript doc.