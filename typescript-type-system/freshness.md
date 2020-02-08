# Freshness

Чтобы упростить проверку типа литерала объекта, TypeScript предоставляет концепцию 「[Freshness](https://github.com/Microsoft/TypeScript/pull/3823)」 \(также известную как более строгая проверка литерала объекта\), чтобы гарантировать, что литералы объекта являются структурно совместимыми по типу.

Структурная типизация чрезвычайно удобна. Рассмотрим следующий пример кода, который позволяет легко переходить с JavaScript на TypeScript, а также обеспечивает безопасность типов:

```typescript
function logName(something: { name: string }) {
    console.log(something.name);
}

const person = { name: 'matt', job: 'being awesome' };
const animal = { name: 'cow', diet: 'vegan, but has milk of own species' };
const random = { note: `I don't have a name property` };

logName(person); // okay
logName(animal); // okay
logName(random); // Error: property `name` is missing
```

Однако структурная типизация имеет недостаток: она может ввести вас в заблуждение, полагая, что что-то получает больше данных, чем на самом деле. В следующем примере TypeScript выдает предупреждение об ошибке:

```typescript
function logName(something: { name: string }) {
    console.log(something.name);
}

logName({ name: 'matt' }); // okay
logName({ name: 'matt', job: 'being awesome' }); // Error: object literals must only specify known properties. `job` is excessive here
```

{% hint style="warning" %}
Обратите внимание, что это сообщение об ошибке появляется только для литералов объекта.
{% endhint %}

Обратите внимание, что эта ошибка возникает только для литералов объекта. Без этой ошибки можно посмотреть на вызов `logName({ name: 'matt', job: 'being awesome' })` и подумать, что `logName` сделает что-то полезное с `job`, тогда как в действительности оно полностью его игнорирует.

Другой вариант использования - использовать его с интерфейсами с необязательными элементами. Если такой проверки литерала объекта нет, при вводе неправильного слова предупреждение об ошибке не выдается:

```typescript
function logIfHasName(something: { name?: string }) {
  if (something.name) {
    console.log(something.name);
  }
}

const person = { name: 'matt', job: 'being awesome' };
const animal = { name: 'cow', diet: 'vegan, but has milk of own species' };

logIfHasName(person); // okay
logIfHasName(animal); // okay

logIfHasName({ neme: 'I just misspelled name to neme' }); // Error: object literals must only specify known properties. `neme` is excessive here
```

Причина состоит в том, чтобы выполнять проверку типов только для литералов объекта, потому что в этом случае те атрибуты, которые фактически не используются, могут быть написаны с ошибками или неправильно использованы.

## Разрешить дополнительные атрибуты

Тип может включать подпись индекса, чтобы было ясно, что можно использовать дополнительные атрибуты:

```typescript
let x: { foo: number, [x: string]: any };

x = { foo: 1, baz: 2 }; // Ok, `baz` matched by index signature
```

## Пример: React State

Facebook ReactJS предоставляет хороший пример использования Freshness объектов. Обычно в компонентах вы используете только несколько атрибутов вместо того, чтобы передавать все для вызова `setState`:

```typescript
// Гипотеза
interface State {
  foo: string;
  bar: string;
}

// Вы можете захотеть сделать:
this.setState({ foo: 'Hello' }); // Error: не хватает атрибута 'bar'

// Поскольку state содержит «foo» и «bar», TypeScript заставляет вас добавить их:
this.setState({ foo: 'Hello', bar: this.state.bar });
```

Если вы хотите использовать Freshness, вам может потребоваться пометить всех участников как необязательных, которые все равно будут ловить опечатки:

```typescript
// Гипотеза
interface State {
  foo?: string;
  bar?: string;
}

// Вы могли бы хотеть сделать
this.setState({ foo: 'Hello' }); // Yay works fine!

// Благодаря Freshness вы можете предотвратить опечатки
this.setState({ foos: 'Hello' }}; // Error: Объект может указывать только известные свойства

// Там еще будет проверка типа
this.setState({ foo: 123 }}; // Error: Нельзя добавить number для типа string
```



