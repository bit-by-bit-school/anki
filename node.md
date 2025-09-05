---
## Node Interview Quesions
---
# What is Node.js?**
     Node.js is an open-source, cross-platform, back-end JavaScript runtime environment that runs on the V8 engine (the same engine that powers Google Chrome) and executes JavaScript code outside a web browser. It allows developers to use JavaScript for server-side scripting.
# Why use Node.js? What are its advantages?**

    *   **Asynchronous and Event-Driven:** Node.js uses a non-blocking, event-driven I/O model, making it lightweight and efficient for I/O-intensive applications.
    *   **JavaScript Everywhere:** Allows developers to use a single language (JavaScript) for both front-end and back-end development.
    *   **Large Ecosystem (NPM):** Node Package Manager (NPM) is the largest ecosystem of open-source libraries in the world.
    *   **Scalability:** Easily scalable for microservices and real-time applications.
    *   **Fast Execution:** Built on Chrome's V8 engine, which compiles JavaScript into native machine code.
    *   **Good for Real-Time Applications:** Its event-driven architecture is well-suited for WebSockets, chat applications, etc.
# How is Node.js single-threaded? How does it handle concurrency?**
     Node.js itself runs JavaScript code in a single main thread. However, it achieves concurrency through its event-driven architecture and non-blocking I/O operations. When an I/O operation (like reading a file or making a network request) is initiated, Node.js offloads it to the system's kernel (or a worker thread pool managed by libuv) and registers a callback. The main thread is then free to handle other requests. When the I/O operation completes, the callback is placed in the event queue and executed by the event loop when the call stack is free.
# What is the Event Loop in Node.js?**
     The Event Loop is the core mechanism that enables Node.js's non-blocking, asynchronous behavior, even though JavaScript is single-threaded. It continuously checks the event queue for pending callback functions. If the call stack is empty, it takes the first callback from the queue and pushes it onto the call stack for execution. Libuv provides the event loop in Node.js.
# What is `libuv`?**
     `libuv` is a C library that provides the asynchronous, event-driven I/O capabilities for Node.js. It handles tasks like the event loop, thread pool for blocking operations (like file system access, DNS lookups, some crypto), and platform-specific I/O.
# What is blocking vs. non-blocking I/O?**

    *   **Blocking I/O:** When a program makes a blocking I/O call, its execution is paused (blocked) until that I/O operation completes. No other code can run in that thread.
    *   **Non-blocking I/O:** When a program makes a non-blocking I/O call, the call returns immediately, allowing the program to continue executing other code. The I/O operation happens in the background, and a callback or other mechanism is used to notify the program when it's complete. Node.js heavily favors non-blocking I/O.
# What is the `process` object in Node.js?**
     The `process` object is a global object that provides information about, and control over, the current Node.js process.
    Examples:
    *   `process.env`: Environment variables.
    *   `process.argv`: Command-line arguments.
    *   `process.cwd()`: Current working directory.
    *   `process.exit()`: Exits the process.
    *   `process.on('uncaughtException', ...)`: Event listener for uncaught exceptions.
# What are `__dirname` and `__filename`?**
     These are global-like variables (scoped to the current module) that provide path information:
    *   `__dirname`: The directory name of the current module (absolute path).
    *   `__filename`: The file name of the current module (absolute path).
# How does Node.js differ from JavaScript in the browser?**

    *   **Environment:** Node.js runs JS on the server; browser JS runs in the client's browser.
    *   **Global Object:** `global` in Node.js; `window` in the browser.
    *   **APIs:** Node.js has access to server-side APIs like file system (`fs`), HTTP server creation (`http`), OS information (`os`), etc. Browser JS has DOM/BOM APIs.
    *   **Module System:** Node.js primarily uses CommonJS (`require`/`module.exports`) and supports ES Modules (`import`/`export`). Browsers use ES Modules via `<script type="module">`.
    *   **Purpose:** Node.js is for building server-side applications, CLI tools, etc. Browser JS is for client-side interactivity and UI manipulation.
# What is the global object in Node.js?**
     The global object in Node.js is `global`. Variables declared without `var`, `let`, or `const` in the top-level scope of a module are *not* automatically added to the `global` object (unlike in browsers with `window`). To explicitly create a global variable, you'd use `global.myVar = ...;`.
# Explain REPL in Node.js.**
     REPL stands for Read-Eval-Print Loop. It's an interactive programming environment that takes single user inputs (expressions), evaluates them, and returns the result to the user. You can start the Node.js REPL by typing `node` in your terminal. It's useful for quick experiments and learning.
# What is `process.nextTick()`? How does it differ from `setImmediate()` and `setTimeout(fn, 0)`?**

    *   **`process.nextTick(callback)`:** Schedules the `callback` to be executed on the *next tick* of the event loop, i.e., after the current operation completes but *before* any other I/O events or timers are processed. Callbacks passed to `nextTick` are processed before the event loop continues. It has higher priority than `setImmediate`.
    *   **`setImmediate(callback)`:** Schedules the `callback` to be executed in the "check" phase of the event loop, which runs after the "poll" phase (where I/O events are handled) and after timers. It effectively runs "immediately after" I/O callbacks.
    *   **`setTimeout(callback, 0)`:** Schedules the `callback` to be executed after a minimum delay of 0 milliseconds. In practice, the delay can be longer due to OS scheduling and other tasks. Its callbacks are processed in the "timers" phase of the event loop.
    **Order:** `process.nextTick` callbacks run first, then Promises, then `setTimeout(0)` (or other timers whose delay has elapsed), then `setImmediate`. The exact order between `setTimeout(0)` and `setImmediate` can be unpredictable if they are called outside an I/O cycle, but within an I/O cycle, `setImmediate` is guaranteed to run after I/O callbacks and before timers.
# What are environment variables and how to use them in Node.js?**
     Environment variables are key-value pairs that are part of the environment in which a process runs. They are used for configuration (e.g., API keys, database credentials, port numbers) without hardcoding them.
    In Node.js, you access them via `process.env`:
    ```javascript
    const apiKey = process.env.API_KEY;
    const port = process.env.PORT || 3000;
    console.log(`API Key: ${apiKey}, Port: ${port}`);
    ```
    They can be set in the shell before running the Node app (e.g., `PORT=8080 node app.js`) or using a `.env` file with a library like `dotenv`.
# How do you get command-line arguments in a Node.js script?**
     Using `process.argv`. It's an array where:
    *   `process.argv[0]`: Path to the Node.js executable.
    *   `process.argv[1]`: Path to the JavaScript file being executed.
    *   `process.argv[2]...`: The actual command-line arguments passed.
    ```javascript
    // node app.js arg1 arg2
    console.log(process.argv);
    // Output: ['/path/to/node', '/path/to/app.js', 'arg1', 'arg2']
    const myArgs = process.argv.slice(2);
    console.log('My arguments: ', myArgs); // ['arg1', 'arg2']
    ```
# How can you exit a Node.js process?**
     Using `process.exit(code)`.
    *   `code` is an optional integer. A code of `0` indicates success. A non-zero code (typically `1`) indicates an error.
    ```javascript
    if (someErrorCondition) {
        console.error("An error occurred!");
        process.exit(1);
    }
    console.log("Process completed successfully.");
    process.exit(0);
    ```
# What is the purpose of the V8 engine?**
     V8 is Google's open-source high-performance JavaScript and WebAssembly engine, written in C++. Node.js uses V8 to compile JavaScript code into native machine code, enabling fast execution of JavaScript outside the browser.
# What are some common use cases for Node.js?**

    *   Web Servers & APIs (REST, GraphQL)
    *   Real-time applications (chat, gaming servers using WebSockets)
    *   Microservices
    *   Command-Line Interface (CLI) tools
    *   Build tools and task runners (like Webpack, Gulp used with Node.js)
    *   Data streaming applications
    *   Internet of Things (IoT) devices
# Is Node.js suitable for CPU-intensive tasks? Why or why not?**
     Node.js is generally *not* ideal for long-running, CPU-intensive tasks directly in its main thread because it's single-threaded. A CPU-bound operation will block the event loop, preventing other requests from being handled, leading to poor performance and responsiveness.
    However, Node.js can handle CPU-intensive tasks by:
    *   Offloading them to a worker thread pool using native C++ addons.
    *   Using the `worker_threads` module (for CPU-bound JavaScript).
    *   Spawning child processes (`child_process` module).
    *   Using a job queue system and separate worker services.
