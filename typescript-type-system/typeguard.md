---
description: Защита типов позволяет использовать меньшие типы объектов.
---

# Тип защиты

## typeof

TypeScript знаком с использованием операторов `instanceof` и `typeof` в JavaScript. Если вы используете их в блоке условий, TypeScript выведет типы переменных в блоке условий. Как показано в следующем примере, TypeScript распознает, существует ли конкретная функция в строке и произошла ли опечатка:

```typescript
function doSome(x: number | string) {
  if (typeof x === 'string') {
    // В этом блоке TypeScript знает, что переменная `x` должена быть string
    console.log(x.subtr(1)); // Error: метод 'subtr' не существует у `string`
    console.log(x.substr(1)); // ok
  }

  x.substr(1); // Error: Нет гарантии, что `x` имеет тип string
}
```

## instanceof

Вот пример c `class` и `instanceof`:

```typescript
class Foo {
  foo = 123;
  common = '123';
}

class Bar {
  bar = 123;
  common = '123';
}

function doStuff(arg: Foo | Bar) {
  if (arg instanceof Foo) {
    console.log(arg.foo); // ok
    console.log(arg.bar); // Error
  }
  if (arg instanceof Bar) {
    console.log(arg.foo); // Error
    console.log(arg.bar); // ok
  }
}

doStuff(new Foo());
doStuff(new Bar());
```

TypeScript может даже понять `else`. Когда вы используете `if` для сужения типов, TypeScript знает, что типы в других блоках не являются типами в `if`:

```typescript
class Foo {
  foo = 123;
}

class Bar {
  bar = 123;
}

function doStuff(arg: Foo | Bar) {
  if (arg instanceof Foo) {
    console.log(arg.foo); // ok
    console.log(arg.bar); // Error
  } else {
    // В этом блоке должно быть `Bar`
    console.log(arg.foo); // Error
    console.log(arg.bar); // ok
  }
}

doStuff(new Foo());
doStuff(new Bar());
```

## in

Оператор `in` может безопасно проверить наличие атрибута на объекте, а также обычно используется для защиты типов:

```typescript
interface A {
  x: number;
}

interface B {
  y: string;
}

function doStuff(q: A | B) {
  if ('x' in q) {
    // q: A
  } else {
    // q: B
  }
}
```

## Защита буквального типа

Когда вы используете литеральные типы в типах объединения, вы можете проверить, отличаются ли они:

```typescript
type Foo = {
  kind: 'foo'; // Буквенный тип
  foo: number;
};

type Bar = {
  kind: 'bar'; // Буквенный тип
  bar: number;
};

function doStuff(arg: Foo | Bar) {
  if (arg.kind === 'foo') {
    console.log(arg.foo); // ok
    console.log(arg.bar); // Error
  } else {
    // 一定是 Bar
    console.log(arg.foo); // Error
    console.log(arg.bar); // ok
  }
}
```

## null и undefined с `strictNullChecks`

TypeScript достаточно умен, чтобы исключить как `null`, так и `undefined` с помощью проверки `== null / != null`. Например:

```typescript
function foo(a?: number | null) {
  if (a == null) return;

  // a is number now.
}
```

## Использовать защиту определенного типа

В JavaScript нет очень богатого встроенного механизма самоконтроля во время выполнения. Когда вы используете обычные объекты JavaScript \(и используете структурные типы, это более выгодно\), вы даже не можете получить доступ к `instanceof` и `typeof`. В этом сценарии вы можете создать пользовательскую функцию защиты типа, которая является просто функцией, которая возвращает значение, аналогичное `someArgumentName is SomeType`, следующим образом:

```typescript
// Просто один interface
interface Foo {
  foo: number;
  common: string;
}

interface Bar {
  bar: number;
  common: string;
}

// Определяемый пользователем тип защиты!
function isFoo(arg: Foo | Bar): arg is Foo {
  return (arg as Foo).foo !== undefined;
}

// Определяемые пользователем варианты использования защиты типов:
function doStuff(arg: Foo | Bar) {
  if (isFoo(arg)) {
    console.log(arg.foo); // ok
    console.log(arg.bar); // Error
  } else {
    console.log(arg.foo); // Error
    console.log(arg.bar); // ok
  }
}

doStuff({ foo: 123, common: '123' });
doStuff({ bar: 123, common: '123' });
```

