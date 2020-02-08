---
description: 'Литералы являются точной переменной, предоставляемой самим JavaScript.'
---

# Литеральный тип

## Строковый литерал

Вы можете использовать строковый литерал в качестве типа:

```typescript
let foo: 'Hello';
```

Здесь мы создали переменную с именем `foo`, которая принимает только одну переменную, буквальное значение которой равно `Hello`:

```typescript
let foo: 'Hello';
foo = 'bar'; // Error: 'bar' не может быть назначен типу 'Hello'
```

Они сами по себе не очень практичны, но могут быть объединены для создания мощной \(практической\) абстракции в объединенном типе:

```typescript
type CardinalDirection = 'North' | 'East' | 'South' | 'West';

function move(distance: number, direction: CardinalDirection) {
  // ...
}

move(1, 'North'); // ok
move(1, 'Nurth'); // Error
```

## Другие литеральные типы

TypeScript также предоставляет `boolean` и `number` литеральные типы:

```typescript
type OneToFive = 1 | 2 | 3 | 4 | 5;
type Bools = true | false;
```

## Вывод

Довольно часто вы получаете сообщение об ошибке типа `Type string is not assignable to type 'foo'`. Следующий пример демонстрирует это:

```typescript
function iTakeFoo(foo: 'foo') {}
const test = {
  someProp: 'foo'
};

iTakeFoo(test.someProp); // Error: Argument of type string is not assignable to parameter of type 'foo'
```

Это потому, что `test` выводится как `{ someProp: string }`, мы можем использовать утверждение простого типа, чтобы сообщить TypeScript литералы, которые вы хотите вывести:

```typescript
function iTakeFoo(foo: 'foo') {}

const test = {
  someProp: 'foo' as 'foo'
};

iTakeFoo(test.someProp); // ok
```

Или используйте аннотации типов, чтобы помочь TypeScript вывести правильный тип:

```typescript
function iTakeFoo(foo: 'foo') {}

type Test = {
  someProp: 'foo';
};

const test: Test = {
  // Предположим, что 'someProp' всегда 'foo'
  someProp: 'foo'
};

iTakeFoo(test.someProp); // ok
```

## Случаи использования

Типы перечислений TypeScript основаны на числах. Вы можете использовать типы объединения со строковыми литералами для имитации типа перечисления на основе строк, так же как и `CardinalDirection`, предложенный выше. Вы даже можете использовать следующие функции для генерации структуры `key: value`:

```typescript
// Функция для создания списка строк, сопоставленных с `K: V`
function strEnum<T extends string>(o: Array<T>): { [K in T]: K } {
  return o.reduce((res, key) => {
    res[key] = key;
    return res;
  }, Object.create(null));
}
```

Затем вы можете использовать `keyof`, `typeof` для генерации типа объединения строки. Вот полный пример:

```typescript
// Функция для создания списка строк, сопоставленных с `K: V`
function strEnum<T extends string>(o: Array<T>): { [K in T]: K } {
  return o.reduce((res, key) => {
    res[key] = key;
    return res;
  }, Object.create(null));
}

// Создание K: V
const Direction = strEnum(['North', 'South', 'East', 'West']);

// Создание типа
type Direction = keyof typeof Direction;

// Прост в использовании
let sample: Direction;

sample = Direction.North; // Okay
sample = 'North'; // Okay
sample = 'AnythingElse'; // ERROR!
```

## Различать union

Мы объясним это позже в этой книге.

