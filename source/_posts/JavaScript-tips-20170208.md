---
title: JavaScript Tips
date: 2017-02-08 08:31:54
tags: [JavaScript]
categories: [Web前端]
---

[Web technology for developers: JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript)

### JavaScript Basic
+ **data types**
  JavaScript provides seven different data types which are `undefined`, `null`, `boolean`, `string`, `symbol`, `number`, and `object`.
+  In JavaScript we end statements with semicolons.
+ `Variable` names can be made up of numbers, letters, and `$` or `_`, but may not contain spaces or start with a number.
+ In JavaScript all variables and function names are **case sensitive**. This means that capitalization matters.
+ The `remainder` operator `%` gives the remainder of the division of two numbers.
  The `remainder` operator is sometimes incorrectly referred to as the "modulus" operator. It is very similar to modulus, but does not work properly with negative numbers.
+ String values in JavaScript may be written with **single** or **double** quotes, so long as you start and end with the same type of quote.
  In JavaScript, you can `escape` a quote from considering it as an end of string quote by placing a `backslash` (`\`) in front of the quote.
+ You can find the length of a `String` value by writing `.length` after the string variable or string literal.
  `"Alan Peter".length; // 10`
+ **Bracket notation** is a way to get a character at a specific `index` within a string. exp:`firstName[0]`
+ In JavaScript, `String` values are **immutable**, which means that they cannot be altered once created.
  ```JavaScript
  var myStr = "Bob";
  myStr[0] = "J";
  ```
  will **not** change the value of myStr.
+ With JavaScript `array` variables, we can store several pieces of data in one place.
  `var sandwich = ["peanut butter", 12, "bread"];`
+ An easy way to append data to the end of an array is via the `push()` function.
  `.push()` takes one or more `parameters` and "pushes" them onto the end of the array.
+ Another way to change the data in an array is with the `.pop()` function.

  `.pop()` is used to "pop" a value off of the end of an array. We can store this "popped off" value by assigning it to a variable.
+ The `shift()` method removes the first element from an array and returns that element. This method changes the length of the array.
+ The `unshift()` method adds one or more elements to the beginning of an array and returns the new length of the array.
+ *Functions*
```JavaScript
function functionName() {
  console.log("Hello World");
}

function testFun(param1, param2) {
  console.log(param1, param2);
}
```
+ In JavaScript, `scope` refers to the visibility of variables. Variables which are defined outside of a function block have `Global` scope. This means, they can be seen everywhere in your JavaScript code.
  Variables which are used without the `var` keyword are automatically created in the `global` scope. This can create unintended consequences elsewhere in your code or when running a function again. You should **always** declare your variables with `var`.
+ It is possible to have both `local` and `global` variables with the same name. When you do this, the `local` variable takes precedence over the `global` variable.
  ```JavaScript
  var someVar = "Hat";
  function myFun() {
    var someVar = "Head";
    return someVar;
  }
  ```
+ **if**
  ```JavaScript
  if (condition is true) {
  statement is executed
  }
  ```
+ **Comparison** equality operator (`==`)
  In order for JavaScript to compare two different `data types` (for example, `numbers` and `strings`), it must convert one type to another. Once it does, however, it can compare terms as follows:
  ```JavaScript
     1   ==  1    // true
     1   ==  2    // false
     1   == '1'   // true
    "3"  ==  3    // true
  ```
+ **Strict equality**
  Strict equality (`===`) is the counterpart to the equality operator (`==`). Unlike the equality operator, strict equality tests both the `data type` and value of the compared elements.
    ```JavaScript
    3 === 3   // true
    3 === '3' // false
    ```
+ **inequality operator**
  Like the equality operator, the inequality operator will convert data types of values while comparing.
  ```JavaScript
  1 != 2      // true
  1 != "1"    // false
  1 != '1'    // false
  1 != true   // false
  0 != false  // false
  ```
+ **Strict Inequality Operator**
  The strict inequality operator (`!==`) is the opposite of the strict equality operator. It means "Strictly Not Equal" and returns `false` where strict equality would return `true` and *vice versa*. Strict inequality will not convert data types.
  ```JavaScript
  3 !== 3   // false
  3 !== '3' // true
  4 !== 3   // true
  ```
+ **Greater Than Or Equal To Operator**
  Like the equality operator, `greater than or equal to` operator will convert data types while comparing.
  ```JavaScript
  6  >=  6  // true
  7  >= '3' // true
  2  >=  3  // false
  '7' >=  9  // false
  ```

   Like the equality operator, `less than` operator converts data types while comparing.
   Like the equality operator, `less than or equal to` converts data types.
+ `if ...else if ... else...`
+ **Switch Statements**
  Statements are executed from the first matched case value until a `break` is encountered. `case` values are tested with **strict equality** (`===`). The `break` tells JavaScript to stop executing statements. If the `break` is omitted, the next statement will be executed.
  ```JavaScript
  switch (num) {
    case value1:
      statement1;
      break;
    case value2:
      statement2;
      break;
  ...
    case valueN:
      statementN;
      break;
    default:
      defaultStatement;
  }
  ```
+ **JavaScript Object**
  Objects are similar to `arrays`, except that instead of using indexes to access and modify their data, you access the data in objects through what are called `properties`. example:
  ```JavaScript
  var cat = {
    "name": "Whiskers",
    "legs": 4,
    "tails": 1,
    "enemies": ["Water", "Dogs"]
  };
  ```
+ **Accessing Objects Properties with the Dot Operator**
  There are two ways to access the properties of an object: the dot operator (`.`) and bracket notation (`[]`), similar to an array.

  The dot operator is what you use when you know the name of the property you're trying to access ahead of time.
  ```JavaScript
  var myObj = {
    prop1: "val1",
    prop2: "val2"
  };
  var prop1val = myObj.prop1; // val1
  var prop2val = myObj.prop2; // val2
```
+ **Accessing Objects Properties with Bracket Notation**
  If the property of the object you are trying to access has a **space** in it, you will need to use bracket notation.
  ```JavaScript
    var myObj = {
      "Space Name": "Kirk",
      "More Space": "Spock",
      "name": "tru"
    };
    myObj["Space Name"]; // Kirk
    myObj['More Space']; // Spock
  ```
  + **Delete Properties from a JavaScript Object**
    `delete myObj["Space Name"]` or `delete myObj.name`
  + Sometimes it is useful to check if the property of a given object exists or not. We can use the `.hasOwnProperty(propname)` method of objects to determine if that object has the given property name. `.hasOwnProperty()` returns `true` or `false` if the property is found or not.
  ```JavaScript
  var myObj = {
    top: "hat",
    bottom: "pants"
  };
  myObj.hasOwnProperty("top");    // true
  myObj.hasOwnProperty("middle"); // false
```
+ **For**
  `for ([initialization]; [condition]; [final-expression])`
+ JavaScript has a `Math.random()` function that generates a random decimal number between `0` (inclusive) and not quite up to `1` (exclusive).
+ The `Math.floor()` function returns the largest integer less than or equal to a given number.
+ Generate a random number that falls within a range of two specific numbers.
  `Math.floor(Math.random() * (max - min + 1)) + min`
+ **Regular expressions**
  `/` is the start of the regular expression.
  `/` is the end of the regular expression.
  `g` means `global`, which causes the pattern to return all matches in the string, not just the first one.
  `i` means that we want to ignore the case (uppercase or lowercase) when searching for the pattern.
  `var expressionToGetSoftware = /software/gi;`
  ```JavaScript
  var testString = "How many non-space characters are there in this sentence?";
  var expression = /\S/g;
  var nonSpaceCount = testString.match(expression).length;
  ```

***

### Object Oriented and Functional Programming

+ We are also able to create objects using `constructor` functions. 构造器
  A `constructor` function is given a capitalized name to make it clear that it is a `constructor`.
  ```JavaScript
  var Car = function() {
    this.wheels = 4;
    this.engines = 1;
    this.seats = 5;
  };
  ```
  In a `constructor` the `this` variable refers to the new object being created by the constructor.
  Inside of the `constructor` we are giving the new object it creates a property called `wheels` with a value of `4`.
+ To use a `constructor` function we call it with the `new` keyword in front  of it like:
  `var myCar = new Car();`
  `myCar` is now an instance of the `Car` constructor that looks like the object it described:
  ```
  {
    wheels: 4,
    engines: 1,
    seats: 5
  }
  ```
+ **Make Unique Objects by Passing Parameters to our Constructor**
   We can add `parameters` to our `constructor`.
   ```JavaScript
   var Car = function(wheels, seats, engines) {
     this.wheels = wheels;
     this.seats = seats;
     this.engines = engines;
   };
   ```
   Now, we can pass `arguments` like this: `var myCar = new Car(6, 3, 1);`
+ **Make Object Properties Private**
  Objects have their own attributes, called `properties`, and their own functions, called `methods`.

  Previously, we used the `this` keyword to reference `public properties` of the current object.
  Now, to create `private properties`, we create the variable inside the constructor using the `var` keyword, instead of creating it as a property of `this`.

  ```JavaScript
  var Car = function() {
    // this is a private variable
    var speed = 10;

    // these are public methods
    this.accelerate = function(change) {
      speed += change;
    };

    this.decelerate = function() {
      speed -= 5;
    };

    this.getSpeed = function() {
      return speed;
    };
  };
  ```
+ **Iterate over Arrays with map**
  The `map` method is a convenient way to iterate through arrays.
  ```JavaScript
  var oldArray = [1, 2, 3];
  var timesFour = oldArray.map(function(val){
    return val * 4;
  });
  console.log(timesFour); // returns [4, 8, 12]
  console.log(oldArray);  // returns [1, 2, 3]
  ```
  The `map` method will iterate through every element of the array, creating a new array with values that have been modified by the callback function, and return it. Note that it does not modify the original array.
+ **Condense arrays with reduce**
  The array method `reduce` is used to iterate through an array and condense it into one value.

  To use `reduce` you pass in a callback whose arguments are an accumulator (in this case, `previousVal`) and the current value (`currentVal`).

  The accumulator is like a total that `reduce` keeps track of after each operation. The current value is just the next element in the array you're iterating through.

  `reduce` has an optional second argument which can be used to set the initial value of the accumulator. If no initial value is specified it will be the first array element and `currentVal` will start with the second array element.
  ```JavaScript
  var singleVal = array.reduce(function(previousVal, currentVal) {
    return previousVal - currentVal;
  }, 0);
  ```
+ **Filter Arrays with filter**
  The `filter` method is used to iterate through an array and filter out elements where a given condition is **not** true.

  `filter` is passed a callback function which takes the current value as an argument.

  Any array element for which the callback returns true will be kept and elements that return false will be filtered out.

  ```JavaScript
  var oldArray = [1,2,3,4,5,6,7,8,9,10];

  var newArray = oldArray.filter(function(val){
    return val<6;
  });  //result: [1,2,3,4,5]
  ```
+ **Sort Arrays with sort**
  You can use the method `sort` to easily sort the values in an array alphabetically or numerically.

  Unlike the previous array methods we have been looking at, `sort` actually alters the array **in place**. However, it also returns this sorted array.

  `sort` can be passed a compare function as a callback. The compare function should return a negative number if `a` should be before `b`, a positive number if `a` should be after `b`, or `0` if they are equal.

  If no compare (callback) function is passed in, it will convert the values to strings and sort alphabetically.
  ```JavaScript
  var array = [1, 12, 21, 2];
  array.sort(function(a, b) {
    return a - b;
  });
  ```
+ **Reverse Arrays with reverse**
  You can use the `reverse` method to reverse the elements of an array.

  `reverse` is another array method that alters the array **in place**, but it also returns the reversed array.
  ```JavaScript
  var myArray = [1, 2, 3];
  myArray.reverse();//returns [3, 2, 1]
  ```
+ **Concatenate Arrays with concat**
  `concat` can be used to merge the contents of two arrays into one.

  `concat` takes an array as an argument and returns a new array with the elements of this array concatenated onto the end.

  `newArray = oldArray.concat(otherArray);`
+ **Split Strings with split**
  You can use the `split` method to split a string into an array.

  `split` uses the argument you pass in as a delimiter to determine which points the string should be split at.
  `var array = string.split('s');`
+ **Join Strings with join**
  We can use the `join` method to join each element of an array into a string separated by whatever delimiter you provide as an argument.
