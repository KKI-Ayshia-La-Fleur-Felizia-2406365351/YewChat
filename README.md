# YewChat 💬

> Source code for [Let’s Build a Websocket Chat Project With Rust and Yew 0.19 🦀](#)

## Install

1. Install the required toolchain dependencies:
   ```npm i```

2. Follow the YewChat post!

## Branches

This repository is divided to branches that correspond to the blog post sections:

* main - The starter code.
* routing - The code at the end of the Routing section.
* components-part1 - The code at the end of the Components-Phase 1 section.
* websockets - The code at the end of the Hello Websockets! section.
* components-part2 - The code at the end of the Components-Phase 2 section.
* websockets-part2 - The code at the end of the WebSockets-Phase 2 section.

## Report Entry: Root Cause Analysis of Webpack Compilation Failure
Error Statement: Module not found: Error: Can't resolve './pkg' in '...\YewChat'

C:\Users\LENOVO\ADPRO\tutorial10\YewChat\pkg\index.js doesn't exist

Technical Root Cause
The frontend application uses an older Webpack 5 architecture configured via a custom third-party plugin (@wasm-tool/wasm-pack-plugin). This plugin is tasked with executing the Rust compilation pipeline in the background and generating WebAssembly glue files inside a localized ./pkg/ directory.

The build fails explicitly because Webpack is expecting a module bundler output format featuring an entry wrapper named index.js (as imported by bootstrap.js via import("./pkg")).

However, the automated compilation sequence fails to generate this file due to two cascading environment discrepancies:

Compiler Dependency Mismatch: The tutorial's configuration files specify an legacy version of the wasm-bindgen crate from 2022. Modern Rust toolchains reject this outdated dependency outright, causing the background compilation of the Rust crate to crash silently before generating any assets.

Target Layout Discrepancy: When background compilation is partially salvaged or re-run automatically by the Webpack development script, modern iterations of wasm-pack default to a web-native layout (--target web). This process outputs a file named strictly after the crate name (yewchat.js) rather than the bundler-generic entry file (index.js).

Conclusion for Report
Because the background build pipeline outputs yewchat.js instead of index.js, Webpack's module resolution tracing fails. The bundler cannot locate the explicitly required ./pkg/index.js entry point, causing the webpack-dev-server compilation step to abort with a Module not found termination.

I have spent 4 hours on this.
