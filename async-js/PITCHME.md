@title[Introduction]
# Hello

@fa[code-fork]

+++

Hola!

---

@title[Callbacks]
Goodbye!

+++

```javascript
function test() {
  return new Promise((resolve) => {
    setTimeout(resolve, 1000);
  })
}
```

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
