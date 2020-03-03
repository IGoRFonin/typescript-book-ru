# Тип совместимости

Совместимость типов используется для определения того, может ли тип быть назначен другим типам.

Если тип `string` не совместим с типом `number`:

```typescript
let str: string = "Hello";
let num: number = 123;

str = num; // ERROR: `number` is not assignable to `string`
num = str; // ERROR: `string` is not assignable to `number`
```

## Безопасность

Система типов TypeScript разработана так, чтобы она была удобной и допускала неправильное поведение, например что угодно может быть назначено `any`, что означает указание компилятору позволить вам делать все, что вы хотите:

```typescript
const foo: any = 123;
foo = 'hello';

foo.toPrecision(3);
```

## Структуры

Объекты TypeScript структурно типизированы. Это означает, что имена не имеют значения, пока структуры совпадают

```typescript
interface Point {
    x: number,
    y: number
}

class Point2D {
    constructor(public x:number, public y:number){}
}

let p: Point;
// OK, потому что структурно типизированы
p = new Point2D(1,2);
```

Это позволяет вам создавать объекты на лету \(как вы делаете в `vanilla JS`\) и сохраняете безопасность всякий раз, когда можно сделать вывод.

```typescript
interface Point2D {
    x: number;
    y: number;
}
interface Point3D {
    x: number;
    y: number;
    z: number;
}
var point2D: Point2D = { x: 0, y: 10 }
var point3D: Point3D = { x: 0, y: 10, z: 20 }
function iTakePoint2D(point: Point2D) { /* do something */ }

iTakePoint2D(point2D); // exact match okay
iTakePoint2D(point3D); // extra information okay
iTakePoint2D({ x: 0 }); // Error: missing information `y`
```

## Варианты



