# Пространства имен

При использовании пространств имен в JavaScript это имеет общий и удобный синтаксис:

```typescript
(function(something) {
  something.foo = 123;
})(something || (something = {}));
```

В основном `something || (something = {})` позволяет анонимной функции `function(something) {}` добавить что в существующий объект аргументы или создать новый объект, а затем добавить к этому объекту. Это означает, что у вас может быть два таких блока, разделенных по некоторой границе выполнения:

```typescript
(function(something) {
  something.foo = 123;
})(something || (something = {}));

console.log(something);
// { foo: 123 }

(function(something) {
  something.bar = 456;
})(something || (something = {}));

console.log(something); // { foo: 123, bar: 456 }
```

Это часто встречается в JavaScript, когда гарантируется, что созданные переменные не попадут в глобальные переменные. Вам не нужно беспокоиться об этом при использовании файловых модулей, но этот подход все еще подходит для логической группировки функций. Поэтому TypeScript предоставляет ключевое слово `namespace` для описания этой группировки следующим образом:

```typescript
namespace Utility {
  export function log(msg) {
    console.log(msg);
  }
  export function error(msg) {
    console.log(msg);
  }
}

// использование
Utility.log('Call me');
Utility.error('maybe');
```

После того, как ключевое слово `namespace` скомпилировано из TypeScript, оно совпадает с кодом JavaScript, который мы видим ниже:

```typescript
(function (Utility) {
  // Добавить атрибут в Utility
})(Utility || Utility = {});
```

Стоит отметить, что пространства имен поддерживают вложение. Следовательно, вы можете сделать что-то похожее на вложение пространства имен `Messaging` в пространство имен `Utility`.

Для большинства проектов мы рекомендуем использовать внешний модуль с использованием `namespace` для быстрой демонстрации и переноса старого кода JavaScript.

