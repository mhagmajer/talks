# 7 najczęstszych błędów programistów Reacta
![JavaScript](assets/react-logo.png)

+++

## Czym jest React?

@ol[roman]

- Komponenty
- Render
- Props & State

@olend

---

# React Rangers

## Freelance 2.0

---

# 1. Dziel komponenty na mniejsze

<!-- https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop -->

+++?image=https://mdn.mozillademos.org/files/4617/default.svg&size=auto 80%
+++

## Call Stack

```javascript
const LatestPostsComponent = (props) => (
  <section>
    <div><h1>Latest posts</h1></div>
    <div>
      {props.posts.map(post => (
        <PostPreview
          key={post.slug}
          post={post}
        />
      ))}
    </div>
  </section>
);
```

+++

@ul

- Małe komponenty ułatwiają testowanie

@ulend


---

# 2. Nie nadużywaj stanu

<!-- State is a subset of data that, if changed, causes a React component to re-render itself. -->

+++

<!-- Single source of truth -->

```javascript
constructor(props) {
  this.state = {
    selectedCar: null,
    submitEnabled: false,
  };
}
```

+++

@ul

- Używaj stanu tylko wtedy, gdy to faktycznie niezbędne

@ulend

---

# 3. Unikaj powtarzania kodu

+++

@ul

- Zagmatfany kod, trudny do testowania i zrozumienia
- Plik z kodem, który się powtarza

@ulend

---

# 4. Binduj poprawnie callbacki

+++

## this

+++

```javascript
onClick() {
  alert(`Hello ${this.props.name}`);
}

render() {
  return (
    <button
      onClick={this.onClick}
    >Test</button>
  );
}
```

---

# 5. setState jest asynchroniczny

+++

```javascript
setState({
  count: 5,
});
console.log(this.state.count);
```

---

# 6. Używaj React PropTypes

+++

## Używaj statycznej kontroli typów

---

# 7. Kompozycja zamiast dziedziczenia

---

## `npm/promise-more`

@ul

- async utilities
- queuing & scheduling

@ulend

+++

## marcin @fa[at] hagmajer . com

@ul

- mentoring and code reviews
- remote
- flexible hours
- React, Meteor, Node.js and more

@ulend

+++

## marcin @fa[at] hagmajer . com

@ul

- @fa[link] https://blog.hagmajer.com/
- @fa[facebook] HagmajerBlog
- @fa[twitter] @mhagmajer
- @fa[github] @mhagmajer

@ulend
