# Дженерики

Основная цель разработки дженериков состоит в том, чтобы обеспечить значимые ограничения между членами, которые могут быть:

* Экземпляр класса
* Методы класса
* Функциональный параметр
* Возвращаемое значение функции

## Мотивация и примеры

Вот простая реализация структуры данных «первым пришел - первым вышел», очереди в `TypeScript` и `JavaScript`.

```typescript
class Queue {
  private data = [];
  push = item => this.data.push(item);
  pop = () => this.data.shift();
}
```

В приведенном выше коде есть проблема: он позволяет добавлять в очередь данные любого типа, конечно, когда данные выталкиваются из очереди, они также могут быть любого типа. В приведенном ниже примере кажется, что можно добавить данные типа `string` в очередь, но на практике это использование предполагает, что в очередь будет добавлен только тип `number`.

```typescript
class Queue {
  private data = [];
  push = item => this.data.push(item);
  pop = () => this.data.shift();
}

const queue = new Queue();

queue.push(0);
queue.push('1'); // Oops，ошибка

// Пользователь, в недоразумении
console.log(queue.pop().toPrecision(1));
console.log(queue.pop().toPrecision(1)); // RUNTIME ERROR
```

Одно из решений \(на самом деле это единственное решение, которое не поддерживает универсальные типы\) - это создание специальных классов для этих ограничений, таких как быстрое создание очереди числовых типов:

```typescript
class QueueNumber {
  private data = [];
  push = (item: number) => this.data.push(item);
  pop = (): number => this.data.shift();
}

const queue = new QueueNumber();

queue.push(0);
queue.push('1'); // Error: нельзя добавить `string` только `number` доступно

// Если ошибка исправлена, других проблем не возникнет
```

Конечно, скорость также означает боль. Например, если вы хотите создать очередь из строк, вам придется снова немного изменить код. Один из способов, которым мы действительно хотим, заключается в том, что независимо от того, какой тип помещается в очередь, выдвигаемый тип совпадает с выдвигаемым типом. Это легко, когда вы используете дженерики:

```typescript
// Создать универсальный класс
class Queue<T> {
  private data: T[] = [];
  push = (item: T) => this.data.push(item);
  pop = (): T | undefined => this.data.shift();
}

// Прост в использовании
const queue = new Queue<number>();
queue.push(0);
queue.push('1'); // Error：нельзя добавить `string` только `number` доступно
```

Другой пример, который мы видели: `reverse` функция, которая теперь предоставляет ограничения на параметры функции и возвращаемые значения:

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

reversed[0] = '1'; // Error
reversed = ['1', '2']; // Error

reversed[0] = 1; // ok
reversed = [1, 2]; // ok
```

В этом разделе вы узнали примеры использования дженериков в классах и функциях. Стоит добавить, что вы можете добавить универсальные элементы к функциям-методам, которые вы создаете:

```typescript
class Utility {
  reverse<T>(items: T[]): T[] {
    const toreturn = [];
    for (let i = items.length; i >= 0; i--) {
      toreturn.push(items[i]);
    }
    return toreturn;
  }
}
```

{% hint style="info" %}
Вы можете вызывать универсальные параметры по желанию. Когда вы используете простые обобщенные параметры, обобщенные значения обычно представлены буквами `T`, `U`, `V`. Если у вас есть более одного универсального типа в вашем параметре, вы должны использовать более семантическое имя, такое как `TKey` и `TValue` \(обычно префикс универсального типа `T`, который также называется Шаблон в С++\).
{% endhint %}

## Неправильные дженерики

Я видел, как разработчики используют дженерики только для взлома. Когда вы используете его, вы должны спросить себя: какие ограничения вы хотите использовать для его предоставления. Если вы не можете ответить правильно, вы можете использовать дженерики, такие как:

```typescript
declare function foo<T>(arg: T): void;
```

Здесь нет необходимости использовать дженерики, поскольку они используются только для позиции одного параметра. Возможно, лучше использовать:

```typescript
declare function foo(arg: any): void;
```

## Дизайн шаблона: удобный и универсальный

Рассмотрим следующую функцию:

```typescript
declare function parse<T>(name: string): T;
```

В этом случае универсальный `T` используется только в одном месте, он не обеспечивает ограничение `T` между членами. Это эквивалентно утверждению типа, как это:

```typescript
declare function parse(name: string): any;

const something = parse('something') as TypeOfSomething;
```

Дженерики, которые используются только один раз, не более безопасны, чем утверждение типа. Оба они обеспечивают удобство использования API.

Другой очевидный пример - функция загрузки возвращаемого значения json, которая возвращает `Promise` любого из переданных вами типов:

```typescript
const getJSON = <T>(config: { url: string; headers?: { [key: string]: string } }): Promise<T> => {
  const fetchConfig = {
    method: 'GET',
    Accept: 'application/json',
    'Content-Type': 'application/json',
    ...(config.headers || {})
  };
  return fetch(config.url, fetchConfig).then<T>(response => response.json());
};
```

Обратите внимание, что вам все еще нужно явно сообщить тип, который вам нужен, но подпись `getJSON<T>` в `config => Promise<T>` может сократить некоторые ваши ключевые шаги \(вам не нужно сообщить возвращаемый тип `loadUsers`, потому что его можно вытолкнуть\):

```typescript
type LoadUserResponse = {
  user: {
    name: string;
    email: string;
  }[];
};

function loaderUser() {
  return getJSON<LoadUserResponse>({ url: 'https://example.com/users' });
}
```

Также возвращаемое значение с использованием `Promise<T>` как функции намного лучше, чем некоторые альтернативы, такие как `Promise<any>`.

### Использование с axios

При нормальных обстоятельствах мы будем помещать формат данных бэкэнда в интерфейс отдельно:

```typescript
// Запрос данных интерфейса
export interface ResponseData<T = any> {
  /**
   * Код состояния
   * @type { number }
   */
  code: number;

  /**
   * Данные
   * @type { T }
   */
  result: T;

  /**
   * Сообщение
   * @type { string }
   */
  message: string;
}
```

Когда мы разделяем API на один модуль:

```typescript
// Обработка axios в файле axios.ts, такие как добавление общей конфигурации, перехватчик и т. д.
import Ax from './axios';

import { ResponseData } from './interface.ts';

export function getUser<T>() {
  return Ax.get<ResponseData<T>>('/somepath')
    .then(res => res.data)
    .catch(err => console.error(err));
}
```

Затем мы записываем возвращаемый тип данных `User`, который позволяет TypeScript выводить нужный нам тип:

```typescript
interface User {
  name: string;
  age: number;
}

async function test() {
  // user Был выведен как
  // {
  //  code: number,
  //  result: { name: string, age: number },
  //  message: string
  // }
  const user = await getUser<User>();
}
```

