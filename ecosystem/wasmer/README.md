# Wasmer Runtime

Wasmer allows you to run WebAssembly modules either **Standalone** or **Embedded** within [other languages](./#wasmer-language-integrations) such as C/C++, Rust, Python, Go, PHP, Ruby...

By design, the environment within which a WebAssembly module runs is completely isolated (or _sandboxed_) from the native functionality of the underlying host system. This means that _by default_, Wasm modules are designed to perform nothing more than pure computation.

Consequently, access to "OS"-level resources such as file descriptors, network sockets, the system clock, and random numbers is not normally possible from Wasm.

However, there are many cases in which a Wasm module needs to do more than perform pure computation; they must interact with native "OS" functionality.

Wasmer is designed to provide three key features:

1. Enable programs to run in any programming language
2. Enable extremely portable binaries to run unmodified on any "OS" supported by Wasmer (for example, Linux, macOS, Windows and FreeBSD).
3. Act as a secure bridge for Wasm modules to interact with native "OS" functionality, via Application Binary Interfaces (ABIs) such as [`WASI`](https://github.com/webassembly/wasi) and [`Emscripten`](https://github.com/emscripten-core/emscripten) (version 1.38.43 and earlier).

For the first case, we offer [multiple language integrations](./#wasmer-language-integrations), that allow the same Wasm module to be run from within any programming language.

And for the second case, we offer our [Standalone Runtime](getting-started.md) to run Wasm binaries on any platform and chipset.

{% hint style="warning" %}
#### Important

The term "OS" used above is in quotes to indicate that the native function being called might not actually be provided by the host's operating system.

In reality, native functions always belong to the host environment within which the WebAssembly module is being run, and that could be either the host language's runtime environment (for example, JavaScript, Python, or Ruby), or it might be the actual operating system.

Either way though, from a WebAssembly point of view, we don't need to care too much about this detail. All we need to know is that:

* The host can provide "imported" functions for the WebAssembly module
* Via Wasmer's included ABIs, WebAssembly modules can access a set of operating-system-like functions with varying levels of sandboxing
{% endhint %}

## Wasmer Language Integrations

{% content-ref url="../../integrations/rust/" %}
[rust](../../integrations/rust/)
{% endcontent-ref %}

{% content-ref url="../../integrations/c/" %}
[c](../../integrations/c/)
{% endcontent-ref %}

{% content-ref url="../../integrations/go/" %}
[go](../../integrations/go/)
{% endcontent-ref %}

And many more (for which we will be creating docs here soon!)

* [Python](https://github.com/wasmerio/wasmer-python)
* [PHP](https://github.com/wasmerio/wasmer-php)
* [Ruby](https://github.com/wasmerio/wasmer-ruby)
* [Go](https://github.com/wasmerio/wasmer-go)
* [C#](https://github.com/migueldeicaza/WasmerSharp)
* [R](https://github.com/dirkschumacher/wasmr)
* [Elixir](https://github.com/tessi/wasmex)

Let's now get started with the Wasmer Standalone Runtime!
