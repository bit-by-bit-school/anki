---
## JavaScript Interview Quesions
---

# What is JavaScript?
     JavaScript is a high-level, interpreted (or JIT-compiled) programming language primarily known as the scripting language for Web pages. It is also used in many non-browser environments thanks to Node.js. It's prototype-based, multi-paradigm, single-threaded, dynamic, and supports object-oriented, imperative, and declarative (e.g., functional programming) styles.
# What are the different data types in JavaScript?
     JavaScript has two main categories of data types:
    *   **Primitive Types:**
        *   `string`: Sequence of characters.
        *   `number`: Numeric values (both integer and floating-point).
        *   `bigint`: For arbitrarily large integers.
        *   `boolean`: `true` or `false`.
        *   `undefined`: A variable that has been declared but not assigned a value.
        *   `symbol`: Unique and immutable primitive value.
        *   `null`: Represents the intentional absence of any object value.
    *   **Reference Type (Object):**
        *   `object`: Collections of properties (including `Array`, `Function`, `Date`, `RegExp`, etc.).
# What is the difference between `null` and `undefined`?

    *   `undefined` typically means a variable has been declared but not yet assigned a value. It's also the default return value of functions that don't explicitly return anything.
    *   `null` is an assignment value. It can be assigned to a variable as a representation of "no value" or an intentional absence of any object value.
# What is the difference between `==` and `===`?

    *   `==` (Abstract Equality Comparison): Compares two values for equality *after* performing type coercion if necessary.
    *   `===` (Strict Equality Comparison): Compares two values for equality *without* performing type coercion. Both value and type must be the same. It's generally recommended to use `===`.
    ```javascript
    console.log(5 == "5");  // true (coercion happens)
    console.log(5 === "5"); // false (types are different)
    ```
# What is type coercion in JavaScript?
     Type coercion is the automatic or implicit conversion of values from one data type to another (e.g., string to number, number to boolean). This happens when operators are applied to values of different types.
# What are `var`, `let`, and `const`? What are their differences?

    *   **`var`:**
        *   Function-scoped or globally-scoped.
        *   Can be re-declared and updated.
        *   Hoisted to the top of its scope and initialized with `undefined`.
    *   **`let`:**
        *   Block-scoped (`{}`).
        *   Can be updated but not re-declared within the same scope.
        *   Hoisted but not initialized (Temporal Dead Zone).
    *   **`const`:**
        *   Block-scoped.
        *   Cannot be updated or re-declared.
        *   Must be initialized during declaration.
        *   Hoisted but not initialized (Temporal Dead Zone).
        *   For objects and arrays, the reference is constant, but the content can be modified.
# What is "hoisting" in JavaScript?
     Hoisting is JavaScript's default behavior of moving declarations to the top of their current scope (script or function scope for `var` and function declarations, block scope for `let` and `const` though they are not initialized).
    *   Function declarations are fully hoisted (name and body).
    *   `var` declarations are hoisted and initialized with `undefined`.
    *   `let` and `const` declarations are hoisted but not initialized, leading to a Temporal Dead Zone until their declaration is encountered.
# What is the Temporal Dead Zone (TDZ)?
     The Temporal Dead Zone is the period between entering scope and a `let` or `const` variable's declaration. Accessing the variable in the TDZ results in a `ReferenceError`.
# What are global variables? How can they be problematic?
     Global variables are variables declared outside any function or block, or variables assigned a value without prior declaration (in non-strict mode, this creates a global property). They are accessible from any part of the code.
    Problems:
    *   **Name Collisions:** Different parts of the code might unknowingly use the same global variable name, leading to bugs.
    *   **Reduced Readability/Maintainability:** It's harder to track where a global variable is modified.
    *   **Testing:** Makes unit testing harder as global state can affect test outcomes.
# What does `'use strict';` do?
     `'use strict';` is a directive that enables "strict mode." Strict mode changes certain JavaScript "bad parts" into explicit errors, makes JavaScript more secure, and optimizes performance. For example, it prevents accidental global variable creation, makes `eval()` safer, and throws errors for silent failures.
# Explain the `typeof` operator.
     The `typeof` operator returns a string indicating the type of the unevaluated operand.
    ```javascript
    typeof 42;          // "number"
    typeof "hello";     // "string"
    typeof true;        // "boolean"
    typeof undefined;   // "undefined"
    typeof { a: 1 };    // "object"
    typeof [1, 2];      // "object" (arrays are objects)
    typeof function(){}; // "function" (functions are objects too)
    typeof null;        // "object" (this is a historical bug)
    typeof Symbol();    // "symbol"
    ```
# What is NaN? How can you check if a value is NaN?
     `NaN` stands for "Not a Number." It's a special numeric value that results from an operation that cannot produce a meaningful numerical result (e.g., `0/0`, `Math.sqrt(-1)`).
    You can check for `NaN` using `Number.isNaN()` (preferred) or `isNaN()`. `NaN` is the only value in JavaScript not equal to itself (`NaN === NaN` is `false`).
    ```javascript
    console.log(Number.isNaN(0/0));      // true
    console.log(isNaN("hello" * 5)); // true
    ```
