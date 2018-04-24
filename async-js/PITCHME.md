# Managing asynchronous code
![JavaScript](assets/js-logo.png)

+++

## Agenda

@ol[roman]

- Runtime Concepts
- Asynchronous Operations
- Promises
- Async / Await
- Applications

@olend

---
# Runtime Concepts

<!-- https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop -->

+++?image=https://mdn.mozillademos.org/files/4617/default.svg&size=auto 80%
+++

## Call Stack

```javascript
const multiply = (a, b) => a * b;

const sq = a => multiply(a, a);

const sqrt = a => Math.sqrt(a);

const diagonal = (l, w) => sqrt(sq(l) + sq(w));

diagonal(3, 4)
```

@[1, 3, 5, 7]
@[9]
@[7](diagonal\(3, 4\) ← _**sq(3)** + sq(4)_)
@[3](diagonal\(3, 4\) ⬅ sq\(3\))
@[1](diagonal\(3, 4\) ⬅ sq\(3\) ⬅ multiply\(3, 3\))
@[1](diagonal\(3, 4\) ⬅ sq\(3\) ⬅ 9)
@[3](diagonal\(3, 4\) ⬅ 9)
@[7](diagonal\(3, 4\) ← _9 + **sq(4)**_)
@[3](diagonal\(3, 4\) ⬅ sq\(4\))
@[1](diagonal\(3, 4\) ⬅ sq\(4\) ⬅ multiply\(4, 4\))
@[1](diagonal\(3, 4\) ⬅ sq\(4\) ⬅ 16)
@[3](diagonal\(3, 4\) ⬅ 16)
@[7](diagonal\(3, 4\) ← _9 + 16_)
@[5](diagonal\(3, 4\) ⬅ sqrt\(25\))
@[5](diagonal\(3, 4\) ⬅ sqrt\(25\) ⬅ Math.sqrt\(25\))
@[5](diagonal\(3, 4\) ⬅ sqrt\(25\) ⬅ 5)
@[7](diagonal\(3, 4\) ⬅ 5)
@[9](5)

+++

## Stack trace

```javascript
function bar() {
  throw new Error('Not implemented');
}

function foo() {
  bar();
}

foo();
```

@[9]
@[6](foo\(\))
@[2](foo\(\) ⬅ bar\(\))
@[2](<span style="color: red">Uncaught Error: Not implemented</span>)

+++?image=async-js/img/stack-trace.png&size=auto 80%
+++

## Stack overflow

```javascript
function neverEndingStory() {
  neverEndingStory();
}

neverEndingStory();
```

@[1-3]
@[5]
@[2](neverEndingStory\(\))
@[2](neverEndingStory\(\) ⬅ neverEndingStory\(\) )
@[2](neverEndingStory\(\) ⬅ neverEndingStory\(\) ⬅ ... )
@[2](<span style="color: red">Uncaught RangeError: Maximum call stack size exceeded</span>)

+++?image=https://mdn.mozillademos.org/files/4617/default.svg&size=auto 80%
+++

## Heap references

```javascript
const a = [1], b = a;

a.push(2);

console.log(b);
```

@[1]
@[3]
@[5]
@[5]([1, 2])

+++?image=https://mdn.mozillademos.org/files/4617/default.svg&size=auto 80%
+++?image=https://upload.wikimedia.org/wikipedia/commons/5/52/Data_Queue.svg&color=#004a5d&size=auto 80%
+++

## Event loop

```javascript
while (queue.waitForMessage()) {
  queue.processNextMessage();
}
```

@[1](waits synchronously for a message to arrive)
@[2](runs the given function and message until the stack is empty again)

+++

## `setTimeout`

```javascript
const callback =
  id => console.log(`callback ${id}`);

callback('start');
setTimeout(callback, 20, 'one');
setTimeout(callback);
setTimeout(callback, 0, 'three');
callback('end');
```

@[1-2]
@[4,8]
@[5-7]
@[4-8](what is the execution order?)
@[4](callback start)
@[8](callback end)
@[6](callback undefined)
@[7](callback three)
@[5](callback one)
@[4-8](start, end, undefined, three, one)

+++?image=async-js/img/fib-sequence.gif&size=auto 80%
+++

## Naive Fibonacci implementation

```javascript
function fib(n) {
  if (n < 2) {
    return n;
  }

  return fib(n - 1) + fib(n - 2);
}
```

@[2-4]
@[6]

+++

