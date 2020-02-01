# Динамический импорт выражений

Динамический импорт - это новая функция ECMAScript, которая позволяет асинхронно загружать модуль в любом месте вашей программы. Комитет TC39 JavaScript имеет такое предложение на `stage 4`, которое называется [import\(\) proposal for JavaScript](https://github.com/tc39/proposal-dynamic-import).

Кроме того, в `webpack` есть функция `Code splitting`, которая позволяет разбить код на множество чанков\(разных файлов\), которые могут быть загружены асинхронно в будущем. Поэтому вы можете обеспечить минимальный размер при запуске программы и загружать другие модули асинхронно в будущем.

Естественно думать \(если мы работаем с `webpack` в `dev` режиме\) [TypeScript 2.4 dynamic import expressions](https://github.com/Microsoft/TypeScript/wiki/What%27s-new-in-TypeScript#dynamic-import-expressions) автоматически разбивают ваш окончательно сгенерированный код JavaScript на множество частей\(чанков\). Но это не кажется простым, потому что это зависит от файла конфигурации `tsconfig.json`, который мы используем.

Есть два способа, которыми `webpack` может реализовать разбиение кода: использование `import()` \(предпочтительно, предложение ECMAScript\) и `require.ensure()` \(реализация `webpack`\). Поэтому мы ожидаем, что выходные данные TypeScript сохранят оператор `import()`, а не преобразуют его в любой другой код.

Давайте рассмотрим пример. В этом примере мы продемонстрировали, как настроить `webpack` и TypeScript 2.4+.

В следующем примере я хочу лениво загрузить библиотеку `moment`, а также использовать функцию разделения кода, которая означает, что `moment` будет разделен на отдельный файл JavaScript, а при его использовании он будет загружен асинхронно.

```typescript
import(/* webpackChunkName: "momentjs" */ 'moment')
  .then(moment => {
    // Ленивые загруженные модули имеют все типы и могут работать как обычные
    // Проверка типов будет работать, и ссылки на код также будут работать \o/
    const time = moment().format();
    console.log('TypeScript >= 2.4.0 Dynamic Import Expression:');
    console.log(time);
  })
  .catch(err => {
    console.log('Failed to load moment', err);
  });
```

Это настройки для `tsconfig.json`:

```typescript
{
  "compilerOptions": {
    "target": "es5",
    "module": "esnext",
    "lib": [
      "dom",
      "es5",
      "scripthost",
      "es2015.promise"
    ],
    "jsx": "react",
    "declaration": false,
    "sourceMap": true,
    "outDir": "./dist/js",
    "strict": true,
    "moduleResolution": "node",
    "typeRoots": [
      "./node_modules/@types"
    ],
    "types": [
      "node",
      "react",
      "react-dom"
    ]
  }
}
```

{% hint style="danger" %}
* Используйте параметр `module: esnext`: TypeScript генерирует операторы `import()` для разделения кода `webpack`.
* Для получения дополнительной информации, я рекомендую прочитать эту статью: [Dynamic Import Expressions and webpack 2 Code Splitting integration with TypeScript 2.4.](https://blog.josequinto.com/2017/06/29/dynamic-import-expressions-and-webpack-code-splitting-integration-with-typescript-2-4/)
{% endhint %}

