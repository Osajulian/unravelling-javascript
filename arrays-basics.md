## Arrays

Arrays in JavaSCript are actually a special type of object. They work very much like regular objects (numerical properties can naturally be accessed only using `[]` syntax) but they have one magic property called '`length`'. This is always one more than the highest index in the array.

One way of creating arrays is as follows:

```javascript
let a = new Array();
a[0] = 'dog';
a[1] = 'cat';
a[2] = 'hen';
a.length; //3
```

Am more convenient notation is to use the array literal:

```javascript
let a = ['dog', 'cat', 'hen'];
a.length; // 3
```

> Note that array.length is not necessarily the number of items in the array. Consider the following:

```javascript
let a = ['dog', 'cat', 'hen'];
a[100] = 'fox';
a.length; // 101
```
> Remember - the length of the array is one more than the highest index.

If you query a non-existent array index, you'll get a value of `undefined` in return:

```javascript
typeof a[90]; // undefined
```

If you take thea above `[]` and `length` into account, you can iterate over an array using the following `for loop`:

```javascript
for (let i = 0; i < a.length; i++) {
    // Do something with a[i]
}
```

> ES 2015 introduced the more concise `for...of` loop for iterable objects such as arrays:

```javascript
for(const currentValue of a) {
    // Do something with currentValue
}
```

You could also iterate over an array using a `for...in` loop, however this does not iterate over the array elements, but the **array indices**. Furthermore, if someone added new properties to `Array.prototype`, they would also be iterated over by such a loop. **Therefore this loop type is not recommended for arrays**.

Another way of iterating over an array that was added with ECMAScript 5 is `forEach()`:

```javascript
['dog', 'cat', 'hen'].forEach(function(currentValue, index, array) {
    //Do something with currentValue at array[index]
});
```

if you want to append an item to an array, simpley do it like this:

`a.push(item);`

Arrays come with a number of methods - some examples:

         