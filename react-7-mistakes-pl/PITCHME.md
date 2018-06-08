## 7 najczęstszych błędów programistów Reacta

+++

# React Rangers

+++?image=react-7-mistakes-pl/img/rr_logo.png&size=auto 70%

---?image=assets/react-logo.png&size=auto 70%
+++

## React jest prosty!

@ul

- Komponenty
- Funkcja render
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

# 2. Używaj PropTypes

+++

```javascript
import PropTypes from 'prop-types';

class HelloPage extends React.Component {
  static propTypes = {
    name: PropTypes.string,    
  }

  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    );
  }
}
```

@[1]
@[4-6]
@[1-13]

+++

### Niewłaściwe użycie komponentu

```javascript
render() {
  return (
    <div>
      <HelloPage />
    </div>
  );
}
```
@[3,4]

+++?image=react-7-mistakes-pl/img/prop-types-warning.png&size=auto 30%
+++

## Statyczna kontrola typów

@ul

- Flow (Facebook)
- TypeScript (Microsoft)

@ulend

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

@[1]
@[3-5](Definicja typu)
@[7](Parametry klasowe)
@[9](<span style="color: red">Error! You did not define a `doesNotExist` prop.</span>)

+++

## PropTypes

@ul

- Dokumentacja
- Klarowny interfejs pomiędzy komponentami

@ulend

---

# 3. Nie nadużywaj stanu

+++

## Zmiana stanu powoduje ponowne renderowanie

+++

## Unikaj stanu zależnego

```javascript
class MyComponent extends React.Component {
  state = {
    selectedCar: null,
    submitEnabled: false,
  };  
}
```

@[4]

+++

## Najważniejsze zasady

@ul

- Używaj stanu tylko wtedy, gdy to faktycznie niezbędne
- Stan powinien być źródłem prawdy

@ulend

---

# 4. Znaj cykl życia komponentu

+++

## Zmiana stanu jest asynchroniczna
```javascript
onClick() {
  this.setState({
    count: 5,
  });

  console.log(this.state.count);  
}
```

@[2-4]
@[6]

+++

## Callback po zmianie stanu

```javascript
this.setState({
  count: 5,
}, () => {
  console.log(this.state.count);  
});
```

@[3-5]

+++

## Cykl życia komponetu

```javascript
componentDidUpdate() {
  console.log(this.state.count);    
}
```

@[1](Wywoływana w różnych przypadkach)

---

# 5. Poprawnie przekazuj callbacki

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

@[1-3]
@[7-9]
@[8]

+++

## this

@ul

- Powinien wskazywać na dany komponent
- Zależny od kontekstu wykonania

@ulend

+++

## Metoda obiektu zamiast prototypu
```javascript
onClick = () => {
  alert(`Hello ${this.props.name}`);
}

onClick() {
  alert(`Hello ${this.props.name}`);
}
```

@[1-3]
@[4-6]

+++

## Tworzenie nowej funkcji
```javascript
render() {
  return (
    <button
      onClick={() => this.onClick()}
    >Test</button>
  );
}
```

@[4]

---

# 6. Kompozycja zamiast dziedziczenia

<!-- http://blog.brew.com.hk/react-101-composition-vs-inheritance/ -->

+++

## Bazowy formularz
```javascript
class PaymentMethodForm extends React.Component {
  render() {
    return (
      <div>
        <input type="text" />
      </div>
    );
  }
}
```

+++

## Create - dziedziczenie
```javascript
class CreatePaymentMethodForm extends PaymentMethodForm {
  render() {
    const parent = super.render();
    return (
      <div>
        {parent}
        <button>Create</button>
      </div>
    )
  }
}
```

@[3]

+++

## Update - dziedziczenie
```javascript
class UpdatePaymentMethodForm extends PaymentMethodForm {
  render() {
    const parent = super.render();
    return (
      <div>
        {parent}
        <button>Update</button>
      </div>
    )
  }
}
```

@[3]

+++

## Create - kompozycja
```javascript
class CreatePaymentMethodForm extends React.Component {
  render() {
    return (
      <div>
        <PaymentMethodForm />
        <button>Create</button>
      </div>
    )
  }
}
```

+++

## Używaj dziedziczenia tylko w wyjątkowych wypadkach

@ul

- Implementacja nowych możliwości
- Integracja ze stanem globalnym

@ulend

---

# 7. Unikaj powtarzania kodu

+++

## Bad example
```javascript
const MyComponent = () => (
  <div>
    <OtherComponent type="a" foo={123} bar={456} />
    <OtherComponent type="b" foo={123} bar={456} />    
  </div>
);
```

+++

## GoodExample
```javascript
const MyOtherComponent = ({ type }) => (
  <OtherComponent type={type} foo={123} bar={456} />
);

const MyComponent = () => (
  <div>
    <MyOtherComponent type="a" />
    <MyOtherComponent type="b" />
  </div>
);
```

+++

@ul

- Zagmatfany kod, trudny do testowania i zrozumienia
- Plik z kodem, który się powtarza

@ulend

---

## Podsumowanie

@ol

- Dziel komponenty na mniejsze
- Używaj PropTypes lub statycznego kontroli typów
- Nie nadużywaj stanu
- Znaj cykl życia komponentu
- Poprawnie przekazuj callbacki
- Kompozycja zamiast dziedziczenia
- Unikaj powtarzania kodu

@olend

---?image=react-7-mistakes-pl/img/rr_logo.png&size=auto 70%

# React Rangers
## Freelance 2.0

@ul

- zagraniczne kontrakty z pracą zdalną
- zarządzanie profilem
- wsparcie innych profesjonalistów
- artykuły i wystąpienia

@ulend

+++

## marcin @fa[at] hagmajer . com

@ul

- @fa[link] https://blog.hagmajer.com/
- @fa[facebook] HagmajerBlog
- @fa[twitter] @mhagmajer
- @fa[github] @mhagmajer

@ulend
