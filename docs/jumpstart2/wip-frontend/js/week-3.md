# Introduction to JavaScript

## Topics

- Variables
    - let
    - const
- Data Types
    - Numbers & Strings
    - Arrays
    - Objects
    - null & undefined
- Control Flow
    - if & else
    - while
    - for
- Functions

## What is JavaScript

JavaScript allows you to add interactivity and complex functionality to your website. JavaScript started as a scripting language designed to complete simple tasks on a webpage. Now, we have JavaScript frameworks like React that dominate modern web development. We'll see a lot more JavaScript as we start working with React, but for now we will stick to simple use cases.

JavaScript is fairly intuitive to pick up if you're familiar with Python or a different scripting language but can be tricky if you don't have much general coding experience.

### Where can you write JavaScript

JavaScript is fully integrated with HTML and CSS and comes built into any web browser. You can run JavaScript through the developer console of your web browser.

- On Chrome: Options > More tools > Developer tools

- On Firefox: Options > Web Developer > Web Console

You can also use any number of online JavaScript code editors like [JS Fiddle](jsfiddle.net), [Code Pen](codepen.io/pen), or [repl.it](https://repl.it/~).

## Some Basics

### Comments

To write comments in JavaScript you can either write `//` at the beginning of the line, or you can wrap any code in `/* some code */` to comment it out.

```js
// This is a comment
console.log("Write to console"); // Another comment here
/* This is 
   also a
   comment */
console.log(123);
```

### Printing to the console

To print a value out to the console you can use `console.log("Some value")`. This is useful for debugging some problems while you are developing.


## Variables

Variables hold data and give you a way of accessing values by name. A variable can store any type of data that we talk about below.

JavaScript is a *loosely-typed (dynamic) language* meaning you do not specify the type of data you're using. Instead, JavaScript infers the type of data based on its value.

In the past, all variables were declared with the `var` keyword. Newer versions of JavaScript introduced the keywords `let` and `const` that should now be used in place of `var`.

`let` should be used whenever you have a variable that you want to reassign at some point in your program.

`const` should be used whenever you have a variable that you won't every reassign.

```js
let unAssignedVar;
let changableVar = 1;
const unchangableVar = 2;

unAssignedVar = "A value";
changableVar = 3;
unchangableVar = 4; // This will cause an error!!
```

It's important to keep in mind the scope of your variables. As we learn more about functions it'll become clearer, but the basic rule is that you can only use a variable in the same code block that you defined it or a code block that is nested in the block you defined it.

## Data Types

### Numbers and Strings

Numbers can be specified as whole numbers or decimal numbers simply as you would type them normally.

Strings are just text wrapped in quotes. You can use `"` or `'`. If you want to include a single or double quote in your string you can escape it with a backslash (`\`).

#### Operations

JavaScript supports most basic operations that you may want to do with a number. You can add, subtract, multiply, and divide all with their basic operations `+ - * /`. JavaScript also can do basic negation for a single number by specifying a negative sign `-` in front of a single number.

```js
const add = 1 + 2;
const sub = 1 - 2;
const mul = 1 * 2;
const div = 1 / 2;
const neg = -3;
const decimal = 12.34;
```

Strings in JavaScript can also be operated on with the addition symbol `+`. This will concat the two strings into one longer string.

```js
const hello = 'hello';
const world = "world";
const helloWorld = hello + ' ' + world; // Equivilent to 'hello world'
```

Strings are essentially just a list of characters, so you'll often want to do similar things with strings that you might want to do with lists. 

You can get the length of a string with their `.length` property. 
 
You can also access any specific character of a string by using a square bracket `[]` and the character's index (position in the string). In JavaScript, strings (like arrays) are *0-indexed*, meaning the first character has an index of 0, the next an index of 1, all the way until the last character's index which is the length minus 1.

There are several other built in functions to help when working with strings. One of those includes `.substring(startIndex, endIndex)` which will return a partial string that has the characters from 'startIndex' (inclusive) to 'endIndex' (exclusive).

You can see a full list of built in string operations [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String).

```js
const str = 'Hello World!';
const strLength = str.length; // Equal to 12 (Don't forget to count white space!)
const letterH = str[0];
const exclamation = str[str.length - 1]
const hello = str.substring(0, 5);
```


### Booleans

Booleans are either the value `false` or `true`. You will also hear the term *condition* which refers to a statement that evaluates to a boolean.

#### Operations

JavaScript 2 operators for combining conditions: or `||` & and `&&`. 'or' will return true if either condition is true, and 'and' will only return true only if both conditions are true.

You can also reverse a condition with the not operator `!`.

```js
const orOne = false || true;                  // This is true
const orTwo = false || false || false;        // This is false
const andOne = false && true && true;         // This is false
const andTwo = true && true;                  // This is true
const not = !true                             // This is false
const combination = !true || !false && true;  // This is true
```

An important concept to be aware of when working with logical operators is the idea of *short-circuiting*. Because 'or' only needs one true condition to be true and 'and' only needs one false to be false, as soon as JavaScript encounters one of those values during those operations it'll stop evaluating the rest of the conditions. While this doesn't matter when your conditions are all booleans, once we start using conditions that have *side effects* this behavior will often be exploited.


### Arrays

Arrays in JavaScript are ordered collections of data.

Arrays are wrapped in box brackets `[ ... ]`. Arrays can hold any kind of data including other arrays.

```js
const ourArray = ["A first value", 11, ["Nested Value", 0.12], 'A last value'];
```

#### Operations

You can refer to values in arrays by their *index*. Arrays in JavaScript are *0-indexed* meaning the first value in the array has an index of 0, the next value has an index of 1, and so on until the last index that is the length of the array minus 1.

You can also get the length of an array with the `.length` property, identical to a string.

```js
const arr = [1, "cat", 3.5, ["a", "b"], 4];
const arrLength = arr.length;       // Equal to 5
const secondEl = arr[1];            // Equal to "cat"
const nestedEl = arr[3][0];         // Equal to "a"
const lastEl = arr[arr.length - 1]; // Equal to 4
```

Arrays are also modifiable. You can assign a value to a particular index in an array with the following syntax:

```js
const arr = [1, 2, 3, 4];
const beginning = arr[0]; // Equal to 1
arr[0] = 100;
const newBeginning = arr[0]; // Equal to 100
```

Arrays also define several built in functions. You can read a full list of them on [MDN here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array).

### Objects

Objects in JavaScript are used to hold more complex data structures.

Objects are wrapped in curly brackets `{ ... }`. Within the brackets, you can define a list of properties. A property has a key, which is just a single unquoted word, and a value which can be any other JavaScript data type (including another object!).

```js
const ourObject = {
    aKey: "A String Value",
    numberOfCars: 10,
    list_of_values: [1, 'five', 4.0, "six"],
    aNestedObject: {
        innerKey: "outer key"
    },
    oneLastValue: "Notice this doesn't have a comma"
};
```

Properties have a colon separating the key from the value, and they end with a comma if there are more properties in the object.

#### Operations

You refer to properties in an object by their key. You can either use dot notation `someObject.someKey` or array like notation `someObject['someKey']`. Dot notation is shorter but array like notation gives you the flexibility to use a variable as the key. If you try to access a property that doesn't exist, you'll get the undefined value.

You can add properties to an object just with simple assignment statements, similar to updating an index in an array.

```js
const person = {
    name: "Mike",
    age: 12
};
const personName = person.name; // Equal to Mike
const personAge = person['age']; // Equal to 12
const personWeight = person.weight; // Equal to undefined

const weightProperty = 'weight';
person[weightProperty] = 150;
const newPersonWeightOne = person[weightProperty] // Equal to 150
const newPersonWeightTwo = person.weight; // Equal to 150
```


### null and undefined

`null` and `undefined` are two additional primitive types that JavaScript defines. While they will come up, it's almost never a good idea to intentionally use them in your code design.

`null` refers to an unintialized object. This is not the same thing as an empty object (`{}`).

`undefined` is defined as an unitialized variable.

We will see a couple times when these values may come up, but generally if they appear where you do not expect them it's a good indication that you have a bug in your code.

```js
const someVariable;
const noValue = null;
const isUndefined = someVariable === undefined; // true
```

## Control Flow

Control flow allows you to execute different pieces of code based on different conditions.

A condition is a statement that evaluates to a *boolean* value (`true` or `false`).

### Comparators

Comparators let us get a boolean value by comparing two values.

JavaScript has two types of equality, `==` and `===`. `===` requires that two pieces of data have the exact same value and *the exact same type*. `==` will first try to convert the two values into the same type and *then* do the comparison. This can be hard to predict and can lead to unintended side effects, so in most cases you should use `===`.

We can also compare the inverse of the equality comparators with `!=` and `!==`.

Finally, we can check less than `<`, less than or equal to `<=`, greater than `>`, or greater than or equal to `>=`. These comparators are mostly useful just when working with numbers.

```js
const a = 5 === 5;   // true
const c = 5 === 3;   // false
const b = 5 === "5"; // false

const d = 5 == 5;    // true
const e = 5 == "5";  // true
const f = 5 == 3;    // false
```

Note that for checking equality between objects or arrays JavaScript won't compare each of the elements of an object or an array with `==` or `===`. Instead, it only checks if you are comparing the exact same object.

```js
const obj1 = {
    key1: 1,
    key2: "car"
};
const obj2 = {
    key1: 1,
    key2: "car"
};
const a = obj1 == obj2  // false
const a = obj1 === obj2 // false
const a = obj1 == obj1  // true
const a = obj1 === obj1 // true
```

### if & else

`if` statements are the most basic form of control flow. They take a single condition and execute a given block of code once if the condition is true. This can also be combined with an `else` statement that will execute a block of code if the condition is false.

```js
const conditionOne = false;
const conditionTwo = true;

if (conditionOne) {
    console.log("First Path");
} else if (conditionTwo) {
    console.log("Second Path");
} else {
    console.log("Third Path");
}
```

### while

`while` statements allow you to execute a block of code multiple times based on a changing condition. They take a single condition and execute the given code block until the condition evaluates to false.

```js
let counter = 0;
while (counter < 4) {
    console.log(counter);
    counter = counter + 1;
}
```

You have to make sure that the condition you use in a while loop will change over execution. If you use `while (true) {...}` for example, you will create an infinite loop and your program will likely crash after running out of memory.

### for

`for` statements are similar to while loops but allow you to more succinctly define some common cases.

The basic form of for loop includes declaring a value, defining a condition, and a defining an action to happen at the end of every loop.

`for (decleration; condition; action) { ... }`

The most common use case for this form is to run a block of code a set number of times.

```js
for (let i = 0; i < 8; i = i + 1) {
    // This code will run 8 times
}
```

Another common use case is running a loop for every element in an array. You can do this with the declaration, condition, action syntax, but JavaScript also defines a shorthand.

```js
const arr = [1, 2, 3];
for (const element of arr) {
    console.log(element);
}

// This is equal to...

for (let i = 0; i < arr.length; i++) {
    const element = arr[i];
    console.log(element);
}
```

You can also iterate over the **keys** in an object using a for-in loop.

```js
const obj = {
    keyOne: 1,
    keyTwo: 1
};
for (const key in obj) {
    console.log(key + ' - ' + obj[key]);
}
// Prints "keyOne - 1" and "keyTwo - 2"
```

### break & continue

While working with while and for loops you may encounter a situation where you want to break out of a loop early or skip a certain element. For this, JavaScript defines the keywords `break` and `continue`. `break` will immediately end the innermost loop that it's a part of. `continue` will skip the current element in a loop and continue the loop from the next element.

```js
for (let i = 0; i < 10; i++) {
    if (i == 2 || i == 4) {
        continue;
    } else if (i == 6) {
        break;
    }
    console.log(i);
}
// Only 1, 3, and 5 are output to the console
```

## Functions

### The Basics

Functions are named blocks of code that you can reuse. A function has a name and takes 0 or more arguments as input and returns a single value as output.

```js
// Declaring our function that takes two arguments and returns their result
function ourFunctionName(argumentOne, argumentTwo) {
    const sum = argumentOne + argumentTwo;
    return sum;
}

// Calling our function with 4.2 and 6.4
const functionValue = ourFunctionName(4.2, 6.4);
// functionValue now is equal to 10.6
```

The `return` statement lets JavaScript define a value to return for the function, but it's not required. Any function that doesn't have a `return` statement will return the `undefined` value instead.

You can also use `return` statements in conjunction with control flow to possibly end a function block early.

```js
// Note that in this case it would probably be simpler to reverse the if condition and move
// the console log statement inside the if.
function printIfLessThanFour(num) {
    if (num >= 4) {
        return;
    }
    console.log(num);
}
```

### Functions as Data

Functions in JavaScript are treated very similarly to other types of data like objects or strings. You can assign a variable to a function itself, and you can even pass functions as arguments to other functions.

```js
function adder(num1, num2) {
    return num1 + num2;
}
// Note that this is different than setting 'const adderFunc = adder()'
// Because we ommit the parenthasis, we don't execute the function yet
// Instead adderFunc just holds a reference to the function and can be executred with `adderFunc()`
const adderFunc = adder;

function arrayCombiner(array, combinerFunc) {
    let sum = 0;
    for (const el of array) {
        // Here we are calling the function that we were passed as an argument
        sum = combinerFunc(sum, el);
    }
    return sum;
}

const numberArray = [1, 2, 3, 4, 5];
const arraySum = arrayCombiner(numberArray, adderFunc);
// Array sum is now equal to 15, the sum of every element of numberArray
```

We can also return functions from functions.

```js
function getAdder(num1, num2) {
    return function adder() {
        return num1 + num2;
    }
}

// Here we give the arguments we want to add, 
//  but the actual addition is not executed yet
const adderFunction = getAdder(3, 4);

// Here the addition is executed and sum is equal to 7
const sum = adderFunction();

// Here we condense the two statements, sumTwo now contains 4
const sumTwo = getAdder(2, 2)();
```

You can even hold functions in arrays or as the value in objects.

```js
const numberFunctionsArray = [
    function add(n1, n2) { return n1 + n2; },
    function sub(n1, n2) { return n1 - n2; },
    function mult(n1, n2) { return n1 * n2; },
];
const numberFunctionsObj = {
    addKey: function add(n1, n2) { return n1 + n2; },
    subKey: function sub(n1, n2) { return n1 - n2; },
    multKey: function mult(n1, n2) { return n1 * n2; }
}
let ans = 2;
for (const func of numberFunctionsArray) {
    ans = func(ans, 3);
}
for (const funcKey in numberFunctionsObj) {
    ans = numberFunctionsObj[funcKey](ans, 4);
}
```

### Anonymous Functions

We'll be storing and passing functions around a lot in React, and it becomes tedious to have to write out `function someName()` every time. Furthermore, in our last example we returned a function named 'adder' but that function name is never used anywhere.

To make things more convenient, JavaScript defines a quick way to define functions without names.

```js
const myFunctionLong = function operator(num1, num2) {
    const reverse = num1 * -1;
    return reverse + num2;
}

// This syntax is equivielent to above
const myFunctionQuick = (num1, num2) => {
    const reverse = num1 * -1;
    return reverse + num2;
}
```

An anonymous function drops the `function` keyword and the function name. Instead, we just define the argument list between two parenthasis and use an arrow (`=>`) pointing to the function block.

If our function doesn't have any arguments we just use empty parentheses: `() => { ... }`

We can shorten this syntax even further if we don't have any intermediate steps to preform before returning a value. If we drop the `{}` and point directly to a code statement, and the anonymous function will return that statement as if you had specified it with a `return` keyword.

```js
// These syntaxes are all equivielent!

const fiveReturner = function fiveReturnerInner() {
    return 'five';
}

const fiveReturnerFast = () => {
    return 'five';
}

const fiveReturnerFastest = () => 'five';
```


## Additional Resources

For a great interactive tutorial through the basics of JavaScript try [Codecademy](https://www.codecademy.com/learn/introduction-to-javascript).

For a great reference website for any miscellaneous questions you have try [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference).

