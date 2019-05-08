Along with objects, functions are the core components in understanding JavaScript. The most basic functions couldn't be much simpler
:

```javascript
function add(x, y) {
    let total = x + y;
    return total;
}
```

This demonstrates a basic function. A JavaScript function can take 0 or more named parameters. The function body can contain as many statements as you like and can declare its own variables which are local to that function. The ```return ``` statement can be used to return a value at any time, terminating the function. If no return statement is used (or an empty return with no value), JavaScript returns ```undefined```.

The named parameters turn out to be more like guidelines than anything else. You can call a function without passing the parameters it expects, in which case they will be set to ```undefined```

```javascript
add(); // NaN. 
//You can't perform addition on undefined
```
You can also pass in more arguments than the function is expecting:

```javascript
add(2, 3, 4) // 5
// added the first two; 4 was ignored
```

This may seem a little silly, but functions have access to an additional variable inside their body called 'arguments', which is an array-like object holding all of the values passed to the function.

Let's re-write the add function to take as many values as we want:

```javascript
function add() {
    let sum = 0;
    for(let i = 0, j = arguments.length; i < j; i++) {
        sum+= arguments[i]
    }
    return sum;
}

add(2, 3, 4, 5); // 14
```

That's really not any more useful than writing 2 + 3 + 4 + 5 though. Let's create an averaging function:

```javascript
function avg() {
    let sum = 0;
    for(let i = 0, j = arguments.length; i < j; i++) {
        sum+= arguments[i]
    }
    return sum / arguments.length; 
}

add(2, 3, 4, 5); // 3.5
```

This is pretty useful, but it does seem a little verbose. To reduce this code a bit more, we can look at substituting the use of the arguments array through **Rest parameter syntax**. In this way, we can pass in any number of arguments into the function while keeping our code minimal. The **rest parameter operator** is used in function parameter lists with the format **...variable** and it will include within that variable the entire list of uncaptured arguments that the function was called with. We will also replace the **for** loop with a **for...of** loop to return the values within our variable.

```javascript
function avg(...args) {
    let sum = 0;
    for (let value of args) {
        sum += value;
    }
    return sum / args.length;
}

avg(2, 3, 4, 5); // 3.5
```
In the above code, the variable **args** holds all the value passed into the function.

It is important to note that wherever the rest parameter operator is placed in a function declaration, it will store all arguments *after* its declaration, but not *before*, i.e.

 ```javascript
function avg(firstValue, ...args)
```
will store the first value passed into the function in the **firstValue** variable and the remaining arguments in **args**. That's another useful language feature but it does lead us to a new problem. The ```avg()``` function takes a comma-separated list of arguments - but what if you want to find the average of an array? You could just re-write the function as follows:

```javascript 
function avgArray(arr) {
    let sum = 0;
    for (let i =0; j = arr.length; i < j; i++) {
        sum += arr[i];

    }
    return sum / arr.length;
}

avgArray([2, 3, 4, 5]) // 3.5
```

But it would be nice to be able to reuse the function that we've already created. Luckily, JavaScript lets you call a function with an arbitrary array of arguments, using ```apply()``` method of any function.

```javascript
avg.apply(null, [2, 3, 4, 5]); // 3.5
```

The second argument to ```apply()``` is the array to use as arguments; the first will be discussed later on. This emphasizes the fact that functions are objects too.

You can achieve the same result using the **spread operator** in the function call.
For instance: ```avg(...numbers)```

## Anonymous functions

JavaScript lets you create anonymous functions.

```javascript 
let avg = function() {
    let sum = 0;
    for(i =0, j < arguments.length; i < j; i++) {
        sum += arguments[i];

    }
    return sum / arguments.length
};
```
This is semantically equivalent to the ```function avg()``` form. It's extremely powerful, as it lets you put a full function definition anywhere that you would normally put an expression. This enables all sorts of clever tricks. Here's a way of "hiding" some local variables - like block scope in C:

```javascript
var a = 1;
var b = 2;

(function() {
    var b = 3;
    a += b;
})();
a; // 4
b; // 2
```
## Recursive functions
JavaScript allows you to call functions recursively. This is particularly useful for dealing with tree structures, such as those found in the browser DOM.

