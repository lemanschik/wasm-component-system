# wasm-component-system
Education about WASM components and how they work in the Browser. aka WASM Module System. 

Most people understand about wasm that it is a way to run binary code in the browser. It does so via sharedMemory in ideal case.
To Connect 2 WASM Modules we implemented so called Readable and Writeable Stream interfaces iterator based.

This means 2 WASModules can interact on the same sharedMemory and you do not need to handle Locking so you do not need to write logic.
Everything you need gets supplyed by the VM Already. 


```js
// This defines what happens 
const importObject = { imports: { imported_func: (arg) => console.log(arg) } };

WebAssembly.instantiateStreaming(fetch("simple.wasm"), importObject).then(
  (loadedInMemory) => loadedInMemory.instance.exports.exported_func()
);
```


```js
const wasmModuleBasedInputSource = new ReadableStream({
  start: (controller) => {
    const importObject1 = { imports: { imported_func: (arg) => controller.enqueue(arg) } };
    WebAssembly.instantiateStreaming(fetch("simple.wasm"), importObject).then(
      (loadedInMemory) => loadedInMemory.instance.exports.exported_func()
    );
  }
})
```


```js
const wasmModuleBasedInputSource = new ReadableStream({
  start: async (controller) => (await WebAssembly.instantiateStreaming(fetch("simple.wasm"), 
      { imports: { imported_func: (arg) => controller.enqueue(arg) } })).instance.exports.exported_func(),
})
```