# What is "callback hell" in Node.js and how can it be mitigated?**
     "Callback hell" (Pyramid of Doom) refers to deeply nested callback functions, common in early Node.js code due to its asynchronous nature. This makes code hard to read, debug, and maintain.
    Mitigation:
    *   **Promises:** Use Promises (`.then()`, `.catch()`) for cleaner asynchronous control flow.
    *   **Async/Await:** (Built on Promises) Provides a more synchronous-looking syntax for async code.
    *   **Named Functions:** Break down callbacks into smaller, named functions.
    *   **Modularization:** Divide code into logical modules.
    *   **Control Flow Libraries:** Libraries like `async.js` (though less common with modern JS features).
# How can you make Node.js listen on a specific port, e.g., 80?**
     When creating an HTTP server, you specify the port in the `listen` method. To listen on port 80 (standard HTTP port), you might need superuser/administrator privileges, as ports below 1024 are typically restricted.
    ```javascript
    const http = require('http');
    const server = http.createServer((req, res) => {
      res.writeHead(200, {'Content-Type': 'text/plain'});
      res.end('Hello World\n');
    });
    // To listen on port 80 (may require sudo on Linux/macOS):
    // server.listen(80, '127.0.0.1', () => {
    //   console.log('Server running on port 80');
    // });
    // More commonly, for development:
    server.listen(3000, '127.0.0.1', () => {
      console.log('Server running on port 3000');
    });
    ```
    In production, you often run Node.js on a higher port (e.g., 3000) and use a reverse proxy like Nginx or Apache to handle port 80/443 and forward requests.

# What are modules in Node.js?**
     Modules are reusable blocks of code encapsulated in separate files. Node.js uses a module system to organize code, promote reusability, and prevent namespace pollution. Each file is treated as a separate module.
# Explain CommonJS modules (`require`, `module.exports`, `exports`).**
     CommonJS is the default module system in Node.js for `.js` files (unless configured otherwise).
    *   **`require(modulePath)`:** A built-in function used to import a module. It reads and executes the module file and returns the `module.exports` object.
    *   **`module.exports`:** An object that is exposed by a module. Whatever is assigned to `module.exports` is what `require()` will return. By default, it's an empty object.
    *   **`exports`:** A shorthand reference to `module.exports`. You can add properties to it (e.g., `exports.myFunction = ...`). However, if you reassign `exports` directly (e.g., `exports = myFunction`), it breaks the reference to `module.exports`, and the module will export an empty object unless `module.exports` is also reassigned. It's generally safer to use `module.exports` for reassignments.
# How can you use ES Modules (ESM) in Node.js?**
     Node.js supports ES Modules (`import`/`export` syntax). To use them:
    1.  Use the `.mjs` file extension for your ES modules.
    2.  Or, in your `package.json`, set `"type": "module"`. Then `.js` files will be treated as ES modules. If you need to use CommonJS in this mode, you can use the `.cjs` extension.
    ```javascript
    // my-module.mjs or my-module.js (if "type": "module")
    export const myVar = 123;
    export function greet(name) { return `Hello, ${name}`; }

    // main.mjs or main.js (if "type": "module")
    import { myVar, greet } from './my-module.js'; // Note the .js extension often needed
    console.log(myVar);
    console.log(greet('Node'));
    ```
# What is NPM?**
     NPM (Node Package Manager) is the default package manager for Node.js. It's two things:
    1.  A command-line tool for installing, managing, and publishing Node.js packages.
    2.  An online repository (registry) of open-source Node.js packages.
# What is `package.json`? What are its important fields?**
     `package.json` is a manifest file in the root of a Node.js project. It contains metadata about the project, including its dependencies, scripts, version, etc.
    Important fields:
    *   `name`: The name of the package.
    *   `version`: The current version of the package (follows SemVer).
    *   `description`: A brief description.
    *   `main`: The entry point of the package (e.g., `index.js`).
    *   `scripts`: Defines scripts that can be run with `npm run <script-name>` (e.g., `start`, `test`).
    *   `dependencies`: Packages required for the application to run in production.
    *   `devDependencies`: Packages needed only for development and testing (e.g., linters, testing frameworks).
    *   `repository`: Link to the source code repository.
    *   `license`: The license under which the package is distributed.
    *   `type`: ("module" or "commonjs") Specifies the module system to use.
# What is `package-lock.json` (or `yarn.lock`)? Why is it important?**
     `package-lock.json` (NPM) or `yarn.lock` (Yarn) is an automatically generated file that records the exact versions of all direct and indirect dependencies (and their dependencies) that were installed for a project.
    Importance:
    *   **Reproducible Builds:** Ensures that every installation results in the exact same `node_modules` tree, regardless of when or where `npm install` is run. This prevents "works on my machine" issues.
    *   **Dependency Tree:** Describes the complete dependency tree, making it easier to understand and audit.
    It should be committed to version control.
# What is the difference between `dependencies` and `devDependencies` in `package.json`?**

    *   **`dependencies`:** Packages that your project needs to run in production (e.g., Express, Lodash). Installed with `npm install <package-name>`.
    *   **`devDependencies`:** Packages that are only needed for development and testing (e.g., Jest, ESLint, Nodemon). Installed with `npm install --save-dev <package-name>` or `npm install -D <package-name>`. They are typically not installed when your application is deployed to production (e.g., `npm install --production`).
# Explain Semantic Versioning (SemVer).**
     SemVer is a versioning scheme (e.g., `MAJOR.MINOR.PATCH` like `1.2.3`).
    *   **MAJOR (`1`):** Incremented for incompatible API changes.
    *   **MINOR (`2`):** Incremented for adding functionality in a backward-compatible manner.
    *   **PATCH (`3`):** Incremented for backward-compatible bug fixes.
    Prefixes like `^` (caret) and `~` (tilde) in `package.json` control which versions are acceptable during updates:
    *   `~1.2.3`: Allows patch updates (e.g., `1.2.4`, `1.2.9` but not `1.3.0`).
    *   `^1.2.3`: Allows minor and patch updates (e.g., `1.3.0`, `1.2.4` but not `2.0.0`).
# How do you install a package globally vs. locally? When would you use each?**

    *   **Local Installation:** `npm install <package-name>` (or `npm i <package-name>`). Installs the package in the `node_modules` directory of the current project. This is the default and most common way. Use for project-specific dependencies.
    *   **Global Installation:** `npm install -g <package-name>`. Installs the package in a system-wide location, making its command-line tools accessible from anywhere. Use for CLI tools you want to use across projects (e.g., `nodemon`, `create-react-app`, `npm`).
# What does `npm init` do?**
     `npm init` initializes a new Node.js project by creating a `package.json` file. It will prompt you for various pieces of information (name, version, description, etc.). Using `npm init -y` will skip the prompts and create a `package.json` with default values.
# What are `npm scripts`? How do you run them?**
     `npm scripts` are defined in the `scripts` section of `package.json`. They allow you to automate common tasks like starting your application, running tests, linting code, etc.
    ```json
    // package.json
    "scripts": {
      "start": "node server.js",
      "test": "jest",
      "lint": "eslint ."
    }
    ```
    You run them using `npm run <script-name>` (e.g., `npm run lint`). For some common scripts like `start`, `test`, `stop`, `restart`, you can omit `run` (e.g., `npm start`, `npm test`).
# What is `npx`? How is it different from `npm`?**
     `npx` is a package runner tool that comes with NPM (since version 5.2.0).
    *   It allows you to execute Node.js packages from the NPM registry *without* having to install them globally or locally first. If the package is not found locally, `npx` will download it, run it, and then (usually) remove it.
    *   Useful for running CLI tools on a one-off basis (e.g., `npx create-react-app my-app`) or trying out different versions of a package.
    `npm` is primarily for managing packages (installing, updating, publishing).
# How do you check for outdated packages in your project and update them?**

    *   **Check for outdated packages:** `npm outdated`
    *   **Update packages:**
        *   To update all packages to their latest allowed versions based on `package.json` (respecting SemVer ranges): `npm update`
        *   To update a specific package to its latest version (and update `package.json`): `npm install <package-name>@latest`
        *   For a more interactive update experience, tools like `npm-check-updates` (`ncu`) are popular:
            ```bash
            npm install -g npm-check-updates
            ncu # Shows what can be updated
            ncu -u # Updates package.json
            npm install # Actually installs the updated versions
            ```
