---
description: >-
  Функций играют очень важную роль в системе типов TypeScript, они являются
  основными строительными блоками составной системы.
---

# Функции

## Описание параметров

Вы можете описать параметры функции так же, как вы можете описать другие переменные:

```typescript
// variable annotation
let sampleVariable: { bar: number };

// function parameter annotation
function foo(sampleParameter: { bar: number }) {}
```

Здесь мы используем встроенное описание типов, кроме того, вы также можете использовать интерфейсы и другие методы.

## Возвращаемый тип

Вы можете описывать возвращаемый тип тем же стилем, что и переменную после списка параметров функции, как в примере `:Foo`:

```typescript
interface Foo {
  foo: string;
}

// Return type annotated as `: Foo`
function foo(sample: Foo): Foo {
  return sample;
}
```

Здесь мы используем `interface`, но вы можете использовать другие методы, такие как встроенные аннотации.

Как правило, вам не нужно описывать возвращаемый тип функции, потому что это может быть выведено компилятором автоматически:

```typescript
interface Foo {
  foo: string;
}

function foo(sample: Foo) {
  return sample; // inferred return type 'Foo'
}
```

Тем не менее, часто бывает полезно добавить эти аннотации, чтобы помочь разрешить ошибки, такие как:

```typescript
function foo() {
  return { fou: 'John Doe' }; // You might not find this misspelling of `foo` till it's too late
}

sendAsJSON(foo());
```

Если вы не планируете возвращать что-либо из функции, вы можете пометить это как `:void`. Вы можете удалить `void`, TypeScript выведет тип за вас.

### Необязательные параметры

Так вы можете пометить параметры как необязательные:

```typescript
function foo(bar: number, bas?: string): void {
  // ..
}

foo(123);
foo(123, 'hello');
```

В качестве альтернативы, когда вызывающая сторона не предоставляет параметр, вы можете указать значение по умолчанию \(`= someValue` после объявления параметра\):

```typescript
function foo(bar: number, bas: string = 'hello') {
  console.log(bar, bas);
}

foo(123); // 123, hello
foo(123, 'world'); // 123, world
```

### Перегрузка

TypeScript позволяет объявлять перегрузки функций. Это полезно для документации + безопасности типов. Рассмотрим следующий код:

```typescript
function padding(a: number, b?: number, c?: number, d?: any) {
  if (b === undefined && c === undefined && d === undefined) {
    b = c = d = a;
  } else if (c === undefined && d === undefined) {
    c = a;
    d = b;
  }
  return {
    top: a,
    right: b,
    bottom: c,
    left: d
  };
}
```

Если вы внимательно посмотрите на код, вы обнаружите, что значения a, b, c и d будут меняться в зависимости от количества переданных параметров. Эта функция также требует только 1, 2 или 4 параметра. Вы можете использовать перегрузку функций для обеспечения и документирования этих ограничений. Вам нужно только объявить заголовок функции несколько раз. Последний заголовок функции фактически активен в теле функции, но не доступен извне.

Это выглядит так:

```typescript
// Перегрузка
function padding(all: number);
function padding(topAndBottom: number, leftAndRight: number);
function padding(top: number, right: number, bottom: number, left: number);
// Actual implementation that is a true representation of all the cases the function body needs to handle
function padding(a: number, b?: number, c?: number, d?: number) {
  if (b === undefined && c === undefined && d === undefined) {
    b = c = d = a;
  } else if (c === undefined && d === undefined) {
    c = a;
    d = b;
  }
  return {
    top: a,
    right: b,
    bottom: c,
    left: d
  };
}
```

Здесь первые три заголовка функции могут эффективно вызывать заполнение:

```typescript
padding(1); // Okay: all
padding(1, 1); // Okay: topAndBottom, leftAndRight
padding(1, 1, 1, 1); // Okay: top, right, bottom, left

padding(1, 1, 1); // Error: Not a part of the available overloads
```

Конечно, важно, чтобы окончательное объявление \(реальное объявление, видимое внутри функции\) было совместимо со всеми перегрузками. Это потому, что это истинная природа вызова функции, которую тело функции должно учитывать.

{% hint style="info" %}
Перегрузка функций в TypeScript не требует дополнительных затрат времени выполнения. Это только позволяет документировать, как вы хотите вызывать функцию, а компилятор проверяет остальную часть кода.
{% endhint %}

### Объявление функции

> Быстрый старт: аннотации типов - это способ описать существующие типы реализации

В отсутствие реализации функции существует два способа объявить тип функции:

```typescript
type LongHand = {
  (a: number): number;
};

type ShortHand = (a: number) => number;
```

Два примера в приведенном выше коде абсолютно одинаковы. Однако, когда вы хотите использовать перегрузку функций, вы можете использовать только первый способ:

```typescript
type LongHandAllowsOverloadDeclarations = {
  (a: number): number;
  (a: string): string;
};
```

