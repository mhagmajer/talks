## Managing asynchronous code<br>in JavaScript

<br><br>

##### Marcin Hagmajer

###### Warsaw, April 24th

---

# Event loop
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
@[2](foo\(\) ⬅ bar\(\))
@[2](<span style="color: red">Uncaught Error: Not implemented</span>)

+++
+++?image=./img/stack-trace.png&size=auto 80%
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

---
@title[Promises]
# Promises

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

---
@title[Demo]
# Demo

<button
  style="padding: 20px; font-weight: bold; font-size: 20pt"
  onclick="alert('hello world!')">Hello</button>
