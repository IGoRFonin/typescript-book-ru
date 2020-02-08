# readonly

Система типов TypeScript позволяет помечать атрибуты только для чтения\(`readonly`\) в интерфейсе. Это позволяет вам работать более безопасно \(непредсказуемые изменения ужасны\):

```typescript
function foo(config: { readonly bar: number, readonly bas: number }) {
  // ..
}

const config = { bar: 123, bas: 123 };
foo(config);

// Теперь вы можете убедиться, что `config` не может быть изменен
```

Конечно, вы также можете использовать `readonly` в `interface` и `type`:

```typescript
type Foo = {
  readonly bar: number;
  readonly bas: number;
};

// Инициализация
const foo: Foo = { bar: 123, bas: 456 };

// Не может быть изменено
foo.bar = 456; // Error: foo.bar aтрибут только для чтения
```

Вы также можете указать свойства класса только для чтения, а затем инициализировать их во время объявления или в конструкторе, как показано ниже:

```typescript
class Foo {
  readonly bar = 1; // OK
  readonly baz: string;
  constructor() {
    this.baz = 'hello'; // OK
  }
}
```

## Readonly

Существует тип `Readonly`, который принимает универсальный `T`, чтобы пометить все его свойства `readonly`:

```typescript
type Foo = {
  bar: number;
  bas: number;
};

type FooReadonly = Readonly<Foo>;

const foo: Foo = { bar: 123, bas: 456 };
const fooReadonly: FooReadonly = { bar: 123, bas: 456 };

foo.bar = 456; // ok
fooReadonly.bar = 456; // Error: bar aтрибут только для чтения
```

## Другие варианты использования

### ReactJS

`ReactJS` - это библиотека, которая любит использовать неизменные данные. Вы можете пометить свои `props` и `state` как неизменные данные:

```typescript
interface Props {
  readonly foo: number;
}

interface State {
  readonly bar: number;
}

export class Something extends React.Component<Props, State> {
  someMethod() {
    // Вы можете быть уверены, что никто не будет так делать
    this.props.foo = 123; // Error: props неизменен
    this.state.baz = 456; // Error: Вы должны использовать this.setState()
  }
}
```

Однако вам не нужно этого делать. Файл объявлений `React` помечает их как `readonly` \(путем передачи общих параметров во внутреннюю оболочку, чтобы пометить каждое свойство как `readonly`, как показано в примере выше\),

```typescript
export class Something extends React.Component<{ foo: number }, { baz: number }> {
  someMethod() {
    this.props.foo = 123; // Error: props неизменен
    this.state.baz = 456; // Error: Вы должны использовать this.setState()
  }
}
```

### Абсолютно неизменный

Вы даже можете пометить подпись индекса как доступную только для чтения:

```typescript
interface Foo {
  readonly [x: number]: number;
}

// Использование

const foo: Foo = { 0: 123, 2: 345 };
console.log(foo[0]); // ok(Чтение)
foo[0] = 456; // Error: Атрибут только для чтения
```

Если вы хотите использовать собственные массивы JavaScript согласованным образом, вы можете использовать интерфейс \`ReadonlyArray T, предоставляемый TypeScript:

```typescript
let foo: ReadonlyArray<number> = [1, 2, 3];
console.log(foo[0]); // ok
foo.push(4); // Error: ReadonlyArray `push` не существует, потому что он меняет массив
foo = foo.concat(4); // ok, cоздана копия
```

### Автоматический вывод

В некоторых случаях компилятор может выводить некоторые свойства как `readonly`. Например, в классе, если у вас есть свойство только с `getter`, но без `setter`, оно может быть выведено как доступное только для чтения:

```typescript
class Person {
  firstName: string = 'John';
  lastName: string = 'Doe';

  get fullName() {
    return this.firstName + this.lastName;
  }
}

const person = new Person();

console.log(person.fullName); // John Doe
person.fullName = 'Dear Reader'; // Error, fullName только для чтения
```

## Отличается от `const` 

`const` 

* для переменных;
* переменные не могут быть переназначены ни на что другое.

`readonly` 

* для атрибутов;
* используется для псевдонима, вы можете изменить атрибуты.

Простой пример 1:

```typescript
const foo = 123; // Переменная
let bar: {
  readonly bar: number; // свойство
};
```

Простой пример 2:

```typescript
const foo: {
  readonly bar: number;
} = {
  bar: 123
};

function iMutateFoo(foo: { bar: number }) {
  foo.bar = 456;
}

iMutateFoo(foo);
console.log(foo.bar); // 456
```

`readonly` гарантирует, что «Я» не могу изменить атрибут, но когда вы передаете этот атрибут другим пользователям, у которых нет этой гарантии \(учитывая причины совместимости типов\), они могут изменить его. Конечно, если `iMutateFoo` явно заявляет, что их параметры не могут быть изменены, компилятор выдаст предупреждение об ошибке:

```typescript
interface Foo {
  readonly bar: number;
}

let foo: Foo = {
  bar: 123
};

function iTakeFoo(foo: Foo) {
  foo.bar = 456; // Error: bar aтрибут только для чтения
}

iTakeFoo(foo);
```