```javascript
function countChars(elm) {
    if(elm.nodeType == 3) {// TEXT_NODE
    return elm.nodeValue.length;
    }

    let count = 0;
    for(let i =0, child; child = elm.childNodes[i]; i++) {
        count += countChars(child);
    }

    return count;
}
```
This highlights a potential problem with anonymous functions; how do we call them recursively if they don't have a name? JavaScript lets you name function expressions for this. You can use named **IIFES (Immediately Invoked Function Expressions)** as shown below:

```javascript
let charsInBody = (function counter(elm) {
    if(elm.nodeType == 3) {//TEXT_NODE 
    return elm.nodeValue.length;
    }
    let count = 0;
    for(let i = 0, child; child = elm.childNodes[i]; i++) {
        count += counter(child);
    }
    return count;
})(document.body)
```
The name provided to a function expression as above is only available to the function's own scope. This allows more optimizations to be done by the engine and results in more readable code. The name also shows up in the debugger and some stack traces, which can save you time when debugging.

Note that JavaScript functions are themselves objects - like everything else in JavaScript - and you can add or change properties on them just like we've seen earlier in the objects section.

## Inner functions
JavaScript function declarations are allowed inside other functions. We've seen this once before, with an earlier ```makePerson()``` function. An important detail of nested functions in JavaScript is that they can access variables in their parent function's scope:

```javascript
function parentFunc() {
    let a = 1;

    function nestedFunc() {
        let b = 4; //parentFunc can't use this
        return a + B;
    }
    return nestedFunc(); // 5
}
```

This provides a great deal of utility in writing more maintainable code. If a called function relies on one or two other functions that are not useful to any part of your code, you can nest those utility functions inside it. This keeps the number of functions that re in the global scope down, which is always a good thing.

This is also a great counter to the lure og global variables. When writing complex code, it is often tempting to use global variables to share values between multiple functions - which leads to code that is hard to maintain. Nested functions can share variables in their parent, so you can use that mechanism to couple functions together when it makes sense without polluting your global namespace - "local globals" if you like. This technique should be used with caution, but it's a useful ability to have.

## Closures

Closures lead us to one of the most powerful abstractions that JavaScript has to offer - but also the most potentially confusing. What does this do?

```javascript
function makeAdder(a) {
    return function(b){
        return a+ b;
    };

}
let add5 = makeAdder(5);
let add20 = makeAdder(20);

add5(6); // ?
add20(7); // ?
```

The name of the `makeAdder()` function should give it away; it creates new 'adder' functions, each of which, when called with one argument, adds it to the argument that it was created with.

What's happening here is pretty much the same as was happening with the inner functions earlier on: a function defined inside another function has access to the outer function's variables. The only difference here is that the outer function has returned, and hence common sense would seem to dictate that its local variables no longer exist. What's more, there are two different "copies" of `makeAdder()`'s local variables - one in which a is 5 and the other one where a is 20. So the result of the function calls is as follows:

```javascript
add5(6); // returns 11
add20(7); // returns 27
```

Here's what's actually happening. Whenever JavaScript executes a function, a 'scope' object is created to hold the local variables created within that function. It is initialized with any variables passed in as function parameters. This is similar to the global object that ll global variables and functions live in, but with a couple of important differences: firstly, a brand new scope object is created every time a function starts executing, and secondly, unlike the global object (which is accessible as `this` and in browsers as `window`) these scope objects cannot be directly accessed from your JavaScript code. There is no mechanism for iterating over the properties of the current scope object, for example.

So when `makeAdder()` is called, a scope object is created with one property `a`, which is the argument passed to the `makeAdder()` function. `makeAdder()` then returns a newly created function. Normally JavaScript's garbage collector would clean up the scope object created for `makeAdder()` at this point, but the returned function maintains a reference back to that scope object. As a result, the scope object will not be garbage-collected until there are no more references to that function object that `makeAdder()` returned.

Some objects form a chain called the scope chain, similar to the prototype chain used by JavaScript's object system.

> A **closure** is the combination of a function and the scope object in which it was created. Closures let you save state - as such, they can often be used in place of objects.