# What are circular dependencies in Node.js modules, and how can they be problematic?**
     A circular dependency occurs when Module A `require`s Module B, and Module B (directly or indirectly) `require`s Module A.
    Problem: When Node.js encounters this, one of the modules will receive an incomplete version (an empty object `{}` or partially built `module.exports`) of the other during its initial loading phase. This can lead to `undefined` errors or unexpected behavior.
    Mitigation:
    *   Restructure code to break the cycle (e.g., by moving shared functionality to a third module).
    *   Delay one of the `require` calls until after the module has fully loaded (e.g., inside a function).
# What is the difference between `npm ci` and `npm install`?**

    *   **`npm install` (or `npm i`):**
        *   Installs dependencies based on `package.json` and `package-lock.json`.
        *   If `package-lock.json` exists and is consistent with `package.json`, it uses the lockfile.
        *   If `package-lock.json` doesn't exist or is inconsistent, it might update versions in `package-lock.json` based on SemVer ranges in `package.json`.
        *   Can install individual packages (`npm i lodash`).
    *   **`npm ci` (Clean Install):**
        *   Designed for automated environments like CI/CD pipelines.
        *   Requires an existing `package-lock.json` or `npm-shrinkwrap.json`. If not present, it will error.
        *   It installs dependencies *exactly* as specified in the lockfile. It does *not* modify the lockfile.
        *   If `package.json` and `package-lock.json` are out of sync, `npm ci` will fail.
        *   First, it deletes the existing `node_modules` directory to ensure a clean install.
        *   Generally faster and more reliable for CI/CD builds.

# What is an error-first callback pattern?**
     A common convention in Node.js for asynchronous functions that use callbacks. The callback function is passed as the last argument, and it expects its first argument to be an error object. If no error occurred, the first argument is `null` or `undefined`, and subsequent arguments contain the result data.
    ```javascript
    fs.readFile('/path/to/file', (err, data) => {
        if (err) {
            // Handle error
            console.error('Error reading file:', err);
            return;
        }
        // Process data
        console.log('File content:', data.toString());
    });
    ```
# How are Promises used in Node.js for async operations?**
     Promises provide a cleaner way to handle asynchronous operations than nested callbacks. Many core Node.js modules now offer Promise-based APIs (e.g., `fs.promises`).
    ```javascript
    const fs = require('fs').promises;

    fs.readFile('/path/to/file')
        .then(data => {
            console.log('File content:', data.toString());
            return fs.writeFile('/path/to/copy', data);
        })
        .then(() => {
            console.log('File copied successfully.');
        })
        .catch(err => {
            console.error('An error occurred:', err);
        });
    ```
# Explain `async/await` in the context of Node.js.**
     `async/await` is syntactic sugar on top of Promises, making asynchronous code look and behave more like synchronous code, improving readability.
    *   `async` functions implicitly return a Promise.
    *   `await` pauses the execution of the `async` function until the awaited Promise settles.
    ```javascript
    const fs = require('fs').promises;

    async function processFile() {
        try {
            const data = await fs.readFile('/path/to/file');
            console.log('File content:', data.toString());
            await fs.writeFile('/path/to/copy', data);
            console.log('File copied successfully.');
            return "Processing complete";
        } catch (err) {
            console.error('An error occurred:', err);
            throw err; // Re-throw or handle
        }
    }

    processFile()
        .then(message => console.log(message))
        .catch(err => console.error("Caught in caller:", err));
    ```
# What is the `EventEmitter` class? Give an example.**
     The `EventEmitter` class, from the `events` core module, is central to Node.js's event-driven architecture. Many Node.js objects (like HTTP servers, streams) inherit from `EventEmitter`. It allows objects to emit named events that cause registered listener functions to be called.
    ```javascript
    const EventEmitter = require('events');

    class MyEmitter extends EventEmitter {}

    const myEmitter = new MyEmitter();

    myEmitter.on('greet', (name) => { // Register a listener
        console.log(`Hello, ${name}!`);
    });

    myEmitter.once('specialEvent', () => { // Listener runs only once
        console.log('This special event happened only once.');
    });

    myEmitter.emit('greet', 'Node.js User'); // Emit the event
    myEmitter.emit('greet', 'Developer');
    myEmitter.emit('specialEvent');
    myEmitter.emit('specialEvent'); // This won't trigger the 'once' listener again
    ```
    Common methods: `on()`, `once()`, `emit()`, `removeListener()`, `removeAllListeners()`.
# What is `util.promisify`?**
     `util.promisify(originalFunction)` is a utility function in the `util` core module. It takes a function that follows the common Node.js error-first callback style and returns a version that returns a Promise.
    ```javascript
    const util = require('util');
    const fs = require('fs');

    const readFilePromise = util.promisify(fs.readFile);

    readFilePromise('/path/to/file', 'utf8')
        .then(data => console.log(data))
        .catch(err => console.error(err));
    ```
    Many modern Node.js APIs offer a direct Promise version (e.g., `fs.promises`), so `promisify` is more for older APIs or third-party libraries that only use callbacks.
# How does the event loop handle different phases (timers, I/O, poll, check, close)?**
     The Node.js event loop has distinct phases, processed in a specific order in each "tick":
    1.  **Timers:** Executes callbacks scheduled by `setTimeout()` and `setInterval()`.
    2.  **Pending Callbacks (or I/O Callbacks):** Executes I/O callbacks deferred to the next loop iteration (e.g., some system errors). (Often called "I/O Callbacks" in simpler diagrams).
    3.  **Idle, Prepare:** Internal use only.
    4.  **Poll:** Retrieves new I/O events; executes I/O-related callbacks (most common callbacks, like network connections, file operations). Blocks if necessary until new I/O events arrive or timers are ready.
    5.  **Check:** Executes callbacks scheduled by `setImmediate()`.
    6.  **Close Callbacks:** Executes close event callbacks (e.g., `socket.on('close', ...)`).
    Between each phase, and after processing callbacks from a phase, Node.js checks for `process.nextTick()` callbacks and Promise microtasks and executes them.
# What are microtasks and macrotasks in Node.js event loop context?**

    *   **Macrotasks (or Tasks):** Callbacks from `setTimeout`, `setInterval`, `setImmediate`, I/O operations. Each phase of the event loop processes a queue of macrotasks.
    *   **Microtasks:** Callbacks from `process.nextTick` and resolved/rejected Promises (`.then`, `.catch`, `.finally`). The microtask queue is processed *after* each macrotask from the macrotask queue finishes, and *before* the event loop moves to the next phase or next macrotask. `process.nextTick` has higher priority within the microtask queue than Promise callbacks.
# Can you block the event loop? What are the consequences?**
     Yes, long-running synchronous code (CPU-intensive calculations, synchronous I/O like `fs.readFileSync` on large files) can block the event loop.
    Consequences:
    *   The server becomes unresponsive.
    *   No other incoming requests can be handled.
    *   No other asynchronous operations can complete their callbacks.
    *   This severely degrades performance and scalability. It's crucial to keep synchronous operations short or offload them.
# When would you use `setImmediate()` over `setTimeout(fn, 0)`?**

    *   Use `setImmediate()` if you want a script to run *after* the current poll phase (I/O events) has completed. It's designed for "run this function as soon as the current I/O cycle is finished."
    *   `setTimeout(fn, 0)` schedules a script to run after a minimum of 0ms in the timers phase. The actual delay can be influenced by OS scheduling and other factors.
    *   If both are called from within an I/O cycle (e.g., inside a `fs.readFile` callback), `setImmediate` will always execute before any timers scheduled in that I/O cycle. If called outside an I/O cycle, their order is non-deterministic.
    *   Generally, `setImmediate` is preferred for breaking up long-running, CPU-bound tasks to give the event loop a chance to process I/O.
# What are `worker_threads` in Node.js? Why use them?**
     The `worker_threads` module (available since Node.js v10.5.0, stable in v12) allows you to run JavaScript code in parallel on separate threads.
    Why use them:
    *   For CPU-intensive JavaScript operations (e.g., complex calculations, image processing in JS) that would otherwise block the main event loop.
    *   Unlike `child_process.fork()`, worker threads can share memory efficiently through `SharedArrayBuffer` (though care must be taken with synchronization).
    *   They are more lightweight than spawning full child processes for CPU-bound tasks that can be done in JS.
    Communication between threads is done via message passing (`parentPort.postMessage()`, `worker.postMessage()`).


