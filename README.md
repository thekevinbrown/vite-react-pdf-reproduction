# vite-react-pdf-reproduction

Reproduces an issue trying to use React PDF in a Vite app.

## To Run

```
$ yarn
$ yarn dev
```

Expected Result: Successful render
Actual Result:

```
Uncaught ReferenceError: global is not defined
    at node_modules/blob/index.js (index.js:5)
    at __require (chunk-KVFJW2XH.js?v=ce5c473e:12)
    at node_modules/blob-stream/index.js (index.js:3)
    at __require (chunk-KVFJW2XH.js?v=ce5c473e:12)
    at asyncToGenerator.js:32
```

## Investigation

The code throwing this error appears to be from the `blob` library here: https://github.com/amitport/blob/blob/master/main.js#L5

React PDF requires `blob-stream` which requires `blob`.

What's strange is that it's being rewritten to have `global` in front of it, even though we're in a browser context. It's definitely not written as `global` in what's published to NPM: https://unpkg.com/blob@0.1.0/main.js

What causes `global` to appear here?

Also, interestingly, the `@react-pdf/renderer` types say that the library exports `Document`, `Page`, `Text` and a bunch of other components, but when I look at what ends up in `.vite/@react-pdf/renderer` the export does not actually have these things.

My theory was that this was a case of https://github.com/vitejs/vite/issues/2329, but it doesn't appear to be, as none of these libraries have a server entry point.
