# lerna-rollup-example

```sh
nvm use 8
npm install
npm run watch
```

### The Problem

All of the expected lerna logging is replaced by the initial watch logging:

```sh
rollup v0.48.2
gamma: src/index.js → dist/index.js...
alpha: created dist/index.js in 19ms
bravo: src/index.js → dist/index.js...
gamma: created dist/index.js in 17ms
bravo: created dist/index.js in 16ms
alpha: waiting for changes...
gamma: waiting for changes...
bravo: waiting for changes...
```

Touch one of the JS files under `packages`, and you'll notice the output gets cleared, despite only one package changing:

```sh
rollup v0.48.2
alpha: src/index.js → dist/index.js...
alpha: created dist/index.js in 3ms
alpha: waiting for changes...
```

Finally, once you `^C` out of the watchers, you'll find that you're still in the alternate buffer, which is not good. (In OS X Terminal.app's menubar, "View -> Hide Alternate Screen" is now enabled)

### The Solution

`npm link` a local rollup with this PR, and the output continues to scroll, does not disappear after changes occur, and remains after exiting the process:

```sh
$ npm run watch

> lerna-rollup-example@1.0.0 prewatch /Users/daniels/github/lerna-rollup-example
> npm run bootstrap


> lerna-rollup-example@1.0.0 bootstrap /Users/daniels/github/lerna-rollup-example
> lerna bootstrap

lerna info version 2.0.0
lerna info Bootstrapping 3 packages
lerna info lifecycle preinstall
lerna info Symlinking packages and binaries
lerna info lifecycle postinstall
lerna info lifecycle prepublish
lerna success Bootstrapped 3 packages

> lerna-rollup-example@1.0.0 watch /Users/daniels/github/lerna-rollup-example
> lerna exec --parallel -- rollup -f cjs -i src/index.js -o dist/index.js -w

lerna info version 2.0.0
lerna info exec in 3 package(s): rollup -f cjs -i src/index.js -o dist/index.js -w
alpha: rollup v0.48.2
gamma: rollup v0.48.2
bravo: rollup v0.48.2
alpha: bundles src/index.js → dist/index.js...
gamma: bundles src/index.js → dist/index.js...
bravo: bundles src/index.js → dist/index.js...
alpha: created dist/index.js in 18ms
gamma: created dist/index.js in 15ms
bravo: created dist/index.js in 17ms
alpha: bundles src/index.js → dist/index.js...
alpha: created dist/index.js in 4ms
^C
$
```