<div class="buttons">
  <button onclick="window.intId != null ? (clearInterval(window.intId), delete window.intId) : window.intId=n=setInterval('for(n+=7,i=k,P=\'p.\\n\';i-=1/k;P+=P[i%2?(i%2*j-j+n/k^j)&1:2])j=k/i;p.innerHTML=P',k=64)">
    Toggle animation
  </button>
  <button onclick="const fib = n => n < 2 ? n : fib(n - 1) + fib(n - 2); fib(36)">
    fib(36)
  </button>
  <button onclick="const fib = n => n < 2 ? n : fib(n - 1) + fib(n - 2); fib(38)">
    fib(38)
  </button>
</div>

<div id="p" style="font-size: 12px; height: 480px; font-family: monospace; white-space: pre;" />

+++?image=async-js/img/chrome-unresponsive.png&size=auto 65%
+++

## Event loop never blocks

+++?image=async-js/img/event-loop.png&size=auto 80%
+++

# Q&A

@ul

- When does a JavaScript program end?

@ulend

---
# Asynchronous Operations

+++

## Examples

@ul

- Database writes
- Resource fetches
- HTTP calls
- User input

@ulend

+++

## Possible outcomes

+++?image=async-js/img/async-op-pending.gif&size=auto 60%
+++?image=async-js/img/async-op-fail.gif&size=auto 60%
+++?image=async-js/img/async-op-succeed.gif&size=auto 60%
+++

## Error-first callback

```javascript
import fs from 'fs';

fs.readFile('/etc/passwd', (err, data) => {
  if (err) {
    throw new Error('Unable to fetch the file');
  }
  console.log(data);
});
```

@[1]
@[3](first argument is an optional error)
@[4-6](we check for error at the beginning)
@[7](finally we process the results)

+++?image=async-js/img/callback-soup.png&size=auto 50%
+++

## Error handling

```javascript
try {
  fs.readFile('/etc/passwd', (err, data) => {
    if (err) throw err;
    console.log(data);
  });  
} catch (e) {
  console.error(e);
}
```

@[2-5]
@[3](we throw back any error we get)
@[6-8](catch all errors \(?\))
@[3](<span style="color: red">Program terminated</span>)

+++

# Streams

## TBD

---
# Promises

+++

## Basic tool for modeling asynchronous operations

+++

## Promise creation

+++?image=async-js/img/promise-states.png&size=auto 60%
+++

```javascript
new Promise((resolve, reject) => {
  legacyOperation((err, result) => {
    if (err) {
      reject(err);
    } else {
      resolve(result);
    }
  });
});
```

@[1](created with a single argument - the executor)
@[2](legacy asynchronous operation)
@[3-7](settle promise accordingly)
@[1-9](common pattern to wrap legacy operations)

+++

### `Promise.reject`

```javascript
new Promise((resolve, reject) =>
  reject(new Error('Invalid password')));

Promise.reject(
  new Error('Invalid password'));
```

@[1-2](executor runs immediately)
@[4-5](short version)

+++

### `Promise.resolve`

```javascript
new Promise(resolve => resolve(420));

Promise.resolve(420);
```

@[1](executor runs immediately)
@[3](short version)

+++

### `Promise.resolve` continues

```javascript
Promise.resolve(Promise.resolve(420));

Promise.resolve(Promise.reject(
  new Error('Invalid password')));

Promise.resolve(x); // -> promise
```

@[1]
@[3-4]
@[6](ensure it's a promise)
@[1-6]

+++

## Promise continuation

+++

### Task example

<!-- simple task with delay -->

```javascript
const delay = (ms, succeed = true)
  => new Promise((resolve) => {
    setTimeout(succeed ? resolve : reject, ms, ms);
  });
```

+++

### `Promise.prototype.then`

<!-- https://bitsofco.de/javascript-promises-101/ -->

```javascript
fetch(url).then((result) => {
  // you can return value
  // or return promise
  // or throw error
});
```

+++

### `Promise.prototype.then`

<!-- https://bitsofco.de/javascript-promises-101/ -->

```javascript
fetch(url).then((result) => {
  /* successFunction */
}, (err) => {
  /* errorFunction */
});
```

+++?image=async-js/img/promise-then.png&color=#006984&size=auto 80%
+++

### `Promise.prototype.catch`

+++

### `Promise.prototype.finally` (stage 4)

---
# Async / Await

---
# Applications

+++

```javascript
function test() {
  return new Promise((resolve) => {
    setTimeout(resolve, 1000);
  })
}
```

@[1](function)
@[2-4](function body)
@[3](setTimeout)

<button
  style="padding: 20px; font-weight: bold; font-size: 20pt"
  onclick="alert('hello world!')">Hello</button>

---

# Thanks

<!-- closing notes -->

<span style="font-size: 30pt">
marcin @fa[at] hagmajer . com
</span>
