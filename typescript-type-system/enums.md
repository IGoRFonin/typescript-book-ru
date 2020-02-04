# Перечисления

Перечисление - это способ организации коллекции связанных переменных. Многие языки программирования \(такие как С / С\# / Java\) имеют тип данных `enum`. Вот как определить тип перечисления в TypeScript:

```typescript
enum CardSuit {
  Clubs,
  Diamonds,
  Hearts,
  Spades
}

// Простое использование перечислимых типов
let Card = CardSuit.Clubs;

// Проверка безопасности
Card = 'not a member of card suit'; // Error : string is not assignable to type `CardSuit`
```

Эти значения перечислений являются `number`, поэтому я буду называть их числовыми перечислениями.

## Числовые перечисления и числа

Перечисления TypeScript основаны на числах. Это означает, что числа могут быть назначены экземпляру перечисления, как и все, что совместимо с числом.

```typescript
enum Color {
  Red,
  Green,
  Blue
}

let col = Color.Red;
col = 0; // Тоже самое что и Color.Red
```

## Числовые перечисления и строки

Прежде чем мы углубимся в перечисления, давайте рассмотрим генерируемый им JavaScript, вот пример TypeScript:

```typescript
enum Tristate {
    False,
    True,
    Unknown
}
```

Скомпилированный JavaScript:

```typescript
var Tristate;
(function (Tristate) {
    Tristate[Tristate["False"] = 0] = "False";
    Tristate[Tristate["True"] = 1] = "True";
    Tristate[Tristate["Unknown"] = 2] = "Unknown";
})(Tristate || (Tristate = {}));
```

давайте сосредоточимся на строке `Tristate [Tristate['False'] = 0] = 'False' ;`. Внутри него `Tristate ['False'] = 0` должен быть самоочевидным, т. e. присваивает элементу `False` переменной `Tristate` значение `0`. Обратите внимание, что в JavaScript оператор присваивания возвращает назначенное значение \(в данном случае `0`\). Поэтому следующая вещь, выполняемая средой выполнения JavaScript, это `Tristate[0] = 'False'`. Это означает, что вы можете использовать переменную `Tristate` для преобразования строковой версии перечисления в число или числовую версию перечисления в строку. Это продемонстрировано ниже:

```typescript
enum Tristate {
  False,
  True,
  Unknown
}

console.log(Tristate[0]); // 'False'
console.log(Tristate['False']); // 0
console.log(Tristate[Tristate.False]); // 'False' because `Tristate.False == 0`
```

## Изменить число, связанное с числовым перечислением

По умолчанию первое значение перечисления равно `0`, а каждое последующее значение увеличивается на единицу:

```typescript
enum Color {
  Red, // 0
  Green, // 1
  Blue // 2
}
```

Однако вы можете изменить число, связанное с любым членом перечисления, с помощью специального назначения. Для следующего примера мы увеличиваем с 3:

```typescript
enum Color {
  DarkRed = 3, // 3
  DarkGreen, // 4
  DarkBlue // 5
}
```

{% hint style="info" %}
Я обычно инициализирую с `= 1`, потому что это позволяет вам делать безопасную и надежную проверку перечисляемых значений.
{% endhint %}

## Числовые перечисления как флаги

Одним из превосходных применений перечислений является возможность использовать перечисления в качестве флагов. Флаги позволяют вам проверить, верно ли определенное условие из набора условий. Рассмотрим следующий пример, где у нас есть набор свойств о животных:

```typescript
enum AnimalFlags {
  None        = 0,
  HasClaws    = 1 << 0,
  CanFly      = 1 << 1,
  EatsFish    = 1 << 2,
  Endangered  = 1 << 3
}
```

Здесь мы используем оператор побитового сдвига влево, чтобы сдвинуть двоичную позицию числа `1` влево, и получить числа `0001`, `0010`, `0100` и `1000` \(в десятичных терминах: 1, 2, 4, 8\). Когда вы используете этот флаг, ваши битовые операторы `|` \(или\), `&` \(и\), `~` \(нет\) не при работе с флагами:

```typescript
enum AnimalFlags {
  None        = 0,
  HasClaws    = 1 << 0,
  CanFly      = 1 << 1
}

interface Animal {
  flags: AnimalFlags;
  [key: string]: any;
}

function printAnimalAbilities(animal: Animal) {
  var animalFlags = animal.flags;
  if (animalFlags & AnimalFlags.HasClaws) {
    console.log('animal has claws');
  }
  if (animalFlags & AnimalFlags.CanFly) {
    console.log('animal can fly');
  }
  if (animalFlags == AnimalFlags.None) {
    console.log('nothing');
  }
}

var animal = { flags: AnimalFlags.None };
printAnimalAbilities(animal); // nothing
animal.flags |= AnimalFlags.HasClaws;
printAnimalAbilities(animal); // animal has claws
animal.flags &= ~AnimalFlags.HasClaws;
printAnimalAbilities(animal); // nothing
animal.flags |= AnimalFlags.HasClaws | AnimalFlags.CanFly;
printAnimalAbilities(animal); // animal has claws, animal can fly
```

Здесь:

* Мы использовали `|=`, чтобы добавить флаги;
* комбинацию `&=` и `~`, чтобы убрать флаг;
* `|` объединить флаги.

{% hint style="info" %}
Вы можете комбинировать флаги для определения удобных и быстрых способов в перечисляемых типах следующим образом `EndangeredFlyingClawedFishEating`:
{% endhint %}

```typescript
enum AnimalFlags {
  None        = 0,
  HasClaws    = 1 << 0,
  CanFly      = 1 << 1,

  EndangeredFlyingClawedFishEating = HasClaws | CanFly | EatsFish | Endangered
}
```

## Перечисления строк

Мы смотрели только на перечисления, где значения членов являются числами. На самом деле вам также разрешено иметь члены перечисления со строковыми значениями. Например:

```typescript
export enum EvidenceTypeEnum {
  UNKNOWN = '',
  PASSPORT_VISA = 'passport_visa',
  PASSPORT = 'passport',
  SIGHTED_STUDENT_CARD = 'sighted_tertiary_edu_id',
  SIGHTED_KEYPASS_CARD = 'sighted_keypass_card',
  SIGHTED_PROOF_OF_AGE_CARD = 'sighted_proof_of_age_card'
}
```

С ними проще работать и дебажить, так как они предоставляют понятные строковые значения.

Вы можете использовать эти значения для простого сравнения строк. Например:

```typescript
// Where `someStringFromBackend` will be '' | 'passport_visa' | 'passport' ... etc.
const value = someStringFromBackend as EvidenceTypeEnum;

// Sample use in code
if (value === EvidenceTypeEnum.PASSPORT) {
  console.log('You provided a passport');
  console.log(value); // `passport`
}
```

## Постоянное перечисление

```typescript
enum Tristate {
  False,
  True,
  Unknown
}

const lie = Tristate.False;
```

Строка `const lie = Tristate.False` скомпилируется в `const lie = Tristate.False` \(да, выходные данные совпадают с входными данными\). Это означает, что во время выполнения необходимо будет выполнить поиск `Tristate`, а затем `Tristate.False`. Для повышения производительности вы можете пометить перечисление как постоянное перечисление\(`const enum`\). Это продемонстрировано ниже:

```typescript
const enum Tristate {
  False,
  True,
  Unknown
}

const lie = Tristate.False;
```

Будет скомпилировано в:

```typescript
var lie = 0;
```

Компилятор будет:

* Любое использование встроенных перечислений \(`0` вместо `Tristate.False`\);
* Не генерирует JavaScript для определения enum \(во время выполнения отсутствует переменная `Tristate`\), поскольку его использование встроено.

### Опция `preserveConstEnums` 

Встраивание имеет очевидные преимущества в производительности. Тот факт, что во время выполнения отсутствует переменная `Tristate`, просто помогает компилятору, не генерировать JavaScript, который фактически не используется во время выполнения. Однако вы, возможно, захотите, чтобы компилятор по-прежнему генерировал JavaScript-версию определения перечисления для таких вещей, как получения числа через строку или поиск строки через число, как мы видели. В этом случае вы можете использовать флаг компилятора `--preserveConstEnums`, и он все равно сгенерирует определение `var Tristate`, так что вы можете использовать `Tristate['False']` или `Tristate[0]` вручную во время выполнения, если хотите. Это никак не влияет на врезку.

## Перечисление со статическими методами

Вы можете добавить статические методы к типам `enum`, используя объявление `enum` + `namespace`. Как показано в следующем примере, мы добавляем статический метод `isBusinessDay` в перечисление:

```typescript
enum Weekday {
  Monday,
  Tuseday,
  Wednesday,
  Thursday,
  Friday,
  Saturday,
  Sunday
}

namespace Weekday {
  export function isBusinessDay(day: Weekday) {
    switch (day) {
      case Weekday.Saturday:
      case Weekday.Sunday:
        return false;
      default:
        return true;
    }
  }
}

const mon = Weekday.Monday;
const sun = Weekday.Sunday;

console.log(Weekday.isBusinessDay(mon)); // true
console.log(Weekday.isBusinessDay(sun));
```

## Перечисления открыты для добавления

{% hint style="info" %}
Открытые перечисления имеют смысл, только если вы не используете модули, вы должны использовать модули, так что эта часть находится в конце статьи.
{% endhint %}

Давайте еще раз посмотрим, как выглядит перечисление, скомпилированное в JavaScript:

```typescript
var Tristate;
(function(Tristate) {
  Tristate[(Tristate['False'] = 0)] = 'False';
  Tristate[(Tristate['True'] = 1)] = 'True';
  Tristate[(Tristate['Unknown'] = 2)] = 'Unknown';
})(Tristate || (Tristate = {}));
```

Мы объяснили что `Tristate [Tristate ['False'] = 0] = 'False'`, теперь давайте посмотрим на функцию-оболочку `(function (Tristate) {/  код здесь  /}) (Tristate || (Tristate = { }))`, Особенно на часть `(Tristate || (Tristate = {}))`. Он захватывает локальную переменную `Tristate`, которая либо указывает на уже существующее значение `Tristate`, либо инициализирует его новым пустым объектом.

Это означает, что вы можете разделить \(и расширить\) определение перечисления на несколько файлов, как показано ниже, вы можете разделить определение `Color` на два блока:

```typescript
enum Color {
  Red,
  Green,
  Blue
}

enum Color {
  DarkRed = 3,
  DarkGreen,
  DarkBlue
}
```

{% hint style="info" %}
Вы должны задать числовое значение первому члену в блоке продолжения перечисления, чтобы оно не перезатерло предыдущие значения. TypeScript выдаст предупреждение, если вы определите начальное значение \(сообщение об ошибке: `In an enum with multiple declarations, only one declaration can omit an initializer for its first enum element`.\).
{% endhint %}

