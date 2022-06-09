## Function declaration

### inferred return type
```
function sum(a: number, b: number) {
  return a + b
}
```

### defined return type
```
function sum(a: number, b: number): number {
  return a + b
}
```

### named function expression
```
const sum = function sum(a: number, b: number): number {
  return a + b
}
```

### annonymous function expression
```
const sum = function (a: number, b: number): number {
  return a + b
}
```
### arrow function
```
const sum = (a: number, b: number): number => {
  return a + b
}
```

### implicit return
```
const sum = (a: number, b: number): number => a + b
```

### implicit return of an object requires parentheses to disambiguate the curly braces
```
const sum = (a: number, b: number): {result: number} => ({result: a + b})

//typed
const sum: (a: number, b: number) => number = (a, b) => a + b
//OR
type MathFn = (a: number, b: number) => number
const sum: MathFn = (a, b) => a + b
```
### Or you can use the object type syntax:
```
type MathFn = {
  (a: number, b: number): number
}
const sum: MathFn = (a, b) => a + b
```
### you can add addition properties to the object
```
type MathFn = {
  (a: number, b: number): number
  operator: string
}
const sum: MathFn = (a, b) => a + b
sum.operator = '+'
```

## declare vs interface vs type
### type
```
type MathFnType = {
  (a:number, b:number): number
  operator: string
}

const sumType: MathFnType = (a, b) => a / b
sumType.operator = '/'

console.log(`Using Type: ${sumType.operator} and ${sumType(8, 2)}`);
```
### declare
```
declare function MathFnDeclare(a:number, b:number):number;
declare namespace MathFnDeclare {
  let operator: '+'
}

const sumDeclare: typeof MathFnDeclare = (a, b) => a + b
sumDeclare.operator = '+'

console.log(`Using Declare: ${sumDeclare.operator} and ${sumDeclare(1, 2)}`);
```

### interface
```
interface MathFnInterface {
  (a:number, b:number): number,
  operator: string
}

const sumInterface: MathFnInterface = (a, b) => a * b
sumInterface.operator = '*'

console.log(`Using Interface: ${sumInterface.operator} and ${sumInterface(1, 2)}`);
```
# When to use what - declare vs interface vs type

## declare
- It's useful when you use libs that don't have official typings yet
- It's for layering on top of existing functionality without affecting actual execution.
- e.g. Let's say you import a library that has no types, but you want to use a function from that library in a type-safe way. You can include a `.d.ts` file in your project with `declare` statements that are never executed, but layered on top for the compiler.

Here's the canonical example: https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types

That's the repo where people add types to public packages that don't have their own.

⚠️ Some believed that it could be used for function overloading

```
declare function f1(p1: number, p2: number);
declare function f1(p1: number, p2: string, p3: number);
function f1(p1: number, p2: number | string, p3?: number) {
  if (p3) {
    console.log(p1, p2, p3)
  } else {
    console.log(p1, p2);
  }
}

console.log(f1(1,2));
console.log(f1(1,'2',3));
```
But since the implementation code is accessible then overloading would anyways
work without the declare keyworkd (try removing the above two declare)

## type vs interface


 * By and large - they are more or less the same
 * > Interface was built to shape an object
 * > For rest of typing use type


### ⭕️ Interface - you can extend
```
interface Mammals {
  kind: string
}

interface Person extends Mammals {
  name: string
  hungry: boolean
}

class Harry implements Person {
  name = 'nemo'
  hungry = false
  kind = 'Human'
}
```
### Interface - Decleration merging
```
interface SomeInterface {
  prop2: string
}

interface SomeInterface {
  prop1: string
  prop3: boolean
}

class SomeClass implements SomeInterface {
  prop2: string
  prop1: string
  prop3: boolean
}
```

### ⭕️ types
- They are aliases for types that you want to refer many times
- They are simple

```
type Hungry = boolean
const harry: Hungry = true
const nemo: Hungry = 'false' //this gives error on the original type and not the alais
```
### type alias objects
```
type School = {
  name: string
  feedback: boolean
}

const myhome: School = {
  name: "",
  feedback: false
}
```

### You can also implements a type in a class
```
class mystudio implements School {
  name: string
  feedback: boolean
}
```
### Types cant extent other types but you can use union or intersection
```
type SeriousPeople = {
  name: string
  place: string
}

type Youtuber = {
  isFamous: boolean
}
```

Union
```
type Teen = SeriousPeople | Youtuber

// The IDE does not even give code suggestion to implement missing props
const ramzo: Teen = {
  isFamous: false
}
```
Intersection
```
type Biker = {
  brand: string
} & Youtuber

const ravi: Biker = {
  brand: 'Honda',
  isFamous: false
}
```

## When to use what (type vs interface)?

> From Typescript documenation

That said, we recommend you use interfaces over type aliases. Specifically, because you will get better error messages. If you hover over the errors on IDE, you can see how TypeScript can provide terser and more focused messages when working with interfaces than type.


One major difference between type aliases vs interfaces are that interfaces are open and type aliases are closed. This means you can extend an interface by declaring it a second time.

### When to use type:

    - Use type when defining an alias for primitive types (string, boolean, number, bigint, symbol, etc)
    - Use type when defining tuple types
    - Use type when defining function types
    - Use type when defining a union
    - Use type when trying to overload functions in object types via composition
    - Use type when needing to take advantage of mapped types

### When to use interface:

    - Use interface for all object types where using type is not required (see above)
    - Use interface when you want to take advantage of declaration merging.

### ⭕️ Primitive types
The easiest difference to see between type and interface is that only type can be used to alias a primitive:

```
type Nullish = null | undefined;
type Fruit = 'apple' | 'pear' | 'orange';
type Num = number | bigint;
```

### ⭕️ Tuple types

Tuples can only be typed via the type keyword:

```
type row = [key: number, value: string];
```

### ⭕️ Function types

Functions can be typed by both the type and interface keywords:

```
// via type
type Sum = (x: number, y: number) => number;

// via interface
interface Sum {
  (x: number, y: number): number;
}
```

Since the same effect can be achieved either way, the rule will be to use type in these scenarios since it's a little easier to read (and less verbose).


### ⭕️ Union types

Union types can only be achieved with the type keyword:

```
type Fruit = 'apple' | 'pear' | 'orange';
type Vegetable = 'broccoli' | 'carrot' | 'lettuce';

// 'apple' | 'pear' | 'orange' | 'broccoli' | 'carrot' | 'lettuce';
type HealthyFoods = Fruit | Vegetable;
```