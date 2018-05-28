## Паттерны

* [Простой компонент](#Простой-компонент)
* [Передача свойств](#Передача-свойств)
* [Деструктуризация аргументов](#Деструктуризация-аргументов)
* [Условный рендеринг](#Условный-рендеринг)
* [Типы потомков](#Типы-потомков)
* [Массив как потомок](#Массив-как-потомок)
* [Функция как потомок](#Функция-как-потомок)
* [Рендер-коллбек](#Рендер-коллбек)
* [Проход по потомкам](#Проход-по-потомкам)
* [Перенаправление компонента](#Перенаправление-компонента)
* [Стилизация компонентов](#Стилизация-компонентов)
* [Переключатель событий](#Переключатель-событий)
* [Компонент-макет](#Компонент-макет)
* [Компонент-контейнер](#Компонент-контейнер)
* [Компоненты высшего порядка](#Компоненты-высшего-порядка)
* [Пробрасывание состояния](#Пробрасывание-состояния)
* [Управляемое поле](#Управляемое-поле)

## Простой компонент

[Простые компоненты](https://facebook.github.io/react/docs/components-and-props.html) идеальный способ написать универсальный компонент. У них нет состояния `state`; это просто функции.

```js
const Greeting = () => <div>Hi there!</div>
```

В них передаются свойства `props` и контекст `context`.

```js
const Greeting = (props, context) =>
  <div style={{color: context.color}}>Hi {props.name}!</div>
```

Можно определять локальные переменные (это все еще обычная функция).

```js
const Greeting = (props, context) => {
  const style = {
    fontWeight: "bold",
    color: context.color,
  }

  return <div style={style}>{props.name}</div>
}
```

Можно получить такой же результат используя вспомогательные функции.

```js
const getStyle = context => ({
  fontWeight: "bold",
  color: context.color,
})

const Greeting = (props, context) =>
  <div style={getStyle(context)}>{props.name}</div>
```

Есть возможность определять дефолтные свойства `defaultProps`, их типы `propTypes` и тип данных в контексте `contextTypes`.

```js
Greeting.propTypes = {
  name: PropTypes.string.isRequired
}
Greeting.defaultProps = {
  name: "Guest"
}
Greeting.contextTypes = {
  color: PropTypes.string
}
```


## Передача свойств

"Распределение" свойств - это фича JSX, основанная на [операторе расширения](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Operators/Spread_operator). Синтаксический сахар для передачи всех свойств как атрибуты компонента.

Эти два примера эквивалентны.
```js
// своства передаются как атрибуты
<main className="main" role="main">{children}</main>

// свойства "распределены" из объекта
<main {...{className: "main", role: "main", children}} />
```

Используется для прокидывания свойств `props` в создаваемые компоненты.

```js
const FancyDiv = props =>
  <div className="fancy" {...props} />
```

Теперь вы можете быть уверены, что в компоненте `FancyDiv`, нужный атрибут будет присутствовать (`className`), так же как и те которые вы не указали напрямую в функции а передали в нее вместе с `props`

```js
<FancyDiv data-id="my-fancy-div">So Fancy</FancyDiv>

// output: <div className="fancy" data-id="my-fancy-div">So Fancy</div>
```

Имейте ввиду, что порядок имеет значение. Если `props.className` определено, то это свойство перепишет `className` определенное в `FancyDiv`.

```js
<FancyDiv className="my-fancy-div" />

// output: <div className="my-fancy-div"></div>
```

Можно сделать так что `className`, описанный в компоненте `FancyDiv`, будет всегда одинаковый, переместив его после оператора "распределения" `({...props})`.

```js
// my `className` clobbers your `className`
const FancyDiv = props =>
  <div {...props} className="fancy" />
```

Есть более изящный подход — объединить оба свойства.

```js
const FancyDiv = ({ className, ...props }) =>
  <div
    className={["fancy", className].join(' ')}
    {...props}
  />
```


## Деструктуризация аргументов

[Деструктурирующее присвоение](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) это фича ES2015. Она отлично сочетается с простыми компонентами.

Эти примеры эквивалентны.
```js
const Greeting = props => <div>Hi {props.name}!</div>

const Greeting = ({ name }) => <div>Hi {name}!</div>
```

Синтаксис [оператора rest](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters) (`...`) позволяет собрать все оставшиеся свойства в один объект.

```js
const Greeting = ({ name, ...props }) =>
  <div>Hi {name}!</div>
```

Далее, используя [передачу свойств](#Передача-свойств), можно прокинуть их дальше.

```js
const Greeting = ({ name, ...props }) =>
  <div {...props}>Hi {name}!</div>
```

## Условный рендеринг

У вас не получится использовать обычный if/else синтаксис в компонентах. По-этому [тернарный оператор](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Operators/%D0%A3%D1%81%D0%BB%D0%BE%D0%B2%D0%BD%D1%8B%D0%B9_%D0%BE%D0%BF%D0%B5%D1%80%D0%B0%D1%82%D0%BE%D1%80) - это ваш выбор.

`if`

```js
{condition && <span>Rendered when `truthy`</span> }
```

`unless`

```js
{condition || <span>Rendered when `falsey`</span> }
```

`if-else` (однострочные блоки)

```js
{condition
  ? <span>Rendered when `truthy`</span>
  : <span>Rendered when `falsey`</span>
}
```

`if-else` (многострочные блоки)

```js
{condition ? (
  <span>
    Rendered when `truthy`
  </span>
) : (
  <span>
    Rendered when `falsey`
  </span>
)}
```


## Типы потомков

React может рендерить потомков любого типа. В основном это массив или строка.

`string`

```js
<div>
  Hello World!
</div>
```

`array`

```js
<div>
  {["Hello ", <span>World</span>, "!"]}
</div>
```

Функции могут быть так же использованы как потомки. Однако, нужно [координировать их поведение с родительским компонентом](#Функция-в-render).

`function`

```js
<div>
  {(() => { return "hello world!"})()}
</div>
```


## Массив как потомок

Использование массива потомков, это обычный паттерн, например так вы делаете списки в React.

Мы используем map(), чтобы сделать массив элементов React, для каждого значения в массиве.

```js
<ul>
  {["first", "second"].map((item) => (
    <li>{item}</li>
  ))}
</ul>
```

Это эквивалентно литералу массива с объектами

```js
<ul>
  {[
    <li>first</li>,
    <li>second</li>,
  ]}
</ul>
```

Такой паттерн может быть использован совместно с деструктуризацией, распределением атрибутов и другими фичами, чтобы упростить написание кода

```js
<ul>
  {arrayOfMessageObjects.map(({ id, ...message }) =>
    <Message key={id} {...message} />
  )}
</ul>
```


## Функция как потомок

Использование функции как `children` не является по своей сути полезным.

```js
<div>{() => { return "hello world!"}()}</div>
```

Однако, они могут придать вашим компонентам супер силу, такая техника обычно называется [рендер-коллбэк](#Рендер-коллбек).

Эта мощная техника используется в таких библиотеках как [ReactMotion](https://github.com/chenglou/react-motion). Когда вы применяете ее, логика рендера может управляйся из родительского компонента, вместо того, чтобы полностью передать ее самому компоненту.

Посмотри [Рендер-коллбек](#Рендер-коллбек), чтобы узнать подробнее.

## Рендер-коллбек

Вот пример компонента который использует рендер-коллбэк. Он в целом бесполезен, однако хорошо демонстрирует возможности такого подхода.

```js
const Width = ({ children }) => children(500)
```

Компонент вызывает потомков, как функцию с определенным аргументом. В данном случае это число `500`.

Чтобы использовать этот компонент мы передаем ему [функцию как потомка](#Функция-как-потомок).

```js
<Width>
  {width => <div>window is {width}</div>}
</Width>
```

Получим такой результат.

```js
<div>window is 500</div>
```

При таком подходе, можно использовать параметр `width`, для условного рендеринга.

```js
<Width>
  {width =>
    width > 600
      ? <div>min-width requirement met!</div>
      : null
  }
</Width>
```

Если планируем использовать такое условие много раз, то мы можем определить другой компонент, чтобы передать ему эту логику.

```js
const MinWidth = ({ width: minWidth, children }) =>
  <Width>
    {width =>
      width > minWidth
        ? children
        : null
    }
  </Width>
```


Очевидно, что статичный компонент `Width`, не очень полезен, но мы можем наблюдать за размерами окна браузера при таком подходе.

```js
class WindowWidth extends React.Component {
  constructor() {
    super()
    this.state = { width: 0 }
  }

  componentDidMount() {
    this.setState(
      {width: window.innerWidth},
      window.addEventListener(
        "resize",
        ({ target }) =>
          this.setState({width: target.innerWidth})
      )
    )
  }

  render() {
    return this.props.children(this.state.width)
  }
}
```

Многие предпочитают [Higher Order Components](#higher-order-component) (компоненты высшего порядка) для такого типа функционала. Это вопрос личных предпочтений.


## Проход по потомкам

You might create a component designed to apply `context` and render its `children`.

```js
class SomeContextProvider extends React.Component {
  getChildContext() {
    return {some: "context"}
  }

  render() {
    // how best do we return `children`?
  }
}
```

You're faced with a decision. Wrap `children` in an extraneous `<div />` or return `children` directly. The first options gives you extra markup (which can break some stylesheets). The second will result in unhelpful errors.

```js
// option 1: extra div
return <div>{children}</div>

// option 2: unhelpful errors
return children
```

It's best to treat `children` as an opaque data type. React provides `React.Children` for dealing with `children` appropriately.

```js
return React.Children.only(this.props.children)
```

## Перенаправление компонента

*(I'm not sure if this name makes sense)*

Buttons are everywhere in web apps. And every one of them must have the `type` attribute set to "button".

```js
<button type="button">
```

Writing this attribute hundreds of times is error prone. We can write a higher level component to proxy `props` to a lower-level `button` component.

```js
const Button = props =>
  <button type="button" {...props}>
```

We can use `Button` in place of `button` and ensure that the `type` attribute is consistently applied everywhere.

```js
<Button />
// <button type="button"><button>

<Button className="CTA">Send Money</Button>
// <button type="button" class="CTA">Send Money</button>
```

## Стилизация компонентов

This is a [Proxy component](#proxy-component) applied to the practices of style.

Say we have a button. It uses classes to be styled as a "primary" button.

```js
<button type="button" className="btn btn-primary">
```

We can generate this output using a couple single-purpose components.

```js
import classnames from 'classnames'

const PrimaryBtn = props =>
  <Btn {...props} primary />

const Btn = ({ className, primary, ...props }) =>
  <button
    type="button"
    className={classnames(
      "btn",
      primary && "btn-primary",
      className
    )}
    {...props}
  />
```

It can help to visualize this.

```js
PrimaryBtn()
  ↳ Btn({primary: true})
    ↳ Button({className: "btn btn-primary"}, type: "button"})
      ↳ '<button type="button" class="btn btn-primary"></button>'
```

Using these components, all of these result in the same output.
```js
<PrimaryBtn />
<Btn primary />
<button type="button" className="btn btn-primary" />
```

This can be a huge boon to style maintenance. It isolates all concerns of style to a single component.

## Переключатель событий


When writing event handlers it's common to adopt the `handle{eventName}` naming convention.

```js
handleClick(e) { /* do something */ }
```

For components that handle several event types, these function names can be repetitive. The names themselves might not provide much value, as they simply proxy to other actions/functions.

```js
handleClick() { require("./actions/doStuff")(/* action stuff */) }
handleMouseEnter() { this.setState({ hovered: true }) }
handleMouseLeave() { this.setState({ hovered: false }) }
```

Consider writing a single event handler for your component and switching on `event.type`.

```js
handleEvent({type}) {
  switch(type) {
    case "click":
      return require("./actions/doStuff")(/* action dates */)
    case "mouseenter":
      return this.setState({ hovered: true })
    case "mouseleave":
      return this.setState({ hovered: false })
    default:
      return console.warn(`No case for event type "${type}"`)
  }
}
```

Alternatively, for simple components, you can call imported actions/functions directly from components, using arrow functions.

```js
<div onClick={() => someImportedAction({ action: "DO_STUFF" })}
```

Don't fret about performance optimizations until you have problems. Seriously don't.


## Компонент-макет


Layout components result in some form of static DOM element. It might not need to update frequently, if ever.

Consider a component that renders two `children` side-by-side.

```js
<HorizontalSplit
  leftSide={<SomeSmartComponent />}
  rightSide={<AnotherSmartComponent />}
/>
```

We can aggressively optimize this component.

While `HorizontalSplit` will be `parent` to both components, it will never be their `owner`. We can tell it to update never, without interrupting the lifecycle of the components inside.

```js
class HorizontalSplit extends React.Component {
  shouldComponentUpdate() {
    return false
  }

  render() {
    <FlexContainer>
      <div>{this.props.leftSide}</div>
      <div>{this.props.rightSide}</div>
    </FlexContainer>
  }
}
```


## Компонент-контейнер

"A container does data fetching and then renders its corresponding sub-component. That’s it."&mdash;[Jason Bonta](https://twitter.com/jasonbonta)

Given this reusable `CommentList` component.

```js
const CommentList = ({ comments }) =>
  <ul>
    {comments.map(comment =>
      <li>{comment.body}-{comment.author}</li>
    )}
  </ul>
```

We can create a new component responsible for fetching data and rendering the stateless `CommentList` component.

```js
class CommentListContainer extends React.Component {
  constructor() {
    super()
    this.state = { comments: [] }
  }

  componentDidMount() {
    $.ajax({
      url: "/my-comments.json",
      dataType: 'json',
      success: comments =>
        this.setState({comments: comments});
    })
  }

  render() {
    return <CommentList comments={this.state.comments} />
  }
}
```

We can write different containers for different application contexts.


## Компоненты высшего порядка

A [higher-order function](https://en.wikipedia.org/wiki/Higher-order_function) is a function that takes and/or returns a function. It's not more complicated than that. So, what's a higher-order component?

If you're already using [container components](#container-component), these are just generic containers, wrapped up in a function.

Let's start with our stateless `Greeting` component.

```js
const Greeting = ({ name }) => {
  if (!name) { return <div>Connecting...</div> }

  return <div>Hi {name}!</div>
}
```

If it gets `props.name`, it's gonna render that data. Otherwise it'll say that it's "Connecting...". Now for the the higher-order bit.

```js
const Connect = ComposedComponent =>
  class extends React.Component {
    constructor() {
      super()
      this.state = { name: "" }
    }

    componentDidMount() {
      // this would fetch or connect to a store
      this.setState({ name: "Michael" })
    }

    render() {
      return (
        <ComposedComponent
          {...this.props}
          name={this.state.name}
        />
      )
    }
  }
```

This is just a function that returns component that renders the component we passed as an argument.

Last step, we need to wrap our our `Greeting` component in `Connect`.

```js
const ConnectedMyComponent = Connect(Greeting)
```

This is a powerful pattern for providing fetching and providing data to any number of [stateless function components](#stateless-function).

## Пробрасывание состояния
[Stateless functions](#stateless-function) don't hold state (as the name implies).

Events are changes in state.
Their data needs to be passed to stateful [container components](#container-component) parents.

This is called "state hoisting".
It's accomplished by passing a callback from a container component to a child component.

```js
class NameContainer extends React.Component {
  render() {
    return <Name onChange={newName => alert(newName)} />
  }
}

const Name = ({ onChange }) =>
  <input onChange={e => onChange(e.target.value)} />
```

`Name` receives an `onChange` callback from `NameContainer` and calls on events.

The `alert` above makes for a terse demo but it's not changing state.
Let's change the internal state of `NameContainer`.

```js
class NameContainer extends React.Component {
  constructor() {
    super()
    this.state = {name: ""}
  }

  render() {
    return <Name onChange={newName => this.setState({name: newName})} />
  }
}
```

The state is _hoisted_ to the container, by the provided callback, where it's used to update local state.
This sets a nice clear boundary and maximizes the re-usability of stateless function.

This pattern isn't limited to stateless functions.
Because stateless function don't have lifecycle events,
you'll use this pattern with component classes as well.

*[Controlled input](#controlled-input) is an important pattern to know for use with state hoisting*

*(It's best to process the event object on the stateful component)*


## Управляемое поле
It's hard to talk about controlled inputs in the abstract.
Let's start with an uncontrolled (normal) input and go from there.

```js
<input type="text" />
```

When you fiddle with this input in the browser, you see your changes.
This is normal.

A controlled input disallows the DOM mutations that make this possible.
You set the `value` of the input in component-land and it doesn't change in DOM-land.

```js
<input type="text" value="This won't change. Try it." />
```

Obviously static inputs aren't very useful to your users.
So, we derive a `value` from state.

```js
class ControlledNameInput extends React.Component {
  constructor() {
    super()
    this.state = {name: ""}
  }

  render() {
    return <input type="text" value={this.state.name} />
  }
}
```

Then, changing the input is a matter of changing component state.

```js
    return (
      <input
        value={this.state.name}
        onChange={e => this.setState({ name: e.target.value })}
      />
    )
```

This is a controlled input.
It only updates the DOM when state has changed in our component.
This is invaluable when creating consistent UIs.

*If you're using [stateless functions](#stateless-function) for form elements,
read about using [state hoisting](#state-hoisting) to move new state up the component tree.*