**`fs` (File System)**
# How do you read a file synchronously and asynchronously in Node.js?**

    *   **Asynchronously (preferred):**
        ```javascript
        const fs = require('fs');
        fs.readFile('example.txt', 'utf8', (err, data) => {
            if (err) {
                console.error('Error reading file:', err);
                return;
            }
            console.log('File content (async):', data);
        });
        ```
    *   **Synchronously (blocks event loop, use sparingly):**
        ```javascript
        const fs = require('fs');
        try {
            const data = fs.readFileSync('example.txt', 'utf8');
            console.log('File content (sync):', data);
        } catch (err) {
            console.error('Error reading file (sync):', err);
        }
        ```
    *   **Promise-based (modern async):**
        ```javascript
        const fs = require('fs').promises;
        async function readFileAsync() {
            try {
                const data = await fs.readFile('example.txt', 'utf8');
                console.log('File content (promise):', data);
            } catch (err) {
                console.error('Error reading file (promise):', err);
            }
        }
        readFileAsync();
        ```
# How do you write data to a file in Node.js?**

    *   **Asynchronously:**
        ```javascript
        const fs = require('fs');
        const content = 'Hello from Node.js!';
        fs.writeFile('output.txt', content, 'utf8', (err) => {
            if (err) {
                console.error('Error writing file:', err);
                return;
            }
            console.log('File written successfully!');
        });
        ```
    *   **Using `fs.promises`:**
        ```javascript
        const fs = require('fs').promises;
        async function writeFileAsync() {
            try {
                await fs.writeFile('output.txt', 'Hello via Promises!', 'utf8');
                console.log('File written successfully (promise)!');
            } catch (err) {
                console.error('Error writing file (promise):', err);
            }
        }
        writeFileAsync();
        ```
# What is the difference between `fs.readFile` and `fs.createReadStream`?**

    *   **`fs.readFile`:** Reads the *entire* file content into memory as a buffer or string before passing it to the callback. This can be problematic for very large files as it consumes a lot of memory.
    *   **`fs.createReadStream`:** Reads the file in chunks (a stream of data). This is memory-efficient for large files because the entire file is not loaded into memory at once. It's suitable for processing large files, copying files, or piping data.

