# Storybook addons and Prettier issue reproduction

### The problem
We can't use a global installation of `prettier` in yarn scripts (or npm scripts) when certain storybook packages like `@storybook/addon-storysource` or `@storybook/source-loader` are installed.

### Steps to reproduce
0. Run `yarn global add prettier@2.0.5` to install `prettier` globally.
1. Clone this repo
2. Run `yarn install`
3. Run `yarn format` (the script `format` is defined as `prettier --version && which prettier && prettier '*.js'`)

This will print
```
yarn run v1.22.0
$ prettier --version && which prettier && prettier '*.js'
1.19.1
/Users/manjunathad/Projects/personal/storybook-prettier-issue-reproduction/node_modules/.bin/prettier
const log = message => console.log(message);
✨  Done in 0.41s.
```

Notice that it is using prettier 1.19.1, which is installed as a dependency of the mentioned storybook addons instead of using the globally installed version 2.0.5.

The intention is to use the global installation of `prettier` for formatting, but the `prettier` which is specified as a dependency of storybook packages comes inbetween and breaks stuff.

### Why?
The packages `@storybook/addon-storysource` and `@storybook/source-loader` include `prettier` as a `dependency`. Because of this, `prettier` gets installed in local `node_modules` whenever we install these addons. When we run `prettier` in a yarn script (or npm script), it first looks for `prettier` in `node_modules`. Since it finds `prettier` installed in `node_modules`, it'll use that instead of using the global `prettier` installed. This behavior breaks the vscode prettier extension as well.
