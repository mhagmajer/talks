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

### `Promise.prototype.then`

```javascript
fetch(url).then((result) => {
  // a) return value
  // b) return promise
  // c) throw error
});
```

@[2](handle fulfilled state)
@[3]
@[4]
@[5](new promise)
@[1-5]

+++

### `Promise.prototype.catch`

```javascript
fetch(url).catch((reason) => {
  // a) return value
  // b) return promise
  // c) throw error
});
```

@[2](handle rejected state)
@[3]
@[4]
@[5](new promise)
@[1-5]

+++?image=async-js/img/promise-then-catch.png&color=#006984&size=auto 80%
+++

### Task example

```javascript
const delay = (ms, succeed = true)
  => new Promise(resolve => setTimeout(
    succeed ? resolve : reject,
    ms,
    ms,
  ));
```

@[1]
@[2-6]
@[3](we will either resolve or reject)
@[4](after `ms`)
@[5](`ms` will be passed as result or reason)

+++

### Chaining promises (A)

```javascript
delay(2000)
  .then(ms => delay(ms + 500))
  .then(ms => delay(ms + 500))
  .then(ms => delay(ms + 500));
```

<!--
https://bevacqua.github.io/promisees/

const delay = (n, succeed = true) => new Function(`
  return new Promise(function d${n}(resolve, reject) {
    setTimeout(${succeed} ? resolve : reject, ${n}, ${n});
  });
`)();
-->

+++?image=async-js/img/async-then-delay.gif&size=auto 60%
+++

### Chaining promises (B)

```javascript
delay(2000)
  .then(ms => delay(ms + 500, false))
  .then(ms => delay(ms + 500))
  .then(ms => delay(ms + 500));
```

+++?image=async-js/img/async-then-delay-error.gif&size=auto 60%
+++

### Chaining promises (C)

```javascript
delay(2000)
  .then(ms => delay(ms + 500, false).catch(ms => ms))
  .then(ms => delay(ms + 500))
  .then(ms => delay(ms + 500));
```

+++?image=async-js/img/async-then-delay-error-catch.gif&size=auto 60%
+++

### `Promise.prototype.finally`
#### Stage 4

```javascript
promise.finally(() => {
  cleanup();
});
```

@[1](doesn't pass results)
@[1-3]

+++

### `Promise.all`

```javascript
Promise.all([
  delay(1500),
  delay(2000),
  delay(2500),
]);
```

@[1](resolved as soon as all resolve)
@[1-5](returns array of results or reason)

+++?image=async-js/img/async-promise-all.gif&size=auto 60%
+++

### `Promise.race`

```javascript
Promise.race([
  delay(2000),
  delay(4000, false),
  delay(6000),
]);
```

@[1](resolved as soon as any resolve)
@[1-5](returns a result or reason)

+++?image=async-js/img/async-promise-race.gif&size=auto 60%
+++

+++

# Q&A

---

# Async / Await

+++

```javascript
getData(a => {
  getMoreData((a, b) => {
    getMoreData((b, c) => {
      getMoreData((c, d) => {
        getMoreData((d, e) => {
          console.log(e);
        });
      });
    });
  });
});
```

+++

```javascript
getData(a)
.then(a => genMoreData(a))
.then(b => genMoreData(b))
.then(c => genMoreData(c))
.then(d => genMoreData(d))
.then(e => console.log(e));
```

@[2](gen- prefix)

+++

```javascript
(async () => {
  const a = await genData(a);
  const b = await genMoreData(a);
  const c = await genMoreData(b);
  const d = await genMoreData(c);
  const e = await genMoreData(d);
  console.log(e);
})();
```

@[1](async block allows resolving promises)
@[2](special keyword await)

+++?image=async-js/img/js-callbacks-promises-asyncawait.gif&size=auto 60%
+++

```javascript
async function genData() {
  try {
    // ...
  } catch (e) {
    console.error(e);
  }
}
```

@[3](always wrap async function in try-catch)

+++

### Don't abuse async-await

+++

```javascript
async function genOrderItems() {
  const items = await genCartItems();
  for (const item of items) {
    await genSendRequest(item);
  }
}
```

+++

```javascript
async function genOrderItems() {
  const items = await genCartItems();
  await Promise.all(items.map(
    item => genSendRequest(item)));
}
```

## Rules of thumb

@ul

- Find statements which depend on the execution of other statements
- Group-dependent statements in async functions
- Execute these async functions concurrently

@ulend

+++

# Q&A

---

# Applications

---

# Thanks

<span style="font-size: 30pt">
marcin @fa[at] hagmajer . com
</span>

<!--
If you're interested in working with this stuff professionally shoot my an email at marcin@hagmajer.com or let's talk after the event. I know a few cool companies who hire for remote work in JavaScript and React especially that I can hook you up with.
-->
