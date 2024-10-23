---
title: Javascriptの超基本
emoji: 👶
type: tech
topics:
  - javascript
published: false
---
## Declaring Variables

### Variables that can be reassigned
```js
let age; 
```

### Variables with a constant value
- Must have an assignment 
- Cannot be reassigned later (results in runtime error)
```js
const numberOfFingers = 20; 
```

### Undefined 
- `undefined` is a primitive JavaScript value that represents the lack of a defined value (declared variables that aren't initialized to a value will have the value `undefined`)

### Null 
- `null` is a primitive data type that represents the intentional absence of value 

## Assignment Operators 
- `+=` addition assignment 
- `-=` subtraction assignment 
- `*=` multiplication assignment 
- `/=` division assignment 

```js
let number = 100;

// Both statements will add 10 
number = number + 10;
number += 10;

console.log(number);
// Prints: 120 

```

## Template Literals (String Interpolation)
- Template literals are strings that allow embedded expressions: `${expression}`

```js
let age = 7;

// String interpolation
`Tommy is ${age} years old.`;
```

## Methods 
- Methods return information about an object, and are called by appending an instance with a period `.`, the method name, and parentheses

```js
console.log();
```

## Build in Objects 
- Built in objects contain methods that can be called

```js 
Math.random();
// Math is the built in object 
```

## Ternary Operator 
- condition `?` expression when true `:` expression when false 

```js
let price = 10.5;
let day = "Monday";

day === "Monday" ? price -= 1.5 : price += 1.5;
```

## Arrow Functions 

```js
// Arrow function with two parameters 
const sum = (firstParam, secondParam) => { 
  return firstParam + secondParam; 
}; 
console.log(sum(2,5)); // Prints: 7 

// Arrow function with no parameters 
const printHello = () => { 
  console.log('hello'); 
}; 
printHello(); // Prints: hello

// Arrow functions with a single parameter 
const checkWeight = weight => { 
  console.log(`Baggage weight : ${weight} kilograms.`); 
}; 
checkWeight(25); // Prints: Baggage weight : 25 kilograms.

// Concise arrow functions
const multiply = (a, b) => a * b; 
console.log(multiply(2, 30)); // Prints: 60
```