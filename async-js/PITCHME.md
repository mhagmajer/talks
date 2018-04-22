## Managing asynchronous code in JavaScript

### Marcin Hagmajer

#### Warsaw, April 24th

---

# Event loop
<!-- https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop -->

+++

![JS memory](https://mdn.mozillademos.org/files/4617/default.svg)

+++

## Stack

```javascript
const multiply = (a, b) => a * b;

const sq = n => multiply(n, n);

const sqrt = x => Math.sqrt(x);

const diagonal = (l, w) => sqrt(sq(l) + sq(w));

diagonal(3, 4)
```

@[1](function)

+++

```javascript
const multiply = (a, b) => a * b;

const sq = n => multiply(n, n);

const sqrt = x => Math.sqrt(x);

const diagonal = (l, w) => sqrt(sq(l) + sq(w));

diagonal(3, 4)
```

@[1](function)
@[3](Stack: a + b)
@[5](*Stack*: <b>a</b> + b)

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
