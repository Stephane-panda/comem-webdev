# Node.js Introduction

<runkit global enabled='true'></runkit>

Learn how to use [Node.js][node], an asynchronous JavaScript runtime that can run on your local machine or server.

<!-- slide-include ../../BANNER.md -->

**You will need**

* A Unix CLI

**Recommended reading**

* [Command line](../cli/)
* [JavaScript](../js/)

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [What is [Node.js][node]?](#what-is-nodejsnode)
  - [Installation](#installation)
  - [Which Node.js version to choose?](#which-nodejs-version-to-choose)
  - [Install Node.js and make sure it works](#install-nodejs-and-make-sure-it-works)
  - [Create and execute a Node.js file](#create-and-execute-a-nodejs-file)
  - [Node.js modules](#nodejs-modules)
  - [Using modules](#using-modules)
  - [Writing your own module](#writing-your-own-module)
  - [Requiring local modules](#requiring-local-modules)
  - [Export properties](#export-properties)
  - [Function as the main export](#function-as-the-main-export)
  - [Require paths](#require-paths)
- [Synchronous vs. Asynchronous](#synchronous-vs-asynchronous)
  - [Synchronous code](#synchronous-code)
  - [Asynchronous code](#asynchronous-code)
  - [Non-blocking I/O](#non-blocking-io)
  - [Your Node.js code is single-threaded](#your-nodejs-code-is-single-threaded)
- [The event loop](#the-event-loop)
  - [A short reminder](#a-short-reminder)
  - [The call stack](#the-call-stack)
  - [Stack overflow](#stack-overflow)
  - [Platform APIs](#platform-apis)
  - [Other event-driven, non-blocking I/O architectures](#other-event-driven-non-blocking-io-architectures)
- [Node.js callback convention](#nodejs-callback-convention)
  - [**Always** check for errors](#always-check-for-errors)
- [Spot the mistake](#spot-the-mistake)
  - [Mistake 1](#mistake-1)
  - [Mistake 2](#mistake-2)
- [The HTTP module](#the-http-module)
  - [Modern web language](#modern-web-language)
  - [Event emitters](#event-emitters)
- [Resources](#resources)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->



## What is [Node.js][node]?

<!-- slide-front-matter class: center, middle, image-header -->

<p class='center'><img src='images/node.png' width='50%' /></p>

> "Node.js is an **asynchronous JavaScript runtime** built on Chrome's V8 JavaScript engine.
> Node.js uses an **event-driven**, **non-blocking I/O** model that makes it lightweight and efficient."

> "Node.js is used on servers to develop fast, scalable web applications."



### Installation

<p class='center'><img src='images/installation.png' width='80%' /></p>



### Which Node.js version to choose?

<p class='center'><img src='images/lts-schedule.png' width='80%' /></p>

* Odd-numbered versions (e.g. v5, v7, v9) are **unstable** releases with the latest features, and will **no longer be supported after 6-9 months**.
* Even-numbered versions (e.g. v4, v6, v8, v10) have **long term support (LTS)**.
  They are actively developed for 6 months.
  They are supported for 18 months after that.
  They are still maintained (e.g. security fixes) for 12 months after that.
  So they are **supported for 36 months**.



### Install Node.js and make sure it works

Download and install Node.js now.

If the installation was successfull, you should be able to access Node.js in your CLI:

```bash
$> node --version
v10.11.0

$> node
> 1 + 2
3
```

Type `Ctrl-C` or `.exit` to exit.



### Create and execute a Node.js file

Create a `script.js` file in a new `node-demo` project directory:

```js
function hello(name) {
  console.log('Hello ' + name + '!');
}

hello('World');
```

Execute it by running it with the `node` executable:

```bash
$> cd /path/to/projects/node-demo

$> node script.js
Hello World!
```

Originally, JavaScript was only executable in web browsers,
but here you are running JavaScript code **locally with Node.js**, like you would other scripting languages (e.g. PHP, Ruby or Python).



### Node.js modules

Node.js code is organized in **modules**.
These are the core modules available to you out of the box:

<!-- slide-column 30 -->

* Assertion Testing
* Buffer
* C/C++ Addons
* **Child Processes**
* Cluster
* Command Line Options
* Console
* **Crypto**
* Debugger
* DNS
* Domain
* Errors

<!-- slide-column 30 -->

* **Events**
* **File System**
* Globals
* **HTTP**
* **HTTPS**
* Modules
* Net
* OS
* **Path**
* **Process**
* Punycode
* Query Strings

<!-- slide-column 30 -->

* Readline
* REPL
* Stream
* String Decoder
* Timers
* TLS/SSL
* TTY
* UDP/Datagram
* URL
* Utilities
* V8
* VM
* ZLIB



### Using modules

You can use modules in your code with `require()`:

```js
// Require the operating system core module
*const os = require('os');

function hello(name) {
  console.log('Hello ' + name + '!');
* console.log('I am running on ' + os.platform());
}

hello('World');
```

This will log the platform on which you are running:

```bash
$> node script.js
Hello World!
I am running on darwin
```



### Writing your own module

<runkit disabled></runkit>

Let's say we want to extract the `hello` function to another module.
Create a `utils.js` file:

```js
const os = require('os');

// Attach properties to exports so that you can use
// them when requiring this file
`exports`.hello = function(name) {
  console.log('Hello ' + name + '!');
  console.log('I am running on ' + os.platform());
};
```

Attaching properties to the `exports` object is how you expose the module's functionality.
Code that uses `require()` on that file will receive the `exports` object.



### Requiring local modules

<runkit disabled></runkit>

You also use `require()` for your own module, but instead of just a name you have to provide a **relative file path**.
Modify `script.js` as follows:

```js
// Require the utils.js file in the current directory
// (You can omit the .js extension)
const utils = require(`'./utils'`);

// Use the exported function
utils.hello('World');
```

It should still work:

```bash
$> node script.js
Hello World!
I am running on darwin
```



### Export properties

<runkit disabled></runkit>

You can attach whatever you want to the `exports` object:

```js
`exports.theMeaningOfLife` = 42;
```

And use it where it is required:

```js
const utils = require('./utils');
utils.hello('World');
console.log('The meaning of life is ' + `utils.theMeaningOfLife`);
```

This will print:

```bash
$> node script.js
Hello World!
I am running on darwin
*The meaning of life is 42
```



### Function as the main export

<runkit disabled></runkit>

Some modules only export a function instead of an object with properties.
Add a `doIt.js` file:

```js
// Override module.exports to replace the whole exports object
`module.exports =` function() {
  console.log('Doing it');
};
```

Modify `script.js`:

```js
*const doIt = require('./doIt');
const utils = require('./utils');
utils.hello('World');
console.log('The meaning of life is ' + utils.theMeaningOfLife);
*doIt();
```

The additional text `Doing it` will be logged as well.



### Require paths

A short summary on how to require files:

Statement                     | Effect
:---                          | :---
`require('coreModule')`       | Require the core module (or npm package, more on that later) named `coreModule`
`require('./foo.js')`         | Require the `foo.js` file in the current directory (relative to the current file)
`require('./foo/bar/baz.js')` | Require the `baz.js` file in the `foo/bar` directory (relative to the current file)
`require('../../qux.js')`     | Require the `qux.js` file two directories above (relative to the current file)



## Synchronous vs. Asynchronous

<!-- slide-front-matter class: center, middle -->



### Synchronous code

Basic JavaScript code is synchronous.

It means that only one command or function can be executed at a time.

```js
function getRandomNumber() {
  return Math.random();
}

console.log('Hello');

const result = getRandomNumber();

console.log('Result: ' + result);
console.log('End of program');
```

Code executes **sequentially**:

```txt
Hello
Result: 0.12438
End of program
```

The call to `getRandomNumber()` blocks the thread until its execution is complete.



### Asynchronous code

With asynchronous code, some operations are executed **in parallel**:

```js
const fs = require('fs');

console.log('Hello');

// List the files at the root of the file system
fs.readdir('/', function(err, result) {
  console.log('Files: ' + result.join(', '));
  console.log('Done');
});

console.log('End of program');
```

Code execution is **not sequential**:

```txt
Hello
End of program
Result: 0.581
Done
```

How does this work?



### Non-blocking I/O

The signature of `fs.readFile` is:

```
  fs.readFile(file[, options], callback)
```

The third argument is a **callback function**:

* With synchronous code, the call blocks the thread until it is done
* With asynchronous code, the rest of the code **keeps executing**;
  you pass a function to `fs.readFile` and Node.js will **call you back** when it is done

Under the hood, Node.js will read the file in a separate thread,
then execute your callback function when it's ready.

This is called **non-blocking I/O**, because all I/O operations are executed in separate threads and are therefore non-blocking:

* Database access
* File system access
* HTTP requests
* Etc.



### Your Node.js code is single-threaded

Although I/O operations are non-blocking, **your code always executes in a single thread**:

```js
const fs = require('fs');
let fileCount = 0;

fs.readdir('/', function(err, result) {
  `fileCount = fileCount + result.length`;
  console.log('Files listed: ' + fileCount);
});

console.log('End of program: ' + fileCount);
```

This will **always** log `End of program: 0` first, then `Files listed: N`.

Even if the operating system is very fast and the directory is listed *instantaneously*,
Node.js **guarantees** that the last line, `console.log('End of program:', fileCount)`, will be executed first.

Callback functions will always wait for **blocking code** to finish executing.





## The event loop

The [event loop][event-loop] is the main component of JavaScript's concurrency model,
and is what produces the behavior described in the previous slides.

<p class='center'><img class='w95' src='images/event-loop.png' /></p>

<!-- slide-notes -->

* Event loop:
  * Run the initial script (which will register callbacks)
  * Get the next event in the queue
  * Invoke the registered callbacks in sequence
  * Delegate I/O operations to the Node platform (in separate, non-blocking threads)

### A short reminder

Before explaining the event loop,
you must be clear on something.

You've probably often encountered something that looks like this while programming
(not this specific message, but similar-looking blocks of lines in a console):

```
Error: Both arguments must be numbers
    at add (/path/to/project/st-demo.js:3:11)
    at compute (/path/to/project/st-demo.js:10:10)
    at demo (/path/to/project/st-demo.js:14:17)
    at Object.<anonymous> (/path/to/project/st-demo.js:18:1)
    at Module._compile (internal/modules/cjs/loader.js:689:30)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:700:10)
    at Module.load (internal/modules/cjs/loader.js:599:32)
    at tryModuleLoad (internal/modules/cjs/loader.js:538:12)
    at Function.Module._load (internal/modules/cjs/loader.js:530:3)
    at Function.Module.runMain (internal/modules/cjs/loader.js:742:12)
```

What is this called and what does it mean?

#### Reading a stack trace

```
Error: Both arguments must be numbers
    at `add` (/path/to/project/`st-demo.js:3`:11)
    at `compute` (/path/to/project/`st-demo.js:10`:10)
    at `demo` (/path/to/project/`st-demo.js:14`:17)
    at Object.<anonymous> (/path/to/project/`st-demo.js:18`:1)
```

Here's the `st-demo.js` file:

```js
function add(a, b) {
  if (typeof a !== 'number' || typeof b !== 'number') {
*   throw new Error('Both arguments must be numbers');
  }

  return a + b;
}

function compute(a, b, op) {
* return op(a, b);
}

function demo() {
* const value = compute(2, 'foo', add);
  console.log(value);
}

*demo();
```



### The call stack

The [**call stack**][stack] is a mechanism for the JavaScript interpreter to keep track of its place in a script that calls multiple functions: what function is being run, which should be called next, etc.

* When called, a function is added to the top of the stack.
* Functions called by that function are added to the stack further up.
* When a function finishes, the interpreter takes it off the stack and resumes where it left off in the last stack item.

<!-- slide-column -->

**What will the stack look like** as the following code is executed?

[Check it out with Loupe](http://latentflip.com/loupe/?code=ZnVuY3Rpb24gbXVsdGlwbHkoYSwgYikgewogIHJldHVybiBhICogYjsKfQoKZnVuY3Rpb24gc3F1YXJlKGEpIHsKICByZXR1cm4gbXVsdGlwbHkoYSwgYSk7Cn0KCmZ1bmN0aW9uIHByaW50U3F1YXJlKGEpIHsKICBjb25zdCBzcXVhcmVkID0gc3F1YXJlKGEpOwogIGNvbnNvbGUubG9nKHNxdWFyZWQpOwp9CgpwcmludFNxdWFyZSg0KTs%3D!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D)

<!-- slide-column 60 -->

```js
function multiply(a, b) {
  return a * b;
}

function square(a) {
  return multiply(a, a);
}

function printSquare(a) {
  const squared = square(a);
  console.log(squared);
}

printSquare(4);
```



### Stack overflow

Taking into account the fact that the call stack has a limited size
(unfortunately, your computer does not have infinite memory),
**what will happen to the call stack** when the following code is run?

```js
function eagerlyMultiply(a) {
  return a * eagerlyMultiply(a);
}

eagerlyMultiply();
```

[Check it out with Loupe](http://latentflip.com/loupe/?code=ZnVuY3Rpb24gZWFnZXJseU11bHRpcGx5KGEpIHsKICByZXR1cm4gYSAqIGVhZ2VybHlNdWx0aXBseShhKTsKfQoKZWFnZXJseU11bHRpcGx5KCk7!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D)



### Platform APIs

The **JavaScript runtime** (whether in Node.js or a web browser) **can only run one thing at a time**.

But some functions are not run by the Node.js runtime;
they are run by the underlying platform: the v8 engine for Node.js or the web browser for a website.
For example:

* `readFile` (Node.js API)
* `XMLHttpRequest` (Web API)
* `setTimeout` (Node.js & Web API)

<!-- slide-column -->

**What will happen** when the following code is run?

[Check it out with Loupe](http://latentflip.com/loupe/?code=ZnVuY3Rpb24gcHJvZ3JhbSgpIHsKICBjb25zb2xlLmxvZygnU3RhcnQgb2YgcHJvZ3JhbScpOwoKICBzZXRUaW1lb3V0KGZ1bmN0aW9uIGNiKCkgewogICAgY29uc29sZS5sb2coJ0hlbGxvJyk7CiAgfSwgNTAwMCk7CgogIGNvbnNvbGUubG9nKCdFbmQgb2YgcHJvZ3JhbScpOwp9Cgpwcm9ncmFtKCk7!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D)

<!-- slide-column 60 -->

```js
function program() {
  console.log('Start of program');

  setTimeout(function cb() {
    console.log('Hello');
  }, 5000);

  console.log('End of program');
}

program();
```

#### The JavaScript runtime is single-threaded

What does this mean?

Let's use `setTimeout` as before, but this time set the value of the timeout to zero.
It should call the function right away, right?

<!-- slide-column -->

**What will happen** when the following code is run?

[Check it out with Loupe](http://latentflip.com/loupe/?code=ZnVuY3Rpb24gcHJvZ3JhbSgpIHsKICBjb25zb2xlLmxvZygnU3RhcnQgb2YgcHJvZ3JhbScpOwoKICBzZXRUaW1lb3V0KGZ1bmN0aW9uIGNiKCkgewogICAgY29uc29sZS5sb2coJ0hlbGxvJyk7CiAgfSwgMCk7CgogIGNvbnNvbGUubG9nKCdFbmQgb2YgcHJvZ3JhbScpOwp9Cgpwcm9ncmFtKCk7!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D)

<!-- slide-column 60 -->

```js
function program() {
  console.log('Start of program');

  setTimeout(function cb() {
    console.log('Hello');
  }, `0`);

  console.log('End of program');
}

program();
```



### Other event-driven, non-blocking I/O architectures

Node.js is not the only tool to use an event-driven architecture with an event loop.
Similar mechanisms are used in other frameworks and tools:

* JavaScript running in the browser also runs on an event loop
* [Event Machine][event-machine] (Ruby event-processing library)
* [nginx][nginx] (web server written in C with an event-driven architecture)
* [Twisted][twisted] (Python event-driven networking engine)



## Node.js callback convention

<runkit disabled></runkit>

Node.js callback functions usually have this signature:

```
  function(err, result)
```

Like the previous example:

```js
fs.readdir('/', `function(err, result) {`
  // ...
`}`);
```

There are two ways that the function can be called back by Node.js:

1. The operation **failed**:
  * `err` contains an **error** describing the problem
  * `result` is `null` or `undefined`

2. The operation **succeeded**:
  * `err` is `null` or `undefined`
  * `result` contains the **result** of the operation



### **Always** check for errors

You should never forget to check for errors:

```js
const fs = require('fs');
fs.readFile('name.txt', 'utf-8', function(err, data) {
* if (err) {
*   return console.warn('Could not read the file because: ' + err.message);
* }

  console.log('Hello ' + data);
});
```

If you forget to check `err`, this code could log `Hello undefined` if the operation fails (e.g. the file doesn't exist, is corrupt, etc).

Do not forget the `return` either, or use `else`, to ensure that your "success" code is not run when an error occurs.



## Spot the mistake

<!-- slide-front-matter class: center, middle -->



### Mistake 1

What's wrong with this code?

```js
const fs = require('fs');

// Save a salutation into hello.txt
const newSalutation = 'Hello Bob!';
fs.writeFile('hello.txt', newSalutation, 'utf-8', function(err) {
  if (err) {
    console.warn('Could not write in file because: ' + err.message);
  }
});

// Read the salutation from hello.txt
const salutation = fs.readFile('hello.txt', 'utf-8', function(err, data) {
  if (err) {
    return console.warn('Could not read file because: ' + err.message);
  }

  return data;
});

// Log the salutation read from hello.txt
console.log(salutation);
```

Save it to a file and run it with `node` or run it with **RunKit** to see the issue.

#### Mistake 1 result

If you save the script to `bug1.js` and execute it this is what will happen:

```bash
$> node bug1.js
undefined
Could not read file because: ENOENT: no such file or directory, open 'hello.txt'
```

#### Mistake 1 asynchronous issue

<runkit disabled></runkit>

There are two problems with this code. First, Node.js I/O functions (such as file operations) are **asynchronous**.

When `fs.writeFile()` is called, Node.js will start a thread and write the file in the background.
Meanwhile, **your code will keep executing** and the call to `fs.readFile` will occur **before the callback function of `fs.writeFile` is called back**
and **before Node.js is done writing the file**.

```js
// Save a salutation into hello.txt
const newSalutation = 'Hello Bob!';
`fs.writeFile`('hello.txt', newSalutation, 'utf-8', function(err) {
  if (err) {
    console.warn('Could not write in file because: ' + err.message);
  }
});

// Read the salutation from hello.txt
const salutation = `fs.readFile`('hello.txt', 'utf-8', function(err, data) {
  if (err) {
    return console.warn('Could not read file because: ' + err.message);
  }

  return data;
});
```

#### Mistake 1 return issue

<runkit disabled></runkit>

Second, even if there was no asynchronous issue, the assignment of `const salutation` would still be `undefined`:

* You are calling `fs.readFile()`, which **always** returns `undefined`, and that is what is stored in the `salutation` variable.
* **When** Node.js is done reading the file in a separate thread, **it will call your callback function (later)**.
* The `return data;` of your callback function is **not going anywhere**.

```js
// Read the salutation from hello.txt
`const salutation = fs.readFile`('hello.txt', 'utf-8', function(err, data) {
  if (err) {
    return console.warn('Could not read file because: ' + err.message);
  }

  `return data;`
});
```

#### Mistake 1 correct implementation

The second asynchronous call must be performed **inside the callback function of the previous call**.
That way, it will not be executed **until the first call is done** and Node.js has called your callback function.

The `console.log()` must also be performed **inside the second callback**.

```js
const fs = require('fs');

// Save a salutation into hello.txt
const newSalutation = 'Hello Bob!';
fs.writeFile('hello.txt', newSalutation, 'utf-8', `function(err) {`
  if (err) {
    console.warn('Could not write in file because: ' + err.message);
  }

  // Read the salutation from hello.txt
  fs.readFile('hello.txt', 'utf-8', `function(err, data) {`
    if (err) {
      return console.warn('Could not read file because: ' + err.message);
    }

    // Log the salutation read from hello.txt
    `console.log(data);`
  `}`);
`}`);
```



### Mistake 2

This is an example of **error handling**.

The intended behavior is that if the file does not exist,
the text `Could not read file because: some error` should be printed,
otherwise it should print the contents of the file in upper case.

```js
const fs = require('fs');

// Read the contents of a file
fs.readFile('file-that-does-not-exist.txt', 'utf-8', function(err, text) {
  if (err) {
    console.warn('Could not read file because: ' + err.message);
  }

  // Log the salutation in upper case
  console.log(text.toUpperCase());
});
```

What's wrong with this code?

#### Mistake 2 result

If you save this script in `bug2.js` and execute it, this is what will happen:

```bash
$> node bug2.js
Could not read file because: ENOENT: no such file or directory, open 'file-...'
/path/to/projects/node-demo/bug2.js:9
  console.log(text.toUpperCase());
                  ^

TypeError: Cannot read property 'toUpperCase' of undefined
    at ReadFileContext.callback (/path/to/projects/node-demo/bug2.js:9:19)
    at FSReqWrap.readFileAfterOpen [as oncomplete] (fs.js:365:13)
```

As expected, we see the `Could not read file because: ...` log.
But we also see another **unexpected error** and its stack trace.

#### Mistake 2 issue

<runkit disabled></runkit>

There is an error check, but execution of the callback function is **not stopped**
as there is no `return` and no `else`.

If an error occurs, **both the `console.warn` and the `console.log` calls will be executed**.
This will cause a "null pointer exception":

```js
const fs = require('fs');

// Read the contents of a file
fs.readFile('file-that-does-not-exist.txt', 'utf-8', function(err, text) {
  if (err) {
*   console.warn('Could not read file because: ' + err.message);
  }

  // Log the contents in upper case
* console.log(text.toUpperCase());
});
```

#### Mistake 2 correct implementation

You can add a `return` to solve the issue:

```js
const fs = require('fs');
// Read the contents of a file
fs.readFile('file-that-does-not-exist.txt', 'utf-8', function(err, text) {
  if (err) {
    `return` console.warn('Could not read file because: ' + err.message);
  }
  // Log the contents in upper case
  console.log(text.toUpperCase());
});
```

Or use an `if/else`:

```js
const fs = require('fs');
// Read the contents of a file
fs.readFile('file-that-does-not-exist.txt', 'utf-8', function(err, text) {
  `if (err) {`
    console.warn('Could not read file because: ' + err.message);
  `} else {`
    // Log the contents in upper case
    console.log(text.toUpperCase());
  `}`
});
```



## The HTTP module

<!-- slide-front-matter class: center, middle -->



### Modern web language

Node.js provides a ready-to-use HTTP server.
Thanks to the event loop, this one small server can handle many clients concurrently.

```js
// Require the HTTP module.
const http = require('http');

// Define configuration properties.
const hostname = '127.0.0.1';
const port = 3000;

// Create an HTTP server that will respond to
// all requests with "Hello World" in plain text.
const server = http.createServer(function(req, res) {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World\n');
});

// Run the server on the configured host and port.
// Register a callback function to be notified when
// the server has started successfully.
server.listen(port, hostname, function() {
  console.log('Server running at http://' + hostname + ':' + port + '/');
});

process.env.RUNKIT_ENDPOINT_URL // (for running with RunKit)
```



### Event emitters

<runkit disabled></runkit>

Many Node.js objects are [event emitters][node-event-emitter].
You can register callback functions to **react** to these events:

<!-- slide-column 30 -->

<img src='images/http-events.png' width='100%' />

<!-- slide-column 70 -->

```js
server.on('connection', function(socket) {
  console.log(socket.remoteAddress + ' connected');
});

server.on('request', function(message) {
  console.log(message.url + ' requested');
});
```



## Resources

**Documentation**

* [Core modules (10.x)][node-10-api]

**Further reading**

* [What is Node.js][mixu-node-book]
* [JavaScript Concurrency Model and Event Loop][event-loop]
* [Understanding the Node.js Event Loop][event-loop-strongloop]
* [Philip Roberts: What the heck is the event loop anyway? (YouTube)][event-loop-wth]
* [Node.js Explained (video)][node-explained-video]



[event-loop]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop
[event-loop-strongloop]: http://strongloop.com/strongblog/node-js-event-loop/
[event-loop-wth]: https://www.youtube.com/watch?v=8aGhZQkoFbQ
[event-machine]: http://rubyeventmachine.com
[mixu-node-book]: http://book.mixu.net/node/ch2.html
[nginx]: https://www.nginx.com
[node]: https://nodejs.org/en/
[node-10-api]: https://nodejs.org/dist/latest-v10.x/docs/api/
[node-event-emitter]: https://nodejs.org/api/events.html
[node-explained-video]: http://kunkle.org/talks/
[stack]: https://developer.mozilla.org/en-US/docs/Glossary/Call_stack
[twisted]: http://twistedmatrix.com/trac/