# What are template literals (template strings)?
     Template literals are string literals allowing embedded expressions, multi-line strings, and string interpolation. They are enclosed by backticks (`` ` ``).
    ```javascript
    const name = "World";
    const greeting = `Hello, ${name}!
    This is a multi-line string.`;
    console.log(greeting);
    ```
# What is an IIFE (Immediately Invoked Function Expression)? Why use it?
     An IIFE is a JavaScript function that runs as soon as it is defined.
    ```javascript
    (function() {
        console.log("IIFE executed!");
        var localScopedVar = "I am local";
    })();
    ```
    Uses:
    *   **Avoids polluting the global namespace:** Variables declared inside an IIFE are not visible outside, creating a private scope.
    *   **Module pattern:** Can be used to create modules with private and public members.
# What are JavaScript truthy and falsy values?

    *   **Falsy values:** Values that evaluate to `false` in a boolean context.
        *   `false`
        *   `0` (zero)
        *   `-0` (minus zero)
        *   `0n` (BigInt zero)
        *   `""` (empty string)
        *   `null`
        *   `undefined`
        *   `NaN`
    *   **Truthy values:** All other values are truthy, including empty objects `{}`, empty arrays `[]`, and non-empty strings.





















# What are the different types of loops in JavaScript?

    *   `for`: Loops through a block of code a number of times.
    *   `for...in`: Loops through the properties of an object (enumerable, non-Symbol properties).
    *   `for...of`: Loops over iterable objects (like `Array`, `Map`, `Set`, `String`, etc.), giving you the values.
    *   `while`: Loops through a block of code while a specified condition is true.
    *   `do...while`: Loops through a block of code once, and then repeats the loop while a specified condition is true.
    *   Array iteration methods like `forEach`, `map`, `filter`, etc.
# What's the difference between `for...in` and `for...of`?

    *   `for...in` iterates over the *enumerable property names (keys)* of an object. It can also iterate over inherited properties.
    *   `for...of` (ES6) iterates over the *values* of an iterable object (e.g., `Array`, `String`, `Map`, `Set`). It does not work directly on plain objects because they are not inherently iterable.
# What is the use of the `break` and `continue` statements?

    *   `break`: Terminates the current loop, `switch`, or label statement and transfers program control to the statement following the terminated statement.
    *   `continue`: Terminates execution of the statements in the current iteration of the current or labeled loop, and continues execution of the loop with the next iteration.
# What is short-circuiting in JavaScript?
     Short-circuiting refers to how logical operators (`&&`, `||`) evaluate expressions.
    *   `&&` (AND): If the first operand is falsy, it returns that operand without evaluating the second. If the first is truthy, it evaluates and returns the second operand.
    *   `||` (OR): If the first operand is truthy, it returns that operand without evaluating the second. If the first is falsy, it evaluates and returns the second operand.
    ```javascript
    let x = true && "Hello"; // x is "Hello"
    let y = false && "Hello"; // y is false
    let z = true || "World";  // z is true
    let w = false || "World"; // w is "World"
    ```
# What is the conditional (ternary) operator?
     The ternary operator is a shorthand for an `if...else` statement. It takes three operands: a condition followed by a question mark (`?`), then an expression to execute if the condition is truthy, followed by a colon (`:`), and finally the expression to execute if the condition is falsy.
    ```javascript
    const age = 20;
    const canVote = (age >= 18) ? "Yes" : "No";
    console.log(canVote); // "Yes"
    ```


# What are the ways to declare a function in JavaScript?

    *   **Function Declaration (Statement):**
        ```javascript
        function greet(name) {
            return `Hello, ${name}!`;
        }
        ```
    *   **Function Expression (Anonymous or Named):**
        ```javascript
        const greet = function(name) { // Anonymous
            return `Hello, ${name}!`;
        };
        const greetNamed = function sayHello(name) { // Named
            return `Hello, ${name}!`;
        };
        ```
    *   **Arrow Function (ES6):**
        ```javascript
        const greet = (name) => `Hello, ${name}!`;
        ```
    *   **Function Constructor (rarely used):**
        ```javascript
        const greet = new Function('name', 'return `Hello, ${name}!`;');
        ```
# What is the difference between a function declaration and a function expression?

    *   **Hoisting:** Function declarations are hoisted completely (name and body). Function expressions are only partially hoisted if declared with `var` (variable name hoisted, initialized to `undefined`), or not initialized if declared with `let`/`const` (TDZ).
    *   **Usage:** Function declarations can be called before they are defined in the code. Function expressions must be defined before they are called (unless `var` is used, then it's `undefined`).
    *   **Conditional Definition:** Function expressions can be conditionally defined (e.g., inside an `if` block), whereas function declarations in blocks can behave inconsistently across environments (strict mode helps).
# What are arrow functions? How do they differ from regular functions?
     Arrow functions (ES6) provide a concise syntax for writing functions.
    Differences:
    *   **Syntax:** Shorter syntax.
    *   **`this` binding:** Arrow functions do not have their own `this` context. They lexically inherit `this` from the surrounding (parent) scope.
    *   **`arguments` object:** Arrow functions do not have their own `arguments` object. You can use rest parameters (`...args`) instead.
    *   **`new` keyword:** Cannot be used as constructors (cannot be called with `new`).
    *   **`prototype` property:** Do not have a `prototype` property.
    *   **No `super` or `new.target`:** They don't have their own bindings for these.
# What is the `this` keyword in JavaScript? How does its value get determined?
     The `this` keyword refers to the context in which a function is executed. Its value is determined by how the function is called (invocation context):
    *   **Global Context:** In the global execution context (outside any function), `this` refers to the global object (`window` in browsers, `global` in Node.js). In strict mode, `this` is `undefined` in the global context when not assigned.
    *   **Function Context (Default Binding):** When a regular function is called directly (not as a method of an object), `this` usually refers to the global object. In strict mode, `this` is `undefined`.
    *   **Method Invocation (Implicit Binding):** When a function is called as a method of an object, `this` refers to the object the method is called on.
    *   **Constructor Invocation (New Binding):** When a function is called with the `new` keyword, `this` refers to the newly created instance.
    *   **Explicit Binding (`call`, `apply`, `bind`):** These methods allow you to explicitly set the value of `this` for a function call.
    *   **Arrow Functions:** `this` is lexically bound; it takes the `this` value of its surrounding lexical scope.
# Explain `call()`, `apply()`, and `bind()`.
     These are methods of the `Function.prototype` used to control the `this` context of a function and pass arguments.
    *   **`call(thisArg, arg1, arg2, ...)`:** Invokes the function immediately with a specified `this` value and arguments provided individually.
    *   **`apply(thisArg, [argsArray])`:** Invokes the function immediately with a specified `this` value and arguments provided as an array (or an array-like object).
    *   **`bind(thisArg, arg1, arg2, ...)`:** Returns a *new function* where `this` is permanently bound to `thisArg`, and optionally, some initial arguments are pre-set (currying). The new function can be called later.
# What is a closure? Give an example and a use case.
     A closure is a function that has access to its own scope, the scope of the outer function, and the global scope. Essentially, a closure "remembers" the environment (variables) in which it was created, even after the outer function has finished executing.
    ```javascript
    function outerFunction(outerVariable) {
        return function innerFunction(innerVariable) {
            console.log("Outer variable: " + outerVariable);
            console.log("Inner variable: " + innerVariable);
        };
    }

    const newFunction = outerFunction("outside");
    newFunction("inside");
    // Output:
    // Outer variable: outside
    // Inner variable: inside
    ```
    **Use Cases:**
    *   **Data Encapsulation / Private Variables:** Creating private variables that can only be accessed through specific methods.
    *   **Callbacks and Event Handlers:** Maintaining state in asynchronous operations.
    *   **Currying and Partial Application.**
    *   **Module Pattern.**
# What are higher-order functions?
     Higher-order functions are functions that either:
    1.  Take one or more functions as arguments.
    2.  Return a function as their result.
    Examples include `map`, `filter`, `reduce`, `setTimeout`, and functions that return other functions (like in closures or currying).
# What are pure functions? Why are they useful?
     A pure function is a function that:
    1.  Given the same input, will always return the same output.
    2.  Produces no side effects (e.g., doesn't modify external state, log to console, mutate its input arguments if they are objects/arrays).
    **Usefulness:**
    *   **Predictability:** Easier to reason about and understand.
    *   **Testability:** Simple to test as output only depends on input.
    *   **Memoization/Caching:** Results can be cached.
    *   **Parallelism:** Can be run in parallel without interference.
# What is function currying?
     Currying is a technique of transforming a function that takes multiple arguments into a sequence of functions, each taking a single argument. Each function returns another function until all arguments are supplied, at which point the final result is returned.
    ```javascript
    function add(a) {
        return function(b) {
            return function(c) {
                return a + b + c;
            };
        };
    }
    console.log(add(1)(2)(3)); // 6

    // Or with arrow functions:
    const addArrow = a => b => c => a + b + c;
    console.log(addArrow(1)(2)(3)); // 6
    ```

# What are default parameters in functions?
     Default parameters (ES6) allow you to initialize named parameters with default values if no value or `undefined` is passed.
    ```javascript
    function greet(name = "Guest", greeting = "Hello") {
        console.log(`${greeting}, ${name}!`);
    }
    greet();                 // "Hello, Guest!"
    greet("Alice");          // "Hello, Alice!"
    greet("Bob", "Hi");      // "Hi, Bob!"
    ```
# What are rest parameters?
     Rest parameters (ES6, syntax: `...paramName`) allow a function to accept an indefinite number of arguments as an array. It collects all remaining arguments passed to the function into a single array. It must be the last parameter in the function definition.
    ```javascript
    function sumAll(...numbers) { // numbers will be an array
        return numbers.reduce((sum, num) => sum + num, 0);
    }
    console.log(sumAll(1, 2, 3));    // 6
    console.log(sumAll(10, 20, 30, 40)); // 100
    ```
# What is the `arguments` object? How does it differ from rest parameters?
     The `arguments` object is an array-like object (not a true array) accessible inside regular functions (not arrow functions) that contains the values of the arguments passed to that function.
    Differences from rest parameters:
    *   **Type:** `arguments` is array-like; rest parameters create a true array.
    *   **Availability:** `arguments` is not available in arrow functions.
    *   **Specificity:** Rest parameters capture only the "rest" of the arguments not explicitly named, while `arguments` captures all of them.
    *   **Clarity:** Rest parameters are generally more explicit and easier to use.
# What is recursion? Give an example.
     Recursion is a programming technique where a function calls itself directly or indirectly to solve a problem. A recursive function must have a base case to stop the recursion and prevent an infinite loop (stack overflow).
    ```javascript
    // Factorial example
    function factorial(n) {
        if (n === 0 || n === 1) { // Base case
            return 1;
        } else { // Recursive step
            return n * factorial(n - 1);
        }
    }
    console.log(factorial(5)); // 120
    ```
# What is a callback function?
     A callback function is a function passed into another function as an argument, which is then invoked inside the outer function to complete some kind of routine or action. Callbacks are fundamental to asynchronous programming in JavaScript.
    ```javascript
    function processData(data, callback) {
        // ... processing data ...
        const result = data.toUpperCase();
        callback(result);
    }
    processData("hello", function(processedResult) {
        console.log("Processed: " + processedResult); // Processed: HELLO
    });
    ```
# What is "callback hell" and how can it be avoided?
     "Callback hell" (also known as the Pyramid of Doom) refers to deeply nested callback functions, which can make code hard to read, debug, and maintain.
    Avoidance strategies:
    *   **Named Functions:** Break callbacks into smaller, named functions.
    *   **Modularity:** Create modules to handle specific parts of the asynchronous logic.
    *   **Promises (ES6):** Provide a cleaner way to handle asynchronous operations and chaining.
    *   **Async/Await (ES7+):** Syntactic sugar on top of Promises, making asynchronous code look more synchronous.


# How do you create an object in JavaScript?

    *   **Object Literal:**
        ```javascript
        const person = { name: "Alice", age: 30 };
        ```
    *   **`new Object()` Constructor:**
        ```javascript
        const person = new Object();
        person.name = "Bob";
        person.age = 25;
        ```
    *   **Constructor Function:**
        ```javascript
        function Person(name, age) {
            this.name = name;
            this.age = age;
        }
        const person = new Person("Charlie", 35);
        ```
    *   **`Object.create()`:** Creates a new object with a specified prototype object.
        ```javascript
        const proto = { greet: function() { console.log("Hello!"); } };
        const person = Object.create(proto);
        person.name = "David";
        ```
    *   **ES6 Classes:**
        ```javascript
        class Person {
            constructor(name, age) {
                this.name = name;
                this.age = age;
            }
        }
        const person = new Person("Eve", 28);
        ```
# How do you access properties of an object?

    *   **Dot Notation:** `object.propertyName`
        ```javascript
        const person = { name: "Alice" };
        console.log(person.name); // Alice
        ```
    *   **Bracket Notation:** `object['propertyName']` (useful when property name is dynamic or has special characters)
        ```javascript
        const person = { "full-name": "Bob Smith" };
        console.log(person['full-name']); // Bob Smith
        const prop = "full-name";
        console.log(person[prop]); // Bob Smith
        ```
# How do you iterate over an object's properties?

    *   **`for...in` loop:** Iterates over enumerable property names (keys), including inherited ones. Use `hasOwnProperty` to check for own properties.
        ```javascript
        const obj = { a: 1, b: 2 };
        for (const key in obj) {
            if (obj.hasOwnProperty(key)) {
                console.log(key, obj[key]);
            }
        }
        ```
    *   **`Object.keys(obj)`:** Returns an array of an object's own enumerable property names.
    *   **`Object.values(obj)`:** Returns an array of an object's own enumerable property values.
    *   **`Object.entries(obj)`:** Returns an array of an object's own enumerable `[key, value]` pairs.
        ```javascript
        Object.keys(obj).forEach(key => console.log(key, obj[key]));
        Object.values(obj).forEach(value => console.log(value));
        Object.entries(obj).forEach(([key, value]) => console.log(key, value));
        ```
# What is the `hasOwnProperty` method?
     `obj.hasOwnProperty(prop)` returns a boolean indicating whether the object has the specified property as its *own* property (as opposed to inheriting it).
# How do you create an array in JavaScript?

    *   **Array Literal:**
        ```javascript
        const fruits = ["Apple", "Banana", "Cherry"];
        ```
    *   **`new Array()` Constructor:**
        ```javascript
        const numbers = new Array(1, 2, 3);
        const emptyArr = new Array(5); // Creates an array with 5 empty slots
        ```
    *   **`Array.from()`:** Creates a new array instance from an array-like or iterable object.
    *   **`Array.of()`:** Creates a new array instance with a variable number of arguments, regardless of number or type.
# What are some common array methods? (Mention at least 5)

    *   **`push()`:** Adds one or more elements to the end of an array and returns the new length.
    *   **`pop()`:** Removes the last element from an array and returns that element.
    *   **`shift()`:** Removes the first element from an array and returns that element.
    *   **`unshift()`:** Adds one or more elements to the beginning of an array and returns the new length.
    *   **`slice(start, end)`:** Returns a shallow copy of a portion of an array into a new array object. Original array is not modified.
    *   **`splice(start, deleteCount, ...itemsToAdd)`:** Changes the contents of an array by removing or replacing existing elements and/or adding new elements in place. Modifies the original array.
    *   **`concat()`:** Merges two or more arrays. Returns a new array.
    *   **`join(separator)`:** Joins all elements of an array into a string.
    *   **`indexOf(element)`:** Returns the first index at which a given element can be found, or -1 if not present.
    *   **`includes(element)`:** Determines whether an array includes a certain value, returning `true` or `false`.
# Explain `forEach`, `map`, `filter`, and `reduce` array methods.
     These are higher-order functions for array iteration and transformation:
    *   **`forEach(callbackFn)`:** Executes a provided function once for each array element. Does not return a new array; its return value is `undefined`. Primarily used for side effects.
        ```javascript
        [1, 2, 3].forEach(num => console.log(num * 2)); // Logs 2, 4, 6
        ```
    *   **`map(callbackFn)`:** Creates a *new array* populated with the results of calling a provided function on every element in the calling array.
        ```javascript
        const numbers = [1, 2, 3];
        const doubled = numbers.map(num => num * 2); // doubled is [2, 4, 6]
        ```
    *   **`filter(callbackFn)`:** Creates a *new array* with all elements that pass the test implemented by the provided function.
        ```javascript
        const numbers = [1, 2, 3, 4, 5];
        const evens = numbers.filter(num => num % 2 === 0); // evens is [2, 4]
        ```
    *   **`reduce(callbackFn, initialValue)`:** Executes a "reducer" function on each element of the array, resulting in a single output value. The reducer function takes an accumulator and the current value as arguments.
        ```javascript
        const numbers = [1, 2, 3, 4];
        const sum = numbers.reduce((accumulator, currentValue) => accumulator + currentValue, 0); // sum is 10
        ```
# What is array destructuring? Give an example.
     Array destructuring (ES6) is a syntax that allows unpacking values from arrays, or properties from objects, into distinct variables.
    ```javascript
    const numbers = [10, 20, 30, 40, 50];

    // Basic destructuring
    const [first, second] = numbers;
    console.log(first);  // 10
    console.log(second); // 20

    // Skipping elements
    const [, , third] = numbers;
    console.log(third); // 30

    // Using rest operator
    const [one, two, ...rest] = numbers;
    console.log(one);  // 10
    console.log(two);  // 20
    console.log(rest); // [30, 40, 50]
    ```
# What is object destructuring? Give an example.
     Object destructuring (ES6) allows unpacking properties from objects into distinct variables.
    ```javascript
    const person = {
        firstName: "John",
        lastName: "Doe",
        age: 30,
        address: {
            city: "New York",
            country: "USA"
        }
    };

    // Basic destructuring
    const { firstName, age } = person;
    console.log(firstName); // John
    console.log(age);       // 30

    // Assigning to new variable names
    const { lastName: surname } = person;
    console.log(surname); // Doe

    // Nested destructuring
    const { address: { city } } = person;
    console.log(city); // New York

    // Default values
    const { middleName = "N/A" } = person;
    console.log(middleName); // N/A
    ```
# What is the spread operator (`...`)? How is it used with arrays and objects?
     The spread operator (ES6) allows an iterable (like an array or string) to be expanded in places where zero or more arguments (for function calls) or elements (for array literals) are expected. For objects (ES2018+), it copies an object's own enumerable properties into a new object.
    *   **With Arrays:**
        ```javascript
        const arr1 = [1, 2, 3];
        const arr2 = [4, 5, 6];
        const combined = [...arr1, ...arr2, 7]; // [1, 2, 3, 4, 5, 6, 7] - Concatenation
        const copy = [...arr1];                 // [1, 2, 3] - Shallow copy

        function sum(x, y, z) { return x + y + z; }
        sum(...arr1); // Equivalent to sum(1, 2, 3)
        ```
    *   **With Objects (ES2018+):**
        ```javascript
        const obj1 = { a: 1, b: 2 };
        const obj2 = { b: 3, c: 4 };
        const merged = { ...obj1, ...obj2 }; // { a: 1, b: 3, c: 4 } (obj2's b overwrites obj1's)
        const cloned = { ...obj1 };           // { a: 1, b: 2 } - Shallow clone
        ```
# How can you check if a variable is an array?
     The most reliable way is `Array.isArray()`:
    ```javascript
    const arr = [1, 2, 3];
    const obj = { a: 1 };
    console.log(Array.isArray(arr)); // true
    console.log(Array.isArray(obj)); // false
    ```
    Using `instanceof Array` can also work but might fail across different `<iframe>` or realm contexts. `typeof []` returns `"object"`, which is not specific enough.
# What is the difference between shallow copy and deep copy of an object/array? How can you achieve them?

    *   **Shallow Copy:** Creates a new object/array, but if it contains nested objects/arrays, only references to those nested structures are copied, not the structures themselves. Modifying a nested object in the copy will also affect the original.
        *   Achieved using: Spread operator (`...`), `Object.assign()`, `Array.prototype.slice()`, `Array.from()`.
    *   **Deep Copy:** Creates a new object/array and recursively copies all nested objects/arrays, so the copy is completely independent of the original.
        *   Achieved using:
            *   `JSON.parse(JSON.stringify(obj))` (Simple but has limitations: loses functions, `undefined`, `Date` objects become strings, etc.)
            *   Libraries like Lodash (`_.cloneDeep()`).
            *   Custom recursive function.
            *   `structuredClone()` (modern browser API for deep cloning)
# What are `Object.freeze()`, `Object.seal()`, and `Object.preventExtensions()`?

    *   **`Object.preventExtensions(obj)`:** Prevents new properties from being added to an object. Existing properties can still be modified or deleted.
    *   **`Object.seal(obj)`:** Prevents new properties from being added and marks all existing properties as non-configurable (meaning they cannot be deleted, and their descriptors cannot be changed, but their values can still be changed if they are writable).
    *   **`Object.freeze(obj)`:** Prevents new properties from being added, makes all existing properties non-configurable, and makes all data properties non-writable. The object becomes effectively immutable (at a shallow level; nested objects are not automatically frozen).

# What is JSON? What are `JSON.stringify()` and `JSON.parse()`?

    *   **JSON (JavaScript Object Notation):** A lightweight data-interchange format. It's easy for humans to read and write and easy for machines to parse and generate. It's based on a subset of JavaScript syntax but is language-independent.
    *   **`JSON.stringify(value)`:** Converts a JavaScript value (object, array, primitive) into a JSON string. Functions and `undefined` values are typically omitted or converted to `null` (in arrays).
    *   **`JSON.parse(text)`:** Parses a JSON string, constructing the JavaScript value or object described by the string.
# What are JavaScript `Map` and `Set` objects (ES6)?

    *   **`Set`:** A collection of unique values of any type. Values in a `Set` can only occur once.
        ```javascript
        const mySet = new Set([1, 2, 2, 3, "hello", "hello"]);
        console.log(mySet); // Set(4) { 1, 2, 3, 'hello' }
        mySet.add(4);
        mySet.has(2); // true
        ```
    *   **`Map`:** A collection of key-value pairs where keys can be of any data type (unlike object literals where keys are strings or Symbols). Remembers the original insertion order of the keys.
        ```javascript
        const myMap = new Map();
        const keyObj = {};
        myMap.set("name", "Alice");
        myMap.set(keyObj, "An object key");
        console.log(myMap.get("name")); // Alice
        console.log(myMap.get(keyObj)); // An object key
        ```
# How to find an element in an array?

    *   **`indexOf(element, fromIndex)`:** Returns the first index of an element, or -1.
    *   **`lastIndexOf(element, fromIndex)`:** Returns the last index of an element, or -1.
    *   **`includes(element, fromIndex)`:** Returns `true` if element is found, `false` otherwise. (ES7)
    *   **`find(callbackFn)`:** Returns the *value* of the first element that satisfies the testing function, or `undefined`.
    *   **`findIndex(callbackFn)`:** Returns the *index* of the first element that satisfies the testing function, or -1.
    ```javascript
    const arr = [10, 20, 30, 20];
    console.log(arr.indexOf(20)); // 1
    console.log(arr.includes(30)); // true
    const found = arr.find(el => el > 25); // 30
    ```
# How to remove an element from an array?

    *   **`pop()`:** Removes the last element.
    *   **`shift()`:** Removes the first element.
    *   **`splice(startIndex, deleteCount)`:** Removes elements from a specific index. Modifies the original array.
        ```javascript
        const arr = [1, 2, 3, 4, 5];
        arr.splice(2, 1); // Removes 1 element starting at index 2. arr is now [1, 2, 4, 5]
        ```
    *   **`filter()` (to create a new array without the element):**
        ```javascript
        const arr = [1, 2, 3, 2, 4];
        const newArr = arr.filter(el => el !== 2); // newArr is [1, 3, 4]
        ```
# How to check if an object is empty?

    ```javascript
    function isEmptyObject(obj) {
        if (obj === null || typeof obj !== 'object') return true; // Or handle as an error
        return Object.keys(obj).length === 0 && obj.constructor === Object;
    }
    console.log(isEmptyObject({})); // true
    console.log(isEmptyObject({a:1})); // false
    console.log(isEmptyObject(new Date())); // false (constructor check)
    ```
    A simpler check if you only care about own enumerable properties:
    `Object.keys(obj).length === 0`
# What does `Object.keys()`, `Object.values()`, `Object.entries()` do?

    *   `Object.keys(obj)`: Returns an array of a given object's own enumerable property *names* (keys).
    *   `Object.values(obj)`: Returns an array of a given object's own enumerable property *values*.
    *   `Object.entries(obj)`: Returns an array of a given object's own enumerable string-keyed property *[key, value] pairs*.
# How can you create an immutable object or array in JavaScript (conceptually)?
     True immutability is hard to enforce deeply without libraries.
    *   **`Object.freeze()`:** Provides shallow immutability for objects. For arrays, it prevents adding/removing elements, but elements themselves (if objects/arrays) can still be mutated.
    *   **Discipline/Convention:** Always create new objects/arrays instead of modifying existing ones when making changes (e.g., using spread operator, `slice`, `map`, `filter`).
    *   **Libraries:** Use libraries like Immer or Immutable.js which provide efficient immutable data structures.
    ```javascript
    // Conceptual example with spread for arrays
    const originalArray = [1, 2, { val: 3 }];
    const newArray = [...originalArray, 4]; // Adding an element
    const modifiedArray = originalArray.map(item =>
        item.val === 3 ? { ...item, val: 30 } : item
    ); // "Modifying" a nested object immutably
    ```
# What is synchronous vs. asynchronous programming?

    *   **Synchronous:** Code execution happens in sequence. Each operation must complete before the next one starts. If an operation takes a long time, it blocks further execution.
    *   **Asynchronous:** Code execution does not necessarily happen in sequence. Operations (especially I/O-bound like network requests, file system operations) can be initiated, and the program can continue to execute other tasks while waiting for these operations to complete. When an async operation finishes, a callback, Promise, or other mechanism handles the result.
# Explain the JavaScript Event Loop.
     The JavaScript Event Loop is a mechanism that allows Node.js or the browser to perform non-blocking I/O operations despite JavaScript being single-threaded.
    It consists of:
    *   **Call Stack:** Where JavaScript code is executed. Functions are pushed onto the stack when called and popped off when they return.
    *   **Web APIs / C++ APIs (Node.js):** Handles asynchronous operations (e.g., `setTimeout`, DOM events, `fetch`). When an async task completes, its callback is placed in a queue.
    *   **Callback Queue (Task Queue / Message Queue):** Holds callback functions ready to be executed once the Call Stack is empty.
    *   **Microtask Queue (Job Queue):** Holds callbacks from Promises (`.then`, `.catch`, `.finally`) and `MutationObserver`. Microtasks have higher priority and are executed after the current script and before the browser renders or any new tasks from the Callback Queue are processed.
    The Event Loop continuously checks if the Call Stack is empty. If it is, it takes the first task from the Microtask Queue (if any) and pushes it onto the Call Stack. If the Microtask Queue is empty, it takes a task from the Callback Queue.
# What are Promises? What states can a Promise be in?
     A Promise is an object representing the eventual completion (or failure) of an asynchronous operation and its resulting value. It's a placeholder for a future value.
    States:
    1.  **`pending`**: Initial state, neither fulfilled nor rejected.
    2.  **`fulfilled` (or `resolved`)**: The operation completed successfully, and the Promise has a resulting value.
    3.  **`rejected`**: The operation failed, and the Promise has a reason for the failure.
    A promise is *settled* if it's either fulfilled or rejected (i.e., not pending).
# How do you use Promises? (e.g., `then`, `catch`, `finally`)

    *   **`promise.then(onFulfilled, onRejected)`:** Attaches callbacks to handle the fulfilled or rejected state of the Promise. `onFulfilled` is called with the resolved value, and `onRejected` (optional) is called with the rejection reason. Returns a new Promise, allowing chaining.
    *   **`promise.catch(onRejected)`:** A shorthand for `promise.then(null, onRejected)`. Attaches a callback to handle only the rejected state. Also returns a new Promise.
    *   **`promise.finally(onFinally)`:** Attaches a callback that is executed when the Promise is settled (either fulfilled or rejected). It receives no arguments. Useful for cleanup. Returns a new Promise.
    ```javascript
    function fetchData() {
        return new Promise((resolve, reject) => {
            setTimeout(() => {
                const success = Math.random() > 0.5;
                if (success) {
                    resolve("Data fetched successfully!");
                } else {
                    reject(new Error("Failed to fetch data."));
                }
            }, 1000);
        });
    }

    fetchData()
        .then(data => {
            console.log(data); // "Data fetched successfully!"
            return data.toUpperCase(); // Value passed to next .then
        })
        .then(processedData => {
            console.log("Processed:", processedData);
        })
        .catch(error => {
            console.error(error.message); // "Failed to fetch data."
        })
        .finally(() => {
            console.log("Fetch operation finished.");
        });
    ```
# What is `async/await`? How does it relate to Promises?
     `async/await` (ES2017) is syntactic sugar built on top of Promises, making asynchronous code look and behave a bit more like synchronous code, which can improve readability.
    *   **`async` keyword:** Used to declare an asynchronous function. An `async` function implicitly returns a Promise. If the function returns a value, the Promise will be resolved with that value. If it throws an error, the Promise will be rejected.
    *   **`await` keyword:** Can only be used inside an `async` function. It pauses the execution of the `async` function until the Promise it's waiting for is settled (resolved or rejected). If the Promise resolves, `await` returns the resolved value. If it rejects, `await` throws the rejection reason (which can be caught with `try...catch`).
    ```javascript
    async function fetchDataAsync() {
        try {
            console.log("Fetching data...");
            const data = await fetchData(); // fetchData is the Promise-returning function from above
            console.log(data);
            const processedData = data.toUpperCase();
            console.log("Processed:", processedData);
            return processedData;
        } catch (error) {
            console.error("Error in async function:", error.message);
            throw error; // Re-throw if needed
        } finally {
            console.log("Async fetch operation finished.");
        }
    }
    fetchDataAsync().then(result => console.log("Final result:", result));
    ```
# How do you handle errors in `async/await`?
     Errors in `async/await` are typically handled using standard `try...catch...finally` blocks, just like synchronous code. When an `await`ed Promise rejects, it throws an error that can be caught by a `catch` block.
    ```javascript
    async function riskyOperation() {
        if (Math.random() < 0.5) {
            throw new Error("Something went wrong!");
        }
        return "Success!";
    }

    async function performTask() {
        try {
            const result = await riskyOperation();
            console.log(result);
        } catch (error) {
            console.error("Caught an error:", error.message);
        }
    }
    performTask();
    ```
# What is `Promise.all()`?
     `Promise.all(iterable)` takes an iterable (e.g., an array) of Promises and returns a single new Promise. This new Promise fulfills when *all* of the input Promises have fulfilled, with an array of their fulfillment values (in the same order as the input Promises). It rejects immediately if *any* of the input Promises reject, with the reason of the first Promise that rejected.
# What is `Promise.race()`?
     `Promise.race(iterable)` takes an iterable of Promises and returns a single new Promise. This new Promise settles (fulfills or rejects) as soon as *one* of the input Promises settles, with the value or reason from that first-settled Promise.
# What is `Promise.allSettled()`?
     `Promise.allSettled(iterable)` (ES2020) takes an iterable of Promises and returns a single new Promise. This new Promise fulfills when *all* of the input Promises have settled (either fulfilled or rejected). The fulfillment value is an array of objects, each describing the outcome of one Promise in the input iterable. Each result object has a `status` (`"fulfilled"` or `"rejected"`) and either a `value` (if fulfilled) or a `reason` (if rejected). Unlike `Promise.all()`, it doesn't short-circuit on rejection.
# What is `Promise.any()`?
     `Promise.any(iterable)` (ES2021) takes an iterable of Promises and returns a single new Promise. This new Promise fulfills as soon as *one* of the input Promises fulfills, with the value of that first fulfilled Promise. If *all* of the input Promises reject, then the returned Promise is rejected with an `AggregateError`, a new type of error object that groups together individual errors.
# Can you explain `setTimeout(callback, delay)` and `setInterval(callback, delay)`?

    *   **`setTimeout(callback, delay)`:** Executes a `callback` function once after a specified `delay` (in milliseconds). It returns a timeout ID which can be used with `clearTimeout()` to cancel the timeout.
    *   **`setInterval(callback, delay)`:** Repeatedly executes a `callback` function with a fixed `delay` (in milliseconds) between each call. It returns an interval ID which can be used with `clearInterval()` to stop the repetitions.
# What's the difference between the Microtask Queue and the Macrotask (Callback) Queue?

    *   **Macrotask Queue (Task Queue / Callback Queue):** Holds tasks like `setTimeout`, `setInterval` callbacks, I/O operations, UI rendering (in browsers).
    *   **Microtask Queue (Job Queue):** Holds tasks like Promise callbacks (`.then`, `.catch`, `.finally`), `queueMicrotask()`, `MutationObserver` callbacks.
    **Execution Order:** The Event Loop prioritizes the Microtask Queue. After each macrotask from the Callback Queue finishes (and the call stack becomes empty), the Event Loop processes *all* tasks currently in the Microtask Queue before moving on to the next macrotask or rendering. This means microtasks can starve macrotasks if they keep adding more microtasks.
# What is `fetch()` API? How does it differ from `XMLHttpRequest`?
     The `fetch()` API is a modern JavaScript interface for making network requests (e.g., HTTP requests to servers). It's Promise-based.
    Differences from `XMLHttpRequest` (XHR):
    *   **Promises:** `fetch()` returns a Promise, making it easier to work with `async/await` and chain operations. XHR is event-based.
    *   **Simpler API:** `fetch()` has a cleaner and more straightforward API for common use cases.
    *   **CORS:** `fetch()` handles CORS more gracefully by default (e.g., opaque responses for cross-origin no-CORS requests).
    *   **Streaming:** `fetch()` supports Request and Response streaming.
    *   **Error Handling:** `fetch()` only rejects its Promise on network errors (e.g., DNS failure, no connection). HTTP error statuses (like 404 or 500) are *not* considered network errors, so the Promise resolves. You need to check `response.ok` or `response.status`. XHR handles HTTP errors through its `onerror` or by checking status in `onreadystatechange`.
# How do you make a POST request using `fetch()`?

    ```javascript
    async function postData(url = '', data = {}) {
        const response = await fetch(url, {
            method: 'POST', // *GET, POST, PUT, DELETE, etc.
            headers: {
                'Content-Type': 'application/json'
                // 'Content-Type': 'application/x-www-form-urlencoded',
            },
            body: JSON.stringify(data) // body data type must match "Content-Type" header
        });
        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }
        return response.json(); // parses JSON response into native JavaScript objects
    }

    postData('https://api.example.com/submit', { item: 'test' })
        .then(data => {
            console.log(data);
        })
        .catch(error => {
            console.error('Error:', error);
        });
    ```
# What are Web Workers? Why use them?
     Web Workers allow you to run JavaScript code in a background thread, separate from the main browser thread (which handles UI updates and user interactions).
    **Why use them?**
    *   **Prevent UI Freezing:** Offload long-running or CPU-intensive tasks to a worker thread, preventing the main thread from becoming unresponsive and the UI from freezing.
    *   **Improved Performance:** Can lead to better perceived performance and smoother user experience for complex applications.
    Workers communicate with the main thread using `postMessage()` and the `onmessage` event handler. They do not have direct access to the DOM.


# What is prototypal inheritance in JavaScript?
     Prototypal inheritance is a feature in JavaScript where objects can inherit properties and methods from other objects (their "prototype"). Each object has a private property (often accessible via `__proto__` or `Object.getPrototypeOf()`) that links to its prototype. If a property is accessed on an object and not found, JavaScript looks up the prototype chain until the property is found or the chain ends (at `null`).
# What is the `prototype` property on functions?
     Every JavaScript function (except arrow functions) has a `prototype` property. This property is an object that becomes the prototype for all instances created when that function is used as a constructor (with the `new` keyword). Methods and properties added to a constructor function's `prototype` object are shared among all instances created by that constructor.
# What is the difference between `__proto__` (or `[[Prototype]]`) and `prototype`?

    *   **`prototype`:** Is a property of a *constructor function*. It's the object that will become the prototype for objects created by that constructor.
    *   **`__proto__` (or `[[Prototype]]` internal slot, accessible via `Object.getPrototypeOf()`):** Is an internal property of an *object instance*. It's a reference to the object's prototype (i.e., the constructor function's `prototype` object from which it was instantiated, or another object if set via `Object.create()`).
# How does the `new` keyword work?
     When `new` is used with a constructor function (e.g., `new Person(...)`):
    1.  A new, empty plain JavaScript object is created.
    2.  This new object's `[[Prototype]]` (or `__proto__`) is linked to the constructor function's `prototype` object.
    3.  The constructor function is called with `this` bound to the newly created object.
    4.  If the constructor function does not explicitly return an object, the newly created object (`this`) is returned. Otherwise, the explicitly returned object is used.
# What are ES6 Classes? How do they relate to prototypal inheritance?
     ES6 Classes provide a cleaner, more familiar syntax (for those coming from class-based languages) for creating objects and implementing inheritance. They are primarily syntactic sugar over JavaScript's existing prototypal inheritance mechanism.
    ```javascript
    class Animal {
        constructor(name) {
            this.name = name;
        }
        speak() {
            console.log(`${this.name} makes a noise.`);
        }
    }

    class Dog extends Animal { // 'extends' sets up the prototype chain
        constructor(name, breed) {
            super(name); // Calls the parent class's constructor
            this.breed = breed;
        }
        speak() { // Overrides parent method
            console.log(`${this.name} barks.`);
        }
    }

    const myDog = new Dog("Rex", "German Shepherd");
    myDog.speak(); // Rex barks.
    console.log(myDog instanceof Dog);    // true
    console.log(myDog instanceof Animal); // true
    ```
    Under the hood, `Dog.prototype` will inherit from `Animal.prototype`.
# Explain `super()` in ES6 classes.

    *   In a subclass constructor, `super()` must be called *before* `this` can be used. It calls the constructor of the parent class and binds `this` to the new instance correctly.
    *   In a subclass method, `super.methodName()` can be used to call the corresponding method on the parent class.
# What are static methods and properties in ES6 classes?
     Static methods and properties belong to the class itself, not to instances of the class. They are called directly on the class, not on an instance. They are often utility functions or properties related to the class but not specific to any instance.
    ```javascript
    class MathHelper {
        static PI = 3.14159; // Static property (ES2022 proposal, widely supported)

        static add(x, y) { // Static method
            return x + y;
        }
    }
    console.log(MathHelper.PI);    // 3.14159
    console.log(MathHelper.add(5, 3)); // 8
    // const helper = new MathHelper();
    // helper.add(1,1) // This would be an error
    ```
# Can you achieve private members in ES6 classes?
     Yes, with ES2022 private class fields:
    *   **Private instance fields:** Declared with a `#` prefix (e.g., `#myPrivateField`). They are only accessible from within the class.
    *   **Private methods:** Also declared with a `#` prefix (e.g., `#myPrivateMethod()`).
    ```javascript
    class Counter {
        #count = 0; // Private instance field

        constructor(initialCount = 0) {
            this.#count = initialCount;
        }

        #increment() { // Private method
            this.#count++;
        }

        incrementPublic() {
            this.#increment();
        }

        getCount() {
            return this.#count;
        }
    }

    const c = new Counter(5);
    c.incrementPublic();
    console.log(c.getCount()); // 6
    // console.log(c.#count); // SyntaxError: Private field '#count' must be declared in an enclosing class
    // c.#increment();        // SyntaxError
    ```
    Before this feature, "privacy" was often achieved through conventions (e.g., `_` prefix) or closures (in factory functions/module pattern).
# What is method overriding in classes?
     Method overriding occurs when a subclass provides a specific implementation for a method that is already defined in its superclass. The subclass's method "overrides" the superclass's method for instances of the subclass.
    ```javascript
    class Animal {
      speak() { console.log("Animal sound"); }
    }
    class Dog extends Animal {
      speak() { console.log("Woof!"); } // Overrides Animal's speak
    }
    const dog = new Dog();
    dog.speak(); // Woof!
    ```
# What is the `instanceof` operator?
     The `instanceof` operator tests whether the `prototype` property of a constructor appears anywhere in the prototype chain of an object.
    ```javascript
    function Car() {}
    const myCar = new Car();
    console.log(myCar instanceof Car);    // true
    console.log(myCar instanceof Object); // true (Object is at the top of the prototype chain)
    console.log([] instanceof Array);     // true
    console.log([] instanceof Object);    // true
    ```


# What is the DOM (Document Object Model)?
     The DOM is a programming interface for web documents (HTML, XML). It represents the page structure as a tree of objects (nodes), where each node represents a part of the document (e.g., an element, text, comment). JavaScript can interact with the DOM to dynamically read and modify the content, structure, and style of a web page.
# How do you select an HTML element using JavaScript? (Mention a few ways)

    *   `document.getElementById('elementId')`: Selects an element by its ID.
    *   `document.getElementsByClassName('className')`: Returns an HTMLCollection of elements with the given class name.
    *   `document.getElementsByTagName('tagName')`: Returns an HTMLCollection of elements with the given tag name.
    *   `document.querySelector('cssSelector')`: Returns the *first* element that matches the specified CSS selector.
    *   `document.querySelectorAll('cssSelector')`: Returns a static NodeList representing a list of elements that match the specified CSS selector.
