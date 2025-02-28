Compile

```bash
dart compile wasm web/main.dart -o site/main.wasm
```

Copy index.html and style.css to site/

```bash
cp web/index.html web/styles.css site/
```

site/main.dart.js

```js
(async function () {
    let dart2wasm_runtime;
    let moduleInstance;
    try {
        const dartModulePromise = WebAssembly.compileStreaming(fetch('main.wasm'));
        const imports = {};
        dart2wasm_runtime = await import('./main.mjs');
        moduleInstance = await dart2wasm_runtime.instantiate(dartModulePromise, imports);
    } catch (exception) {
        console.error(`Failed to fetch and instantiate wasm module: ${exception}`);
        console.error('See https://dart.dev/web/wasm for more information.');
    }

    if (moduleInstance) {
        try {
            await dart2wasm_runtime.invoke(moduleInstance);
        } catch (exception) {
            console.error(`Exception while invoking test: ${exception}`);
        }
    }
})();
```