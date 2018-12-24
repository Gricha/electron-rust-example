# electron-rust-example
Simple example of how to create bloated Electron app with Rust dependencies


## What is this?

Intention of this project is to provide an example and step-by-step description of how to integrate Rust modules to Electron app without too much hassle with boilerplate. The project built that way will be pretty bloated and a resource hog.

It's not meant as a tool that does the job and more of a reference of how the things can be tied together.

## Why?

It can be useful for experimentation with Rust if you care about presentation layer being more matured than what Rust currently offers. This comes with a cost of having entire Electron app.

## How to use it?

You can check out the code in examples to see how the integration is done. You can copy over the code from there, but since underlying frameworks can change over time, I'd recommend following the steps below.

## Building Electron app with Rust dependencies

These steps will set up an Electron app using electron-react-boilerplate. This means it will have React/Redux/Flow integrated in it.

Rust module will be put in as separate dependency that can be pulled in by main app.

1. Follow install instructions: https://github.com/electron-react-boilerplate/electron-react-boilerplate
```
git clone --depth 1 --single-branch --branch master https://github.com/electron-react-boilerplate/electron-react-boilerplate.git my-electron-project
cd my-electron-project
yarn
```
Verify that Electron can start with it by running `yarn dev`.

Remove git artifacts and init your own repository in a root.

```
rm -rf .git
```

2. Follow steps to install Neon: https://github.com/neon-bindings/neon
3. Create new project using Neon (do it in the root):
```
# Get to root
cd ../
neon new example-rust
```

4. Export addon from your rust library. Swap `example-rust/lib/index.js` for:
``` index.js

var addon = require('../native');

module.exports = addon;

```

5. Pull in dependency back to electron project.
```
yarn add file:../example-rust
```
This approach has downside - everytime code is updated in example-rust, version has to be upgraded.
Ideally linking would work better. In few next steps we'll make it less painful.

6. `yarn add --dev electron-build-env`
7. Build Rust project: `electron-build-env neon build example-rust`
8. At this point entire project should work with rust. Try importing module in Home.js and calling a function:
```
import Native from 'example-rust';

console.log(Native.hello());
```

Verify that hello is written in console log when running `yarn dev`.

## Optional steps

9. To speed up iteration process for the modules, we can wrap upgrading and rebuilding rust module with simple script.
Copy over `rust` directory from root of this project to root of your electron project and add script to package.json:
```
"upgrade-rust-dependency": "sh ./rust/UpgradeRustDependency.sh"
```

Then you can call `yarn upgrade-rust-dependency example-rust` to iterate on Rust module.