**`http` / `https`**
# How do you create a simple HTTP server in Node.js?**

    ```javascript
    const http = require('http');

    const server = http.createServer((req, res) => {
        console.log(`Request received: ${req.method} ${req.url}`);
        if (req.url === '/') {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('Hello, World!');
        } else if (req.url === '/about') {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('This is the about page.');
        } else {
            res.writeHead(404, { 'Content-Type': 'text/plain' });
            res.end('Page Not Found');
        }
    });

    const PORT = 3000;
    server.listen(PORT, () => {
        console.log(`Server running at http://localhost:${PORT}/`);
    });
    ```
# How do you handle different HTTP methods (GET, POST, etc.) in a Node.js HTTP server?**
     You can check the `req.method` property:
    ```javascript
    const http = require('http');
    http.createServer((req, res) => {
        if (req.method === 'GET' && req.url === '/data') {
            res.writeHead(200, {'Content-Type': 'application/json'});
            res.end(JSON.stringify({ message: 'GET request data' }));
        } else if (req.method === 'POST' && req.url === '/submit') {
            let body = '';
            req.on('data', chunk => { // Listen for data chunks
                body += chunk.toString();
            });
            req.on('end', () => { // All data received
                console.log('Received POST data:', body);
                res.writeHead(200, {'Content-Type': 'application/json'});
                res.end(JSON.stringify({ received: body }));
            });
        } else {
            // ... handle other methods/routes or send 404
        }
    }).listen(3000);
    ```
    Frameworks like Express.js simplify routing and method handling significantly.
# How do you make an HTTP GET request from a Node.js application?**
     Using the `http.get()` or `http.request()` method (or `https.get`/`https.request` for HTTPS URLs).
    ```javascript
    const https = require('https'); // or const http = require('http');

    https.get('https://jsonplaceholder.typicode.com/todos/1', (res) => {
        let data = '';
        res.on('data', (chunk) => { data += chunk; });
        res.on('end', () => {
            try {
                const jsonData = JSON.parse(data);
                console.log(jsonData);
            } catch (e) {
                console.error('Error parsing JSON:', e);
            }
        });
    }).on('error', (err) => {
        console.error('Error making GET request:', err);
    });
    ```
    Libraries like `axios` or `node-fetch` provide more convenient APIs.
# What is the difference between `http` and `https` modules?**

    *   `http`: Implements the HTTP protocol for creating HTTP servers and clients. Communication is unencrypted.
    *   `https`: Implements the HTTPS (HTTP Secure) protocol. It's essentially the `http` module over TLS/SSL, providing encrypted communication. It requires SSL certificates.

**`path`**
# Why is the `path` module important? Give some examples of its methods.**
     The `path` module provides utilities for working with file and directory paths in a platform-independent way (handles differences between Windows `\` and POSIX `/` separators).
    Important because:
    *   Ensures cross-platform compatibility for path manipulation.
    *   Avoids errors from manual string concatenation for paths.
    Examples:
    *   `path.join([...paths])`: Joins all given path segments together using the platform-specific separator.
        ```javascript
        const fullPath = path.join('/users', 'joe', 'docs', 'file.txt'); // /users/joe/docs/file.txt (on POSIX)
        ```
    *   `path.resolve([...paths])`: Resolves a sequence of paths or path segments into an absolute path.
        ```javascript
        const absPath = path.resolve('src', 'app.js'); // /current/working/directory/src/app.js
        ```
    *   `path.basename(path[, ext])`: Returns the last portion of a path (filename).
    *   `path.dirname(path)`: Returns the directory name of a path.
    *   `path.extname(path)`: Returns the extension of the path (e.g., `.txt`).
    *   `path.isAbsolute(path)`: Checks if a path is absolute.

**`os`**
# What kind of information can you get from the `os` module?**
     The `os` module provides operating system-related utility methods and properties.
    Examples:
    *   `os.EOL`: The OS-specific end-of-line marker (`\n` on POSIX, `\r\n` on Windows).
    *   `os.arch()`: CPU architecture (e.g., `x64`).
    *   `os.cpus()`: Array of objects containing information about each logical CPU core.
    *   `os.freemem()`: Amount of free system memory in bytes.
    *   `os.totalmem()`: Total amount of system memory in bytes.
    *   `os.homedir()`: Home directory of the current user.
    *   `os.hostname()`: Hostname of the OS.
    *   `os.platform()`: Operating system platform (e.g., `darwin`, `win32`, `linux`).
    *   `os.type()`: OS name (e.g., `Linux`, `Darwin`, `Windows_NT`).
    *   `os.userInfo()`: Information about the current effective user.

**Streams**
# What are Streams in Node.js? What are the different types of streams?**
     Streams are objects that let you read data from a source or write data to a destination in a continuous fashion (in chunks), rather than loading everything into memory at once. They are fundamental for handling large amounts of data efficiently.
    Types:
    1.  **Readable Streams:** For reading data from a source (e.g., `fs.createReadStream()`, an HTTP request on the server).
    2.  **Writable Streams:** For writing data to a destination (e.g., `fs.createWriteStream()`, an HTTP response on the server).
    3.  **Duplex Streams:** Both readable and writable (e.g., a TCP socket, `zlib` streams).
    4.  **Transform Streams:** A type of Duplex stream where the output is computed based on the input (e.g., `crypto.createCipher()`, `zlib.createGzip()`).
# Why are streams useful?**

    *   **Memory Efficiency:** Process data in chunks without loading everything into memory. Essential for large files or data sets.
    *   **Time Efficiency (Composability):** Data can be processed as soon as it arrives, rather than waiting for the entire payload.
    *   **Composability (`pipe()`):** Streams can be easily piped together, allowing data to flow from a readable stream through zero or more transform streams to a writable stream.
        ```javascript
        const fs = require('fs');
        const zlib = require('zlib');

        const readable = fs.createReadStream('large_file.txt');
        const gzip = zlib.createGzip(); // Transform stream
        const writable = fs.createWriteStream('large_file.txt.gz');

        readable.pipe(gzip).pipe(writable);
        console.log('File compressed!');
        ```
# Explain the `pipe()` method in streams.**
     The `readableStream.pipe(writableStream)` method is used to connect the output of a Readable stream to the input of a Writable stream. It automatically manages the flow of data, handling backpressure (when the writable stream is slower than the readable stream) to prevent memory issues. It returns a reference to the destination stream, allowing for chaining pipes.
# What is "backpressure" in Node.js streams?**
     Backpressure is a mechanism that signals a readable stream to pause producing data when the writable stream it's piped to (or any transform stream in between) cannot keep up with processing the incoming data. The `writable.write()` method returns `false` when its internal buffer is full, signaling the need to pause. The readable stream then waits for the `drain` event on the writable stream before resuming. `pipe()` handles this automatically.

**Buffers**
# What is a Buffer in Node.js? Why is it needed?**
     A `Buffer` is a Node.js global object used to handle binary data directly. It represents a fixed-size chunk of memory allocated outside the V8 JavaScript heap.
    Needed because:
    *   JavaScript traditionally didn't have a good way to handle raw binary data (strings are for text).
    *   Node.js deals with binary data frequently (e.g., network streams, file system operations).
    Buffers can be converted to and from strings using specified encodings (e.g., 'utf8', 'hex', 'base64').
# How do you create a Buffer? How do you convert it to a string?**

    *   **Creating a Buffer:**
        ```javascript
        // From a string (UTF-8 by default)
        const bufFromString = Buffer.from('Hello World');

        // From an array of octets
        const bufFromArray = Buffer.from([0x48, 0x65, 0x6c, 0x6c, 0x6f]); // "Hello"

        // Allocating an empty buffer of a specific size (filled with zeros)
        const bufAlloc = Buffer.alloc(10);

        // Allocating an uninitialized buffer (faster, but may contain old data - use with caution)
        const bufAllocUnsafe = Buffer.allocUnsafe(10);
        ```
    *   **Converting Buffer to String:**
        ```javascript
        console.log(bufFromString.toString('utf8')); // Default is 'utf8'
        console.log(bufFromString.toString('hex'));
        console.log(bufFromString.toString('base64'));
        ```

**`child_process`**
# What is the `child_process` module used for?**
     The `child_process` module provides the ability to spawn new processes from a Node.js application. This is useful for:
    *   Running external system commands.
    *   Executing other scripts or programs.
    *   Distributing CPU-intensive work across multiple processes to leverage multi-core CPUs.
# What are the differences between `spawn()`, `exec()`, `execFile()`, and `fork()`?**

    *   **`child_process.spawn(command[, args][, options])`:**
        *   Spawns a new process using the given `command`.
        *   Does not create a shell by default. More efficient and secure if you don't need shell features.
        *   Streams I/O (stdin, stdout, stderr) - good for large amounts of data.
        *   Asynchronous.
    *   **`child_process.exec(command[, options][, callback])`:**
        *   Spawns a shell and then executes the `command` within that shell.
        *   Buffers the command's output (max default 1MB) and passes it to the callback. Not suitable for commands with very large output.
        *   Convenient for simple shell commands.
        *   Asynchronous.
    *   **`child_process.execFile(file[, args][, options][, callback])`:**
        *   Similar to `exec()`, but executes a specified `file` directly without spawning a shell by default. More secure than `exec()`.
        *   Buffers output like `exec()`.
        *   Asynchronous.
    *   **`child_process.fork(modulePath[, args][, options])`:**
        *   A special case of `spawn()` specifically for spawning new Node.js processes.
        *   The child process has an IPC (Inter-Process Communication) channel established, allowing messages to be passed between parent and child using `.send()` and listening to `'message'` events.
        *   Each forked process has its own memory and V8 instance.

**`crypto`**
# How can you perform hashing (e.g., SHA256) in Node.js?**
     Using the `crypto` module:
    ```javascript
    const crypto = require('crypto');

    const dataToHash = 'Hello Node.js Crypto!';
    const hash = crypto.createHash('sha256') // Algorithm (sha256, md5, etc.)
                       .update(dataToHash)   // Data to hash
                       .digest('hex');        // Output format (hex, base64, latin1)

    console.log('SHA256 Hash:', hash);
    ```
# How can you perform symmetric encryption/decryption (e.g., AES) in Node.js?**

    ```javascript
    const crypto = require('crypto');

    const algorithm = 'aes-256-cbc';
    // Key must be 32 bytes for aes-256
    const key = crypto.randomBytes(32);
    // IV must be 16 bytes for aes-cbc
    const iv = crypto.randomBytes(16);

    function encrypt(text) {
        const cipher = crypto.createCipheriv(algorithm, key, iv);
        let encrypted = cipher.update(text, 'utf8', 'hex');
        encrypted += cipher.final('hex');
        return encrypted;
    }

    function decrypt(encryptedText) {
        const decipher = crypto.createDecipheriv(algorithm, key, iv);
        let decrypted = decipher.update(encryptedText, 'hex', 'utf8');
        decrypted += decipher.final('utf8');
        return decrypted;
    }

    const originalText = "This is a secret message!";
    const encrypted = encrypt(originalText);
    console.log("Encrypted:", encrypted);
    const decrypted = decrypt(encrypted);
    console.log("Decrypted:", decrypted);
    ```
    **Note:** Secure key management is crucial in real applications. IVs should ideally be unique per encryption and can be prepended to the ciphertext.

**`url`**
# How do you parse a URL string in Node.js?**
     Using the `URL` class (recommended, WHATWG URL API) or the legacy `url.parse()` method.
    *   **`URL` Class (Modern):**
        ```javascript
        const myURL = new URL('https://user:pass@sub.example.com:8080/p/a/t/h?query=string#hash');
        console.log('Hostname:', myURL.hostname);       // sub.example.com
        console.log('Pathname:', myURL.pathname);       // /p/a/t/h
        console.log('Search:', myURL.search);           // ?query=string
        console.log('Search Params:', myURL.searchParams.get('query')); // string
        console.log('Hash:', myURL.hash);               // #hash
        console.log('Port:', myURL.port);               // 8080
        console.log('Protocol:', myURL.protocol);       // https:
        ```
    *   **Legacy `url.parse()`:**
        ```javascript
        const url = require('url');
        const legacyParsed = url.parse('http://example.com/path?q=test', true); // true parses query string
        console.log(legacyParsed.pathname); // /path
        console.log(legacyParsed.query.q);  // test
        ```

**`querystring`**
# What is the `querystring` module used for?**
     The `querystring` module provides utilities for parsing and formatting URL query strings (e.g., `foo=bar&abc=xyz`).
    ```javascript
    const querystring = require('querystring');

    // Parsing
    const qs = 'name=Alice&age=30&city=Wonderland';
    const parsed = querystring.parse(qs);
    console.log(parsed); // { name: 'Alice', age: '30', city: 'Wonderland' }
    console.log(parsed.name); // Alice

    // Formatting
    const obj = { term: 'Node.js', page: 1 };
    const formatted = querystring.stringify(obj);
    console.log(formatted); // term=Node.js&page=1
    ```
    Note: For full URL parsing, the `URL` class's `searchParams` property is generally preferred over direct use of `querystring`.

**`zlib`**
# What is the `zlib` module used for? Give an example.**
     The `zlib` module provides compression and decompression functionalities using Gzip, Deflate/Inflate, and Brotli algorithms. It's often used for compressing HTTP responses or files.
    ```javascript
    const zlib = require('zlib');
    const fs = require('fs');

    // Compressing a file
    const gzip = zlib.createGzip();
    const input = fs.createReadStream('input.txt');
    const output = fs.createWriteStream('input.txt.gz');
    input.pipe(gzip).pipe(output);
    console.log('File compressed.');

    // Decompressing (example, you'd typically have a separate script or logic)
    // const gunzip = zlib.createGunzip();
    // const compressedInput = fs.createReadStream('input.txt.gz');
    // const decompressedOutput = fs.createWriteStream('input_decompressed.txt');
    // compressedInput.pipe(gunzip).pipe(decompressedOutput);
    ```

**`dns`**
# What can you do with the `dns` module?**
     The `dns` module provides functions for DNS (Domain Name System) resolution. You can use it to look up IP addresses for hostnames, and vice-versa.
    ```javascript
    const dns = require('dns');

    dns.lookup('google.com', (err, address, family) => {
        if (err) throw err;
        console.log('Address for google.com:', address, 'Family:', family); // family is IPv4 or IPv6
    });

    dns.resolveMx('google.com', (err, addresses) => {
        if (err) throw err;
        console.log('MX records for google.com:', addresses);
    });

    // Also dns.promises for Promise-based API
    async function lookupPromise() {
        try {
            const result = await dns.promises.lookup('nodejs.org');
            console.log('nodejs.org IP:', result.address);
        } catch (err) {
            console.error(err);
        }
    }
    lookupPromise();
    ```

**`timers`**
# Besides `setTimeout`, `setInterval`, `setImmediate`, are there other timer-related functions in Node.js?**
     Yes, and their Promise-based counterparts:
    *   **`clearTimeout(timeoutObject)`:** Cancels a timeout created by `setTimeout()`.
    *   **`clearInterval(intervalObject)`:** Cancels an interval created by `setInterval()`.
    *   **`clearImmediate(immediateObject)`:** Cancels an immediate created by `setImmediate()`.
    *   **Promise-based timers (from `require('timers/promises')`):**
        ```javascript
        const { setTimeout: sleep, setImmediate: immediateP } = require('timers/promises');
        async function demoTimers() {
            console.log('Start');
            await sleep(1000); // Pauses for 1 second
            console.log('After 1 second');
            const res = await immediateP('Immediate value');
            console.log('After immediate:', res);
        }
        demoTimers();
        ```
    *   Timer objects also have methods like `ref()` (default, keeps event loop alive) and `unref()` (allows program to exit if it's the only thing keeping event loop active).

**`assert`**
# What is the `assert` module used for? When would you use it?**
     The `assert` module provides a set of assertion functions primarily used for writing unit tests to verify invariants. If an assertion fails, it throws an `AssertionError`.
    When to use:
    *   In automated testing to check if code behaves as expected.
    *   Less commonly, for internal checks within application logic (though custom error handling is often preferred here).
    ```javascript
    const assert = require('assert');

    function add(a, b) { return a + b; }

    // Test cases
    assert.strictEqual(add(1, 2), 3, 'Test Case 1 Failed: 1 + 2 should be 3');
    assert.notStrictEqual(add(1, 2), 4, 'Test Case 2 Failed: 1 + 2 should not be 4');

    const obj1 = { a: 1 };
    const obj2 = { a: 1 };
    // assert.strictEqual(obj1, obj2); // This would fail (different objects)
    assert.deepStrictEqual(obj1, obj2, 'Test Case 3 Failed: Objects should be deeply equal');

    console.log('All assertions passed!');
    ```
    Methods include `assert.ok()`, `assert.equal()`, `assert.strictEqual()`, `assert.deepStrictEqual()`, `assert.throws()`, etc. `strict` versions perform strict equality (`===`).

# How should you handle errors in synchronous Node.js code?**
     Using standard JavaScript `try...catch` blocks:
    ```javascript
    function mightThrow() {
        if (Math.random() < 0.5) {
            throw new Error("Something went wrong in sync code!");
        }
        return "Success";
    }
    try {
        const result = mightThrow();
        console.log(result);
    } catch (error) {
        console.error("Caught synchronous error:", error.message);
        // Handle the error, log it, or exit
    }
    ```
# How should you handle errors in asynchronous Node.js code (callbacks, Promises, async/await)?**

    *   **Callbacks (Error-First):** Check the `err` argument first.
        ```javascript
        fs.readFile('file.txt', (err, data) => {
            if (err) return console.error('Callback error:', err);
            // process data
        });
        ```
    *   **Promises:** Use `.catch()` method.
        ```javascript
        somePromiseFunc()
            .then(result => { /* process result */ })
            .catch(err => console.error('Promise rejection:', err));
        ```
    *   **Async/Await:** Use `try...catch` blocks.
        ```javascript
        async function myAsyncFunc() {
            try {
                const result = await anotherAsyncFunc();
                // process result
            } catch (err) {
                console.error('Async/await error:', err);
            }
        }
        ```
# What is the `domain` module, and why is it deprecated?**
     The `domain` module provided a way to intercept unhandled errors within a specific "domain" or context. It aimed to provide better error isolation for asynchronous operations.
    Deprecated because:
    *   It had performance overhead and complex semantics.
    *   Promises and `async/await` provide better, more standard ways of handling asynchronous errors.
    *   It didn't integrate well with the V8 engine's error handling mechanisms.
    Using `process.on('uncaughtException')` and `process.on('unhandledRejection')` carefully, along with robust Promise error handling, is the modern approach.
# What are `uncaughtException` and `unhandledRejection` process events? Should you use them?**

    *   **`process.on('uncaughtException', (err, origin) => { ... })`:** Emitted when a JavaScript error is not caught by any `try...catch` block.
    *   **`process.on('unhandledRejection', (reason, promise) => { ... })`:** Emitted whenever a Promise is rejected and no error handler (`.catch()`) is attached to the promise within a turn of the event loop.
    **Should you use them?**
    *   The official Node.js documentation advises that using `uncaughtException` as a general error handling mechanism is a **bad idea**. The process is in an unknown, potentially corrupted state.
    *   **Recommended Use:** For synchronous cleanup of allocated resources (e.g., file descriptors, handles) and then **shutting down the process gracefully**. Do not resume normal operation.
    *   For `unhandledRejection`, it's better to ensure all Promises have `.catch()` handlers. If used, it should also primarily be for logging the error and potentially terminating the process if the error indicates a critical state. Node.js behavior for unhandled rejections has changed over versions (it might terminate the process by default in future versions or currently with flags).
# How can you debug a Node.js application? (Mention tools/techniques)**

    *   **`console.log()` / `console.dir()` / `console.trace()`:** Simple but effective for tracing values and execution flow.
    *   **Node.js Built-in Debugger (Legacy):** `node inspect myscript.js` (or `node debug myscript.js` in older versions).
    *   **Chrome DevTools for Node:**
        *   Run Node.js with the `--inspect` or `--inspect-brk` flag: `node --inspect-brk myscript.js`.
        *   Open Chrome and go to `chrome://inspect`. Your Node.js process should appear.
        *   Provides a powerful GUI debugger (breakpoints, call stack, scope inspection, profiling).
    *   **IDE Debuggers:** Most modern IDEs (VS Code, WebStorm, IntelliJ) have excellent built-in Node.js debuggers that integrate seamlessly with `--inspect`.
    *   **Logging Libraries:** Winston, Pino for structured and configurable logging.
    *   **APM Tools:** New Relic, Datadog for monitoring and debugging in production.
