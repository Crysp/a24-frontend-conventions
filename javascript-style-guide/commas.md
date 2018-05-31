## Запятые

- [1.1](#commas--leading-trailing) Ведущие запятые: **Неа.** eslint: [`comma-style`](https://eslint.org/docs/rules/comma-style.html)

```js
// плохо
const story = [
    once
  , upon
  , aTime
];

// хорошо
const story = [
  once,
  upon,
  aTime,
];

// плохо
const hero = {
    firstName: 'Ada'
  , lastName: 'Lovelace'
  , birthYear: 1815
  , superPower: 'computers'
};

// хорошо
const hero = {
  firstName: 'Ada',
  lastName: 'Lovelace',
  birthYear: 1815,
  superPower: 'computers',
};
```

- [1.2](#commas--dangling) Дополнительная запятая: **Ага.** eslint: [`comma-dangle`](https://eslint.org/docs/rules/comma-dangle.html)

> Зачем? Это позволяет лучше понять что было изменено, при сравнении версий в git. К тому же, транспилеры, на подобии Babel, удаляют дополнительную запятую в транспилированном коде, что означает, что вам не нужно беспокоится о [проблеме с дополнительной запятой](https://github.com/airbnb/javascript/blob/es5-deprecated/es5/README.md#commas) в устаревших браузерах.

```diff
// плохо - сравнение в git без конечной запятой
const hero = {
     firstName: 'Florence',
-    lastName: 'Nightingale'
+    lastName: 'Nightingale',
+    inventorOf: ['coxcomb chart', 'modern nursing']
};

// хорошо - сравнение в git с конечной запятой
const hero = {
     firstName: 'Florence',
     lastName: 'Nightingale',
+    inventorOf: ['coxcomb chart', 'modern nursing'],
};
```

```js
// плохо
const hero = {
  firstName: 'Dana',
  lastName: 'Scully'
};

const heroes = [
  'Batman',
  'Superman'
];

// хорошо
const hero = {
  firstName: 'Dana',
  lastName: 'Scully',
};

const heroes = [
  'Batman',
  'Superman',
];

// плохо
function createHero(
  firstName,
  lastName,
  inventorOf
) {
    // ничего не делает
}

// хорошо
function createHero(
  firstName,
  lastName,
  inventorOf,
) {
    // ничего не делает
}

// хорошо (обратите внимание, что запятая не должна появляться после "rest" элемента)
function createHero(
  firstName,
  lastName,
  inventorOf,
  ...heroArgs
) {
    // ничего не делает
}

// плохо
createHero(
  firstName,
  lastName,
  inventorOf
);

// хорошо
createHero(
  firstName,
  lastName,
  inventorOf,
);

// хорошо (обратите внимание, что запятая не должна появляться после "rest" элемента)
createHero(
  firstName,
  lastName,
  inventorOf,
  ...heroArgs
);
```