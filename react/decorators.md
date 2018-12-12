## Декораторы

* [Данные для компонента](#prostoi-komponent)

## Данные для компонента

Вся логика с **HOC**-компонентами, которые достают данные для компонента, выносится в отдельный файл.

Например, есть такой компонент.

```
./User
  - index.js
  - query.graphql
```

```javascript
import React from 'react';
import { connect } from 'react-redux';
import { compose, graphql } from 'react-apollo';
import query from './query.graphql';

@compose(
    connect(
        state => ({ profileId: state.profile.id })
    ),
    graphql(query)
)
class User extends React.Component {
    // ...
}
```

Он разделяется на два файла:
  - `index.js` - представление
  - `data/index.js` - HOC для получения необходимых данных, которые передаются в свойства компонента
  
Получается

```
./User
  - ./data
    - index.js
  - index.js
  - query.graphql
```
  
```javascript
import React from 'react';
import withData from './data';

@withData
class User extends React.Component {
    // ...
}
```

```javascript
import { connect } from 'react-redux';
import { compose, graphql } from 'react-apollo';
import query from '../query.graphql';

const withData = compose(
    connect(
        state => ({ profileId: state.profile.id })
    ),
    graphql(query)
);

export default withData;
```