# What are some best practices for logging in a Node.js application?**

    *   **Use a Dedicated Logging Library:** (e.g., Winston, Pino, Bunyan) for features like log levels, formatting, transports (console, file, remote services).
    *   **Use Log Levels:** (e.g., `error`, `warn`, `info`, `debug`, `verbose`) to control verbosity.
    *   **Structured Logging (JSON):** Makes logs easier to parse, search, and analyze by machines (e.g., ELK stack, Splunk).
    *   **Timestamp Logs:** Essential for correlating events.
    *   **Contextual Information:** Include relevant context like request IDs, user IDs, module names.
    *   **Avoid Logging Sensitive Information:** (Passwords, API keys, PII).
    *   **Consistent Format:** Across all services/modules.
    *   **Configurable Log Output:** Control where logs go (console for dev, files/services for prod) via environment variables.
# How can you create custom Error types in Node.js?**
     By extending the built-in `Error` class:
    ```javascript
    class NetworkError extends Error {
        constructor(message, statusCode) {
            super(message); // Call parent constructor
            this.name = 'NetworkError'; // Custom error name
            this.statusCode = statusCode; // Custom property
            Error.captureStackTrace(this, this.constructor); // Maintains proper stack trace
        }
    }

    try {
        throw new NetworkError('Failed to connect to server', 503);
    } catch (e) {
        if (e instanceof NetworkError) {
            console.error(`${e.name} (${e.statusCode}): ${e.message}`);
            // console.error(e.stack);
        } else {
            console.error('An unknown error occurred:', e);
        }
    }
    ```
# What does `Error.captureStackTrace` do?**
     `Error.captureStackTrace(targetObject[, constructorOpt])` is a V8-specific function (available in Node.js) that creates a `.stack` property on `targetObject`. It captures the call stack at the point `captureStackTrace` is called, omitting frames above `constructorOpt` (if provided). This is useful when creating custom error classes to ensure the stack trace starts from where the custom error was instantiated, not from within the `Error` class's constructor.
# How do you manage application configuration (e.g., database URLs, API keys) securely in Node.js?**

    *   **Environment Variables:** Store configuration in environment variables, not hardcoded in source code.
    *   **`.env` Files (for Development):** Use libraries like `dotenv` to load environment variables from a `.env` file during development. **Ensure `.env` is in `.gitignore`**.
    *   **Configuration Management Services:** For production, use dedicated services like HashiCorp Vault, AWS Secrets Manager, Azure Key Vault, or platform-provided config maps (e.g., Kubernetes ConfigMaps/Secrets).
    *   **Role-Based Access Control (RBAC):** Grant Node.js processes only the necessary permissions to access secrets.
    *   **Encrypted Configuration Files:** If files must be used, encrypt sensitive values.
