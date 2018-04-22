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
@[7](diagonal\(3, 4\)     &nbsp;_**sq(3)** + sq(4)_)
@[3](diagonal\(3, 4\) ⬅ sq\(3\))
@[1](diagonal\(3, 4\) ⬅ sq\(3\) ⬅ multiply\(3, 3\))
@[1](diagonal\(3, 4\) ⬅ sq\(3\) ⬅ _9_)
@[3](diagonal\(3, 4\) ⬅ _9_)
@[7](diagonal\(3, 4\) _9 + **sq(4)**_)
@[3](diagonal\(3, 4\) ⬅ sq\(4\))
@[1](diagonal\(3, 4\) ⬅ sq\(4\) ⬅ multiply\(4, 4\))
@[1](diagonal\(3, 4\) ⬅ sq\(4\) ⬅ _16_)
@[3](diagonal\(3, 4\) ⬅ _16_)
@[7](diagonal\(3, 4\) _9 + 16_)
@[5](diagonal\(3, 4\) ⬅ sqrt\(25\))
@[5](diagonal\(3, 4\) ⬅ sqrt\(25\) ⬅ Math.sqrt\(25\))
@[5](diagonal\(3, 4\) ⬅ sqrt\(25\) ⬅ _5_)
@[5](diagonal\(3, 4\) ⬅ _5_)
@[7](_5_)

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
