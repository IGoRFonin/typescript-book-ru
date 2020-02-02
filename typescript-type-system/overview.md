# Обзор

## TypeScript Система типов

Обсуждая, [почему используется TypeScript](https://igorfonin.gitbook.io/typescript-book-ru/), мы рассмотрели основные возможности системы типов TypeScript. Вот некоторые ключевые моменты, которые обсуждались:

* Дизайн системы типов TypeScript не является обязательным, поэтому ваш JavaScript - это TypeScript;
* TypeScript не препятствует запуску JavaScript, даже если есть ошибки типов, что позволит постепенно переходить с JavaScript на TypeScript.

Теперь давайте начнем изучать синтаксис системы типов TypeScript. В этой главе вы сможете добавить аннотации типов в свой код и увидеть его преимущества. Это проложит путь для нашего дальнейшего понимания системы типов.

## Основные заметки

Как упоминалось ранее, аннотации типов используют синтаксис `:TypeAnnotation`. Все, что доступно в пространстве объявления типа, может использоваться как аннотация типа.

В следующем примере используются аннотации типов для переменных, параметров функции и возвращаемых значений функции.

```typescript
const num: number = 123;
function identity(num: number): number {
  return num;
}
```

## Примитивные типы

Примитивные типы JavaScript также адаптированы к системе типов TypeScript, поэтому `string`, `number`, `boolean` также могут использоваться в качестве аннотаций типов:

```typescript
let num: number;
let str: string;
let bool: boolean;

num = 123;
num = 123.456;
num = '123'; // Error

str = '123';
str = 123; // Error

bool = true;
bool = false;
bool = 'false'; // Error
```

## Массивы

TypeScript предоставляет специальный синтаксис типов для массивов, поэтому вы можете легко описывать массивы. Он использует суффикс `[]`, и вы можете добавлять любые допустимые аннотации типов по мере необходимости \(например, `:boolean[]`\). Он позволяет безопасно использовать любые связанные с массивами операции, а также предотвращает некоторые варианты поведения, аналогичные назначению неверных типов. Это выглядит так:

```typescript
let boolArray: boolean[];

boolArray = [true, false];
console.log(boolArray[0]); // true
console.log(boolArray.length); // 2

boolArray[1] = true;
boolArray = [false, false];

boolArray[0] = 'false'; // Error
boolArray = 'false'; // Error
boolArray = [true, 'false']; // Error
```

## Интерфейсы

Интерфейс является основным инструментов в TypeScript, он может объединять множество типов в одно объявление типа:

```typescript
interface Name {
  first: string;
  second: string;
}

let name: Name;
name = {
  first: 'John',
  second: 'Doe'
};

name = {
  // Error: 'Second is missing'
  first: 'John'
};

name = {
  // Error: 'Second is the wrong type'
  first: 'John',
  second: 1337
};
```

Здесь мы объединяем аннотацию типа: `first: string` + `second: string` в аннотацию нового типа `Name`, которое может принудительно проверять тип для каждого члена. Интерфейсы обладают большой силой в TypeScript, и далее в этой главе мы будем использовать целые главы, чтобы объяснить, как их лучше использовать.

## Встроенные аннотации типа

Вместо создания интерфейса вы можете аннотировать что-либо с помощью встроенного синтаксиса аннотации: `:{ /Structure/ }` 

```typescript
let name: {
  first: string;
  second: string;
};

name = {
  first: 'John',
  second: 'Doe'
};

name = {
  // Error: 'Second is missing'
  first: 'John'
};

name = {
  // Error: 'Second is the wrong type'
  first: 'John',
  second: 1337
};
```

Встроенные типы могут быстро предоставить вам аннотацию типа. Это может помочь вам избежать проблем с именами \(вы можете использовать плохое имя\). Однако, если вы обнаружите, что вам нужно использовать одну и ту же встроенную аннотацию несколько раз, рекомендуется рассмотреть возможность ее рефакторинга в интерфейс \(или добавьте `type alias`, который будет упомянут в следующем разделе\).

## Специальные типы

В дополнение к упомянутым примитивным типам в TypeScript есть несколько специальных типов. Это `any`, `null`, `undefined` и `void`.

### any

`any` тип занимает особое место в системе типов TypeScript. Он предоставляет вам «заднюю дверь» к системе типов, а TypeScript отключит проверку типов. `any` совместим со всеми типами \(включая себя\) в системе типов. Следовательно, ему могут быть назначены все типы, и он может быть назначен любому другому типу. Вот пример доказательства:

```typescript
let power: any;

// Можно назначить любой тип
power = '123';
power = 123;

// Он также совместимо с любым типом
let num: number;
power = num;
num = power;
```

Когда вы переходите с JavaScript на TypeScript, вы часто будете использовать `any`. Но вы должны уменьшить свою зависимость от него, потому что вам нужно обеспечить безопасность типов. При использовании `any`, вы в основном говорите редактору TypeScript не выполнять никакой проверки типов.

### null и undefined

В системе типов `null` и `undefined` литералы в JavaScript могут быть назначены любому типу переменной, как и другим переменным, помеченным `any` типом, как показано в следующем примере:

```typescript
// strictNullChecks: false

let num: number;
let str: string;

// Эти литералы могут быть назначены чему угодно
num = null;
str = undefined;
```

### void

Используйте `:void`, чтобы указать, что функция не имеет возвращаемого значения

```typescript
function log(message: string): void {
  console.log(message);
}
```

## Дженерики

В информатике многие алгоритмы и структуры данных не зависят от реального типа объекта. Тем не менее, вы все еще хотите применить ограничения к каждой переменной. Например: функция принимает список и возвращает список обратно. Ограничения относятся к параметрам, передаваемым в функцию, и возвращаемому значению функции:

```typescript
function reverse<T>(items: T[]): T[] {
  const toreturn = [];
  for (let i = items.length - 1; i >= 0; i--) {
    toreturn.push(items[i]);
  }
  return toreturn;
}

const sample = [1, 2, 3];
let reversed = reverse(sample);

console.log(reversed); // 3, 2, 1

// Safety
reversed[0] = '1'; // Error
reversed = ['1', '2']; // Error

reversed[0] = 1; // ok
reversed = [1, 2]; // ok
```

В предыдущем примере функция `reverse` принимает массив типа `T` \(обратите внимание на параметр типа `reverse<T>`\) \(`items: T[]`\) и возвращает массив типа `T` \(`: T[]`\). У функция `reverse` возвращаемое значение имеет тот же тип, что и принимаемых параметрах. Когда вы передаете `const sample = [1, 2, 3]`, TypeScript может сделать вывод, что реверс имеет тотже тип`number[]` , обеспечивая вам безопасность типов. Точно так же, когда вы передаете массив типа `string[]`, TypeScript может сделать вывод, что `reverse` имеет тип `string[]`, как показано в следующем примере:

```typescript
const strArr = ['1', '2'];
let reversedStrs = reverse(strArr);

reversedStrs = [1, 2]; // Error
```

Фактически, у массивов JavaScript уже есть метод `reverse`, а TypeScript использует обобщенные значения для определения его структуры:

```typescript
interface Array<T> {
  reverse(): T[];
}
```

Это означает, что когда вы вызываете метод `.reverse` для массива, вы получаете безопасность типов:

```typescript
let numArr = [1, 2];
let reversedNums = numArr.reverse();

reversedNums = ['1', '2']; // Error
```

Мы обсудим больше об интерфейсе `Array<T>` позже, когда представим `lib.d.ts` в разделе [Декларации окружения](https://igorfonin.gitbook.io/typescript-book-ru/typescript-type-system/ambient).

## Объединенный тип

Как правило, в JavaScript вы хотите, чтобы свойство было одним из нескольких типов, например строка или число. Для этого пригодится объединенный тип \(обозначенный `|` в аннотации типа, например, `string | number`\). Обычный вариант использования - это функция, которая может принимать одну строку или массив строк, например:

```typescript
function formatCommandline(command: string[] | string) {
  let line = '';
  if (typeof command === 'string') {
    line = command.trim();
  } else {
    line = command.join(' ').trim();
  }

  // Do stuff with line: string
}
```

## Тип пересечения

В JavaScript `extend` является очень распространенным шаблоном. В этом режиме вы можете создать новый объект из двух других, созданный объект будет содержать все атрибуты обоих объектов. Перекрестные типы позволяют безопасно использовать этот режим:

```typescript
function extend<T, U>(first: T, second: U): T & U {
  const result = <T & U>{};
  for (let id in first) {
    (<T>result)[id] = first[id];
  }
  for (let id in second) {
    if (!result.hasOwnProperty(id)) {
      (<U>result)[id] = second[id];
    }
  }

  return result;
}

const x = extend({ a: 'hello' }, { b: 42 });

// x теперь имеет атрибуты a и b
const a = x.a;
const b = x.b;
```

## Тип кортеж

JavaScript не поддерживает кортежи из коробки. Разработчики обычно могут использовать массивы только для представления кортежей, но система типов TypeScript поддерживает их. Используйте `:[typeofmember1, typeofmember2]`, чтобы добавить аннотацию типов для кортежа. Кортежи могут содержать любое количество членов. Следующий пример иллюстрирует кортежи:

```typescript
let nameNumber: [string, number];

// Ok
nameNumber = ['Jenny', 221345];

// Error
nameNumber = ['Jenny', '221345'];
```

Используйте его с деструктуризацией в TypeScript:

```typescript
let nameNumber: [string, number];
nameNumber = ['Jenny', 322134];

const [name, num] = nameNumber;
```

## Тип всевдоним

TypeScript предоставляет удобный синтаксис для использования аннотаций типов. Синтаксис `type SomeName = someValidTypeAnnotation` можно использовать для создания псевдонимов:

```typescript
type StrOrNum = string | number;

// Использование
let sample: StrOrNum;
sample = 123;
sample = '123';

// Проверка типа
sample = true; // Error
```

В отличие от интерфейсов, вы можете предоставить псевдонимы типов для аннотаций произвольных типов \(полезно для объединяемых типов и перекрестных типов\). Вот несколько примеров, чтобы познакомить вас с синтаксисом.

```typescript
type Text = string | { text: string };
type Coordinates = [number, number];
type Callback = (data: string) => void;
```

{% hint style="info" %}
* Если вам нужно использовать иерархию аннотаций типов, используйте интерфейс. Он может использовать `implements` и `extends`
* Чтобы использовать псевдоним типа для простого типа объекта \(например, `Coordinates` в примере выше\), просто дайте ему семантическое имя. Кроме того, если вы хотите предоставить семантические имена для объединяемых и перекрестных типов, псевдоним типа будет хорошим выбором.
{% endhint %}

## В заключении

Теперь, когда вы смогли добавить аннотации типов в большую часть кода JavaScript, давайте углубимся в систему типов TypeScript.

