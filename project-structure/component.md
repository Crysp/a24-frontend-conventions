# Компонент

* [Структура]()
* [Именование]()
* [Класс]()
* [Стили]()
* [HOC-data]()

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