# What's the difference between an HTMLCollection and a NodeList?

    *   **HTMLCollection:** A *live* collection of elements. If the DOM changes (e.g., an element is added or removed that matches the collection's criteria), the HTMLCollection updates automatically. It typically contains only Element nodes. Methods like `getElementsByClassName` and `getElementsByTagName` return HTMLCollections.
    *   **NodeList:** Can be *live* or *static*.
        *   `document.childNodes` is live.
        *   `document.querySelectorAll()` returns a *static* NodeList. This means it's a snapshot of the elements at the time the query was made and doesn't update if the DOM changes.
        *   NodeLists can contain any node type (Element, Text, Comment nodes), not just Element nodes.
    Both are array-like but not true arrays (though they have a `length` property and can be iterated). You can convert them to arrays using `Array.from()` or the spread operator.
# How do you add/remove/modify attributes of an HTML element?

    *   **Get Attribute:** `element.getAttribute('attributeName')`
    *   **Set Attribute:** `element.setAttribute('attributeName', 'value')`
    *   **Remove Attribute:** `element.removeAttribute('attributeName')`
    *   **Check for Attribute:** `element.hasAttribute('attributeName')`
    *   For common attributes like `id`, `class`, `src`, you can also access them as direct properties: `element.id`, `element.className`, `element.src`.
# How do you change the content of an HTML element?

    *   **`element.innerHTML`:** Gets or sets the HTML content (markup) within an element. Can be a security risk if setting content from untrusted sources (XSS).
    *   **`element.textContent`:** Gets or sets the text content of an element and its descendants, ignoring HTML tags. Safer for setting plain text.
    *   **`element.innerText`:** Similar to `textContent` but is aware of CSS styling (e.g., won't include text from hidden elements) and can be slower. `textContent` is generally preferred for performance and consistency.
# How do you add an event listener to an HTML element?
     Using `element.addEventListener(eventType, listenerFunction, useCaptureOrOptions)`:
    ```javascript
    const myButton = document.getElementById('myBtn');
    function handleClick() {
        console.log('Button clicked!');
    }
    myButton.addEventListener('click', handleClick);

    // To remove:
    // myButton.removeEventListener('click', handleClick);
    ```
    `eventType` is a string like `'click'`, `'mouseover'`, `'keydown'`.
    `listenerFunction` is the function to call when the event occurs.
    `useCaptureOrOptions` can be a boolean (for capture phase) or an options object (e.g., `{ capture: true, once: true, passive: true }`).
# What is event bubbling and event capturing?
     These are two phases of event propagation in the DOM:
    *   **Capturing Phase:** When an event occurs on an element, the browser first checks if any ancestors have registered a capturing listener for that event type, starting from the `window` down to the target element's parent.
    *   **Target Phase:** The event reaches the target element where it originated.
    *   **Bubbling Phase:** After the target phase, the event "bubbles" up through the target's ancestors, from the parent up to the `window`. Listeners registered for bubbling (default) will be triggered.
    You can control which phase your listener activates in using the third argument of `addEventListener`. `true` for capturing, `false` (or omitted) for bubbling.
# What is `event.preventDefault()` and `event.stopPropagation()`?

    *   **`event.preventDefault()`:** If an event is cancelable (e.g., a form submission, a link click), calling this method prevents the browser's default action associated with that event.
    *   **`event.stopPropagation()`:** Prevents further propagation of the current event in the capturing and bubbling phases. The event will not trigger listeners on any other elements further up or down the DOM tree.
# What is `localStorage` and `sessionStorage`? What are their differences?
     Both are Web Storage APIs that allow websites to store key-value pairs locally within the user's browser.
    *   **`localStorage`:**
        *   Persists data even after the browser window is closed and reopened.
        *   Data is specific to the protocol/host/port (origin).
        *   Storage limit is typically around 5-10MB.
        *   Data is never sent to the server automatically.
    *   **`sessionStorage`:**
        *   Persists data only for the duration of the page session (while the browser tab is open). Data is cleared when the tab/window is closed.
        *   Data is specific to the origin and also isolated per browser tab/window.
        *   Storage limit is similar to `localStorage`.
        *   Data is never sent to the server automatically.
    Both store data as strings. You need `JSON.stringify()` and `JSON.parse()` for objects.
# What are cookies? How do they differ from Web Storage?

    *   **Cookies:** Small pieces of data stored by the browser for a specific domain. They are sent with every HTTP request to that domain.
    Differences from Web Storage (`localStorage`/`sessionStorage`):
    *   **Purpose:** Cookies are primarily for server-side communication (session management, tracking), while Web Storage is for client-side storage.
    *   **Data Transfer:** Cookies are sent with every HTTP request, potentially increasing request size. Web Storage data is not automatically sent.
    *   **Capacity:** Cookies have a smaller capacity (around 4KB). Web Storage has more (5-10MB).
    *   **Accessibility:** Cookies can be accessed on both server-side and client-side (if not `HttpOnly`). Web Storage is client-side only.
    *   **Expiration:** Cookies can have an expiration date. `localStorage` persists indefinitely, `sessionStorage` for the session.


# How do you handle errors in JavaScript?

    *   **`try...catch...finally` Statement:**
        *   `try`: Code block to attempt execution.
        *   `catch (error)`: Code block to execute if an error occurs in the `try` block. The `error` object contains information about the error.
        *   `finally`: Code block that executes regardless of whether an error occurred or not. Useful for cleanup.
    *   **`throw` Statement:** Manually throws a custom error. You can throw any expression (string, number, boolean, object). It's best to throw `Error` objects or instances of `Error` subclasses.
    *   **Window `onerror` Handler (Browser):** A global error handler for uncaught exceptions in the browser.
    *   **Node.js `process.on('uncaughtException')`:** Global error handler for uncaught exceptions in Node.js.
    *   **Promise `.catch()` and `async/await` `try/catch`:** For handling asynchronous errors.
# What are common types of errors in JavaScript?

    *   **`SyntaxError`:** Occurs when the JavaScript engine encounters code that violates the language's syntax rules (e.g., missing parenthesis, invalid keyword usage). Caught during parsing, before execution.
    *   **`ReferenceError`:** Occurs when trying to access a variable that has not been declared or is outside the current scope.
    *   **`TypeError`:** Occurs when an operation is performed on a value of an inappropriate type (e.g., calling a non-function, accessing properties of `null` or `undefined`).
    *   **`RangeError`:** Occurs when a numeric variable or parameter is outside its valid range (e.g., invalid array length).
    *   **`URIError`:** Occurs when global URI handling functions (like `encodeURIComponent()`) are used incorrectly.
    *   Custom errors can also be created by extending the `Error` object.
# What is "strict mode" (`'use strict';`) and what are some of its benefits?
     `'use strict';` is a directive that enables strict mode for JavaScript code.
    Benefits:
    *   **Changes "bad syntax" into real errors:** For example, assigning to a non-writable property, deleting an undeletable property, or using duplicate parameter names now throws errors.
    *   **Prevents accidental globals:** Assigning a value to an undeclared variable throws a `ReferenceError` instead of creating a global variable.
    *   **Makes `eval()` and `arguments` safer and less weird.**
    *   **`this` is `undefined` in functions called without context (not methods), instead of the global object.**
    *   **Prevents use of reserved keywords like `implements`, `interface`, `let`, `package`, `private`, `protected`, `public`, `static`, and `yield` as variable names.**
    *   Generally makes code more secure, robust, and potentially optimizable.
# What are JavaScript Modules (ES6 Modules)? (`import`/`export`)
     ES6 Modules allow you to break your code into separate files (modules) for better organization, reusability, and maintainability.
    *   **`export`:** Used to make variables, functions, or classes available from a module to other modules.
        *   **Named exports:** `export const myVar = ...; export function myFunc() {...}`
        *   **Default export:** `export default function myFunc() {...}` (only one per module)
    *   **`import`:** Used to bring exported members from another module into the current module's scope.
        *   **Named imports:** `import { myVar, myFunc } from './myModule.js';`
        *   **Default import:** `import myDefaultFunc from './myModule.js';`
        *   **Namespace import:** `import * as MyModule from './myModule.js';`
    Modules are loaded asynchronously in browsers (if using `<script type="module">`) and have their own scope.
# What is tree shaking?
     Tree shaking is a term commonly used in the context of JavaScript module bundlers (like Webpack, Rollup, Parcel). It's a dead code elimination process. It works by analyzing `import` and `export` statements to detect which parts of your code (or imported libraries) are actually being used. Unused code is then "shaken off" from the final bundle, resulting in smaller bundle sizes and faster load times. This is particularly effective with ES6 modules due to their static structure.
# What is the `Symbol` data type? Why might you use it?
     `Symbol` (ES6) is a primitive data type whose instances are unique and immutable. Every `Symbol()` call returns a new, unique Symbol.
    **Use Cases:**
    *   **Unique Object Property Keys:** To create "hidden" or non-enumerable properties on objects that won't collide with string keys (e.g., for metadata or internal properties).
    *   **Well-known Symbols:** JavaScript defines several built-in Symbols (e.g., `Symbol.iterator`, `Symbol.hasInstance`) that allow you to customize certain built-in language behaviors for your objects.
# What is memoization? Provide a simple example.
     Memoization is an optimization technique where the results of expensive function calls are cached (stored) and returned when the same inputs occur again, avoiding redundant computation.
    ```javascript
    function memoize(fn) {
        const cache = {};
        return function(...args) {
            const key = JSON.stringify(args); // Create a unique key from arguments
            if (cache[key]) {
                console.log("Fetching from cache for key:", key);
                return cache[key];
            } else {
                console.log("Calculating result for key:", key);
                const result = fn(...args);
                cache[key] = result;
                return result;
            }
        };
    }

    function slowAdd(a, b) {
        // Simulate a slow operation
        for (let i = 0; i < 1e8; i++) {}
        return a + b;
    }

    const memoizedAdd = memoize(slowAdd);
    console.log(memoizedAdd(2, 3)); // Calculating result for key: [2,3] -> 5
    console.log(memoizedAdd(2, 3)); // Fetching from cache for key: [2,3] -> 5
    console.log(memoizedAdd(5, 5)); // Calculating result for key: [5,5] -> 10
    ```
# What is the `console` object and some of its useful methods beyond `log()`?
     The `console` object provides access to the browser's debugging console or Node.js console.
    Useful methods:
    *   `console.log()`: General output of logging information.
    *   `console.error()`: Outputs an error message. Often styled differently.
    *   `console.warn()`: Outputs a warning message.
    *   `console.info()`: Outputs an informational message.
    *   `console.table(data)`: Displays tabular data as a table.
    *   `console.dir(object)`: Displays an interactive list of the properties of a JavaScript object.
    *   `console.group(label)` / `console.groupEnd()`: Creates a new inline group, indenting all following output. Call `groupEnd` to exit the group.
    *   `console.groupCollapsed(label)`: Similar to `group` but starts collapsed.
    *   `console.time(label)` / `console.timeEnd(label)`: Starts a timer to track how long an operation takes.
    *   `console.trace()`: Outputs a stack trace.
    *   `console.assert(assertion, message)`: Logs a message and stack trace if an assertion is false.
    *   `console.clear()`: Clears the console.
# What are some common JavaScript performance optimization techniques?

    *   **Minimize DOM Manipulation:** DOM operations are expensive. Batch updates, use DocumentFragments, or update elements off-DOM.
    *   **Debouncing and Throttling:** For event handlers that fire frequently (e.g., scroll, resize, keyup).
        *   *Debounce:* Delays function execution until a certain amount of time has passed without the event firing.
        *   *Throttle:* Ensures a function is called at most once per specified interval.
    *   **Efficient Looping:** Use appropriate loops. `for` loops are often faster than `forEach` for very large arrays in performance-critical sections.
    *   **Code Splitting / Lazy Loading:** Load only the JavaScript needed for the current view/functionality.
    *   **Tree Shaking / Dead Code Elimination:** Remove unused code.
    *   **Caching / Memoization:** Store results of expensive computations.
    *   **Optimize Images and Assets:** Compress images, use appropriate formats.
    *   **Use Web Workers for CPU-intensive tasks.**
    *   **Avoid Global Variables:** Faster lookup for local variables.
    *   **Minimize Reflows and Repaints:** Changing styles or layout can trigger these.
    *   **Use efficient data structures (e.g., `Map`, `Set` when appropriate).**
# If you encountered a bug in production JavaScript code, what would be your general approach to debugging it?

    1.  **Reproduce the Bug:** Try to reliably reproduce the issue in a development or staging environment. Understand the steps that lead to it.
    2.  **Gather Information:**
        *   Check browser console for error messages, stack traces, and logs.
        *   Note the browser version, OS, and any specific user actions.
        *   If server-side (Node.js), check server logs.
    3.  **Isolate the Problem:**
        *   Use `console.log` statements or the debugger to trace variable values and execution flow around the suspected area.
        *   Comment out sections of code to narrow down where the error originates.
        *   If using version control (like Git), check recent changes (`git blame`, `git bisect`) that might have introduced the bug.
    4.  **Use Browser Developer Tools:**
        *   **Debugger:** Set breakpoints, step through code, inspect variables, watch expressions.
        *   **Network Tab:** Inspect API requests and responses if it's a data-related bug.
        *   **Elements Tab:** Inspect DOM structure and styles if it's a UI bug.
    5.  **Formulate a Hypothesis:** Based on the information, make an educated guess about the cause.
    6.  **Test the Hypothesis:** Try a fix. If it doesn't work, re-evaluate.
    7.  **Consider Edge Cases:** Once a potential fix is found, think about related edge cases or scenarios it might affect.
    8.  **Write a Test (if applicable):** For complex bugs, write a unit or integration test that reproduces the bug to ensure the fix works and to prevent regressions.
    9.  **Deploy Fix & Monitor:** Deploy the corrected code and monitor to ensure the bug is resolved and no new issues are introduced.
    10. **Documentation/Root Cause Analysis:** Document the bug, its cause, and the solution for future reference, especially for complex or recurring issues.

# What are the various data types in JavaScript?

In JavaScript, data types can be categorized into `primitive` and `non-primitive` types:

**Primitive data types**

- **Number**: Represents both integers and floating-point numbers.
- **String**: Represents sequences of characters.
- **Boolean**: Represents `true` or `false` values.
- **Undefined**: A variable that has been declared but not assigned a value.
- **Null**: Represents the intentional absence of any object value.
- **Symbol**: A unique and immutable value used as object property keys. Read more in our [deep dive on `Symbol`s](https://www.greatfrontend.com/questions/quiz/what-are-symbols-used-for).
- **BigInt**: Represents integers with arbitrary precision.

**Non-primitive (Reference) data types**

- **Object**: Used to store collections of data.
- **Array**: An ordered collection of data.
- **Function**: A callable object.
- **Date**: Represents dates and times.
- **RegExp**: Represents regular expressions.
- **Map**: A collection of keyed data items.
- **Set**: A collection of unique values.

The primitive types store a single value, while non-primitive types can store collections of data or complex entities.

# How do you check the data type of a variable?

To check the data type of a variable in JavaScript, you can use the `typeof` operator. For example, `typeof variableName` will return a string indicating the type of the variable, such as `"string"`, `"number"`, `"boolean"`, `"object"`, `"function"`, `"undefined"`, or `"symbol"`. For arrays and `null`, you can use `Array.isArray(variableName)` and `variableName === null`, respectively.

# What's the difference between a JavaScript variable that is: `null`, `undefined` or undeclared?

| Trait                        | `null`                                                                   | `undefined`                                         | Undeclared                            |
| ---------------------------- | ------------------------------------------------------------------------ | --------------------------------------------------- | ------------------------------------- |
| Meaning                      | Explicitly set by the developer to indicate that a variable has no value | Variable has been declared but not assigned a value | Variable has not been declared at all |
| Type (via `typeof` operator) | `'object'`                                                               | `'undefined'`                                       | `'undefined'`                         |
| Equality Comparison          | `null == undefined` is `true`                                            | `undefined == null` is `true`                       | Throws a `ReferenceError`             |

# What are the differences between JavaScript variables created using `let`, `var` or `const`?

In JavaScript, `let`, `var`, and `const` are all keywords used to declare variables, but they differ significantly in terms of scope, initialization rules, whether they can be redeclared or reassigned, and the behavior when they are accessed before declaration:

| Behavior                     | `var`              | `let`            | `const`          |
| ---------------------------- | ------------------ | ---------------- | ---------------- |
| Scope                        | Function or Global | Block            | Block            |
| Initialization               | Optional           | Optional         | Required         |
| Redeclaration                | Yes                | No               | No               |
| Reassignment                 | Yes                | Yes              | No               |
| Accessing before declaration | `undefined`        | `ReferenceError` | `ReferenceError` |

# Why is it, in general, a good idea to leave the global JavaScript scope of a website as-is and never touch it?

JavaScript that is executed in the browser has access to the global scope (the `window` object). In general it's a good software engineering practice to not pollute the global namespace unless you are working on a feature that truly needs to be global – it is needed by the entire page. Several reasons to avoid touching the global scope:

- **Naming conflicts**: Sharing the global scope across scripts can cause conflicts and bugs when new global variables or changes are introduced.
- **Cluttered global namespace**: Keeping the global namespace minimal avoids making the codebase hard to manage and maintain.
- **Scope leaks**: Unintentional references to global variables in closures or event handlers can cause memory leaks and performance issues.
- **Modularity and encapsulation**: Good design promotes keeping variables and functions within their specific scopes, enhancing organization, reusability, and maintainability.
- **Security concerns**: Global variables are accessible by all scripts, including potentially malicious ones, posing security risks, especially if sensitive data is stored there.
- **Compatibility and portability**: Heavy reliance on global variables reduces code portability and integration ease with other libraries or frameworks.

Follow these best practices to avoid global scope pollution:

- **Use local variables**: Declare variables within functions or blocks using `var`, `let`, or `const` to limit their scope.
- **Pass variables as function parameters**: Maintain encapsulation by passing variables as parameters instead of accessing them globally.
- **Use immediately invoked function expressions (IIFE)**: Create new scopes with IIFEs to prevent adding variables to the global scope.
- **Use modules**: Encapsulate code with module systems to maintain separate scopes and manageability.

# How do you convert a string to a number in JavaScript?

In JavaScript, you can convert a string to a number using several methods. The most common ones are `Number()`, `parseInt()`, `parseFloat()`, and the unary plus operator (`+`). For example, `Number("123")` converts the string `"123"` to the number `123`, and `parseInt("123.45")` converts the string `"123.45"` to the integer `123`.

# What are template literals and how are they used?

Template literals are a feature in JavaScript that allow for easier string interpolation and multi-line strings. They are enclosed by backticks (`` ` ``) instead of single or double quotes. You can embed expressions within template literals using `${expression}` syntax.

Example:

```js live
const myName = "John";
const greeting = `Hello, ${myName}!`;
console.log(greeting); // Output: Hello, John!
```

# Explain the concept of tagged templates

Tagged templates in JavaScript allow you to parse template literals with a function. The function receives the literal strings and the values as arguments, enabling custom processing of the template. For example:

```js live
function tag(strings, ...values) {
  return strings[0] + values[0] + strings[1] + values[1] + strings[2];
}

const result = tag`Hello ${"world"}! How are ${"you"}?`;
console.log(result); // "Hello world! How are you?"
```

# What is the spread operator and how is it used?

The spread operator, represented by three dots (`...`), is used in JavaScript to expand iterable objects like arrays or strings into individual elements. It can also be used to spread object properties. For example, you can use it to combine arrays, copy arrays, or pass array elements as arguments to a function.

```js live
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
const combined = [...arr1, ...arr2];
console.log(combined); // [1, 2, 3, 4, 5, 6]

const obj1 = { a: 1, b: 2 };
const obj2 = { c: 3, d: 4 };
const combinedObj = { ...obj1, ...obj2 };
console.log(combinedObj); // { a: 1, b: 2, c: 3, d: 4 }
```

# What are `Symbol`s used for in JavaScript?

`Symbol`s in JavaScript are a new primitive data type introduced in ES6 (ECMAScript 2015). They are unique and immutable identifiers that are primarily used for object property keys to avoid name collisions. These values can be created using the `Symbol(...)` function, and each `Symbol` value is guaranteed to be unique, even if they have the same key/description. `Symbol` properties are not enumerable in `for...in` loops or `Object.keys()`, making them suitable for creating private/internal object state.

```js live
let sym1 = Symbol();
let sym2 = Symbol("myKey");

console.log(typeof sym1); // "symbol"
console.log(sym1 === sym2); // false, because each symbol is unique

let obj = {};
let sym = Symbol("uniqueKey");

obj[sym] = "value";
console.log(obj[sym]); // "value"
```

**Note**: The `Symbol()` function must be called without the `new` keyword. It is not exactly a constructor because it can only be called as a function instead of with `new Symbol()`.

# What are proxies in JavaScript used for?

In JavaScript, a proxy is an object that acts as an intermediary between an object and the code. Proxies are used to intercept and customize the fundamental operations of JavaScript objects, such as property access, assignment, function invocation, and more.

Here's a basic example of using a `Proxy` to log every property access:

```js live
const myObject = {
  name: "John",
  age: 42,
};

const handler = {
  get: function (target, prop, receiver) {
    console.log(`Someone accessed property "${prop}"`);
    return target[prop];
  },
};

const proxiedObject = new Proxy(myObject, handler);

console.log(proxiedObject.name);
// Someone accessed property "name"
// 'John'

console.log(proxiedObject.age);
// Someone accessed property "age"
// 42
```

Use cases include:

- **Property access interception**: Intercept and customize property access on an object.
  - **Property assignment validation**: Validate property values before they are set on the target object.
  - **Logging and debugging**: Create wrappers for logging and debugging interactions with an object.
  - **Creating reactive systems**: Trigger updates in other parts of your application when object properties change (data binding).
  - **Data transformation**: Transforming data being set or retrieved from an object.
  - **Mocking and stubbing in tests**: Create mock or stub objects for testing purposes, allowing you to isolate dependencies and focus on the unit under test.
- **Function invocation interception**: Used to cache and return the result of frequently accessed methods if they involve network calls or computationally intensive logic, improving performance.
- **Dynamic property creation**: Useful for defining properties on the fly with default values and avoiding storing redundant data in objects.

# Explain the concept of "hoisting" in JavaScript

Hoisting is a JavaScript mechanism where variable and function declarations are moved ("hoisted") to the top of their containing scope during the compile phase.

- **Variable declarations (`var`)**: Declarations are hoisted, but not initializations. The value of the variable is `undefined` if accessed before initialization.
- **Variable declarations (`let` and `const`)**: Declarations are hoisted, but not initialized. Accessing them results in `ReferenceError` until the actual declaration is encountered.
- **Function expressions (`var`)**: Declarations are hoisted, but not initializations. The value of the variable is `undefined` if accessed before initialization.
- **Function declarations (`function`)**: Both declaration and definition are fully hoisted.
- **Class declarations (`class`)**: Declarations are hoisted, but not initialized. Accessing them results in `ReferenceError` until the actual declaration is encountered.
- **Import declarations (`import`)**: Declarations are hoisted, and side effects of importing the module are executed before the rest of the code.

The following behavior summarizes the result of accessing the variables before they are declared.

| Declaration                    | Accessing before declaration |
| ------------------------------ | ---------------------------- |
| `var foo`                      | `undefined`                  |
| `let foo`                      | `ReferenceError`             |
| `const foo`                    | `ReferenceError`             |
| `class Foo`                    | `ReferenceError`             |
| `var foo = function() { ... }` | `undefined`                  |
| `function foo() { ... }`       | Normal                       |
| `import`                       | Normal                       |

# Explain the difference in hoisting between `var`, `let`, and `const`

`var` declarations are hoisted to the top of their scope and initialized with `undefined`, allowing them to be used before their declaration. `let` and `const` declarations are also hoisted but are not initialized, resulting in a `ReferenceError` if accessed before their declaration. `const` additionally requires an initial value at the time of declaration.

# How does hoisting affect function declarations and expressions?

Hoisting in JavaScript means that function declarations are moved to the top of their containing scope during the compile phase, making them available throughout the entire scope. This allows you to call a function before it is defined in the code. However, function expressions are not hoisted in the same way. If you try to call a function expression before it is defined, you will get an error because the variable holding the function is hoisted but not its assignment.

```js live
// Function declaration
console.log(foo()); // Works fine
function foo() {
  return "Hello";
}

// Function expression
console.log(bar()); // Throws TypeError: bar is not a function
var bar = function () {
  return "Hello";
};
```

# What are the potential issues caused by hoisting?

Hoisting can lead to unexpected behavior in JavaScript because variable and function declarations are moved to the top of their containing scope during the compilation phase. This can result in `undefined` values for variables if they are used before their declaration and can cause confusion with function declarations and expressions. For example:

```js live
console.log(a); // undefined
var a = 5;

console.log(b); // ReferenceError: Cannot access 'b' before initialization
let b = 10;
```

# How can you avoid problems related to hoisting?

To avoid problems related to hoisting, always declare variables at the top of their scope using `let` or `const` instead of `var`. This ensures that variables are block-scoped and not hoisted to the top of their containing function or global scope. Additionally, declare functions before they are called to avoid issues with function hoisting.

```js live
// Use let or const
let x = 10;
const y = 20;
console.log(x, y); // Output: 10 20

// Declare functions before calling them
function myFunction() {
  console.log("Hello, world!");
}
myFunction(); // Output: 'Hello, world!'
```

# What is the difference between `==` and `===` in JavaScript?

`==` is the abstract equality operator while `===` is the strict equality operator. `==` performs type coercion before comparing, following the Abstract Equality Comparison algorithm defined in the ECMAScript specification. `===` does not perform coercion and returns `false` whenever the operand types differ. `===` is generally preferred in application code because it eliminates a class of bugs caused by unexpected coercion. The most common exception is `x == null`, which checks for both `null` and `undefined` in a single comparison.

| Operator            | `==`                                                 | `===`                    |
| ------------------- | ---------------------------------------------------- | ------------------------ |
| Name                | Loose (abstract) equality operator                   | Strict equality operator |
| Type coercion       | Yes — per the Abstract Equality Comparison algorithm | No                       |
| Comparison behavior | Types may be coerced before the value comparison     | Types are compared first |

> **Don't confuse `=` with `==` and `===`.** `=` is the [assignment operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Assignment) — it sets a variable's value (`x = 5`) and does not compare anything.

# What language constructs do you use for iterating over object properties and array items in JavaScript?

There are multiple ways to iterate over object properties as well as arrays in JavaScript:

**`for...in` loop**

The `for...in` loop iterates over all enumerable properties of an object, including inherited enumerable properties. So it is important to have a check if you only want to iterate over the object's own properties.

```js live
const obj = {
  a: 1,
  b: 2,
  c: 3,
};

for (const key in obj) {
  // To avoid iterating over inherited properties
  if (Object.hasOwn(obj, key)) {
    console.log(`${key}: ${obj[key]}`);
  }
}
```

**`Object.keys()`**

`Object.keys()` returns an array of the object's own enumerable property names. You can then use a for...of loop or forEach to iterate over this array.

```js live
const obj = {
  a: 1,
  b: 2,
  c: 3,
};

Object.keys(obj).forEach((key) => {
  console.log(`${key}: ${obj[key]}`);
});
```

The most common ways to iterate over an array are using a `for` loop and the `Array.prototype.forEach` method.

**Using `for` loop**

```js live
let array = [1, 2, 3, 4, 5, 6];
for (let index = 0; index < array.length; index++) {
  console.log(array[index]);
}
```

**Using `Array.prototype.forEach` method**

```js live
let array = [1, 2, 3, 4, 5, 6];
array.forEach((number, index) => {
  console.log(`${number} at index ${index}`);
});
```

**Using `for...of`**

This method is the newest and most convenient way to iterate over arrays. It automatically iterates over each element without requiring you to manage the index.

```js live
const numbers = [1, 2, 3, 4, 5];

for (const number of numbers) {
  console.log(number);
}
```

There are also other built-in methods available which are suitable for specific scenarios, for example:

- `Array.prototype.filter`: You can use the `filter` method to create a new array containing only the elements that satisfy a certain condition.
- `Array.prototype.map`: You can use the `map` method to create a new array based on the existing one, transforming each element with a provided function.
- `Array.prototype.reduce`: You can use the `reduce` method to combine all elements into a single value by repeatedly calling a function that takes two arguments: the accumulated value and the current element.

# What is the purpose of the `break` and `continue` statements?

The `break` statement is used to exit a loop or switch statement prematurely, while the `continue` statement skips the current iteration of a loop and proceeds to the next iteration. For example, in a `for` loop, `break` will stop the loop entirely, and `continue` will skip to the next iteration.

```js live
for (let i = 0; i < 10; i++) {
  if (i === 5) break; // exits the loop when i is 5
  console.log(i);
}

for (let i = 0; i < 10; i++) {
  if (i === 5) continue; // skips the iteration when i is 5
  console.log(i);
}
```

# What is the ternary operator and how is it used?

The ternary operator is a shorthand for an `if-else` statement in JavaScript. It takes three operands: a condition, a result for true, and a result for false. The syntax is `condition ? expr1 : expr2`. For example, `let result = (a > b) ? 'a is greater' : 'b is greater';` assigns `'a is greater'` to `result` if `a` is greater than `b`, otherwise it assigns `'b is greater'`.

# How do you access the index of an element in an array during iteration?

To access the index of an element in an array during iteration, you can use methods like `forEach`, `map`, `for...of` with `entries`, or a traditional `for` loop. For example, using `forEach`:

```js live
const array = ["a", "b", "c"];
array.forEach((element, index) => {
  console.log(index, element);
});
```

# What is the purpose of the `switch` statement?

The `switch` statement is used to execute one block of code among many based on the value of an expression. It is an alternative to using multiple `if...else if` statements. The `switch` statement evaluates an expression, matches the expression's value to a `case` label, and executes the associated block of code. If no `case` matches, the `default` block is executed.

```js
switch (expression) {
  case value1:
    // code to be executed if expression === value1
    break;
  case value2:
    // code to be executed if expression === value2
    break;
  default:
  // code to be executed if no case matches
}
```

# What are rest parameters and how are they used?

Rest parameters in JavaScript allow a function to accept an indefinite number of arguments as an array. They are denoted by three dots (`...`) followed by the name of the array. This feature is useful for functions that need to handle multiple arguments without knowing the exact number in advance.

```js live
function sum(...numbers) {
  return numbers.reduce((acc, curr) => acc + curr, 0);
}

console.log(sum(1, 2, 3, 4)); // Output: 10
```

# Explain the concept of the spread operator and its uses

The spread operator (`...`) in JavaScript allows you to expand elements of an iterable (like an array or object) into individual elements. It is commonly used for copying arrays or objects, merging arrays or objects, and passing elements of an array as arguments to a function.

```js live
// Copying an array
const arr1 = [1, 2, 3];
const arr2 = [...arr1];
console.log(arr2); // Output: [1, 2, 3]

// Merging arrays
const arr3 = [4, 5, 6];
const mergedArray = [...arr1, ...arr3];
console.log(mergedArray); // Output: [1, 2, 3, 4, 5, 6]

// Copying an object
const obj1 = { a: 1, b: 2 };
const obj2 = { ...obj1 };
console.log(obj2); // Output: { a: 1, b: 2 }

// Merging objects
const obj3 = { c: 3, d: 4 };
const mergedObject = { ...obj1, ...obj3 };
console.log(mergedObject); // Output: { a: 1, b: 2, c: 3, d: 4 }

// Passing array elements as function arguments
const sum = (x, y, z) => x + y + z;
const numbers = [1, 2, 3];
console.log(sum(...numbers)); // Output: 6
```

# What are the benefits of using spread syntax in JavaScript and how is it different from rest syntax?

**Spread syntax** (`...`) allows an iterable (like an array or string) to be expanded into individual elements. This is often used as a convenient and modern way to create new arrays or objects by combining existing ones.

| Operation      | Traditional                     | Spread                 |
| -------------- | ------------------------------- | ---------------------- |
| Array cloning  | `arr.slice()`                   | `[...arr]`             |
| Array merging  | `arr1.concat(arr2)`             | `[...arr1, ...arr2]`   |
| Object cloning | `Object.assign({}, obj)`        | `{ ...obj }`           |
| Object merging | `Object.assign({}, obj1, obj2)` | `{ ...obj1, ...obj2 }` |

**Rest syntax** is the opposite of what spread syntax does. It collects a variable number of arguments into an array. This is often used in function parameters to handle a dynamic number of arguments.

```js live
// Using rest syntax in a function
function sum(...numbers) {
  return numbers.reduce((total, num) => total + num, 0);
}

console.log(sum(1, 2, 3)); // Output: 6
```

# What are iterators and generators in JavaScript and what are they used for?

In JavaScript, iterators and generators are powerful tools for managing sequences of data and controlling the flow of execution in a more flexible way.

**Iterators** are objects that define a sequence and potentially a return value upon its termination. They adhere to a specific interface:

- An iterator object must implement a `next()` method.
- The `next()` method returns an object with two properties:
  - `value`: The next value in the sequence.
  - `done`: A boolean that is `true` if the iterator has finished its sequence, otherwise `false`.

Here's an example of an object implementing the iterator interface.

```js live
const iterator = {
  current: 0,
  last: 5,
  next() {
    if (this.current <= this.last) {
      return { value: this.current++, done: false };
    } else {
      return { value: undefined, done: true };
    }
  },
};

let result = iterator.next();
while (!result.done) {
  console.log(result.value); // Logs 0, 1, 2, 3, 4, 5
  result = iterator.next();
}
```

**Generators** are special functions that **can pause execution and resume at a later point**. They use the `function*` syntax and the `yield` keyword to control the flow of execution. When you call a generator function, it doesn't execute completely like a regular function. Instead, it returns an iterator object. Calling the `next()` method on the returned iterator advances the generator to the next `yield` statement, and the value after `yield` becomes the return value of `next()`.

```js live
function* numberGenerator() {
  let num = 0;
  while (num <= 5) {
    yield num++;
  }
}

const gen = numberGenerator();
console.log(gen.next()); // { value: 0, done: false }
console.log(gen.next()); // { value: 1, done: false }
console.log(gen.next()); // { value: 2, done: false }
console.log(gen.next()); // { value: 3, done: false }
console.log(gen.next()); // { value: 4, done: false }
console.log(gen.next()); // { value: 5, done: false }
console.log(gen.next()); // { value: undefined, done: true }
```

Generators are powerful for creating iterators on-demand, especially for infinite sequences or complex iteration logic. They can be used for:

- Lazy evaluation – processing elements only when needed, improving memory efficiency for large datasets.
- Implementing iterators for custom data structures.
- Creating asynchronous iterators for handling data streams.

# Explain the differences on the usage of `foo` between `function foo() {}` and `var foo = function() {}` in JavaScript

`function foo() {}` is a function declaration while `var foo = function() {}` is a function expression. The key difference is that function declarations have their bodies hoisted but the bodies of function expressions are not (they have the same hoisting behavior as `var`-declared variables).

If you try to invoke a function expression before it is declared, you will get an `Uncaught TypeError: XXX is not a function` error.

Function declarations can be called in the enclosing scope even before they are declared.

```js live
foo(); // 'FOOOOO'
function foo() {
  console.log("FOOOOO");
}
```

Function expressions if called before they are declared will result in an error.

```js live
foo(); // Uncaught TypeError: foo is not a function
var foo = function () {
  console.log("FOOOOO");
};
```

Another key difference is in the scope of the function name. Function expressions can be named by defining a name after the `function` keyword and before the parentheses. However, when using named function expressions, the function name is only accessible within the function itself. Trying to access it outside will result in an error or `undefined`.

```js live
const myFunc = function namedFunc() {
  console.log(namedFunc); // Works
};

myFunc(); // Runs the function and logs the function reference
console.log(namedFunc); // ReferenceError: namedFunc is not defined
```

**Note**: The examples use `var` due to legacy reasons. Function expressions can be defined using `let` and `const`, and the key difference is in the hoisting behavior of those keywords.

# What is the difference between a parameter and an argument?

A parameter is a variable in the declaration of a function, while an argument is the actual value passed to the function when it is called. For example, in the function `function add(a, b) { return a + b; }`, `a` and `b` are parameters. When you call `add(2, 3)`, `2` and `3` are arguments.

# Explain the concept of hoisting with regards to functions

Hoisting in JavaScript is a behavior where function declarations are moved to the top of their containing scope during the compile phase. This means you can call a function before it is defined in the code. However, this does not apply to function expressions or arrow functions, which are not hoisted in the same way.

```js live
// Function declaration
hoistedFunction(); // Works fine
function hoistedFunction() {
  console.log("This function is hoisted");
}

// Function expression
nonHoistedFunction(); // Throws an error
var nonHoistedFunction = function () {
  console.log("This function is not hoisted");
};
```

# What's the difference between `.call` and `.apply` in JavaScript?

`.call` and `.apply` are both used to invoke functions with a specific `this` context and arguments. The primary difference lies in how they accept arguments:

- `.call(thisArg, arg1, arg2, ...)`: Takes arguments individually.
- `.apply(thisArg, [argsArray])`: Takes arguments as an array.

Assuming we have a function `add`, the function can be invoked using `.call` and `.apply` in the following manner:

```js live
function add(a, b) {
  return a + b;
}

console.log(add.call(null, 1, 2)); // 3
console.log(add.apply(null, [1, 2])); // 3
```

# Can you offer a use case for the new arrow => function syntax?

Arrow functions provide a concise syntax for writing functions in JavaScript. They are particularly useful for maintaining the `this` context within methods and callbacks. For example, in an event handler or array method like `map`, arrow functions can simplify the code and avoid issues with `this` binding.

```js live
const numbers = [1, 2, 3];
const doubled = numbers.map((n) => n * 2);
console.log(doubled); // [2, 4, 6]
```

# Difference between: `function Person(){}`, `const person = Person()`, and `const person = new Person()` in JavaScript?

- `function Person(){}`: A function declaration in JavaScript. It can be used as a regular function or as a constructor.
- `const person = Person()`: Calls `Person` as a regular function, not a constructor. If `Person` is intended to be a constructor, this will lead to unexpected behavior.
- `const person = new Person()`: Creates a new instance of `Person`, correctly utilizing the constructor function to initialize the new object.

| Aspect            | `function Person(){}` | `const person = Person()`                      | `const person = new Person()`      |
| ----------------- | --------------------- | ---------------------------------------------- | ---------------------------------- |
| Type              | Function declaration  | Function call                                  | Constructor call                   |
| Usage             | Defines a function    | Invokes `Person` as a regular function         | Creates a new instance of `Person` |
| Instance Creation | No instance created   | No instance created                            | New instance created               |
| Common Mistake    | N/A                   | Misusing as constructor leading to `undefined` | None (when used correctly)         |

# What is the definition of a higher-order function in JavaScript?

A higher-order function is any function that takes one or more functions as arguments, which it uses to operate on some data, and/or returns a function as a result.

Higher-order functions are meant to abstract some operation that is performed repeatedly. The classic example of this is `Array.prototype.map()`, which takes an array and a function as arguments. `Array.prototype.map()` then uses this function to transform each item in the array, returning a new array with the transformed data. Other popular examples in JavaScript are `Array.prototype.forEach()`, `Array.prototype.filter()`, and `Array.prototype.reduce()`. A higher-order function doesn't just need to be manipulating arrays as there are many use cases for returning a function from another function. `Function.prototype.bind()` is an example that returns another function.

Imagine a scenario where we have an array of names that we need to transform to uppercase. The imperative way will be as such:

```js live
const names = ["irish", "daisy", "anna"];

function transformNamesToUppercase(names) {
  const results = [];
  for (let i = 0; i < names.length; i++) {
    results.push(names[i].toUpperCase());
  }
  return results;
}

console.log(transformNamesToUppercase(names)); // ['IRISH', 'DAISY', 'ANNA']
```

Using `Array.prototype.map(transformerFn)` makes the code shorter and more declarative.

```js live
const names = ["irish", "daisy", "anna"];

function transformNamesToUppercase(names) {
  return names.map((name) => name.toUpperCase());
}

console.log(transformNamesToUppercase(names)); // ['IRISH', 'DAISY', 'ANNA']
```

# What are callback functions and how are they used?

A callback function is a function passed into another function as an argument, which is then invoked inside the outer function to complete some kind of routine or action. They are commonly used for asynchronous operations like handling events, making API calls, or reading files. For example:

```js live
function fetchData(callback) {
  // assume an asynchronous operation to fetch data
  const data = { name: "John Doe" };
  callback(data);
}

function handleData(data) {
  console.log(data);
}

fetchData(handleData);
```

# What's a typical use case for anonymous functions in JavaScript?

An anonymous function in JavaScript is a function that does not have any name associated with it. They are typically used as arguments to other functions or assigned to variables.

```js live
const arr = [-1, 0, 5, 6];

// The filter method is passed an anonymous function.
arr.filter((x) => x > 1); // [5, 6]
```

They are often used as arguments to other functions, known as higher-order functions, which can take functions as input and return a function as output. Anonymous functions can access variables from the outer scope, a concept known as closures, allowing them to "close over" and remember the environment in which they were created.

```js live
// Encapsulating Code
(function () {
  // Some code here.
})();

// Callbacks
setTimeout(function () {
  console.log("Hello world!");
}, 1000);

// Functional programming constructs
const arr = [1, 2, 3];
const double = arr.map(function (el) {
  return el * 2;
});
console.log(double); // [2, 4, 6]
```

# What is recursion and how is it used in JavaScript?

Recursion is a programming technique where a function calls itself to solve a problem. In JavaScript, recursion is used to solve problems that can be broken down into smaller, similar sub-problems. A base case is essential to stop the recursive calls and prevent infinite loops. For example, calculating the factorial of a number can be done using recursion:

```js live
function factorial(n) {
  if (n === 0) {
    return 1;
  }
  return n * factorial(n - 1);
}

console.log(factorial(4)); // Output: 24
```

# What are default parameters and how are they used?

Default parameters in JavaScript allow you to set default values for function parameters if no value or `undefined` is passed. This helps avoid `undefined` values and makes your code more robust. You can define default parameters by assigning a value to the parameter in the function definition.

```js live
function greet(name = "Guest") {
  console.log(`Hello, ${name}!`);
}

greet(); // Output: Hello, Guest!
greet("Alice"); // Output: Hello, Alice!
```

# Explain why the following doesn't work as an IIFE: `function foo(){}();`. What needs to be changed to properly make it an IIFE?

The code `function foo(){}();` doesn't work as an Immediately Invoked Function Expression (IIFE) because the JavaScript parser treats `function foo(){}` as a function declaration, not an expression. To make it an IIFE, you need to wrap the function in parentheses to turn it into a function expression: `(function foo(){})();`.

# What are the various ways to create objects in JavaScript?

Creating objects in JavaScript offers several methods:

- **Object literals (`{}`)**: Simplest and most popular approach. Define key-value pairs within curly braces.
- **`Object()` constructor**: Use `new Object()` with dot notation to add properties.
- **`Object.create()`**: Create new objects using existing objects as prototypes, inheriting properties and methods.
- **Constructor functions**: Define blueprints for objects using functions, creating instances with `new`.
- **ES2015 classes**: Structured syntax similar to other languages, using `class` and `constructor` keywords.

# Explain the difference between dot notation and bracket notation for accessing object properties

Dot notation and bracket notation are two ways to access properties of an object in JavaScript. Dot notation is more concise and readable but can only be used with valid JavaScript identifiers. Bracket notation is more flexible and can be used with property names that are not valid identifiers, such as those containing spaces or special characters.

```js live
const obj = { name: "Alice", "favorite color": "blue" };

// Dot notation
console.log(obj.name); // Alice

// Bracket notation
console.log(obj["favorite color"]); // blue
```

# What are the different methods for iterating over an array?

There are several methods to iterate over an array in JavaScript. The most common ones include `for` loops, `forEach`, `map`, `filter`, `reduce`, and `for...of`. Each method has its own use case. For example, `for` loops are versatile and can be used for any kind of iteration, while `forEach` is specifically for executing a function on each array element. `map` is used for transforming arrays, `filter` for filtering elements, `reduce` for accumulating values, and `for...of` for iterating over iterable objects.

# How do you add, remove, and update elements in an array?

To add elements to an array, you can use methods like `push`, `unshift`, or `splice`. To remove elements, you can use `pop`, `shift`, or `splice`. To update elements, you can directly access the array index and assign a new value.

```js live
let arr = [1, 2, 3];

// Add elements
arr.push(4); // [1, 2, 3, 4]
arr.unshift(0); // [0, 1, 2, 3, 4]
arr.splice(2, 0, 1.5); // [0, 1, 1.5, 2, 3, 4]

// Remove elements
arr.pop(); // [0, 1, 1.5, 2, 3]
arr.shift(); // [1, 1.5, 2, 3]
arr.splice(1, 1); // [1, 2, 3]

// Update elements
arr[1] = 5; // [1, 5, 3]
console.log(arr); // Final state: [1, 5, 3]
```

**Note**: If you try to `console.log(arr)` after each operation in some environments (like Chrome DevTools), you may only see the final state of `arr`. This happens because the console sometimes keeps a live reference to the array instead of logging its state at the exact moment. To see intermediate states properly, store snapshots using `console.log([...arr])` or print values immediately after each operation.

# What are the different ways to copy an object or an array?

To copy an object or an array in JavaScript, you can use several methods. For shallow copies, you can use the spread operator (`...`) or `Object.assign()`. For deep copies, you can use `JSON.parse(JSON.stringify())` or libraries like Lodash's `_.cloneDeep()`.

```js live
// Shallow copy of an array
const originalArray = [1, 2, 3];
const shallowCopyArray = [...originalArray];
console.log(shallowCopyArray); // [1, 2, 3]

// Shallow copy of an object
const originalObject = { a: 1, b: 2 };
const shallowCopyObject = { ...originalObject };
console.log(shallowCopyObject); // { a: 1, b: 2 };

// Deep copy using JSON methods
const deepCopyObject = JSON.parse(JSON.stringify(originalObject));
console.log(deepCopyObject); // { a: 1, b: 2 };
```

# Explain the difference between shallow copy and deep copy

A shallow copy duplicates the top-level properties of an object, but nested objects are still referenced. A deep copy duplicates all levels of an object, creating entirely new instances of nested objects. `Object.assign()` and the spread operator (`...`) create shallow copies. `structuredClone()` is the modern built-in for deep copies. `JSON.parse(JSON.stringify())` and Lodash's `_.cloneDeep` are other common approaches, each with different tradeoffs around which values they can faithfully clone.

```js live
// Shallow copy — nested object is shared
let obj1 = { a: 1, b: { c: 2 } };
let shallowCopy = { ...obj1 };
shallowCopy.b.c = 3;
console.log(obj1.b.c); // 3 — original mutated too

// Deep copy — fully independent
let obj2 = { a: 1, b: { c: 2 } };
let deepCopy = structuredClone(obj2);
deepCopy.b.c = 4;
console.log(obj2.b.c); // 2 — original unchanged
```

# What are the advantages of using the spread operator with arrays and objects?

The spread operator (`...`) in JavaScript allows you to easily copy arrays and objects, merge them, and add new elements or properties. It simplifies syntax and improves readability. For arrays, it can be used to concatenate or clone arrays. For objects, it can be used to merge objects or add new properties.

```js live
// Arrays
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 4, 5];
console.log(arr2); // [1, 2, 3, 4, 5]

// Objects
const obj1 = { a: 1, b: 2 };
const obj2 = { ...obj1, c: 3 };
console.log(obj2); // { a: 1, b: 2, c: 3 }
```

# How do you check if an object has a specific property?

To check if an object has a specific property, you can use the `in` operator or the `hasOwnProperty` method. The `in` operator checks for both own and inherited properties, while `hasOwnProperty` checks only for own properties.

```js live
const obj = { key: "value" };

// Using the `in` operator
if ("key" in obj) {
  console.log("Property exists");
}

// Using `hasOwnProperty`
if (obj.hasOwnProperty("key")) {
  console.log("Property exists");
}
```

# Explain the difference between mutable and immutable objects in JavaScript

**Mutable objects** allow for modification of properties and values after creation, which is the default behavior for most objects.

```js live
const mutableObject = {
  name: "John",
  age: 30,
};

// Modify the object
mutableObject.name = "Jane";

// The object has been modified
console.log(mutableObject); // Output: { name: 'Jane', age: 30 }
```

**Immutable objects** cannot be directly modified after creation. Their contents cannot be changed without creating an entirely new value.

```js live
const immutableObject = Object.freeze({
  name: "John",
  age: 30,
});

// Attempt to modify the object
immutableObject.name = "Jane";

// The object remains unchanged
console.log(immutableObject); // Output: { name: 'John', age: 30 }
```

The key difference between mutable and immutable objects is modifiability. Immutable objects cannot be modified after they are created, while mutable objects can be.

# Explain the concept of destructuring assignment for objects and arrays

Destructuring assignment is a syntax in JavaScript that allows you to unpack values from arrays or properties from objects into distinct variables. For arrays, you use square brackets, and for objects, you use curly braces. For example:

```js live
// Array destructuring
const [a, b] = [1, 2];

// Object destructuring
const { name, age } = { name: "John", age: 30 };
```

# What is `Object.freeze()` for?

`Object.freeze()` is used to make an object immutable. Once an object is frozen, you cannot add, remove, or modify its properties. This is useful for creating constants or ensuring that an object remains unchanged throughout the program.

```js live
const obj = { name: "John" };
Object.freeze(obj);
obj.name = "Doe"; // This will not change the name property

console.log(obj); // { name: 'John' }
```

# What is `Object.seal()` for?

`Object.seal()` is used to prevent new properties from being added to an object and to mark all existing properties as non-configurable. This means you can still modify the values of existing properties, but you cannot delete them or add new ones. Doing so will throw errors in strict mode but fail silently in non-strict mode. In the following examples, you can uncomment the 'use strict' comment to see this.

```js live
// 'use strict'

const obj = { name: "John" };
Object.seal(obj);

obj.name = "Jane"; // Allowed
obj.age = 30; // Not allowed, throws an error in strict mode
delete obj.name; // Not allowed, throws an error in strict mode

console.log(obj); // { name: 'Jane } (unchanged)
```

# What is `Object.preventExtensions()` for?

`Object.preventExtensions()` is a method in JavaScript that prevents new properties from being added to an object. However, it does not affect the deletion or modification of existing properties. This method is useful when you want to ensure that an object remains in a certain shape and no additional properties can be added to it.

```js live
const obj = { name: "John" };
Object.preventExtensions(obj);

obj.age = 30; // This will not work, as the object is not extensible
console.log(obj.age); // undefined
```

# What are JavaScript object getters and setters for?

JavaScript object getters and setters are used to control access to an object's properties. They provide a way to encapsulate the implementation details of a property and define custom behavior when getting or setting its value.

Getters and setters are defined using the `get` and `set` keywords, respectively, followed by a function that is executed when the property is accessed or assigned a new value.

Here's a code example demonstrating the use of getters and setters:

```js live
const person = {
  _name: "John Doe", // Private property

  get name() {
    // Getter
    return this._name;
  },
  set name(newName) {
    // Setter
    if (newName.trim().length > 0) {
      this._name = newName;
    } else {
      console.log("Invalid name");
    }
  },
};

// Accessing the name property using the getter
console.log(person.name); // Output: 'John Doe'

// Setting the name property using the setter
person.name = "Jane Smith"; // Setter is called
console.log(person.name); // Output: 'Jane Smith'

person.name = ""; // Setter is called, but the value is not set due to validation
console.log(person.name); // Output: 'Jane Smith'
```

# What are JavaScript object property flags and descriptors?

In JavaScript, property flags and descriptors manage the behavior and attributes of object properties.

**Property flags**

Property flags are used to specify the behavior of a property on an object. Here are the available flags:

- `writable`: Specifies whether the property can be written to. Defaults to `true`.
- `enumerable`: Specifies whether the property is enumerable. Defaults to `true`.
- `configurable`: Specifies whether the property can be deleted or its attributes changed. Default is `true`.

**Property descriptors**

These provide detailed information about an object's property, including its value and flags. They are retrieved using `Object.getOwnPropertyDescriptor()` and set using `Object.defineProperty()`.

The use cases of property descriptors are as follows:

- Making a property non-writable by setting `writable: false` to ensure data consistency.
- Hiding a property from enumeration by setting `enumerable: false`.
- Preventing property deletion and modification by setting `configurable: false`.
- Freezing or sealing objects to prevent modifications globally.

# How do you reliably determine whether an object is empty?

To reliably determine whether an object is empty, you can use `Object.keys()` to check if the object has any enumerable properties. If the length of the array returned by `Object.keys()` is zero, the object is empty.

```js live
const isEmpty = (obj) => Object.keys(obj).length === 0;

const obj = {};
console.log(isEmpty(obj)); // true
```

# What is the event loop in JavaScript runtimes?

The event loop is a concept within the JavaScript runtime environment regarding how asynchronous operations are executed within JavaScript engines. It works as such:

1. The JavaScript engine starts executing scripts, placing synchronous operations on the call stack.
2. When an asynchronous operation is encountered (e.g., `setTimeout()`, HTTP request), it is offloaded to the respective Web API or Node.js API to handle the operation in the background.
3. Once the asynchronous operation completes, its callback function is placed in the respective queues – task queues (also known as macrotask queues / callback queues) or microtask queues. We will refer to "task queue" as "macrotask queue" from here on to better differentiate from the microtask queue.
4. The event loop continuously monitors the call stack and executes items on the call stack. If/when the call stack is empty:
   1. Microtask queue is processed. Microtasks include promise callbacks (`then`, `catch`, `finally`), `await` continuations, `MutationObserver` callbacks, and calls to `queueMicrotask()`. The event loop takes the first callback from the microtask queue and pushes it to the call stack for execution. This repeats until the microtask queue is empty.
   2. Macrotask queue is processed. Macrotasks include web APIs like `setTimeout()`, HTTP requests, user interface event handlers like clicks, scrolls, etc. The event loop dequeues the first callback from the macrotask queue and pushes it onto the call stack for execution. However, after a macrotask queue callback is processed, the event loop does not proceed with the next macrotask yet! The event loop first checks the microtask queue. Checking the microtask queue is necessary as microtasks have higher priority than macrotask queue callbacks. The macrotask queue callback that was just executed could have added more microtasks!
      1. If the microtask queue is non-empty, process them as per the previous step.
      2. If the microtask queue is empty, the next macrotask queue callback is processed. This repeats until the macrotask queue is empty.
5. This process continues indefinitely, allowing the JavaScript engine to handle both synchronous and asynchronous operations efficiently without blocking the call stack.

# Explain the difference between synchronous and asynchronous functions in JavaScript

Synchronous functions are blocking while asynchronous functions are not. In synchronous functions, statements complete before the next statement is run. As a result, programs containing only synchronous code are evaluated exactly in order of the statements. The execution of the program is paused if one of the statements takes a very long time.

```js live
function sum(a, b) {
  console.log("Inside sum function");
  return a + b;
}

const result = sum(2, 3); // The program waits for sum() to complete before assigning the result
console.log("Result: ", result); // Output: 5
```

Asynchronous functions usually accept a callback as a parameter and execution continues on to the next line immediately after the asynchronous function is invoked. The callback is only invoked when the asynchronous operation is complete and the call stack is empty. Heavy duty operations such as loading data from a web server or querying a database should be done asynchronously so that the main thread can continue executing other operations instead of blocking until that long operation completes (in the case of browsers, the UI will freeze).

```js live
function fetchData(callback) {
  setTimeout(() => {
    const data = { name: "John", age: 30 };
    callback(data); // Calling the callback function with data
  }, 2000); // Simulating a 2-second delay
}

console.log("Fetching data...");

fetchData((data) => {
  console.log(data); // Output: { name: 'John', age: 30 } (after 2 seconds)
});

console.log("Call made to fetch data"); // This will print before the data is fetched
```

# Explain the concept of a callback function in asynchronous operations

A callback function is a function passed as an argument to another function, which is then invoked inside the outer function to complete some kind of routine or action. In asynchronous operations, callbacks are used to handle tasks that take time to complete, such as network requests or file I/O, without blocking the execution of the rest of the code. For example:

```js live
function fetchData(callback) {
  setTimeout(() => {
    const data = { name: "John", age: 30 };
    callback(data);
  }, 1000);
}

fetchData((data) => {
  console.log(data);
});
```

# What are Promises and how do they work?

Promises in JavaScript are objects that represent the eventual completion (or failure) of an asynchronous operation and its resulting value. They have three states: `pending`, `fulfilled`, and `rejected`. You can handle the results of a promise using the `.then()` method for success and the `.catch()` method for errors.

```js live
let promise = new Promise((resolve, reject) => {
  // asynchronous operation
  const success = true;
  if (success) {
    resolve("Success!");
  } else {
    reject("Error!");
  }
});

promise
  .then((result) => {
    console.log(result); // 'Success!' (this will print)
  })
  .catch((error) => {
    console.error(error); // 'Error!'
  });
```

# Explain the different states of a Promise

A `Promise` in JavaScript can be in one of three states: `pending`, `fulfilled`, or `rejected`. When a `Promise` is created, it starts in the `pending` state. If the operation completes successfully, the `Promise` transitions to the `fulfilled` state, and if it fails, it transitions to the `rejected` state. Here's a quick example:

```js
let promise = new Promise((resolve, reject) => {
  // some asynchronous operation
  if (success) {
    resolve("Success!");
  } else {
    reject("Error!");
  }
});
```

# What are the pros and cons of using Promises instead of callbacks in JavaScript?

Promises offer a cleaner alternative to callbacks, helping to avoid callback hell and making asynchronous code more readable. They facilitate writing sequential and parallel asynchronous operations with ease. However, using promises may introduce slightly more complex code.

# What is the use of `Promise.all()`

`Promise.all()` is a method in JavaScript that takes an array of promises and returns a single promise. This returned promise resolves when all the input promises have resolved, or it rejects if any of the input promises reject. It is useful for running multiple asynchronous operations in parallel and waiting for all of them to complete.

```js live
const promise1 = Promise.resolve(3);
const promise2 = 42;
const promise3 = new Promise((resolve, reject) => {
  setTimeout(resolve, 100, "foo");
});

Promise.all([promise1, promise2, promise3]).then((values) => {
  console.log(values); // [3, 42, 'foo']
});
```

# How is `Promise.all()` different from `Promise.allSettled()`?

`Promise.all()` and `Promise.allSettled()` are both methods for handling multiple promises in JavaScript, but they behave differently. `Promise.all()` waits for all promises to resolve and fails fast if any promise rejects, returning a single rejected promise. `Promise.allSettled()`, on the other hand, waits for all promises to settle (either resolve or reject) and returns an array of objects describing the outcome of each promise.

# What is async/await and how does it simplify asynchronous code?

`async/await` is a modern syntax in JavaScript that simplifies working with promises. By using the `async` keyword before a function, you can use the `await` keyword inside that function to pause execution until a promise is resolved. This makes asynchronous code look and behave more like synchronous code, making it easier to read and maintain.

```js live
async function fetchData() {
  try {
    const response = await fetch(
      "https://jsonplaceholder.typicode.com/posts/1",
    );
    const data = await response.json();
    console.log(data);
  } catch (error) {
    console.error("Error fetching data:", error);
  }
}
fetchData();
```

# How do you handle errors in asynchronous operations?

To handle errors in asynchronous operations, you can use `try...catch` blocks with `async/await` syntax or `.catch()` method with Promises. For example, with `async/await`, you can wrap your code in a `try...catch` block to catch any errors:

```js live
async function fetchData() {
  try {
    // Invalid URl
    const response = await fetch("https://api.example.com/data");
    const data = await response.json();
    console.log(data);
  } catch (error) {
    console.error("Error fetching data:", error);
  }
}

fetchData(); // Error fetching data: ....
```

With Promises, you can use the `.catch()` method:

```js live
fetch("https://api.example.com/data") // Invalid URl
  .then((response) => response.json())
  .then((data) => console.log(data))
  .catch((error) => console.error("Error fetching data:", error));
```

# Explain the concept of a microtask queue

The microtask queue is a queue of tasks that need to be executed after the currently executing script and before any other task. Microtasks are typically used for tasks that need to be executed immediately after the current operation, such as promise callbacks. The microtask queue is processed before the macrotask queue, ensuring that microtasks are executed as soon as possible.

# What is the difference between `setTimeout()`, `setImmediate()`, and `process.nextTick()`?

`setTimeout()` schedules a callback to run after a minimum delay. `setImmediate()` schedules a callback to run after the current event loop completes. `process.nextTick()` schedules a callback to run before the next event loop iteration begins.

```js
setTimeout(() => console.log("setTimeout"), 0);
setImmediate(() => console.log("setImmediate"));
process.nextTick(() => console.log("nextTick"));
```

In this example, `process.nextTick()` will execute first, followed by either `setTimeout()` or `setImmediate()` depending on the environment.

# Explain how prototypal inheritance works in JavaScript

Prototypical inheritance in JavaScript is a way for objects to inherit properties and methods from other objects. Every JavaScript object has a special hidden property called `[[Prototype]]` (commonly accessed via `__proto__` or using `Object.getPrototypeOf()`) that is a reference to another object, which is called the object's "prototype".

When a property is accessed on an object and if the property is not found on that object, the JavaScript engine looks at the object's `__proto__`, and the `__proto__`'s `__proto__` and so on, until it finds the property defined on one of the `__proto__`s or until it reaches the end of the prototype chain.

This behavior simulates classical inheritance, but it is really more of [delegation than inheritance](https://davidwalsh.name/javascript-objects).

Here's an example of prototypal inheritance:

```js live
// Parent object constructor.
function Animal(name) {
  this.name = name;
}

// Add a method to the parent object's prototype.
Animal.prototype.makeSound = function () {
  console.log("The " + this.constructor.name + " makes a sound.");
};

// Child object constructor.
function Dog(name) {
  Animal.call(this, name); // Call the parent constructor.
}

// Set the child object's prototype to be the parent's prototype.
Object.setPrototypeOf(Dog.prototype, Animal.prototype);

// Add a method to the child object's prototype.
Dog.prototype.bark = function () {
  console.log("Woof!");
};

// Create a new instance of Dog.
const bolt = new Dog("Bolt");

// Call methods on the child object.
console.log(bolt.name); // "Bolt"
bolt.makeSound(); // "The Dog makes a sound."
bolt.bark(); // "Woof!"
```

Things to note are:

- `.makeSound` is not defined on `Dog`, so the JavaScript engine goes up the prototype chain and finds `.makeSound` on the inherited `Animal`.
- Using `Object.create()` to build the inheritance chain is no longer recommended. Use `Object.setPrototypeOf()` instead.

# What is the prototype chain and how does it work?

The prototype chain is a mechanism in JavaScript that allows objects to inherit properties and methods from other objects. When you try to access a property on an object, JavaScript will first look for the property on the object itself. If it doesn't find it, it will look at the object's prototype, and then the prototype's prototype, and so on, until it either finds the property or reaches the end of the chain, which is `null`.

```js live
function Person(name) {
  this.name = name;
}

Person.prototype.greet = function () {
  console.log(`Hello, my name is ${this.name}`);
};

const alice = new Person("Alice");
alice.greet(); // "Hello, my name is Alice"
```

In this example, `alice` inherits the `greet` method from `Person.prototype`.

# Explain the difference between classical inheritance and prototypal inheritance

Classical inheritance is a model where classes inherit from other classes, typically seen in languages like Java and C++. Prototypal inheritance, used in JavaScript, involves objects inheriting directly from other objects. In classical inheritance, you define a class and create instances from it. In prototypal inheritance, you create an object and use it as a prototype for other objects.

# Explain the concept of inheritance in ES2015 classes

Inheritance in ES2015 classes allows one class to extend another, enabling the child class to inherit properties and methods from the parent class. This is done using the `extends` keyword. The `super` keyword is used to call the constructor and methods of the parent class. Here's a quick example:

```js live
class Animal {
  constructor(name) {
    this.name = name;
  }

  speak() {
    console.log(`${this.name} makes a noise.`);
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name);
    this.breed = breed;
  }

  speak() {
    console.log(`${this.name} barks.`);
  }
}

const dog = new Dog("Rex", "German Shepherd");
dog.speak(); // Rex barks.
```

# What is the purpose of the `new` keyword?

The `new` keyword in JavaScript is used to create an instance of a user-defined object type or one of the built-in object types that has a constructor function. When you use `new`, it does four things: it creates a new object, sets the prototype, binds `this` to the new object, and returns the new object.

```js live
function Person(name) {
  this.name = name;
}

const person1 = new Person("Alice");
console.log(person1.name); // Alice
```

# How do you create a constructor function?

To create a constructor function in JavaScript, define a regular function with a capitalized name to indicate it's a constructor. Use the `this` keyword to set properties and methods. When creating an instance, use the `new` keyword.

```js live
function Person(name, age) {
  this.name = name;
  this.age = age;
}

const john = new Person("John", 30);
console.log(john.age); // 30
```

# What are the differences between JavaScript ES2015 classes and ES5 function constructors?

ES2015 introduces a new way of creating classes, which provides a more intuitive and concise way to define and work with objects and inheritance compared to the ES5 function constructor syntax. Here's an example of each:

```js
// ES5 function constructor
function Person(name) {
  this.name = name;
}

// ES2015 Class
class Person {
  constructor(name) {
    this.name = name;
  }
}
```

For simple constructors, they look pretty similar. The main difference in the constructor comes when using inheritance. If we want to create a `Student` class that subclasses `Person` and adds a `studentId` field, this is what we have to do.

```js live
// ES5 inheritance
// Superclass
function Person1(name) {
  this.name = name;
}

// Subclass
function Student1(name, studentId) {
  // Call constructor of superclass to initialize superclass-derived members.
  Person1.call(this, name);

  // Initialize subclass's own members.
  this.studentId = studentId;
}
Student1.prototype = Object.create(Person1.prototype);
Student1.prototype.constructor = Student1;

const student1 = new Student1("John", 1234);
console.log(student1.name, student1.studentId); // "John" 1234

// ES2015 inheritance
// Superclass
class Person2 {
  constructor(name) {
    this.name = name;
  }
}

// Subclass
class Student2 extends Person2 {
  constructor(name, studentId) {
    super(name);
    this.studentId = studentId;
  }
}

const student2 = new Student2("Alice", 5678);
console.log(student2.name, student2.studentId); // "Alice" 5678
```

It's much more verbose to use inheritance in ES5, and the ES2015 version is easier to understand and remember.

**Comparison of ES5 function constructors vs ES2015 classes**

| Feature           | ES5 Function Constructor                                 | ES2015 Class                                |
| ----------------- | -------------------------------------------------------- | ------------------------------------------- |
| Syntax            | Uses function constructors and prototypes                | Uses `class` keyword                        |
| Constructor       | Function with properties assigned using `this`           | `constructor` method inside the class       |
| Method Definition | Defined on the prototype                                 | Defined inside the class body               |
| Static Methods    | Added directly to the constructor function               | Defined using the `static` keyword          |
| Inheritance       | Uses `Object.create()` and manually sets prototype chain | Uses `extends` keyword and `super` function |
| Readability       | Less intuitive and more verbose                          | More concise and intuitive                  |

# What advantage is there for using the JavaScript arrow syntax for a method in a constructor?

The main advantage of using an arrow function as a method inside a constructor is that the value of `this` gets set at the time of the function creation and can't change after that. When the constructor is used to create a new object, `this` will always refer to that object.

For example, let's say we have a `Person` constructor that takes a first name as an argument and has two methods to `console.log()` that name, one as a regular function and one as an arrow function:

```js live
const Person = function (name) {
  this.firstName = name;
  this.sayName1 = function () {
    console.log(this.firstName);
  };
  this.sayName2 = () => {
    console.log(this.firstName);
  };
};

const john = new Person("John");
const dave = new Person("Dave");

john.sayName1(); // John
john.sayName2(); // John

// The regular function can have its `this` value changed, but the arrow function cannot
john.sayName1.call(dave); // Dave (because `this` is now the dave object)
john.sayName2.call(dave); // John

john.sayName1.apply(dave); // Dave (because `this` is now the dave object)
john.sayName2.apply(dave); // John

john.sayName1.bind(dave)(); // Dave (because `this` is now the dave object)
john.sayName2.bind(dave)(); // John

const sayNameFromWindow1 = john.sayName1;
sayNameFromWindow1(); // undefined (because `this` is now the window object)

const sayNameFromWindow2 = john.sayName2;
sayNameFromWindow2(); // John
```

The main takeaway here is that `this` can be changed for a normal function, but `this` always stays the same for an arrow function. So even if you are passing around your arrow function to different parts of your application, you wouldn't have to worry about the value of `this` changing.

# Why might you want to create static class members in JavaScript?

Static class members (properties/methods) have a `static` keyword prepended. Such members cannot be directly accessed on instances of the class. Instead, they're accessed on the class itself.

```js live
class Car {
  static noOfWheels = 4;
  static compare() {
    return "Static method has been called.";
  }
}

console.log(Car.noOfWheels); // 4
```

Static members are useful under the following scenarios:

- **Namespace organization**: Static properties can be used to define constants or configuration values that are specific to a class. This helps organize related data within the class namespace and prevents naming conflicts with other variables. Examples include `Math.PI`, `Math.SQRT2`.
- **Helper functions**: Static methods can be used as helper functions that operate on the class itself or its instances. This can improve code readability and maintainability by separating utility logic from the core functionality of the class. Examples of frequently used static methods include `Object.assign()`, `Math.max()`.
- **Singleton pattern**: In some rare cases, static properties and methods can be used to implement a singleton pattern, where only one instance of a class ever exists. However, this pattern can be tricky to manage and is generally discouraged in favor of more modern dependency injection techniques.

# What is a closure in JavaScript, and how/why would you use one?

In the book ["You Don't Know JS"](https://github.com/getify/You-Dont-Know-JS/tree/2nd-ed/scope-closures) (YDKJS) by Kyle Simpson, a closure is defined as follows:

> Closure is when a function is able to remember and access its lexical scope even when that function is executing outside its lexical scope

In simple terms, functions have access to variables that were in their scope at the time of their creation. This is what we call the function's lexical scope. A closure is a function that retains access to these variables even after the outer function has finished executing. It is as if the function has a memory of its original environment.

```js live
function outerFunction() {
  const outerVar = "I am outside of innerFunction";

  function innerFunction() {
    console.log(outerVar); // `innerFunction` can still access `outerVar`.
  }

  return innerFunction;
}

const inner = outerFunction(); // `inner` now holds a reference to `innerFunction`.

inner(); // "I am outside of innerFunction"
// Even though `outerFunction` has completed execution, `inner` still has access to variables defined inside `outerFunction`.
```

Key points to remember:

- Closure occurs when an inner function has access to variables in its outer (lexical) scope, even when the outer function has finished executing.
- Closure allows a function to **remember** the environment in which it was created, even if that environment is no longer present.
- Closures are used extensively in JavaScript, such as in callbacks, event handlers, and asynchronous functions.

# Explain the concept of lexical scoping

Lexical scoping means that the scope of a variable is determined by its location within the source code, and nested functions have access to variables declared in their outer scope. For example:

```js live
function outerFunction() {
  let outerVariable = "I am outside!";

  function innerFunction() {
    console.log(outerVariable); // 'I am outside!'
  }

  innerFunction();
}

outerFunction();
```

In this example, `innerFunction` can access `outerVariable` because of lexical scoping.

# Explain the concept of scope in JavaScript

In JavaScript, scope determines the accessibility of variables and functions at different parts of the code. There are three main types of scope: global scope, function scope, and block scope. Global scope means the variable is accessible everywhere in the code. Function scope means the variable is accessible only within the function it is declared. Block scope, introduced with ES6, means the variable is accessible only within the block (e.g., within curly braces `{}`) it is declared.

```js live
var globalVar = "I am a global var";

function myFunction() {
  var functionVar = "I am a function-scoped var";

  if (true) {
    let blockVar = "I am a block-scoped var";

    console.log("Inside block:");
    console.log(globalVar); // Accessible
    console.log(functionVar); // Accessible
    console.log(blockVar); // Accessible
  }

  console.log("Inside function:");
  console.log(globalVar); // Accessible
  console.log(functionVar); // Accessible
  // console.log(blockVar); // Uncaught ReferenceError
}

myFunction();

console.log("In global scope:");
console.log(globalVar); // Accessible
// console.log(functionVar); // Uncaught ReferenceError
// console.log(blockVar); // Uncaught ReferenceError
```

# How can closures be used to create private variables?

Closures in JavaScript can be used to create private variables by defining a function within another function. The inner function has access to the outer function's variables, but those variables are not accessible from outside the outer function. This allows you to encapsulate and protect the variables from being accessed or modified directly.

```js live
function createCounter() {
  let count = 0; // private variable

  return {
    increment: function () {
      count++;
      return count;
    },
    decrement: function () {
      count--;
      return count;
    },
    getCount: function () {
      return count;
    },
  };
}

const counter = createCounter();
console.log(counter.increment()); // 1
console.log(counter.getCount()); // 1
console.log(counter.count); // undefined
```

# What are the potential pitfalls of using closures?

Closures can lead to memory leaks if not managed properly, especially when they capture variables that are no longer needed. They can also make debugging more difficult due to the complexity of the scope chain. Additionally, closures can cause performance issues if they are overused or used inappropriately, as they keep references to variables in their scope, which can prevent garbage collection.

# Explain the difference between global scope, function scope, and block scope

Global scope means variables are accessible from anywhere in the code. Function scope means variables are accessible only within the function they are declared in. Block scope means variables are accessible only within the block (e.g., within `{}`) they are declared in.

```js live
var globalVar = "I'm global"; // Global scope

function myFunction() {
  var functionVar = "I'm in a function"; // Function scope
  if (true) {
    let blockVar = "I'm in a block"; // Block scope
    console.log(blockVar); // Accessible here
  }
  // console.log(blockVar); // Uncaught ReferenceError: blockVar is not defined
}
// console.log(functionVar); // Uncaught ReferenceError: functionVar is not defined
myFunction();
```

# Explain how `this` works in JavaScript

There's no simple explanation for `this`; it is one of the most confusing concepts in JavaScript because its behavior differs from many other programming languages. The one-liner explanation of the `this` keyword is that it is a dynamic reference to the context in which a function is executed.

A longer explanation is that `this` follows these rules:

1. If the `new` keyword is used when calling the function, meaning the function was used as a function constructor, the `this` inside the function is the newly-created object instance.
2. If `this` is used in a `class` `constructor`, the `this` inside the `constructor` is the newly-created object instance.
3. If `apply()`, `call()`, or `bind()` is used to call/create a function, `this` inside the function is the object that is passed in as the argument.
4. If a function is called as a method (e.g. `obj.method()`) — `this` is the object that the function is a property of.
5. If a function is invoked as a free function invocation, meaning it was invoked without any of the conditions present above, `this` is the global object. In the browser, the global object is the `window` object. If in strict mode (`'use strict';`), `this` will be `undefined` instead of the global object.
6. If multiple of the above rules apply, the rule that is higher wins and will set the `this` value.
7. If the function is an ES2015 arrow function, it ignores all the rules above and receives the `this` value of its surrounding scope at the time it is created.

For an in-depth explanation, do check out [Arnav Aggrawal's article on Medium](https://codeburst.io/the-simple-rules-to-this-in-javascript-35d97f31bde3).

# Explain `Function.prototype.bind` in JavaScript

`Function.prototype.bind` is a method in JavaScript that allows you to create a new function with a specific `this` value and optional initial arguments. Its primary purpose is to:

- **Binding `this` value to preserve context**: The primary purpose of `bind` is to bind the `this` value of a function to a specific object. When you call `func.bind(thisArg)`, it creates a new function with the same body as `func`, but with `this` permanently bound to `thisArg`.
- **Partial application of arguments**: `bind` also allows you to pre-specify arguments for the new function. Any arguments passed to `bind` after `thisArg` will be prepended to the arguments list when the new function is called.
- **Method borrowing**: `bind` allows you to borrow methods from one object and apply them to another object, even if they were not originally designed to work with that object.

The `bind` method is particularly useful in scenarios where you need to ensure that a function is called with a specific `this` context, such as in event handlers, callbacks, or method borrowing.

# Explain the different ways the `this` keyword can be bound

The `this` keyword in JavaScript can be bound in several ways:

- Default binding: In non-strict mode, `this` refers to the global object (window in browsers). In strict mode, `this` is `undefined`.
- Implicit binding: When a function is called as a method of an object, `this` refers to the object.
- Explicit binding: Using `call`, `apply`, or `bind` methods to explicitly set `this`.
- New binding: When a function is used as a constructor with the `new` keyword, `this` refers to the newly created object.
- Arrow functions: Arrow functions do not have their own `this` and inherit `this` from the surrounding lexical context.

# What are the common pitfalls of using the `this` keyword?

The `this` keyword in JavaScript can be tricky because its value depends on how a function is called. Common pitfalls include losing the context of `this` when passing methods as callbacks, using `this` in nested functions, and misunderstanding `this` in arrow functions. To avoid these issues, you can use `.bind()`, arrow functions, or store the context in a variable.

# Explain the concept of `this` binding in event handlers

In JavaScript, the `this` keyword refers to the object that is currently executing the code. In event handlers, `this` typically refers to the element that triggered the event. However, the value of `this` can change depending on how the event handler is defined and called. To ensure `this` refers to the desired object, you can use methods like `bind()`, arrow functions, or assign the context explicitly.

# What is the DOM and how is it structured?

The DOM, or Document Object Model, is a programming interface for web documents. It represents the page so that programs can change the document structure, style, and content. The DOM is structured as a tree of objects, where each node represents part of the document, such as elements, attributes, and text.

# What's the difference between an "attribute" and a "property" in the DOM?

Attributes are defined in the HTML and provide initial values for properties. Properties are part of the DOM and represent the current state of an element. For example, the `value` attribute of an `<input>` element sets its initial value, while the `value` property reflects the current value as the user interacts with it.

# Explain the difference between `document.querySelector()` and `document.getElementById()`

`document.querySelector()` and `document.getElementById()` are both methods used to select elements from the DOM, but they have key differences. `document.querySelector()` can select any element using a CSS selector and returns the first match, while `document.getElementById()` selects an element by its ID and returns the element with that specific ID.

```js
// Using document.querySelector()
const element = document.querySelector(".my-class");

// Using document.getElementById()
const elementById = document.getElementById("my-id");
```

# How do you add, remove, and modify HTML elements using JavaScript?

To add, remove, and modify HTML elements using JavaScript, you can use methods like `createElement`, `appendChild`, `removeChild`, and properties like `innerHTML` and `textContent`. For example, to add an element, you can create it using `document.createElement` and then append it to a parent element using `appendChild`. To remove an element, you can use `removeChild` on its parent. To modify an element, you can change its `innerHTML` or `textContent`.

```js
// Adding an element
const newElement = document.createElement("div");
newElement.textContent = "Hello, World!";
document.body.appendChild(newElement);

// Removing an element
const elementToRemove = document.getElementById("elementId");
elementToRemove.parentNode.removeChild(elementToRemove);

// Modifying an element
const elementToModify = document.getElementById("elementId");
elementToModify.innerHTML = "New Content";
```

# What are event listeners and how are they used?

Event listeners are functions that wait for specific events to occur on elements, such as clicks or key presses. They are used to execute code in response to these events. You can add an event listener to an element using the `addEventListener` method. For example:

```js
document.getElementById("myButton").addEventListener("click", function () {
  alert("Button was clicked!");
});
```

# Explain the event phases in a browser

In a browser, events go through three phases: capturing, target, and bubbling. During the capturing phase, the event travels from the root to the target element. In the target phase, the event reaches the target element. Finally, in the bubbling phase, the event travels back up from the target element to the root. You can control event handling using `addEventListener` with the `capture` option.

# Describe event bubbling in JavaScript and browsers

Event bubbling is a DOM event propagation mechanism where an event (e.g. a click) starts at the target element and bubbles up to the root of the document. This allows ancestor elements to also respond to the event.

Event bubbling is essential for event delegation, where a single event handler manages events for multiple child elements, enhancing performance and code simplicity. While convenient, failing to manage event propagation properly can lead to unintended behavior, such as multiple handlers firing for a single event.

# Describe event capturing in JavaScript and browsers

Event capturing is a lesser-used counterpart to [event bubbling](https://www.greatfrontend.com/questions/quiz/describe-event-bubbling) in the DOM event propagation mechanism. It follows the opposite order, where an event triggers first on the ancestor element and then travels down to the target element.

Event capturing is rarely used as compared to event bubbling, but it can be used in specific scenarios where you need to intercept events at a higher level before they reach the target element. It is disabled by default but can be enabled through an option on `addEventListener()`.

# Explain event delegation in JavaScript

Event delegation is a technique in JavaScript where a single event listener is attached to a parent element instead of attaching event listeners to multiple child elements. When an event occurs on a child element, the event bubbles up the DOM tree, and the parent element's event listener handles the event based on the target element.

Event delegation provides the following benefits:

- **Improved performance**: Attaching a single event listener is more efficient than attaching multiple event listeners to individual elements, especially for large or dynamic lists. This reduces memory usage and improves overall performance.
- **Simplified event handling**: With event delegation, you only need to write the event handling logic once in the parent element's event listener. This makes the code more maintainable and easier to update.
- **Dynamic element support**: Event delegation automatically handles events for dynamically added or removed elements within the parent element. There's no need to manually attach or remove event listeners when the DOM structure changes.

However, do note that:

- It is important to identify the target element that triggered the event.
- Not all events can be delegated because they are not bubbled. Non-bubbling events include: `focus`, `blur`, `scroll`, `mouseenter`, `mouseleave`, `resize`, etc.

# How do you prevent the default behavior of an event?

To prevent the default behavior of an event in JavaScript, you can use the `preventDefault` method on the event object. For example, if you want to prevent a form from submitting, you can do the following:

```js
document.querySelector("form").addEventListener("submit", function (event) {
  event.preventDefault();
});
```

This method stops the default action associated with the event from occurring.

# What is the difference between `event.preventDefault()` and `event.stopPropagation()`?

`event.preventDefault()` is used to prevent the default action that belongs to the event, such as preventing a form from submitting. `event.stopPropagation()` is used to stop the event from bubbling up to parent elements, preventing any parent event handlers from being executed.

# What is the difference between `mouseenter` and `mouseover` event in JavaScript and browsers?

The main difference lies in the bubbling behavior of `mouseenter` and `mouseover` events. `mouseenter` does not bubble while `mouseover` bubbles.

`mouseenter` events do not bubble. The `mouseenter` event is triggered only when the mouse pointer enters the element itself, not its descendants. If a parent element has child elements, and the mouse pointer enters child elements, the `mouseenter` event will not be triggered on the parent element again; it is only triggered once upon entry of the parent element, without regard for its contents. If both parent and child have `mouseenter` listeners attached and the mouse pointer moves from the parent element to the child element, `mouseenter` will only fire for the child.

`mouseover` events bubble up the DOM tree. The `mouseover` event is triggered when the mouse pointer enters the element or one of its descendants. If a parent element has child elements, and the mouse pointer enters child elements, the `mouseover` event will be triggered on the parent element again as well. If the parent element has multiple child elements, this can result in multiple event callbacks fired. If there are child elements, and the mouse pointer moves from the parent element to the child element, `mouseover` will fire for both the parent and the child.

| Property | `mouseenter`              | `mouseover`                                        |
| -------- | ------------------------- | -------------------------------------------------- |
| Bubbling | No                        | Yes                                                |
| Trigger  | Only when entering itself | When entering itself and when entering descendants |

# What is the difference between `innerHTML` and `textContent`?

`innerHTML` and `textContent` are both properties used to get or set the content of an HTML element, but they serve different purposes. `innerHTML` returns or sets the HTML markup contained within the element, which means it can parse and render HTML tags. On the other hand, `textContent` returns or sets the text content of the element, ignoring any HTML tags and rendering them as plain text.

```js
// Example of innerHTML
element.innerHTML = "<strong>Bold Text</strong>"; // Renders as bold text

// Example of textContent
element.textContent = "<strong>Bold Text</strong>"; // Renders as plain text: <strong>Bold Text</strong>
```

# How do you manipulate CSS styles using JavaScript?

You can manipulate CSS styles using JavaScript by accessing the `style` property of an HTML element. For example, to change the background color of a `div` element with the id `myDiv`, you can use:

```js
document.getElementById("myDiv").style.backgroundColor = "blue";
```

You can also add, remove, or toggle CSS classes using the `classList` property:

```js
document.getElementById("myDiv").classList.add("newClass");
document.getElementById("myDiv").classList.remove("oldClass");
document.getElementById("myDiv").classList.toggle("toggleClass");
```

# Describe the difference between `<script>`, `<script async>` and `<script defer>`

All of these ways (`<script>`, `<script async>`, and `<script defer>`) are used to load and execute JavaScript files in an HTML document, but they differ in how the browser handles loading and execution of the script:

- `<script>` is the default way of including JavaScript. The browser blocks HTML parsing while the script is being downloaded and executed. The browser will not continue rendering the page until the script has finished executing.
- `<script async>` downloads the script asynchronously, in parallel with parsing the HTML. Executes the script as soon as it is available, potentially interrupting the HTML parsing. Multiple `<script async>` tags do not wait for each other and execute in no particular order.
- `<script defer>` downloads the script asynchronously, in parallel with parsing the HTML. However, the execution of the script is deferred until HTML parsing is complete, in the order they appear in the HTML.

Here's a table summarizing the 4 ways of loading `<script>`s in an HTML document. Modern apps almost always use modules, which deserve their own row.

| Feature          | `<script>`             | `<script async>`                                      | `<script defer>`                                              | `<script type="module">`                                                        |
| ---------------- | ---------------------- | ----------------------------------------------------- | ------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| Parsing behavior | Blocks HTML parsing    | Downloads in parallel; execution still blocks parsing | Downloads in parallel; execution deferred until after parsing | Downloads in parallel; execution deferred until after parsing                   |
| Execution order  | In order of appearance | Not guaranteed                                        | In order of appearance                                        | In order of appearance, with each script's `import` dependencies resolved first |
| DOM dependency   | No                     | No                                                    | Yes (waits for DOM)                                           | Yes (waits for DOM)                                                             |

# What is the difference between the Window object and the Document object?

The `Window` object represents the browser window and provides methods to control it, such as opening new windows or accessing the browser history. The `Document` object represents the content of the web page loaded in the window and provides methods to manipulate the DOM, such as selecting elements or modifying their content.

# Describe the difference between a cookie, `sessionStorage` and `localStorage` in browsers

All of the following are mechanisms of storing data on the client, the user's browser in this case. `localStorage` and `sessionStorage` both implement the [Web Storage API interface](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API).

- **Cookies**: Suitable for server-client communication, small storage capacity, can be persistent or session-based, domain-specific. Sent to the server on every request.
- **`localStorage`**: Suitable for long-term storage, data persists even after the browser is closed, accessible across all tabs and windows of the same origin, highest storage capacity among the three.
- **`sessionStorage`**: Suitable for temporary data within a single page session, data is cleared when the tab or window is closed, has a higher storage capacity compared to cookies.

Here's a table summarizing the 3 client storage mechanisms.

| Property                               | Cookie                                               | `localStorage`      | `sessionStorage`    |
| -------------------------------------- | ---------------------------------------------------- | ------------------- | ------------------- |
| Initiator                              | Client or server. Server can use `Set-Cookie` header | Client              | Client              |
| Lifespan                               | As specified                                         | Until deleted       | Until tab is closed |
| Persistent across browser sessions     | If a future expiry date is set                       | Yes                 | No                  |
| Sent to server with every HTTP request | Yes, sent via `Cookie` header                        | No                  | No                  |
| Total capacity (per domain)            | 4kb                                                  | 5MB                 | 5MB                 |
| Access                                 | Across windows/tabs                                  | Across windows/tabs | Same tab            |
| Security                               | JavaScript cannot access `HttpOnly` cookies          | None                | None                |

# How do you make an HTTP request using the Fetch API?

To make an HTTP request using the Fetch API, you can use the `fetch` function, which returns a promise. You can handle the response using `.then()` and `.catch()` for error handling. Here's a basic example of a GET request:

```js live
fetch("https://jsonplaceholder.typicode.com/todos/1")
  .then((response) => response.json())
  .then((data) => console.log(data))
  .catch((error) => console.error("Error:", error));
```

For a POST request, you can pass an options object as the second argument to `fetch`:

```js live
fetch("https://jsonplaceholder.typicode.com/posts", {
  method: "POST",
  body: JSON.stringify({
    title: "foo",
    body: "bar",
    userId: 1,
  }),
  headers: {
    "Content-Type": "application/json; charset=UTF-8",
  },
})
  .then((response) => response.json())
  .then((data) => console.log(data))
  .catch((error) => console.error("Error:", error));
```

# What are the different ways to make an API call in JavaScript?

In JavaScript, you can make API calls using several methods. The most common ones are `XMLHttpRequest`, `fetch`, and third-party libraries like `Axios`. `XMLHttpRequest` is the traditional way but is more verbose. `fetch` is modern and returns promises, making it easier to work with. `Axios` is a popular third-party library that simplifies API calls and provides additional features.

# Explain AJAX in as much detail as possible

AJAX (Asynchronous JavaScript and XML) facilitates asynchronous communication between the client and server, enabling dynamic updates to web pages without reloading. It uses techniques like `XMLHttpRequest` or the `fetch()` API to send and receive data in the background. In modern web applications, the `fetch()` API is more commonly used to implement AJAX.

**Using `XMLHttpRequest`**

```js live
let xhr = new XMLHttpRequest();
xhr.onreadystatechange = function () {
  if (xhr.readyState === XMLHttpRequest.DONE) {
    if (xhr.status === 200) {
      console.log(xhr.responseText);
    } else {
      console.error("Request failed: " + xhr.status);
    }
  }
};
xhr.open("GET", "https://jsonplaceholder.typicode.com/todos/1", true);
xhr.send();
```

**Using `fetch()`**

```js live
fetch("https://jsonplaceholder.typicode.com/todos/1")
  .then((response) => {
    if (!response.ok) {
      throw new Error("Network response was not ok");
    }
    return response.json();
  })
  .then((data) => console.log(data))
  .catch((error) => console.error("Fetch error:", error));
```

# What are the advantages and disadvantages of using AJAX?

AJAX (Asynchronous JavaScript and XML) is a technique in JavaScript that allows web pages to send and retrieve data asynchronously from servers without refreshing or reloading the entire page.

**Advantages**

- **Smoother user experience**: Updates happen without full page reloads, like in mail and chat applications.
- **Lighter server load**: Only necessary data is fetched via AJAX, reducing server load and improving perceived performance of webpages.
- **Maintains client state**: User interactions and any client states are persisted within the page.

**Disadvantages**

- **Reliance on JavaScript**: If disabled, AJAX functionality breaks.
- **Bookmarking issues**: Dynamic content makes bookmarking specific page states difficult.
- **SEO challenges**: Search engines may struggle to index dynamic content.
- **Performance concerns**: Processing AJAX data on low-end devices can be slow.

# What are the differences between `XMLHttpRequest` and `fetch()` in JavaScript and browsers?

`XMLHttpRequest` (XHR) and `fetch()` API are both used for asynchronous HTTP requests in JavaScript (AJAX). `fetch()` offers a cleaner syntax, promise-based approach, and more modern feature set compared to XHR. However, there are some differences:

- `XMLHttpRequest` uses event callbacks, while `fetch()` utilizes promise chaining.
- `fetch()` provides more flexibility in headers and request bodies.
- `fetch()` supports cleaner error handling with `catch()`.
- Handling caching with `XMLHttpRequest` is difficult, but caching is supported by `fetch()` by default via the `cache` value of the second parameter to `fetch()` or `Request()`.
- `fetch()` requires an `AbortController` for cancelation, while `XMLHttpRequest` provides an `abort()` method.
- `XMLHttpRequest` has good support for progress tracking, which `fetch()` lacks.
- `XMLHttpRequest` is only available in the browser and not natively supported in Node.js environments. On the other hand `fetch()` is part of the JavaScript language and is supported on all modern JavaScript runtimes.

These days `fetch()` is preferred for its cleaner syntax and modern features.

# How do you abort a web request using `AbortController` in JavaScript?

`AbortController` is used to cancel ongoing asynchronous operations like fetch requests.

```js live
const controller = new AbortController();
const signal = controller.signal;

fetch("https://jsonplaceholder.typicode.com/todos/1", { signal })
  .then((response) => {
    // Handle response
  })
  .catch((error) => {
    if (error.name === "AbortError") {
      console.log("Request aborted");
    } else {
      console.error("Error:", error);
    }
  });

// Call abort() to abort the request
controller.abort();
```

Aborting web requests is useful for:

- Canceling requests based on user actions.
- Prioritizing the latest requests in scenarios with multiple simultaneous requests.
- Canceling requests that are no longer needed, e.g. after the user has navigated away from the page.

# Explain how JSONP works (and how it's not really Ajax)

JSONP (JSON with Padding) is a technique used to overcome the same-origin policy in web browsers, allowing you to request data from a server on a different domain. It works by dynamically creating a `<script>` tag and setting its `src` attribute to the URL of the data source. The server responds with a script that calls a predefined callback function with the data as its argument. Unlike Ajax, JSONP does not use the XMLHttpRequest object and is limited to GET requests.

# What are workers in JavaScript used for?

Workers in JavaScript are background threads that allow you to run scripts in parallel with the main execution thread, without blocking or interfering with the user interface. Their key features include:

- **Parallel processing**: Workers run in a separate thread from the main thread, allowing your web page to remain responsive to user interactions while the worker performs its tasks. It's useful for moving CPU-intensive work off the main thread and freeing you from JavaScript's single-threaded nature.
- **Communication**: Uses `postMessage()` and `onmessage`/`'message'` event for messaging.
- **Access to web APIs**: Workers have access to various Web APIs, including `fetch()`, IndexedDB, and Web Storage, allowing them to perform tasks like data fetching and persisting data independently.
- **No DOM access**: Workers cannot directly manipulate the DOM, thus cannot interact with the UI, ensuring they don't accidentally interfere with the main thread's operation.

There are three main types of workers in JavaScript:

- **Web workers / Dedicated workers**
  - Run scripts in background threads, separate from the main UI thread.
  - Useful for CPU-intensive tasks like data processing, calculations, etc.
  - Cannot directly access or manipulate the DOM.
- **Service workers**
  - Act as network proxies, handling requests between the app and network.
  - Enable offline functionality, caching, and push notifications.
  - Run independently of the web page, even when it's closed.
- **Shared workers**
  - Can be shared by multiple scripts running in different windows or frames, as long as they're in the same domain.
  - Scripts communicate with the shared worker by sending and receiving messages.
  - Useful for coordinating tasks across different parts of a web page.

# Explain the concept of the Web Socket API

The WebSocket API provides a way to open a persistent connection between a client and a server, allowing for real-time, two-way communication. Unlike HTTP, which is request-response based, WebSocket enables full-duplex communication, meaning both the client and server can send and receive messages independently. This is particularly useful for applications like chat apps, live updates, and online gaming.

The following example uses Postman's WebSocket echo service to demonstrate how web sockets work.

```js live
// Postman's echo server that will echo back messages you send
const socket = new WebSocket("wss://ws.postman-echo.com/raw");

// Event listener for when the connection is open
socket.addEventListener("open", function (event) {
  socket.send("Hello Server!"); // Sends the message to the Postman WebSocket server
});

// Event listener for when a message is received from the server
socket.addEventListener("message", function (event) {
  console.log("Message from server ", event.data);
});
```

# What are JavaScript polyfills for?

Polyfills in JavaScript are pieces of code that provide modern functionality to older browsers that lack native support for those features. They bridge the gap between the JavaScript language features and APIs available in modern browsers and the limited capabilities of older browser versions.

They can be implemented manually or included through libraries and are often used in conjunction with feature detection.

Common use cases include:

- **New JavaScript Methods**: For example, `Array.prototype.includes()`, `Object.assign()`, etc.
- **New APIs**: Such as `fetch()`, `Promise`, `IntersectionObserver`, etc. Modern browsers support these now, but for a long time they had to be polyfilled.

Libraries and services for polyfills:

- **`core-js`**: A modular standard library for JavaScript which includes polyfills for a wide range of ECMAScript features.

  ```js
  import "core-js/actual/array/flat-map"; // With this, Array.prototype.flatMap is available to be used.

  [1, 2].flatMap((it) => [it, it]); // => [1, 1, 2, 2]
  ```

- **Polyfill.io**: A service that provides polyfills based on the features and user agents specified in the request.

  ```js
  <script src="https://polyfill.io/v3/polyfill.min.js"></script>
  ```

# How do you detect if JavaScript is disabled on a page?

To detect if JavaScript is disabled on a page, you can use the `<noscript>` HTML tag. This tag allows you to display content or messages to users who have JavaScript disabled in their browsers. For example, you can include a message within the `<noscript>` tag to inform users that JavaScript is required for the full functionality of the page.

```html
<noscript>
  <p>
    JavaScript is disabled in your browser. Please enable JavaScript for the
    best experience.
  </p>
</noscript>
```

# What is the `Intl` namespace object for?

The `Intl` namespace object in JavaScript is used for internationalization purposes. It provides language-sensitive string comparison, number formatting, and date and time formatting. For example, you can use `Intl.DateTimeFormat` to format dates according to a specific locale:

```js live
const date = new Date();
const formatter = new Intl.DateTimeFormat("en-US");
console.log(formatter.format(date)); // Outputs date in 'MM/DD/YYYY' format
```

# How do you validate form elements using the Constraint Validation API?

The Constraint Validation API provides a way to validate form elements in HTML. You can use properties like `validity`, `validationMessage`, and methods like `checkValidity()` and `setCustomValidity()`. For example, to check if an input is valid, you can use:

```js
const input = document.querySelector("input");
if (input.checkValidity()) {
  console.log("Input is valid");
} else {
  console.log(input.validationMessage);
}
```

# How do you use `window.history` API?

The `window.history` API allows you to manipulate the browser's session history. You can use `history.pushState()` to add a new entry to the history stack, `history.replaceState()` to modify the current entry, and `history.back()`, `history.forward()`, and `history.go()` to navigate through the history. For example, `history.pushState({page: 1}, "title 1", "?page=1")` adds a new entry to the history.

# How do `<iframe>` on a page communicate?

`<iframe>` elements on a page can communicate using the `postMessage` API. This allows for secure cross-origin communication between the parent page and the iframe. The `postMessage` method sends a message, and the `message` event listener receives it. Here's a simple example:

```js
// In the parent page
const iframe = document.querySelector("iframe");
iframe.contentWindow.postMessage("Hello from parent", "*");

// In the iframe
window.addEventListener("message", (event) => {
  console.log(event.data); // 'Hello from parent'
});
```

# Difference between document `load` event and document `DOMContentLoaded` event?

The `DOMContentLoaded` event fires when the initial HTML document has been completely loaded and parsed, without waiting for stylesheets, images, and subframes to finish loading. The `load` event, on the other hand, fires when the entire page, including all dependent resources such as stylesheets and images, has finished loading.

```javascript
document.addEventListener("DOMContentLoaded", function () {
  console.log("DOM fully loaded and parsed");
});

window.addEventListener("load", function () {
  console.log("Page fully loaded");
});
```

# How do you redirect to a new page in JavaScript?

To redirect to a new page in JavaScript, you can use the `window.location` object. The most common methods are `window.location.href` and `window.location.replace()`. For example:

```js
// Using window.location.href
window.location.href = "https://www.example.com";

// Using window.location.replace()
window.location.replace("https://www.example.com");
```

# How do you get the query string values of the current page in JavaScript?

To get the query string values of the current page in JavaScript, you can use the `URLSearchParams` object. First, create a `URLSearchParams` instance with `window.location.search`, then use the `get` method to retrieve specific query parameters. For example:

```js
const params = new URLSearchParams(window.location.search);
const value = params.get("language");
console.log(value);
```

# What are server-sent events?

[Server-sent events (SSE)](https://html.spec.whatwg.org/multipage/comms.html#the-eventsource-interface) is a standard that allows a web page to receive automatic updates from a server via an HTTP connection. Server-sent events are used with `EventSource` instances that open a connection with a server and allow the client to receive events from the server. Connections created by server-sent events are persistent (similar to the `WebSocket`s), however there are a few differences:

| Property  | `WebSocket`                                                   | `EventSource`                           |
| --------- | ------------------------------------------------------------- | --------------------------------------- |
| Direction | Bi-directional – both client and server can exchange messages | Unidirectional – only server sends data |
| Data type | Binary and text data                                          | Only text                               |
| Protocol  | WebSocket protocol (`ws://`)                                  | Regular HTTP (`http://`)                |

**Creating an event source**

```js
const eventSource = new EventSource("/sse-stream");
```

**Listening for events**

```js
// Fired when the connection is established.
eventSource.addEventListener("open", () => {
  console.log("Connection opened");
});

// Fired when a message is received from the server.
eventSource.addEventListener("message", (event) => {
  console.log("Received message:", event.data);
});

// Fired when an error occurs.
eventSource.addEventListener("error", (error) => {
  console.error("Error occurred:", error);
});
```

**Sending events from server**

```js
const express = require("express");
const app = express();

app.get("/sse-stream", (req, res) => {
  // `Content-Type` need to be set to `text/event-stream`.
  res.setHeader("Content-Type", "text/event-stream");
  res.setHeader("Cache-Control", "no-cache");
  res.setHeader("Connection", "keep-alive");

  // Each message should be prefixed with data.
  const sendEvent = (data) => res.write(`data: ${data}\n\n`);

  sendEvent("Hello from server");

  const intervalId = setInterval(() => sendEvent(new Date().toString()), 1000);

  res.on("close", () => {
    console.log("Client closed connection");
    clearInterval(intervalId);
  });
});

app.listen(3000, () => console.log("Server started on port 3000"));
```

In this example, the server sends a "Hello from server" message initially, and then sends the current date every second. The connection is kept alive until the client closes it.

# What are Progressive Web Applications (PWAs)?

Progressive Web Applications (PWAs) are web applications that use modern web capabilities to deliver an app-like experience to users. They are reliable, fast, and engaging. PWAs can work offline, send push notifications, and be installed on a user's home screen. They leverage technologies like service workers, web app manifests, and HTTPS to provide these features.

# What are modules and why are they useful?

Modules are reusable pieces of code that can be imported and exported between different files in a project. They help in organizing code, making it more maintainable and scalable. By using modules, you can avoid global namespace pollution and manage dependencies more effectively. In JavaScript, you can use `import` and `export` statements to work with modules.

```js
// myModule.js
export const myFunction = () => {
  console.log("Hello, World!");
};

// main.js
import { myFunction } from "./myModule.js";
myFunction(); // Outputs: Hello, World!
```

# Explain the differences between CommonJS modules and ES modules in JavaScript

In JavaScript, modules are reusable pieces of code that encapsulate functionality, making it easier to manage, maintain, and structure your applications. Modules allow you to break down your code into smaller, manageable parts, each with its own scope.

**CommonJS** is an older module system that was initially designed for server-side JavaScript development with Node.js. It uses the `require()` function to load modules and the `module.exports` or `exports` object to define the exports of a module.

```js
// my-module.js
const value = 42;
module.exports = { value };

// main.js
const myModule = require("./my-module.js");
console.log(myModule.value); // 42
```

**ES Modules** (ECMAScript Modules) are the standardized module system introduced in ES6 (ECMAScript 2015). They use the `import` and `export` statements to handle module dependencies.

```js
// my-module.js
export const value = 42;

// main.js
import { value } from "./my-module.js";
console.log(value); // 42
```

**CommonJS vs ES modules**

| Feature         | CommonJS                                                 | ES modules                                                                                                                 |
| --------------- | -------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| Module Syntax   | `require()` for importing `module.exports` for exporting | `import` for importing `export` for exporting                                                                              |
| Environment     | Primarily used in Node.js for server-side development    | Designed for both browser and server-side JavaScript (Node.js)                                                             |
| Loading         | Synchronous loading of modules                           | Asynchronous loading of modules                                                                                            |
| Structure       | Dynamic imports, can be conditionally called             | ES modules use static top-level import/export statements, while dynamic loading is supported separately via the `import()` |
| File extensions | `.js` (default)                                          | `.mjs` or `.js` (with `type: "module"` in `package.json`)                                                                  |
| Browser support | Not natively supported in browsers                       | Natively supported in modern browsers                                                                                      |
| Optimization    | Limited optimization due to dynamic nature               | Allows for optimizations like tree-shaking due to static structure                                                         |
| Compatibility   | Widely used in existing Node.js codebases and libraries  | Newer standard, but gaining adoption in modern projects                                                                    |

# How do you import and export modules in JavaScript?

In JavaScript, you can import and export modules using the `import` and `export` statements. To export a module, you can use `export` before a function, variable, or class, or use `export default` for a single default export. To import a module, you use the `import` statement followed by the name of the exported module and the path to the module file.

```js
// Exporting a module
export const myFunction = () => {
  /* ... */
};
export default myFunction;

// Importing a module
import { myFunction } from "./myModule";
import myFunction from "./myModule";
```

# What are the benefits of using a module bundler?

Using a module bundler like Webpack, Rollup, or Parcel helps manage dependencies, optimize performance, and improve the development workflow. It combines multiple JavaScript files into a single file or a few files, which reduces the number of HTTP requests and can include features like code splitting, tree shaking, and hot module replacement.

# Explain the concept of tree shaking in module bundling

Tree shaking is a technique used in module bundling to eliminate dead code, which is code that is never used or executed. This helps to reduce the final bundle size and improve application performance. It works by analyzing the dependency graph of the code and removing any unused exports. Tools like Webpack and Rollup support tree shaking when using ES6 module syntax (`import` and `export`).

# What are the metadata fields of a module?

Metadata fields of a module typically include information such as the module's name, version, description, author, license, and dependencies. These fields are often found in a `package.json` file in JavaScript projects. For example:

```json
{
  "name": "my-module",
  "version": "1.0.0",
  "description": "A sample module",
  "author": "John Doe",
  "license": "MIT",
  "dependencies": {
    "express": "^4.17.1"
  }
}
```

# What do you think of CommonJS vs ESM?

JavaScript has evolved its module systems. ESM (ECMAScript Modules) using `import` / `export` is the official standard, natively supported in modern browsers and Node.js, designed for both synchronous and asynchronous use cases. CommonJS (CJS) using `require` / `module.exports` was the original standard for Node.js, primarily synchronous, and remains prevalent in the Node ecosystem. AMD (Asynchronous Module Definition) using `define` / `require` was an early system designed for asynchronous loading in browsers but is now largely obsolete, replaced by ESM.

# What are the different types of errors in JavaScript?

In JavaScript, there are three main types of errors: syntax errors, runtime errors, and logical errors. Syntax errors occur when the code violates the language's grammar rules, such as missing a parenthesis. Runtime errors happen during code execution, like trying to access a property of `undefined`. Logical errors are mistakes in the code's logic that lead to incorrect results but don't throw an error.

# How do you handle errors using `try...catch` blocks?

To handle errors using `try...catch` blocks, you wrap the code that might throw an error inside a `try` block. If an error occurs, the control is transferred to the `catch` block where you can handle the error. Optionally, you can use a `finally` block to execute code regardless of whether an error occurred or not.

```js
try {
  // Code that may throw an error
} catch (error) {
  // Handle the error
} finally {
  // Code that will run regardless of an error
}
```

# What is the purpose of the `finally` block?

The `finally` block in JavaScript is used to execute code after a `try` and `catch` block, regardless of whether an error was thrown or caught. It ensures that certain cleanup or finalization code runs no matter what. For example:

```js
try {
  // Code that may throw an error
} catch (error) {
  // Code to handle the error
} finally {
  // Code that will always run
}
```

# How can you create custom error objects?

To create custom error objects in JavaScript, you can extend the built-in `Error` class. This allows you to add custom properties and methods to your error objects. Here's a quick example:

```js live
class CustomError extends Error {
  constructor(message) {
    super(message);
    this.name = "CustomError";
  }
}

try {
  throw new CustomError("This is a custom error message");
} catch (error) {
  console.log(error.name); // CustomError
  console.log(error.message); // This is a custom error message
}
```

# Explain the concept of error propagation in JavaScript

Error propagation in JavaScript refers to how errors are passed through the call stack. When an error occurs in a function, it can be caught and handled using `try...catch` blocks. If not caught, the error propagates up the call stack until it is either caught or causes the program to terminate. For example:

```js live
function a() {
  throw new Error("An error occurred");
}

function b() {
  a();
}

try {
  b();
} catch (e) {
  console.error(e.message); // Outputs: An error occurred
}
```

# What is currying and how does it work?

Currying is a technique in functional programming where a function that takes multiple arguments is transformed into a series of functions that each take a single argument. This allows for partial application of functions. For example, a function `f(a, b, c)` can be curried into `f(a)(b)(c)`. Here's a simple example in JavaScript:

```js live
function add(a) {
  return function (b) {
    return function (c) {
      return a + b + c;
    };
  };
}

const addOne = add(1);
console.log(addOne); // function object

const addOneAndTwo = addOne(2);
console.log(addOneAndTwo); // function object

const result = addOneAndTwo(3);
console.log(result); // Output: 6
```

# Explain the concept of partial application

Partial application is a technique in functional programming where a function is applied to some of its arguments, producing a new function that takes the remaining arguments. This allows you to create more specific functions from general ones. For example, if you have a function `add(a, b)`, you can partially apply it to create a new function `add5` that always adds 5 to its argument.

```js live
function add(a, b) {
  return a + b;
}

const add5 = add.bind(null, 5);
console.log(add5(10)); // Outputs 15
```

# What are the benefits of using currying and partial application?

Currying transforms a function with multiple arguments into a sequence of functions, each taking a single argument. This allows for more flexible and reusable code. Partial application, on the other hand, allows you to fix a few arguments of a function and generate a new function. Both techniques help in creating more modular and maintainable code.

# Provide some examples of how currying and partial application can be used

Currying transforms a function with multiple arguments into a sequence of functions, each taking a single argument. Partial application fixes a few arguments of a function, producing another function with a smaller number of arguments. For example, currying a function `add(a, b)` would look like `add(a)(b)`, while partial application of `add(2, b)` would fix the first argument to 2, resulting in a function that only needs the second argument.

Currying example:

```js live
const add = (a) => (b) => a + b;
const addTwo = add(2);
console.log(addTwo(3)); // 5
```

Partial application example:

```js live
const add = (a, b) => a + b;
const addTwo = add.bind(null, 2);
console.log(addTwo(3)); // 5
```

# How do currying and partial application differ from each other?

Currying transforms a function with multiple arguments into a sequence of functions, each taking a single argument. For example, a function `f(a, b, c)` becomes `f(a)(b)(c)`. Partial application, on the other hand, fixes a few arguments of a function and produces another function with a smaller number of arguments. For example, if you partially apply `f(a, b, c)` with `a`, you get a new function `f'(b, c)`.

# What are `Set`s and `Map`s and how are they used?

`Set`s and `Map`s are built-in JavaScript objects that help manage collections of data. A `Set` is a collection of unique values, while a `Map` is a collection of key-value pairs where keys can be of any type. `Set`s are useful for storing unique items, and `Map`s are useful for associating values with keys.

```js live
// Set example
let mySet = new Set([1, 2, 3, 3]); // Set {1, 2, 3} (duplicate values are not added)
mySet.add(4);
console.log(mySet); // Set {1, 2, 3, 4}

// Map example
let myMap = new Map();
myMap.set("key1", "value1");
myMap.set("key2", "value2");
console.log(myMap.get("key1")); // 'value1'
```

# What are the differences between `Map`/`Set` and `WeakMap`/`WeakSet` in JavaScript?

The primary difference between `Map`/`Set` and `WeakMap`/`WeakSet` in JavaScript lies in how they handle keys. Here's a breakdown:

**`Map` vs. `WeakMap`**

`Map`s allow any data type (strings, numbers, objects) as keys. The key-value pairs remain in memory as long as the `Map` object itself is referenced. Thus they are suitable for general-purpose key-value storage where you want to maintain references to both keys and values. Common use cases include storing user data, configuration settings, or relationships between objects.

`WeakMap`s only allow objects as keys. However, these object keys are held weakly. This means the garbage collector can remove them from memory even if the `WeakMap` itself still exists, as long as there are no other references to those objects. `WeakMap`s are ideal for scenarios where you want to associate data with objects without preventing those objects from being garbage collected. This can be useful for things like:

- Caching data based on objects without preventing garbage collection of the objects themselves.
- Storing private data associated with DOM nodes without affecting their lifecycle.

**`Set` vs. `WeakSet`**

Similar to `Map`, `Set`s allow any data type as keys. The elements within a `Set` must be unique. `Set`s are useful for storing unique values and checking for membership efficiently. Common use cases include removing duplicates from arrays or keeping track of completed tasks.

On the other hand, `WeakSet` only allows objects as elements, and these object elements are held weakly, similar to `WeakMap` keys. `WeakSet`s are less commonly used, but applicable when you want a collection of unique objects without affecting their garbage collection. This might be necessary for:

- Tracking DOM nodes that have been interacted with without affecting their memory management.
- Implementing custom object weak references for specific use cases.

**Here's a table summarizing the key differences:**

| Feature            | Map                                       | WeakMap                                                   | Set                                    | WeakSet                                                       |
| ------------------ | ----------------------------------------- | --------------------------------------------------------- | -------------------------------------- | ------------------------------------------------------------- |
| Key Types          | Any data type                             | Objects (weak references)                                 | Any data type (unique)                 | Objects (weak references, unique)                             |
| Garbage Collection | Keys and values are not garbage collected | Keys can be garbage collected if not referenced elsewhere | Elements are not garbage collected     | Elements can be garbage collected if not referenced elsewhere |
| Use Cases          | General-purpose key-value storage         | Caching, private DOM node data                            | Removing duplicates, membership checks | Object weak references, custom use cases                      |

**Choosing between them**

- Use `Map` and `Set` for most scenarios where you need to store key-value pairs or unique elements and want to maintain references to both the keys/elements and the values.
- Use `WeakMap` and `WeakSet` cautiously in specific situations where you want to associate data with objects without affecting their garbage collection. Be aware of the implications of weak references and potential memory leaks if not used correctly.

# How do you convert a `Set` to an array in JavaScript?

To convert a `Set` to an array in JavaScript, you can use the `Array.from()` method or the spread operator. For example:

```js live
const mySet = new Set([1, 2, 3]);
const myArray = Array.from(mySet);
// OR const myArray = [...mySet];

console.log(myArray); // Output: [1, 2, 3]
```

# What is the difference between a `Map` object and a plain object in JavaScript?

Both `Map` objects and plain objects in JavaScript can store key-value pairs, but they have several key differences:

| Feature       | `Map`                                                                 | Plain object                                    |
| ------------- | --------------------------------------------------------------------- | ----------------------------------------------- |
| Key type      | Any data type                                                         | String (or Symbol)                              |
| Key order     | Maintained                                                            | Not guaranteed                                  |
| Size property | Yes (`size`)                                                          | None                                            |
| Iteration     | `forEach`, `keys()`, `values()`, `entries()`                          | `for...in`, `Object.keys()`, etc.               |
| Inheritance   | No                                                                    | Yes                                             |
| Performance   | Generally better for larger datasets and frequent additions/deletions | Faster for small datasets and simple operations |
| Serializable  | No                                                                    | Yes                                             |

# How do `Set`s and `Map`s handle equality checks for objects?

`Set`s and `Map`s in JavaScript handle equality checks for objects based on reference equality, not deep equality. This means that two objects are considered equal only if they reference the same memory location. For example, if you add two different object literals with the same properties to a `Set`, they will be treated as distinct entries.

```js live
const set = new Set();
const obj1 = { a: 1 };
const obj2 = { a: 1 };

set.add(obj1);
set.add(obj2);

console.log(set.size); // Output: 2
```

# What are some common performance bottlenecks in JavaScript applications?

Common performance bottlenecks in JavaScript applications include inefficient DOM manipulation, excessive use of global variables, blocking the main thread with heavy computations, memory leaks, and improper use of asynchronous operations. To mitigate these issues, you can use techniques like debouncing and throttling, optimizing DOM updates, and leveraging web workers for heavy computations.

# Explain the concept of debouncing and throttling

Debouncing and throttling are techniques used to control the rate at which a function is executed. Debouncing ensures that a function is only called after a specified delay has passed since the last time it was invoked. Throttling ensures that a function is called at most once in a specified time interval.

Debouncing delays the execution of a function until a certain amount of time has passed since it was last called. This is useful for scenarios like search input fields where you want to wait until the user has stopped typing before making an API call.

```js live
function debounce(func, delay) {
  let timeoutId;
  return function (...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => func.apply(this, args), delay);
  };
}

const debouncedHello = debounce(() => console.log("Hello world!"), 2000);
debouncedHello(); // Prints 'Hello world!' after 2 seconds
```

Throttling ensures that a function is called at most once in a specified time interval. This is useful for scenarios like window resizing or scrolling where you want to limit the number of times a function is called.

```js live
function throttle(func, limit) {
  let inThrottle;
  return function (...args) {
    if (!inThrottle) {
      func.apply(this, args);
      inThrottle = true;
      setTimeout(() => (inThrottle = false), limit);
    }
  };
}

const handleResize = throttle(() => {
  // Update element positions
  console.log("Window resized at", new Date().toLocaleTimeString());
}, 2000);

// Simulate rapid calls to handleResize every 100ms
let intervalId = setInterval(() => {
  handleResize();
}, 100);
// 'Window resized' is outputted only every 2 seconds due to throttling
```

# How can you optimize DOM manipulation for better performance?

To optimize DOM manipulation for better performance, minimize direct DOM access and updates. Use techniques like batching DOM changes, using `documentFragment` for multiple elements, and leveraging virtual DOM libraries like React. Also, consider using `requestAnimationFrame` for animations and avoid layout thrashing by reading and writing DOM properties separately.

# What are some techniques for reducing reflows and repaints?

To reduce reflows and repaints, you can minimize DOM manipulations, batch DOM changes, use CSS classes for style changes, avoid complex CSS selectors, and use `requestAnimationFrame` for animations. Additionally, consider using `will-change` for elements that will change frequently and avoid layout thrashing by reading and writing to the DOM separately.

# Explain the concept of lazy loading and how it can improve performance

Lazy loading is a design pattern that delays the loading of resources until they are actually needed. This can significantly improve performance by reducing initial load times and conserving bandwidth. For example, images on a webpage can be lazy-loaded so that they only load when they come into the viewport. This can be achieved using the `loading="lazy"` attribute in HTML or by using JavaScript libraries.

```html
<img src="image.jpg" loading="lazy" alt="Lazy loaded image" />
```

# What are Web Workers and how can they be used to improve performance?

Web Workers are a way to run JavaScript in the background, separate from the main execution thread of a web application. This helps in performing heavy computations without blocking the user interface. You can create a Web Worker using the `Worker` constructor and communicate with it using the `postMessage` and `onmessage` methods.

```js
// main.js
const worker = new Worker("worker.js");
worker.postMessage("Hello, worker!");

worker.onmessage = function (event) {
  console.log("Message from worker:", event.data);
};

// worker.js
onmessage = function (event) {
  console.log("Message from main script:", event.data);
  postMessage("Hello, main script!");
};
```

# Explain the concept of caching and how it can be used to improve performance

Caching is a technique used to store copies of files or data in a temporary storage location to reduce the time it takes to access them. It improves performance by reducing the need to fetch data from the original source repeatedly. In front end development, caching can be implemented using browser cache, service workers, and HTTP headers like `Cache-Control`.

# What are some tools that can be used to measure and analyze JavaScript performance?

To measure and analyze JavaScript performance, you can use tools like Chrome DevTools, Lighthouse, WebPageTest, and JSPerf. Chrome DevTools provides a Performance panel for profiling, Lighthouse offers audits for performance metrics, WebPageTest allows for detailed performance testing, and JSPerf helps in comparing the performance of different JavaScript snippets.

# How can you optimize network requests for better performance?

To optimize network requests for better performance, you can minimize the number of requests, use caching, compress data, and leverage modern web technologies like HTTP/2 and service workers. For example, you can combine multiple CSS files into one to reduce the number of requests, use `Cache-Control` headers to cache static assets, and enable Gzip compression on your server to reduce the size of the data being transferred.

# What are the different types of testing in software development?

In software development, there are several types of testing to ensure the quality and functionality of the application. These include unit testing, integration testing, system testing, and acceptance testing. Unit testing focuses on individual components, integration testing checks the interaction between components, system testing evaluates the entire system, and acceptance testing ensures the software meets user requirements.

# Explain the difference between unit testing, integration testing, and end-to-end testing

Unit testing focuses on testing individual components or functions in isolation to ensure they work as expected. Integration testing checks how different modules or services work together. End-to-end testing simulates real user scenarios to verify the entire application flow from start to finish.

# What are some popular JavaScript testing frameworks?

Some popular JavaScript testing frameworks include Jest, Mocha, Jasmine, and Cypress. Jest is known for its simplicity and integration with React. Mocha is highly flexible and often used with other libraries like Chai for assertions. Jasmine is a behavior-driven development framework that requires no additional libraries. Cypress is an end-to-end testing framework that provides a great developer experience.

# How do you write unit tests for JavaScript code?

To write unit tests for JavaScript code, you typically use a testing framework like Jest or Mocha. First, you set up your testing environment by installing the necessary libraries. Then, you write test cases using functions like `describe`, `it`, or `test` to define your tests. Each test case should focus on a small, isolated piece of functionality. You use assertions to check if the output of your code matches the expected result.

Example using Jest:

```js
// sum.js
function sum(a, b) {
  return a + b;
}
module.exports = sum;

// sum.test.js
const sum = require("./sum");

test("adds 1 + 2 to equal 3", () => {
  expect(sum(1, 2)).toBe(3);
});
```

# Explain the concept of test-driven development (TDD)

Test-driven development (TDD) is a software development approach where you write tests before writing the actual code. The process involves writing a failing test, writing the minimum code to pass the test, and then refactoring the code while keeping the tests passing. This ensures that the code is always tested and helps in maintaining high code quality.

# What are mocks and stubs and how are they used in testing?

Mocks and stubs are tools used in testing to simulate the behavior of real objects. Stubs provide predefined responses to function calls, while mocks are more complex and can verify interactions, such as whether a function was called and with what arguments. Stubs are used to isolate the code being tested from external dependencies, and mocks are used to ensure that the code interacts correctly with those dependencies.

# How can you test asynchronous code in JavaScript?

To test asynchronous code in JavaScript, you can use testing frameworks like Jest or Mocha. These frameworks provide built-in support for handling asynchronous operations. You can use `async`/`await` or return promises in your test functions. For example, in Jest, you can write:

```js
test("fetches data successfully", async () => {
  const data = await fetchData();
  expect(data).toBeDefined();
});
```

Alternatively, you can use callbacks and the `done` function to signal the end of an asynchronous test.

# What are some best practices for writing maintainable and effective tests in JavaScript?

To write maintainable and effective tests, ensure they are clear, concise, and focused on a single behavior. Use descriptive names for test cases and avoid hardcoding values. Mock external dependencies and keep tests isolated. Regularly review and refactor tests to keep them up-to-date with the codebase.

# Explain the concept of code coverage and how it can be used to assess test quality

Code coverage is a metric that measures the percentage of code that is executed when the test suite runs. It helps in assessing the quality of tests by identifying untested parts of the codebase. Higher code coverage generally indicates more thorough testing, but it doesn't guarantee the absence of bugs. Tools like Istanbul or Jest can be used to measure code coverage.

# What are some tools that can be used for JavaScript testing?

For JavaScript testing, you can use tools like Jest, Mocha, Jasmine, and Cypress. Jest is popular for its ease of use and built-in features. Mocha is flexible and can be paired with other libraries. Jasmine is known for its simplicity and behavior-driven development (BDD) style. Cypress is great for end-to-end testing with a focus on real browser interactions.

# What are design patterns and why are they useful?

Design patterns are reusable solutions to common problems in software design. They provide a template for how to solve a problem that can be used in many different situations. They are useful because they help developers avoid common pitfalls, improve code readability, and make it easier to maintain and scale applications.

# Explain the concept of the Singleton pattern

The Singleton pattern ensures that a class has only one instance and provides a global point of access to that instance. This is useful when exactly one object is needed to coordinate actions across the system. In JavaScript, this can be implemented using closures or ES6 classes.

```js live
class Singleton {
  constructor() {
    if (!Singleton.instance) {
      Singleton.instance = this;
    }
    return Singleton.instance;
  }
}

const instance1 = new Singleton();
const instance2 = new Singleton();

console.log(instance1 === instance2); // true
```

# What is the Factory pattern and how is it used?

The Factory pattern is a design pattern used to create objects without specifying the exact class of the object that will be created. It provides a way to encapsulate the instantiation logic and can be particularly useful when the creation process is complex or when the type of object to be created is determined at runtime.

For example, in JavaScript, you can use a factory function to create different types of objects:

```js live
function createAnimal(type) {
  if (type === "dog") {
    return { sound: "woof" };
  } else if (type === "cat") {
    return { sound: "meow" };
  }
}

const dog = createAnimal("dog");
const cat = createAnimal("cat");
```

# Explain the Observer pattern and its use cases

The Observer pattern is a design pattern where an object, known as the subject, maintains a list of its dependents, called observers, and notifies them of any state changes. This pattern is useful for implementing distributed event-handling systems, such as updating the user interface in response to data changes or implementing event-driven architectures.

# What is the Module pattern and how does it help with encapsulation?

The Module pattern in JavaScript is a design pattern used to create self-contained modules of code. It helps with encapsulation by allowing you to define private and public members within a module. Private members are not accessible from outside the module, while public members are exposed through a returned object. This pattern helps in organizing code, avoiding global namespace pollution, and maintaining a clean separation of concerns.

```js live
var myModule = (function () {
  var privateVar = "I am private";

  function privateMethod() {
    console.log(privateVar);
  }

  return {
    publicMethod: function () {
      privateMethod();
    },
  };
})();

myModule.publicMethod(); // Logs: I am private
```

# Explain the concept of the Prototype pattern

The Prototype pattern is a creational design pattern used to create new objects by copying an existing object, known as the prototype. This pattern is useful when the cost of creating a new object is more expensive than cloning an existing one. In JavaScript, this can be achieved using the `Object.create` method or by using the `prototype` property of a constructor function.

```js live
const prototypeObject = {
  greet() {
    console.log("Hello, world!");
  },
};

const newObject = Object.create(prototypeObject);
newObject.greet(); // Outputs: Hello, world!
```

# What is the Decorator pattern and how is it used?

The Decorator pattern is a structural design pattern that allows behavior to be added to individual objects, dynamically, without affecting the behavior of other objects from the same class. It is used to extend the functionalities of objects by wrapping them with additional behavior. In JavaScript, this can be achieved using higher-order functions or classes.

For example, if you have a `Car` class and you want to add features like `GPS` or `Sunroof` without modifying the `Car` class itself, you can create decorators for these features.

```js live
class Car {
  drive() {
    return "Driving";
  }
}

class CarDecorator {
  constructor(car) {
    this.car = car;
  }

  drive() {
    return this.car.drive();
  }
}

class GPSDecorator extends CarDecorator {
  drive() {
    return `${super.drive()} with GPS`;
  }
}

const myCar = new Car();
const myCarWithGPS = new GPSDecorator(myCar);
console.log(myCarWithGPS.drive()); // "Driving with GPS"
```

# Explain the concept of the Strategy pattern

The Strategy pattern is a behavioral design pattern that allows you to define a family of algorithms, encapsulate each one as a separate class, and make them interchangeable. This pattern lets the algorithm vary independently from the clients that use it. For example, if you have different sorting algorithms, you can define each one as a strategy and switch between them without changing the client code.

```js live
class Context {
  constructor(strategy) {
    this.strategy = strategy;
  }

  executeStrategy(data) {
    return this.strategy.doAlgorithm(data);
  }
}

class ConcreteStrategyA {
  doAlgorithm(data) {
    // Implementation of algorithm A
    return "Algorithm A was run on " + data;
  }
}

class ConcreteStrategyB {
  doAlgorithm(data) {
    // Implementation of algorithm B
    return "Algorithm B was run on " + data;
  }
}

// Usage
const context = new Context(new ConcreteStrategyA());
context.executeStrategy("someData"); // Output: Algorithm A was run on someData
```

# What is the Command pattern and how is it used?

The Command pattern is a behavioral design pattern that turns a request into a stand-alone object containing all information about the request. This transformation allows for parameterization of methods with different requests, queuing of requests, and logging of requests. It also supports undoable operations. In JavaScript, it can be implemented by creating command objects with `execute` and `undo` methods.

```js live
class Command {
  execute() {}
  undo() {}
}

class LightOnCommand extends Command {
  constructor(light) {
    super();
    this.light = light;
  }
  execute() {
    this.light.on();
  }
  undo() {
    this.light.off();
  }
}

class Light {
  on() {
    console.log("Light is on");
  }
  off() {
    console.log("Light is off");
  }
}

const light = new Light();
const lightOnCommand = new LightOnCommand(light);
lightOnCommand.execute(); // Light is on
lightOnCommand.undo(); // Light is off
```

# Why is extending built-in JavaScript objects not a good idea?

Extending a built-in/native JavaScript object means adding properties/functions to its `prototype`. While this may seem like a good idea at first, it is dangerous in practice. Imagine your code uses two libraries that both extend the `Array.prototype` by adding the same `contains` method; the implementations will overwrite each other and your code will have unpredictable behavior if these two methods do not work the same way.

The only time you may want to extend a native object is when you want to create a polyfill, essentially providing your own implementation for a method that is part of the JavaScript specification but might not exist in the user's browser due to it being an older browser.

# What is Cross-Site Scripting (XSS) and how can you prevent it?

Cross-Site Scripting (XSS) is a security vulnerability that allows attackers to inject malicious scripts into web pages viewed by other users. This can lead to data theft, session hijacking, and other malicious activities. To prevent XSS, you should validate and sanitize user inputs, use Content Security Policy (CSP), and escape data before rendering it in the browser.

# Explain the concept of Cross-Site Request Forgery (CSRF) and its mitigation techniques

Cross-Site Request Forgery (CSRF) is an attack where a malicious website tricks a user's browser into making an unwanted request to another site where the user is authenticated. This can lead to unauthorized actions being performed on behalf of the user. Mitigation techniques include using anti-CSRF tokens, SameSite cookies, and ensuring proper CORS configurations.

# How can you prevent SQL injection vulnerabilities in JavaScript applications?

To prevent SQL injection vulnerabilities in JavaScript applications, always use parameterized queries or prepared statements instead of string concatenation to construct SQL queries. This ensures that user input is treated as data and not executable code. Additionally, use ORM libraries that handle SQL injection prevention for you, and always validate and sanitize user inputs.

# What are some best practices for handling sensitive data in JavaScript?

Handling sensitive data in JavaScript requires careful attention to security practices. Avoid storing sensitive data in client-side storage like localStorage or sessionStorage. Use HTTPS to encrypt data in transit. Implement proper authentication and authorization mechanisms. Sanitize and validate all inputs to prevent injection attacks. Consider using environment variables for sensitive data in server-side code.

# Explain the concept of Content Security Policy (CSP) and how it enhances security

Content Security Policy (CSP) is a security feature that helps prevent various types of attacks, such as Cross-Site Scripting (XSS) and data injection attacks, by specifying which content sources are trusted. It works by allowing developers to define a whitelist of trusted sources for content like scripts, styles, and images. This is done through HTTP headers or meta tags. For example, you can use the `Content-Security-Policy` header to specify that only scripts from your own domain should be executed:

```http
Content-Security-Policy: script-src 'self'
```

# What are some common security headers and their purpose?

Security headers are HTTP response headers that help protect web applications from various attacks. Some common security headers include:

- `Content-Security-Policy (CSP)`: Prevents cross-site scripting (XSS) and other code injection attacks by specifying allowed content sources.
- `X-Content-Type-Options`: Prevents MIME type sniffing by instructing the browser to follow the declared `Content-Type`.
- `Strict-Transport-Security (HSTS)`: Enforces secure (HTTPS) connections to the server.
- `X-Frame-Options`: Prevents clickjacking by controlling whether a page can be displayed in a frame.
- `X-XSS-Protection`: Enables the cross-site scripting (XSS) filter built into most browsers.
- `Referrer-Policy`: Controls how much referrer information is included with requests.

# How can you prevent clickjacking attacks?

To prevent clickjacking attacks, you can use the `X-Frame-Options` HTTP header to control whether your site can be embedded in iframes. Set it to `DENY` to prevent all framing, or `SAMEORIGIN` to allow framing only from the same origin. Additionally, you can use the `Content-Security-Policy` (CSP) header with the `frame-ancestors` directive to specify which origins are allowed to frame your content.

```http
X-Frame-Options: DENY
```

```http
Content-Security-Policy: frame-ancestors 'self'
```

# Explain the concept of input validation and its importance in security

Input validation is the process of ensuring that user input is correct, safe, and meets the application's requirements. It is crucial for security because it helps prevent attacks like SQL injection, cross-site scripting (XSS), and other forms of data manipulation. By validating input, you ensure that only properly formatted data enters your system, reducing the risk of malicious data causing harm.

# What are some tools and techniques for identifying security vulnerabilities in JavaScript code?

To identify security vulnerabilities in JavaScript code, you can use static code analysis tools like ESLint with security plugins, dynamic analysis tools like OWASP ZAP, and dependency checkers like npm audit. Additionally, manual code reviews and adhering to secure coding practices are essential techniques.

# How can you implement secure authentication and authorization in JavaScript applications?

To implement secure authentication and authorization in JavaScript applications, use HTTPS to encrypt data in transit, and store sensitive data like tokens securely using `localStorage` or `sessionStorage`. Implement token-based authentication using JWTs, and validate tokens on the server side. Use libraries like OAuth for third-party authentication and ensure proper role-based access control (RBAC) for authorization.

# Explain the same-origin policy with regards to JavaScript

The same-origin policy is a security measure implemented in web browsers to prevent malicious scripts on one page from accessing data on another page. It ensures that web pages can only make requests to the same origin, where the origin is defined by the combination of the protocol, domain, and port. For example, a script from `http://example.com` cannot access data from `http://anotherdomain.com`.

# What is `'use strict';` (strict mode) in JavaScript for?

`'use strict'` is a statement used to enable strict mode to entire scripts or individual functions. Strict mode is a way to opt into a restricted variant of JavaScript.

**Advantages**

- Makes it impossible to accidentally create global variables.
- Makes assignments which would otherwise silently fail to throw an exception.
- Makes attempts to delete undeletable properties throw an exception (where before the attempt would simply have no effect).
- Requires that function parameter names be unique.
- `this` is `undefined` in the global context.
- It catches some common coding bloopers, throwing exceptions.
- It disables features that are confusing or poorly thought out.

**Disadvantages**

- Many missing features that some developers might be used to.
- No more access to `function.caller` and `function.arguments`.
- Concatenation of scripts written in different strict modes might cause issues.

Overall, the benefits outweigh the disadvantages and there is not really a need to rely on the features that strict mode prohibits. We should all be using strict mode by default.

# What tools and techniques do you use for debugging JavaScript code?

Some of the most commonly used tools and techniques for debugging JavaScript:

- JavaScript language
  - `console` methods (e.g. `console.log()`, `console.error()`, `console.warn()`, `console.table()`)
  - `debugger` statement
- Breakpoints (browser or IDE)
- JavaScript frameworks
  - [React Devtools](https://github.com/facebook/react-devtools)
  - [Redux Devtools](https://github.com/gaearon/redux-devtools)
  - [Vue Devtools](https://github.com/vuejs/vue-devtools)
- Browser developer tools
  - **Chrome DevTools**: The most widely used tool for debugging JavaScript. It provides a rich set of features including the ability to set breakpoints, inspect variables, view the call stack, and more.
  - **Firefox Developer Tools**: Similar to Chrome DevTools with its own set of features for debugging.
  - **Safari Web Inspector**: Provides tools for debugging on Safari.
  - **Edge Developer Tools**: Similar to Chrome DevTools, as Edge is now Chromium-based.
- Network requests
  - **Postman**: Useful for debugging API calls.
  - **Fiddler**: Helps capture and inspect HTTP/HTTPS traffic.
  - **Charles Proxy**: Another tool for intercepting and debugging network calls.

# How does JavaScript garbage collection work?

Garbage collection in JavaScript is an automatic memory management mechanism that reclaims memory occupied by objects and variables that are no longer in use by the program. The two most common algorithms are mark-and-sweep and generational garbage collection.

**Mark-and-sweep**

The most common garbage collection algorithm used in JavaScript is the Mark-and-sweep algorithm. It operates in two phases:

- **Marking phase**: The garbage collector traverses the object graph, starting from the root objects (global variables, currently executing functions, etc.), and marks all reachable objects as "in-use".
- **Sweeping phase**: The garbage collector sweeps through memory, removing all unmarked objects, as they are considered unreachable and no longer needed.

This algorithm effectively identifies and removes objects that have become unreachable, freeing up memory for new allocations.

**Generational garbage collection**

Leveraged by modern JavaScript engines, objects are divided into different generations based on their age and usage patterns. Frequently accessed objects are moved to younger generations, while less frequently used objects are promoted to older generations. This optimization reduces the overhead of garbage collection by focusing on the younger generations, where most objects are short-lived.

Different JavaScript engines (which differ across browsers) implement different garbage collection algorithms and there's no standard way of doing garbage collection.

# Explain what a single page app is and how to make one SEO-friendly

A single page application (SPA) is a web application that loads a single HTML document and updates its content in the browser via JavaScript, rather than requesting a new page from the server on each navigation. This model provides application-like UX but presents challenges for search engine indexing because the initial HTML does not contain the rendered content.

In current practice, SPAs are made SEO-friendly by producing HTML on the server rather than relying solely on client-side rendering. The available strategies are server-side rendering (SSR), static site generation (SSG), incremental static regeneration (ISR), and streaming with React Server Components. Each strategy offers a different tradeoff between freshness, server cost, and time to first paint, and modern frameworks such as Next.js, Nuxt, Remix, SvelteKit, and Astro support selecting the strategy per route.

# How can you share code between JavaScript files?

To share code between JavaScript files, you can use modules. In modern JavaScript, you can use ES6 modules with `export` and `import` statements. For example, you can export a function from one file and import it into another:

```javascript
// file1.js
export function greet() {
  console.log("Hello, world!");
}

// file2.js
import { greet } from "./file1.js";
greet();
```

Alternatively, in Node.js, you can use `module.exports` and `require`:

```javascript
// file1.js
module.exports = function greet() {
  console.log("Hello, world!");
};

// file2.js
const greet = require("./file1.js");
greet();
```

# How do you organize your code?

I organize my code by following a modular approach, using a clear folder structure, and adhering to coding standards and best practices. I separate concerns by dividing code into different layers such as components, services, and utilities. I also use naming conventions and documentation to ensure code readability and maintainability.

# What are some of the advantages and disadvantages of using TypeScript and compile-to-JavaScript languages

Using languages that compile to JavaScript (most commonly TypeScript today, but historically also CoffeeScript, ReScript, Elm, and ClojureScript) can offer several advantages such as improved syntax, type safety, and better tooling. However, they also come with disadvantages like added build steps, potential performance overhead, and the need to learn new syntax.

Advantages:

- Improved syntax and readability
- Type safety and error checking
- Better tooling and editor support

Disadvantages:

- Added build steps and complexity
- Potential performance overhead
- Learning curve for new syntax

# When would you use `document.write()`?

`document.write()` is rarely used in modern web development because it can overwrite the entire document if called after the page has loaded. It is mainly used for simple tasks like writing content during the initial page load, such as for educational purposes or quick debugging. However, it is generally recommended to use other methods like `innerHTML`, `appendChild()`, or modern frameworks for manipulating the DOM.