# What is a "graceful shutdown" and how can you implement it in Node.js?**
     A graceful shutdown is the process of allowing an application to finish its current operations and clean up resources before exiting, rather than abruptly terminating. This prevents data loss or corruption.
    Implementation:
    1.  Listen for termination signals (e.g., `SIGINT` from Ctrl+C, `SIGTERM` from process managers).
    2.  When a signal is received:
        *   Stop accepting new incoming requests (e.g., close the HTTP server: `server.close()`).
        *   Finish processing any in-flight requests.
        *   Close database connections, file handles, etc.
        *   Perform any other necessary cleanup.
        *   Exit the process (`process.exit(0)`).
    3.  Implement a timeout to force exit if cleanup takes too long.
    ```javascript
    const server = http.createServer(...);
    server.listen(3000);

    function gracefulShutdown() {
        console.log('Received shutdown signal, closing server...');
        server.close(() => {
            console.log('HTTP server closed.');
            // Close other resources (e.g., database connections)
            // mongoose.connection.close(false, () => {
            //   console.log('MongoDb connection closed.');
            //   process.exit(0);
            // });
            process.exit(0); // Exit after server closes
        });

        // Force shutdown after a timeout
        setTimeout(() => {
            console.error('Could not close connections in time, forcefully shutting down');
            process.exit(1);
        }, 10000); // 10 seconds
    }

    process.on('SIGTERM', gracefulShutdown); // For `kill`
    process.on('SIGINT', gracefulShutdown);  // For Ctrl+C
    ```
    Libraries like `http-graceful-shutdown` can simplify this.

# What is the `cluster` module in Node.js? How does it help with performance?**
     The `cluster` module allows you to create child processes (workers) that share the same server port. Each worker runs in its own V8 instance and event loop. The master process listens on the port and distributes incoming connections to the workers (typically using a round-robin approach).
    Helps with performance by:
    *   Allowing a Node.js application to take advantage of multi-core CPUs.
    *   Improving throughput and responsiveness under load, as multiple requests can be handled in parallel by different worker processes.
    *   Providing some resilience: if one worker crashes, the master process can fork a new one.
# How does the `cluster` module's master process distribute connections?**
     The default method on most platforms (except Windows) is round-robin. The master process accepts the new connection and passes the socket handle to a worker process over an IPC channel. On Windows, the master creates a listen socket and sends it to interested workers who then `accept()` connections directly. You can also implement custom logic for connection distribution.
# When should you use `worker_threads` vs. `cluster` vs. `child_process.fork()`?**

    *   **`cluster`:** Primarily for scaling network applications (like HTTP servers) across multiple CPU cores to handle more concurrent connections. Each worker is a separate Node.js process.
    *   **`worker_threads`:** For offloading CPU-intensive JavaScript tasks to separate threads *within the same process*. Good for parallelizing computations without the overhead of full process creation. Can share memory using `SharedArrayBuffer`.
    *   **`child_process.fork()`:** For running entirely separate Node.js programs as child processes. Useful for task delegation or when you need full process isolation with IPC. More overhead than worker threads.
    *   **`child_process.spawn()/exec()`:** For running non-Node.js external commands/programs.
# What are some common performance bottlenecks in Node.js applications?**

    *   **Blocking the Event Loop:** Long-running synchronous code, CPU-intensive tasks on the main thread.
    *   **Excessive I/O Operations:** Too many slow database queries, file system operations, or external API calls without proper concurrency management.
    *   **Memory Leaks:** Objects not being garbage collected, leading to increased memory usage and eventual crashes.
    *   **Inefficient Code:** Poorly written algorithms, excessive object creation, inefficient string/array manipulations.
    *   **Large JSON Payloads:** Parsing and stringifying large JSON objects can be CPU-intensive.
    *   **Unoptimized Regular Expressions (ReDoS).**
    *   **Lack of Caching:** Repeatedly fetching or computing the same data.
# How can you profile a Node.js application to find performance issues?**

    *   **Built-in V8 Profiler:** Use `node --prof myapp.js`. This generates a `v8.log` file that can be processed with `node --prof-process v8.log > processed.txt`.
    *   **Chrome DevTools Profiler:** Use `node --inspect myapp.js`, connect via Chrome DevTools, and use the "Performance" tab to record CPU profiles.
    *   **`console.time()` / `console.timeEnd()`:** For measuring the duration of specific code blocks.
    *   **Application Performance Monitoring (APM) Tools:** New Relic, Datadog, Dynatrace provide detailed performance insights, tracing, and error tracking in production.
    *   **Clinic.js:** An open-source suite of tools (`clinic doctor`, `clinic flame`, `clinic bubbleprof`) to diagnose Node.js performance issues.
# What is PM2? Why is it used?**
     PM2 (Process Manager 2) is a popular, production-ready process manager for Node.js applications.
    Why used:
    *   **Process Monitoring:** Keeps applications alive by restarting them if they crash.
    *   **Clustering:** Built-in load balancing and clustering support (similar to `cluster` module but easier to manage).
    *   **Log Management:** Aggregates logs from different processes.
    *   **Startup Scripts:** Generates scripts to automatically start applications on server boot.
    *   **Zero-Downtime Reloads:** Allows updating applications without downtime.
    *   **Application Monitoring:** Provides insights into CPU/memory usage.
    *   **Easy Deployment & Management:** Simplifies managing multiple Node.js applications.
# What are some common security vulnerabilities in Node.js web applications?**
     Similar to other web applications, but with some Node.js specifics:
    *   **Injection Attacks:** SQL injection, NoSQL injection, OS command injection (if user input is used unsafely in queries or commands).
    *   **Cross-Site Scripting (XSS):** If user input is rendered unsanitized in HTML.
    *   **Cross-Site Request Forgery (CSRF):** If proper CSRF tokens are not used.
    *   **Insecure Deserialization:** If untrusted data is deserialized into objects.
    *   **Dependency Vulnerabilities:** Using outdated or vulnerable NPM packages (`npm audit` helps).
    *   **Broken Authentication/Session Management.**
    *   **Sensitive Data Exposure.**
    *   **Security Misconfiguration:** Default credentials, exposing error details, improper HTTP headers.
    *   **Regular Expression Denial of Service (ReDoS).**
# What is `npm audit`? How does it help with security?**
     `npm audit` is a command that scans your project's dependencies for known security vulnerabilities. It checks against the npm registry's vulnerability database.
    It provides a report detailing:
    *   The vulnerable package.
    *   The severity of the vulnerability.
    *   A link to more information.
    *   Sometimes, a suggested fix (e.g., `npm audit fix` to automatically update to patched versions if possible without breaking changes).
    It helps maintain application security by identifying and prompting developers to address vulnerabilities in third-party code.
# How can you prevent XSS (Cross-Site Scripting) in a Node.js application serving HTML?**

    *   **Output Encoding/Sanitization:** When rendering user-supplied data into HTML, always sanitize or encode it appropriately.
        *   Use templating engines that auto-escape by default (e.g., EJS, Pug with default settings).
        *   If manually constructing HTML, use libraries to escape special HTML characters (e.g., `<` becomes `&lt;`).
    *   **Content Security Policy (CSP):** Implement CSP headers to restrict what resources (scripts, styles) the browser can load, mitigating the impact of any injected scripts.
    *   **Input Validation:** Validate user input on the server-side, though this is more for data integrity than direct XSS prevention (XSS is an output problem).
    *   **Set `HttpOnly` flag on cookies:** Prevents client-side JavaScript from accessing sensitive cookies.
# What is middleware in the context of Node.js web frameworks (like Express.js)?**
     Middleware functions are functions that have access to the request object (`req`), the response object (`res`), and the `next` middleware function in the application's request-response cycle.
    They can:
    *   Execute any code.
    *   Make changes to the request and response objects.
    *   End the request-response cycle.
    *   Call the next middleware function in the stack.
    If a middleware function does not end the cycle, it must call `next()` to pass control to the next middleware, otherwise the request will hang.
    Uses: Logging, parsing request bodies, authentication, authorization, error handling, serving static files, compression.
