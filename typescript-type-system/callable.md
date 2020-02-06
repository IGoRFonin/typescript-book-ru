# Подлежащий выкупу

Вы можете использовать псевдоним типа или интерфейс для представления аннотации вызываемого типа:

```typescript
interface ReturnString {
  (): string;
}
```

Он может представлять функцию, которая возвращает `string`:

```typescript
declare const foo: ReturnString;

const bar = foo(); // bar Выводится как строка.
```

## Практический пример

Конечно, для подобной аннотации вызываемого типа вы также можете передавать любые параметры, необязательные параметры и параметры rest в соответствии с реальной ситуацией. Это немного более сложный пример:

```typescript
interface Complex {
  (foo: string, bar?: number, ...others: boolean[]): number;
}
```

Интерфейс может предоставлять несколько сигнатур вызовов для перегрузки специальных функций:

```typescript
interface Overloaded {
  (foo: string): string;
  (foo: number): number;
}

// Пример реализации интерфейса：
function stringOrNumber(foo: number): number;
function stringOrNumber(foo: string): string;
function stringOrNumber(foo: any): any {
  if (typeof foo === 'number') {
    return foo * foo;
  } else if (typeof foo === 'string') {
    return `hello ${foo}`;
  }
}

const overloaded: Overloaded = stringOrNumber;

// 使用
const str = overloaded(''); // str выводит 'string'
const num = overloaded(123); // num выводит 'number'
```

Это также может быть использовано во встроенных аннотациях:

```typescript
let overloaded: {
  (foo: string): string;
  (foo: number): number;
};
```

## Стрелочные функции

Чтобы упростить задание сигнатур вызываемого типа, TypeScript также позволяет использовать простые аннотации типов функций стрелок. Например, в функции, которая принимает `number` в качестве параметра и `string` в качестве возвращаемого значения, вы можете написать:

```typescript
const simple: (foo: number) => string = foo => foo.toString();
```

{% hint style="info" %}
Можно использоваться только как простую функцию стрелки, вы не можете использовать перегрузки. Если вы хотите использовать перегрузки, вы должны использовать полный синтаксис `{(someArgs): someReturn}`
{% endhint %}

## Newable

Newable - это просто особый тип аннотации вызываемого типа с префиксом `new`. Это просто означает, что вам нужно вызвать с `new`, например,

```typescript
interface CallMeWithNewToGetString {
  new (): string;
}

// 使用
declare const Foo: CallMeWithNewToGetString;
const bar = new Foo(); // bar будет string
```

