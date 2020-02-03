# Интерфейсы

Интерфейсы имеют нулевое влияние JS во время выполнения. В интерфейсах TypeScript есть много возможностей для объявления структуры переменных.

Следующие два являются эквивалентными объявлениями, первое с использованием встроенных аннотаций, а второе с использованием интерфейсов:

```typescript
// Sample A
declare const myPoint: { x: number; y: number };

// Sample B
interface Point {
  x: number;
  y: number;
}
declare const myPoint: Point;
```

Преимущество примера B состоит в том, что если кто-то создает библиотеку на основе `myPoint` для добавления новых аргументов, он может легко добавить в существующее объявление `myPoint`:

```typescript
// Lib a.d.ts
interface Point {
  x: number,
  y: number
}
declare const myPoint: Point

// Lib b.d.ts
interface Point {
  z: number
}

// Your code
let myPoint.z // Allowed!
```

Поскольку интерфейс TypeScript открыт для дополнения, это важный принцип TypeScript, который позволяет использовать интерфейс для имитации расширяемости JavaScript.

## Классы могут реализовывать интерфейсы

Если вы хотите использовать классы, которые должны следовать структуре объекта, которую кто-то объявил для вас в `interface` , вы можете использовать ключевое слово `implements` для обеспечения совместимости:

```typescript
interface Point {
  x: number;
  y: number;
}

class MyPoint implements Point {
  x: number;
  y: number; // Same as Point
}
```

По сути, при наличии `implements` любые изменения внешнего интерфейса `Point` вызовут ошибки компиляции в базе кода, поэтому его можно легко синхронизировать:

```typescript
interface Point {
  x: number;
  y: number;
  z: number; // New member
}

class MyPoint implements Point {
  // ERROR : missing member `z`
  x: number;
  y: number;
}
```

Обратите внимание, что `implements` ограничивает структуру экземпляров классов, а именно:

```typescript
let foo: Point = new MyPoint();
```

Такие вещи, как `foo: Point = MyPoint` - это не одно и то же.

## Обратите внимание

### Не каждый интерфейс прост в реализации

Интерфейс предназначен для объявления любой структуры, которая может существовать в JavaScript.

Рассмотрим следующий пример, где что-то можно вызвать с помощью `new`:

```typescript
interface Crazy {
  new (): {
    hello: number;
  };
}
```

Вы могли бы иметь такой код:

```typescript
class CrazyClass implements Crazy {
  constructor() {
    return { hello: 123 };
  }
}

// Because
const crazy = new CrazyClass(); // crazy would be { hello:123 }
```

Вы можете объявить весь JavaScript, используя интерфейсы, и вы даже можете безопасно использовать их из TypeScript. Это не означает, что вы можете реализовать их, используя классы TypeScript.