# What is a reverse proxy, and why might you use one with a Node.js application?**
     A reverse proxy is a server that sits in front of one or more web servers (like your Node.js app) and forwards client requests to those servers.
    Why use with Node.js:
    *   **Load Balancing:** Distribute traffic across multiple Node.js instances.
    *   **SSL/TLS Termination:** Handle HTTPS encryption/decryption, offloading this from Node.js.
    *   **Serving Static Content:** Efficiently serve static files (CSS, JS, images), freeing up Node.js for dynamic content.
    *   **Caching:** Cache responses to reduce load on the Node.js app.
    *   **Compression:** Handle Gzip/Brotli compression.
    *   **Security:** Can provide an additional layer of security (e.g., WAF, IP blacklisting).
    *   **Port Mapping:** Run Node.js on an unprivileged port (e.g., 3000) and have the reverse proxy listen on port 80/443.
    Common reverse proxies: Nginx, Apache, HAProxy, Caddy.
# How can you manage different Node.js versions on your development machine?**
     Using a Node Version Manager:
    *   **NVM (Node Version Manager):** The most popular choice for Unix-like systems (macOS, Linux). Allows you to install, switch between, and manage multiple Node.js versions easily.
        *   `nvm install <version>`
        *   `nvm use <version>`
        *   `nvm ls`
    *   **nvm-windows:** A similar tool for Windows.
    *   **Volta:** A newer, fast tool that manages JS toolchains (Node, Yarn, package binaries) and can pin versions per project automatically via `package.json`.
# What are some strategies for caching in a Node.js application?**

    *   **In-Memory Caching:**
        *   Simple objects/maps within the Node.js process (e.g., using `Map` or libraries like `node-cache`). Suitable for frequently accessed, small data. Lost on process restart.
    *   **Distributed Caching:**
        *   External caching services like Redis or Memcached. Persistent, shared across multiple Node.js instances, good for scalability.
    *   **HTTP Caching Headers:**
        *   Set headers like `Cache-Control`, `ETag`, `Last-Modified` to instruct browsers and CDNs on how to cache responses.
    *   **CDN (Content Delivery Network):** Caches static assets and sometimes dynamic content closer to users.
    *   **Database Query Caching:** Cache results of expensive or frequent database queries.
    *   **Memoization:** For caching results of pure, expensive functions.
# What are some best practices for writing scalable Node.js applications?**

    *   **Statelessness:** Design application instances to be stateless. Store session state and shared data in external stores (databases, Redis).
    *   **Asynchronous Everywhere:** Embrace non-blocking I/O for all operations.
    *   **Horizontal Scaling:** Use clustering (e.g., `cluster` module, PM2) or container orchestration (Kubernetes, Docker Swarm) to run multiple instances.
    *   **Efficient Resource Use:** Optimize memory and CPU usage, avoid leaks.
    *   **Use a Process Manager:** (e.g., PM2) for monitoring, restarts, and clustering.
    *   **Load Balancing:** Distribute traffic across instances.
    *   **Effective Caching:** Implement caching strategies at various levels.
    *   **Microservices Architecture:** Break down large applications into smaller, independent services.
    *   **Monitor and Profile:** Continuously monitor performance and identify bottlenecks.
# What is a "memory leak" in Node.js? How can you detect and prevent it?**
     A memory leak occurs when parts of memory that are no longer needed by the application are not released, leading to a gradual increase in memory consumption over time, eventually causing performance degradation or crashes.
    Detection:
    *   **Monitoring Tools:** Observe memory usage over time (e.g., using `process.memoryUsage()`, OS tools, APM tools).
    *   **Heap Snapshots:** Use Chrome DevTools (via `--inspect`) or tools like `heapdump` to take heap snapshots at different times and compare them to identify objects that are growing unexpectedly.
    Prevention:
    *   **Proper Scope Management:** Avoid accidental global variables.
    *   **Remove Event Listeners:** If an object with event listeners is no longer needed, remove the listeners to allow it to be garbage collected (e.g., `emitter.removeListener()` or `emitter.off()`).
    *   **Clear Timers/Intervals:** Use `clearTimeout()` and `clearInterval()` for timers that are no longer needed.
    *   **Manage Closures Carefully:** Closures can keep references to parent scope variables alive longer than expected.
    *   **Avoid Large In-Memory Caches without Eviction Policies:** If caching in memory, ensure there's a mechanism to remove old or unused items.
    *   **Be Mindful of Long-Lived Objects:** Ensure they don't unintentionally hold references to short-lived objects.
# Explain the concept of a "singleton" in Node.js module caching.**
     When you `require()` a module in Node.js, it is loaded, compiled, and its `module.exports` is cached by Node.js's module loader. Subsequent `require()` calls for the *same module file* (resolved to the same absolute path) will return the cached `module.exports` object rather than re-executing the module code.
    This means that if a module exports an object or a class instance, all parts of your application that `require` that module will get the *same instance* of that object/class. This effectively makes the exported entity a singleton within that Node.js process.
    Example:
    ```javascript
    // logger.js
    class Logger {
        constructor() { this.logs = []; console.log("Logger instance created"); }
        log(message) { this.logs.push(message); console.log(message); }
    }
    module.exports = new Logger(); // Export a single instance

    // serviceA.js
    const logger = require('./logger');
    logger.log("Message from Service A");

    // serviceB.js
    const logger = require('./logger'); // Gets the SAME instance as in Service A
    logger.log("Message from Service B");
    // "Logger instance created" will only be printed once.
    ```
# What is the role of `NODE_ENV` environment variable?**
     `NODE_ENV` is a convention used to specify the environment in which an application is running (e.g., `development`, `production`, `test`).
    Many libraries and frameworks (like Express.js) change their behavior based on `NODE_ENV`:
    *   **`development`:** May enable more verbose logging, disable caching, provide detailed error messages.
    *   **`production`:** Optimizes for performance and security (e.g., enables caching, minifies assets, less verbose errors).
    Your application code can also check `process.env.NODE_ENV` to apply different configurations or behaviors.
    Setting it: `NODE_ENV=production node app.js`
# What are some common anti-patterns in Node.js development?**

    *   **Blocking the Event Loop:** Using synchronous I/O or long CPU-bound tasks on the main thread.
    *   **Callback Hell:** Deeply nested callbacks instead of using Promises or async/await.
    *   **Not Handling Errors:** Ignoring errors in callbacks or not having `.catch()` on Promises.
    *   **Ignoring `uncaughtException` / `unhandledRejection` (or misusing them to keep running).**
    *   **Modifying `global` Object Excessively:** Leads to namespace pollution and hard-to-debug code.
    *   **Writing Large, Monolithic Modules:** Instead of breaking code into smaller, focused modules.
    *   **Not Using a Process Manager in Production:** (like PM2).
    *   **Storing Sensitive Information in Code/Config Files:** Instead of environment variables or secret managers.
    *   **Not Considering Security Implications of Dependencies.**
# What is server-side rendering (SSR) with Node.js, and what are its benefits?**
     Server-Side Rendering is a technique where a web page's content is rendered on the server (using Node.js and typically a front-end framework like React, Vue, or Angular Universal) into HTML, which is then sent to the client's browser.
    Benefits:
    *   **Better SEO:** Search engine crawlers can easily index the fully rendered HTML content.
    *   **Faster First Contentful Paint (FCP):** Users see content quicker because the browser receives pre-rendered HTML instead of a blank page and a JavaScript bundle to execute.
    *   **Improved Performance on Low-Powered Devices:** Less client-side processing is required initially.
    This contrasts with Client-Side Rendering (CSR), where the browser downloads a minimal HTML shell and JavaScript, and the JS then renders the page. Node.js is often used as the backend to perform SSR for SPAs.
# What is a "fat-free" or "lean" Node.js Docker image, and why is it desirable?**
      A "fat-free" or "lean" Node.js Docker image is one that contains only the bare essentials needed to run the Node.js application, without unnecessary build tools, development dependencies, or system libraries.
     Why desirable:
     *   **Smaller Image Size:** Faster downloads, uploads, and deployments. Reduced storage costs.
     *   **Improved Security:** Smaller attack surface because fewer packages and tools are present that could have vulnerabilities.
     *   **Faster Build Times:** For the final runtime image (if using multi-stage builds).
     *   **Reduced Complexity:** Easier to manage and understand the runtime environment.
     Achieved using techniques like:
     *   **Multi-stage Docker builds:** Use a build stage with all dev tools, then copy only the necessary artifacts (e.g., `node_modules` for production, compiled code) to a minimal base image (like `node:alpine` or `distroless`).
     *   **Using minimal base images:** `node:*-alpine` is smaller than `node:*-slim` or the default Debian-based images.
     *   **Pruning `devDependencies`:** Ensure only `dependencies` are installed in the final image (`npm install --production`).
