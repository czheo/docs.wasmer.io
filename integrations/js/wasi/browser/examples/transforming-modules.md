# Transforming Modules

{% hint style="success" %}
**Note**: The final code for this example can be found on Github: [transforming-modules](https://github.com/wasmerio/docs.wasmer.io/tree/master/integrations/js/wasi/browser/examples/transforming-modules).
{% endhint %}

Irrespective of whether your JavaScript code runs on the client or the server, the statement shown below to [transform a WASI module](https://github.com/wasmerio/docs.wasmer.io/tree/e0f7639306bb4cf18cd0c23876b80f787d6b5876/integrations/js/module-transformation/README.md) will be always needed until browsers land `BigInt` support in WebAssembly.

## Setup Instructions

Please repeat the step-by-step instructions given in the [Hello World](https://github.com/wasmerio/docs.wasmer.io/tree/e0f7639306bb4cf18cd0c23876b80f787d6b5876/integrations/js/wasi/browser/examples/hello-world/README.md) example, but with the following changes:

1. Call your project `wasmer-js-transforming-wasi`
2. Download the Wasm module [`clocktimeget.wasm`](https://github.com/wasmerio/docs.wasmer.io/raw/master/integrations/shared/wat/wasi/clocktimeget.wasm) and store it in the `static` directory

## JavaScript Coding

The coding seen below is very similar to the coding used for the previous Hello World example — but with one very important difference!

Inside function `startWasiTask`, we fetch the Wasm file contents and convert it to a `Uint8Array` as before, but then there is the additional line:

```javascript
const loweredWasmBytes = await lowerI64Imports(wasmBytes)
```

The call to function `lowerI64Imports` performs the all-important transformation that allows a JavaScript `BigInt` to be transferred to a WebAssembly `i64`.

Now that the interface has been transformed, we can instantiate the WebAssembly module and invoke it as before.

```javascript
import { WasmFs } from '@wasmer/wasmfs'
import { lowerI64Imports } from "@wasmer/wasm-transformer"
import { WASI } from '@wasmer/wasi'
import browserBindings from "@wasmer/wasi/lib/bindings/browser"

const wasmFilePath = '/clocktimeget.wasm'  // Path to our WASI module

// Instantiate new WASI and WasmFs Instances
// IMPORTANT:
// Instantiating WasmFs is only needed when running in a browser.
// When running on the server, NodeJS's native FS module is assigned by default
const wasmFs = new WasmFs()

let wasi = new WASI({
  // Arguments passed to the Wasm Module
  // The first argument is usually the filepath to the executable WASI module
  // we want to run.
  args: [wasmFilePath],

  // Environment variables that are accesible to the WASI module
  env: {},

  // Bindings used by the WASI instance (fs, path, etc...)
  bindings: {
    ...browserBindings,
    fs: wasmFs.fs
  }
})

// Async Function to run our WASI module/instance
const startWasiTask =
  async () => {
    // Fetch our Wasm File
    const response  = await fetch(wasmFilePath)
    const wasmBytes = new Uint8Array(await response.arrayBuffer())

    // IMPORTANT EXTRA STEP!
    // We must transform the WebAssembly module interface!
    const loweredWasmBytes = await lowerI64Imports(wasmBytes)

    // Instantiate the WebAssembly file
    let wasmModule = await WebAssembly.compile(loweredWasmBytes);
    let instance = await WebAssembly.instantiate(wasmModule, {
      ...wasi.getImports(wasmModule)
    });

    wasi.start(instance)                      // Start the transformed WASI instance
    let stdout = await wasmFs.getStdOut()     // Get the contents of stdout
    document.write(`Standard Output: ${stdout}`) // Write stdout to the DOM
  }

// Everything starts here
startWasiTask()
```

On the both the browser screen and the JavaScript console, you should see the text `Done!`.

{% hint style="warning" %}
#### Known Limitation

This example is somewhat contrived because the WebAssembly module has been hard-coded to return the text string `Done!` rather than the time value from `clock_time_get`.

Anything written to standard out should be a printable string followed by a carriage return character, not the raw `i32` value returned from `clock_time_get`. Therefore, before being able to return the actual clock time, this WebAssembly module would additionally need to convert the raw `i32` value to a printable string before then writing it to standard out.
{% endhint %}

Next, let's look at handling input and output via WASI.

{% hint style="info" %}
If you want to run the examples from the docs codebase directly, you can also do:

```bash
git clone https://github.com/wasmerio/docs.wasmer.io.git
cd docs.wasmer.io/integrations/js/wasi/browser/examples/transforming-modules
npm run dev
```
{% endhint %}
