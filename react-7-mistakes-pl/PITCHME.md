## 7 najczęstszych błędów programistów Reacta

+++

# React Rangers

+++?image=react-7-mistakes-pl/img/rr_logo.png&size=auto 70%
+++

---

+++?image=assets/react-logo.png&size=auto 70%
+++

## React jest prosty!

@ul

- Komponenty
- Funckja render
- Właściwości (props) i stan (state)

@ulend

---

# 1. Dziel komponenty na mniejsze

+++

```javascript
const LatestPostsComponent = (props) => (
  <section>
    <div><h1>Latest posts</h1></div>
    <div>
      {props.posts.map(post => (
        <PostPreview
          key={post.id}
          post={post}
        />
      ))}
    </div>
  </section>
);
```

@[2, 6](<span style="color: red; font-weight: bold">Wielka litera</span>)

+++

Małe komponenty:

@ul

- ułatwiają testowanie
- są bardziej uniwersalne
- porządkują kod
- powinny mieć oddzielne pliki

@ulend

---

# 2. Używaj React PropTypes

+++

```javascript
import PropTypes from 'prop-types';

class HelloPage extends React.Component {
  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    );
  }
}

HelloPage.propTypes = {
  name: PropTypes.string,
};
```

+++?image=react-7-mistakes-pl/img/prop-types-warning.png&size=auto 80%
+++

## Statyczna kontrola typów - Flow i TypeScript

+++

```javascript
import * as React from 'react';

type Props = {
  +name: string,
};

class HelloPage extends React.Component<Props> {
  render() {
    this.props.doesNotExist;

    return (<h1>Hello, {this.props.name}</h1>);
  }
}
```

@[9](<span style="color: red">Error! You did not define a `doesNotExist` prop.</span>)

---

# 3. Nie nadużywaj stanu

<!-- State is a subset of data that, if changed, causes a React component to re-render itself. -->

+++

## Stan powinien być źródłem prawdy

+++

## Unikaj stanu zależnego

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
- Stan powinien być źródłem prawdy

@ulend

---

# 4. setState jest asynchroniczny

+++

```javascript
setState({
  count: 5,
});
console.log(this.state.count);
```

---

# 5. Binduj poprawnie callbacki

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

# 6. Kompozycja zamiast dziedziczenia

---

# 7. Unikaj powtarzania kodu

+++

@ul

- Zagmatfany kod, trudny do testowania i zrozumienia
- Plik z kodem, który się powtarza

@ulend

---

# React Rangers
## Freelance 2.0

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
