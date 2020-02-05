# lib.d.ts

Когда вы устанавливаете TypeScript, вместе с ним устанавливаются файлы объявлений, такие как `lib.d.ts`. Этот файл содержит различные объявления общей среды, которые существуют во время выполнения JavaScript и DOM.

* Он автоматически включается в контекст компиляции проекта TypeScript;
* Это позволяет быстро начать писать типизированный JavaScript.

Вы можете исключить этот файл из контекста, указав флаг командной строки компилятора `--noLib` \(или указав параметр `noLib: true` в `tsconfig.json`\).

## Пример использования

Посмотрите на следующий пример:

```typescript
const foo = 123;
const bar = foo.toString();
```

Этот тип кода работает нормально, потому что `lib.d.ts` определяет метод `toString` для всех объектов JavaScript.

Если вы используете тот же код с опцией `noLib`, это приведет к ошибке проверки типа:

```typescript
const foo = 123;
const bar = foo.toString(); // ERROR: Property 'toString' does not exist on type 'number'
```

Теперь, когда вы понимаете важность `lib.d.ts` для ее содержания, давайте объясним следующее.

## Заглянем внутрь `lib.d.ts`

Содержимое `lib.d.ts` - это в основном объявления переменных \(такие как: `window`, `document`, `math`\) и некоторые аналогичные объявления интерфейса \(такие как: `Window`, `Document`, `Math`\).

Самый простой способ найти типы кода \(такие как `Math.floor`\) - это использовать `F12`\(для mac `fn` + `F12`\) из IDE \(переход к определению\).

Давайте посмотрим на объявление примерной переменной, поскольку `window` определяется как:

```typescript
declare var window: Window;
```

Это просто `declate var`, за которым следуют имя переменной \(`window`\) и интерфейс для аннотаций типов \(интерфейс `Window`\). Эти переменные обычно указывают на некоторый глобальный интерфейс. Например, ниже приведена небольшая часть интерфейса `Window`:

```typescript
interface Window
  extends EventTarget,
    WindowTimers,
    WindowSessionStorage,
    WindowLocalStorage,
    WindowConsole,
    GlobalEventHandlers,
    IDBEnvironment,
    WindowBase64 {
  animationStartTime: number;
  applicationCache: ApplicationCache;
  clientInformation: Navigator;
  closed: boolean;
  crypto: Crypto;
  // so on and so forth...
}
```

Вы можете видеть много информации о типах в этих интерфейсах. Когда вы не используете TypeScript, вам нужно хранить их в своем мозгу. Теперь вы можете использовать такие вещи, как `intellisense`, которые позволят запоминать что-то более важное.

Выгодно использовать эти глобальные переменные. Позволяет добавлять дополнительные свойства без изменения `lib.d.ts`. Далее мы покажем эти понятия.

## Изменение исходных типов

В TypeScript интерфейсы открыты, что означает, что когда вы хотите использовать несуществующие члены, вам просто нужно добавить их в объявление интерфейса в `lib.d.ts`, и TypeScript автоматически получит его. Обратите внимание, что вам нужно внести эти изменения в [глобальный модуль](https://igorfonin.gitbook.io/typescript-book-ru/project/modules), чтобы связать эти интерфейсы с `lib.d.ts`. Мы рекомендуем вам создать специальный файл с именем `global.d.ts`.

Вот несколько примеров, как мы можем добавить новые свойства в `Window`, `Math`, `Date`:

### Window

Просто добавлено свойство для интерфейса `Window`

```typescript
interface Window {
  helloWorld(): void;
}
```

Это позволит вам использовать его безопасным способом:

```typescript
// Добавьте в код
window.helloWorld = () => console.log('hello world');

// Вызовите
window.helloWorld();

// Неправильное использование может привести к ошибкам
window.helloWorld('gracius'); // Error: Supplied parameters do not match the signature of the call target
```

### Math

Глобальная переменная `Math` определена в `lib.d.ts` как:

```typescript
/** An intrinsic object that provides basic mathematics functionality and constants. */
declare var Math: Math;
```

То есть переменная `Math` является экземпляром `Math`, а интерфейс `Math` определяется как:

```typescript
interface Math {
  E: number;
  LN10: number;
  // others ...
}
```

Когда вы хотите добавить атрибуты, которые вам нужны, в глобальную переменную `Math`, вам нужно только добавить его в глобальный интерфейс `Math`. Например, в [`seedrandom Project`](https://www.npmjs.com/package/seedrandom), он добавляет функцию `seedrandom` в глобальный объект Math. Это можно сделать так:

```typescript
interface Math {
  seedrandom(seed?: string): void;
}
```

А затем использовать это так:

```typescript
Math.seedrandom();

Math.seedrandom('Any string you want');
```

### Date

Если вы ищете объявление определения `Date` в `lib.d.ts`, вы найдете:

```typescript
declare var Date: DateConstructor;
```

Интерфейс `DateConstructor` такой же, как интерфейсы `Math` и `Window`, которые вы видели выше, поскольку он охватывает элементы глобальной переменной `Date`, которые можно использовать \(например, `Date.now()`\). Кроме того, он содержит подпись для конструктора \(например, `new Date()`\), которая позволяет создавать экземпляр `Date`. Часть кода для интерфейса `DateConstructor` выглядит следующим образом:

```typescript
interface DateConstructor {
  new (): Date;
  // ... other construct signatures

  now(): number;

  // ... other member functions
}
```

В [datejs](https://github.com/abritinthebay/datejs) добавляют свойства как к глобальной переменной `Date`, так и к экземплярам `Date`, поэтому определение TypeScript этой библиотеки выглядит следующим образом \([сообщество уже определило его](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/datejs/index.d.ts)\)

```typescript
// DateJS добавил статические методы
interface DateConstructor {
  /** Gets a date that is set to the current date. The time is set to the start of the day (00:00 or 12:00 AM) */
  today(): Date;
  // ... so on and so forth
}

// DateJS дал доступ к публичным методам
interface Date {
  /** Adds the specified number of milliseconds to this instance. */
  addMilliseconds(milliseconds: number): Date;
  // ... so on and so forth
}
```

Это позволяет вам делать это с проверкой типов:

```typescript
const today = Date.today();
const todayAfter1second = today.addMilliseconds(1000);
```

