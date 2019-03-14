# Компонент

* [Структура]()
* [Именование]()
* [Класс]()
* [Стили]()
* [HOC-data]()
* [Как выделить компонент]()

## Структура

```
.
├── ...
├── AwesomeComponent  # Папка компонента
│   ├── components    # Компоненты используемые только в контексте AwesomeComponent
│   │   ├── ...
│   │   └── index.js  # Содержит экспорты всех компонентов в папке components
│   ├── helpers       # Вспомогательные функции
│   │   ├── ...
│   │   └── index.js  # Содержит экспорты всех хелперов
│   ├── data
│   │   └── index.js  # HOC-data компонент, который прокидывает в AwesomeComponent данные из внешних и внутренних API 
│   ├── index.js      # Главный файл
│   └── styled.js     # Стили
└── ...
```

### Именование

Имя каждого стилизованного компонента **обязательно** должно быть в формате [PascalCase](http://wiki.c2.com/?PascalCase)

Если у компонента есть родительский, то имя текущего компонента **не должно** содержать имя родительского. 
Потому что у нас есть вложенность компонентов, которая уже ограничивает область использования компонента.

Например, если ты находишься в офисе, то ты не скажешь "офисный стул", а скажешь просто "стул", т.к. ты уже находишься в
контексте офиса.

❌ **Bad**

```
.
├── ...
├── Order
│   └── components
│       ├── OrderTitle
│       └── OrderDescription
└── ...
```

✅ **Good**

```
.
├── ...
├── Order
│   └── components
│       ├── Title
│       └── Description
└── ...
```

## Класс

```js
import React from 'react';
import PropTypes from 'prop-types';
import { NestedComponent } from './components';
import { Wrapper } from './styled';

class AwesomeButton extends React.Component {
    constructor(props) {}
    
    // lifecycle callbacks
    componentWillMount() {}
    componentDidUpdate() {}
    componentWillUnmount() {}
    
    // custom methods
    getText() {}
    getIcon() {}
    
    render() {
        return (
            <Wrapper>
                <span>{this.getIcon()}</span>
                <NestedComponent/>
                <span>{this.getText()}</span>
            </Wrapper>
        );
    }
}

AwesomeButton.propTypes = {
    text: PropTypes.string,
    icon: PropTypes.string,
    color: PropTypes.string,
    onClick: PropTypes.func,
};

AwesomeButton.defaultProps = {
    text: '',
    color: 'default',
};

export default AwesomeButton;
```

## Стили

🔨 *todo*

```js
import styled from 'styled-components';

const defaultFontSize = 1;

export const Wrapper = styled.button`
    position: relative;
    font-size: ${defaultFontSize}rem;
`;
``` 


## HOC-data

Вся работа с внешними и внутренними API выносится из компонента с представлением в компонент высшего порядка в папку
`data`, которая находится на одном уровне с файлом представления.

❌ **Bad**

```js
import React from 'react';
import { connect } from 'react-redux';

class AwesomeComponent extends React.Component {
    // ...
}

export default connect(/* ... */)(AwesomeComponent);
```

✅ **Good**

`data/index.js`

```js
import { connect } from 'react-redux';

const withData = connect(/* ... */);

export default withData;
```

`index.js`

```js
import React from 'react';
import withData from './data';

@withData
class AwesomeComponent extends React.Component {
    // ...
}

export default AwesomeComponent;
```

## Хелперы

Функциии, которые не зависят от контекста компонента и относятся к безнес-логике.

Каждый статичный метод компонента можно представить в виде вспомогательной функции.
Мы не используем статичные методы класса, поэтому надо **обязательно** выносить статичные методы в хелперы.

❌ **Bad**

```js
class AwesomeComponent extends React.Component {
    // ...
    static getCurrentDate() { /* ... */ }
    // ...
}
```

✅ **Good**

```js
export const getCurrentDate = () => { /* ... */ };
```

## Как выделить компонент

> Perfection is attained
> not when there is nothing more to add,
> but when there is nothing more to take away.
>
> —- <cite>Antoine de Saint Exupéry</cite>

Существует популярный принцип проектирования **SOLID**, который помогает писать простые и понятные классы в ООП.
Этот принцип можно легко применить и к React-компонентам.


### Single Responsibility Principle (Принцип единой ответственности)

Этот принцип говорит тебе, что у компонента должна быть только одна ответственность. 
Все его поведения должны быть направлены исключительно на обеспечение этой ответственности.

Например, есть компонент заказа, который отображает данные по отдельному заказу, открывает модалку с
прикрепленными файлами и открывает чат.

❌ **Bad**

```js
class Order extends React.Component {
    getFilesCount = () => {/* ... */};
    
    openFiles = () => {/* ... */};
    
    openChat = () => {/* ... */};
    
    render() {
        return (
            <div>
                <a href="#">Title</a>
                <button onClick={this.openFiles}>Files ({this.getFilesCount()})</button>
                <button onClick={this.openChat}>Chat</button>
            </div>
        );
    }
}
```

В описании этого компонента идет перечисление, а это уже первая причина к тому что у него несколько ответственностей.
Для разделения ответственности надо разделить этот компонент на несколько более простых.

✅ **Good**

```js
class Order extends React.Component {
    render() {
        return (
            <div>
                <a href="#">Title</a>
                <FilesButton />
                <ChatButton />
            </div>
        );
    }
}

class FilesButton extends React.Component {
    getCount = () => {/* ... */};
    
    open = () => {/* ... */};
    
    render() {
        return <button onClick={this.open}>Files ({this.getCount()})</button>;
    }
}

class ChatButton extends React.Component {
    open = () => {/* ... */};
    
    render() {
        return <button onClick={this.open}>Chat</button>;
    }
}
```
