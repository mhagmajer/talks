# Managing asynchronous code
![JavaScript](assets/js-logo.png)

+++

## Agenda

@ol

- Runtime concepts
- Asynchronous operations <!-- including streams -->
- Promises
- Async/Await
- Applications

<!-- closing notes -->

@olend

---
# Runtime concepts

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
@[5]([1, 2])

+++?image=https://mdn.mozillademos.org/files/4617/default.svg&size=auto 80%
+++

+++?image=https://upload.wikimedia.org/wikipedia/commons/5/52/Data_Queue.svg&color=#004a5d&size=auto 80%
+++

## Event loop

```javascript
while (queue.waitForMessage()) {
  queue.processNextMessage();
}
```

@[1](waits synchronously for a message to arrive)
@[2](runs the given function and message)

+++

## Example 1

+++

## Example 2

+++

+++?image=async-js/img/event-loop.png&size=auto 80%
+++

## Q&A

---
# Asynchronous operations

+++

## Possible outcomes

+++

## Node callback style

+++

# Streams

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

<span style="font-size: 30pt">
marcin @fa[at] hagmajer . com
</span>
