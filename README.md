# Proof of concept - Working with Kotlin and Javascript mulitplatform

# Goals

The benefit of using Kotlin to generate JS binaries is that I can have a single language for all my
source code, without having to master many different languages.

If the following goals can be achieved by this project, then I can use Kotlin Javascript
multiplatform to build CLI (Node.js) apps and Browser based apps (that run in Chrome or electron):

- Use KT to write web apps that run in Node.js (CLI), Browser (Vanilla JS), and Browser (React).
- Be able to import JS deps (that have TS bindings) and use them (eg: lodash, Ink3, etc).
- Be able to debug KT code, after it has compiled to JS and is running Browser or Node.js.

# How it all works

Kotlin MP uses a gradle as its build system, and JB has created a gradle plugin
["Kotlin/JS plugin" - `org.jetbrains.kotlin.js`](https://plugins.gradle.org/plugin/org.jetbrains.kotlin.js)
(very similar to what they did for IDEA plugins) that actually orchestrates a few things:

- Kotlin/JS `IR` compiler that compiles KT code to IR code and then to JS
  - It is possible to use the `LEGACY` compiler, but that isn't recommended because Kotlin 1.4.x is
    all about the new IR compiler backend. Since I don't have any old code, I don't have to worry
    about the lack of binary compatibility (of generated JS) between the `LEGACY` and `IR`
    compilers.
- Yarn to handle npm deps
  - [Dukat](https://github.com/Kotlin/dukat) to handle TS type defs for npm deps
- Webpack to build the artifacts
- Allows for selecting browser or Node.js execution environments
  - Configure Node.js package.json settings
  - Configure browser settings
- Allows for run configurations to be declared
- Allows for test configurations to be declared
- And more.

## Node.js - Configuring the execution environment and deps

Here are the
[official docs](https://kotlinlang.org/docs/js-project-setup.html#execution-environments) on
configuring the `kotlin { js(IR) { nodejs { ... } } }` portion of the `build.gradle.kts` file.

The things in `...` are:

- `binaries.executable()` - This causes the compiler to emit executable `.js` files.
- `dependencies { ... }` - The things that go in here are:
  - The Kotlin/JS [standard library](https://kotlinlang.org/api/latest/jvm/stdlib/index.html) is
    always imported, so there's nothing to add here for it.
  - You have to import the [`kotlin.test`](https://kotlinlang.org/api/latest/kotlin.test/index.html)
    library if you have tests.
  - You can also import the [`kotlinx-nodejs`](https://github.com/Kotlin/kotlinx-nodejs)
    experimental library if you want direct access to Node.js APIs.
  - npm deps can be added here with this
    [syntax](https://kotlinlang.org/docs/js-project-setup.html#npm-dependencies).
    - Eg of npm dep: `implementation(npm("react", "> 14.0.0 <=16.9.0"))`
    - Eg of npm devDep: `implementation(devNpm(...))`

### Debugging support

- Node.js debugging fails!
  - [More info](https://discuss.kotlinlang.org/t/debug-kotlin-js-when-running-on-nodejs/20885)

## TODO Browser - Configuring the execution environment and deps

# TODO Important Gradle tasks that are provided
