# Introduction

V8 provides extensive debugging functionality to both users and embedders. Users will usually interact with the V8 debugger through the [Chrome Devtools](https://developer.chrome.com/devtools) interface. Embedders (including Devtools) need to rely directly on the [Inspector Protocol](https://chromedevtools.github.io/debugger-protocol-viewer/tot/).

This page is intended to give embedders the basic tools they need to implement debugging support in V8.

# Connecting to Inspector

V8's [command-line debug shell d8](Using-D8) includes a simple inspector integration through the [InspectorFrontend](https://cs.chromium.org/chromium/src/v8/src/d8.cc?type=cs&q=InspectorFrontend+package:%5Echromium$&l=1849) and [InspectorClient](https://cs.chromium.org/chromium/src/v8/src/d8.cc?type=cs&q=InspectorClient+package:%5Echromium$&l=1916). The client sets up a communication channel for messages sent from the embedder to V8 in

```c++
  static void SendInspectorMessage(
      const v8::FunctionCallbackInfo<v8::Value>& args) {
    // [...] Create a StringView that Inspector can understand.
    session->dispatchProtocolMessage(message_view);
  }
```

while the frontend establishes a channel for messages sent from V8 to the embedder by implementing
sendResponse and sendNotification, which then forward to:

```c++
  void Send(const v8_inspector::StringView& string) {
    // [...] String transformations.
    // Grab the global property called 'receive' from the current context.
    Local<String> callback_name =
        v8::String::NewFromUtf8(isolate_, "receive", v8::NewStringType::kNormal)
            .ToLocalChecked();
    Local<Context> context = context_.Get(isolate_);
    Local<Value> callback =
        context->Global()->Get(context, callback_name).ToLocalChecked();
    // And call it to pass the message on to JS.
    if (callback->IsFunction()) {
      // [...]
      MaybeLocal<Value> result = Local<Function>::Cast(callback)->Call(
          context, Undefined(isolate_), 1, args);
    }
  }
```

# Using the Inspector Protocol

Continuing with our example, d8 forwards inspector messages to JavaScript. The following code implements a basic, but fully functional interaction with inspector through d8:

```js
// inspector-demo.js
// Receiver function called by d8.
function receive(message) {
  print(message)
}

const msg = JSON.stringify({
      id: 0,
      method: "Debugger.enable",
    });

// Call the function provided by d8.
send(msg);

// Run this file by executing 'd8 --enable-inspector inspector-demo.js'.
```

# Further Documentation

A more fleshed-out example of Inspector API usage is available at [test-api.js](https://cs.chromium.org/chromium/src/v8/test/debugger/test-api.js?type=cs&q=test-api&l=1), which implements a simple debugging API for use by V8's test suite.

V8 also contains an alternative Inspector integration at [inspector-test.cc](https://cs.chromium.org/chromium/src/v8/test/inspector/inspector-test.cc?q=inspector-te+package:%5Echromium$&l=1).

The Chrome Devtools wiki provides [full documentation](https://chromedevtools.github.io/debugger-protocol-viewer/tot/) of all available functions.