Hello Dear!

+++

Hola!

---

Goodbye!

+++

Adiós!

+++?code=async-js/test.js&lang=javascript

+++

<button style="padding: 10px" onclick="$.getScript('async-js/test.js')">
  Hello
</button>

+++

```javascript
function test() {
  return new Promise((resolve) => {
    setTimeout(resolve, 1000);
  })
}
```
