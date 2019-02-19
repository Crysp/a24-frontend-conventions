## Точка с запятой

- [2.1] **Ага.** eslint: [`semi`](https://eslint.org/docs/rules/semi.html)

> Зачем? Когда JavaScript встречает разрыв строки без точки с запятой, он использует набор правил называемых [Automatic Semicolon Insertion](https://tc39.github.io/ecma262/#sec-automatic-semicolon-insertion) (ASI) чтобы определить, следует ли рассматривать этот разрыв строки как конец инструкции, и (как следует из названия) помещать точку с запятой в ваш код до того, как строка сломается. Однако ASI содержит несколько странных моделей поведения, и ваш код будет сломан, если JavaScript неверно поймет ваш разрыв строки. Эти правила становятся сложнее, так как в JavaScript добавляются новые возможности. Явное прерывание ваших инструкций и найстройка вашего linter для поиска пропущенных точек с запятой помогут вамизбежать проблем.

```js
// плохо - вызывает исключение
const luke = {}
const leia = {}
[luke, leia].forEach(jedi => jedi.father = 'vader')

// плохо - вызывает исключение
const reaction = "No! That's impossible!"
(async function meanwhileOnTheFalcon() {
  // handle `leia`, `lando`, `chewie`, `r2`, `c3p0`
  // ...
}())

// плохо - возвращает `undefined` вместо значения на следующей строке - всегда происходит когда `return` находится на линии сам по себе из-за ASI!
function foo() {
  return
    'search your feelings, you know it to be foo'
}

// хорошо
const luke = {};
const leia = {};
[luke, leia].forEach((jedi) => {
  jedi.father = 'vader';
});

// хорошо
const reaction = "No! That's impossible!";
(async function meanwhileOnTheFalcon() {
  // handle `leia`, `lando`, `chewie`, `r2`, `c3p0`
  // ...
}());

// хорошо
function foo() {
  return 'search your feelings, you know it to be foo';
}
```

[Read more](https://stackoverflow.com/questions/7365172/semicolon-before-self-invoking-function/7365214#7365214).
