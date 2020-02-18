---
title: JavaScript Basic Algorithm sample
date: 2017-02-08 20:46:56
tags: [JavaScript]
categories: [Web前端]
---

some excises on freeCodeCamp

### Reverse a String
```JavaScript
function reverseString(str) {
  tempStr = str.split("");
  tempStr.reverse();
  str = tempStr.join("");
  return str;
}

reverseString("hello");//return: olleh
```

### Factorialize a Number
Return the factorial of the provided integer.
```JavaScript
function factorialize(num) {
  var total=1;
  for (var i=1;i<=num;i++){
    total = i*total;
  }
  return total;
}

factorialize(5);
```

### Check for Palindromes
A `palindrome` is a word or sentence that's spelled the same way both forward and backward, ignoring punctuation, case, and spacing.

Return true if the given string is a palindrome. Otherwise, return false.

You'll need to remove all non-alphanumeric characters (punctuation, spaces and symbols) and turn everything lower case in order to check for palindromes.
```JavaScript
function palindrome(str) {
  regexp = /[^a-zA-Z0-9]/g;
  newStrArr = str.replace(regexp, "").toLowerCase().split("");
  newStrArr_r = newStrArr.reverse();
  for(var i =0;i<newStrArr.length;i++){
    if(newStrArr[i] != newStrArr_r[newStrArr.length-1-i]){
      return false;
    }
  }
  return true;
}


palindrome("0_0 (: /-\ :) 0-0") //should return true.
palindrome("five|\_/|four") //should return false.
```

### Find the Longest Word in a String
Return the length of the longest word in the provided sentence.
Your response should be a number.
```JavaScript
function findLongestWord(str) {
  wordList = str.split(" ");
  var longest=0;
  for (var i=0;i<wordList.length;i++){
    if(wordList[i].length > longest){
      longest = wordList[i].length;
    }
  }
  return longest;
}

findLongestWord("The quick brown fox jumped over the lazy dog");
```

### Title Case a Sentence
Return the provided string with the first letter of each word capitalized. Make sure the rest of the word is in lower case.
```JavaScript
function titleCase(str) {
  wordList = str.split(" ");
  var resultArr = new Array();
  for (var i=0;i <wordList.length; i++){
    resultArr.push(wordList[i][0].toUpperCase() + wordList[i].slice(1).toLowerCase());
  }
  return resultArr.join(" ");
}

titleCase("I'm a little tea pot"); //I'm A Little Tea Pot
```
**String.prototype.slice()**
The `slice()` method extracts a section of a string and returns a new string.
`str.slice(beginIndex[, endIndex])`

**Array.prototype.slice()**
The `slice()` method returns a shallow copy of a portion of an array into a new array object selected from begin to end (end not included). The original array will not be modified.
```JavaScript
var a = ['zero', 'one', 'two', 'three'];
var sliced = a.slice(1, 3);

console.log(a);      // ['zero', 'one', 'two', 'three']
console.log(sliced); // ['one', 'two']
```

### Return Largest Numbers in Arrays
Return an array consisting of the largest number from each provided sub-array. For simplicity, the provided array will contain exactly 4 sub-arrays.
```JavaScript
function largestOfFour(arr) {
  // You can do this!
  var resultArr = new Array();
  for(var i=0;i<arr.length;i++){
    var largest = arr[i][0];
    for(var j=1;j<arr[i].length;j++){
      if(arr[i][j]>largest){
        largest = arr[i][j];
      }
    }
    resultArr.push(largest);
  }
  return resultArr;
}

largestOfFour([[4, 5, 1, 3], [13, 27, 18, 26], [32, 35, 37, 39], [1000, 1001, 857, 1]]);
```

### Confirm the Ending
solution 1:
```JavaScript

function confirmEnding(str, target) {
  // "Never give up and good luck will find you."
  // -- Falcor
  if (str.endsWith(target)){
    return true;
  }else{
    return false;
  }
}

confirmEnding("Bastian", "n");
```

solution 2:
```JavaScript
function confirmEnding(str, target) {
  // "Never give up and good luck will find you."
  // -- Falcor
  subStr = str.substring(str.length-target.length);
  if (subStr==target){
    return true;
  }else{
    return false;
  }
}

confirmEnding("Bastian", "n");
```
solution 3:
```JavaScript
function confirmEnding(str, target) {
  // "Never give up and good luck will find you."
  // -- Falcor
  subStr = str.substr(-target.length);
  if (subStr==target){
    return true;
  }else{
    return false;
  }
}

confirmEnding("Bastian", "n");
```
**Tips**
**String.prototype.substr()**
The `substr()` method returns the characters in a string beginning at the specified location through the specified number of characters.
`str.substr(start [, length])`
**Parameters**
*start*
Location at which to begin extracting characters. If a negative number is given, it is treated as strLength + start where strLength is the length of the string (for example, if start is -3 it is treated as strLength - 3.)
*length*
Optional. The number of characters to extract.
```JavaScript
var str = 'abcdefghij';

console.log('(1, 2): '   + str.substr(1, 2));   // '(1, 2): bc'
console.log('(-3, 2): '  + str.substr(-3, 2));  // '(-3, 2): hi'
console.log('(-3): '     + str.substr(-3));     // '(-3): hij'
console.log('(1): '      + str.substr(1));      // '(1): bcdefghij'
console.log('(-20, 2): ' + str.substr(-20, 2)); // '(-20, 2): ab'
console.log('(20, 2): '  + str.substr(20, 2));  // '(20, 2): '
```

**String.prototype.substring()**
The `substring()` method returns a subset of a string between one index and another, or through the end of the string.
`str.substring(indexStart[, indexEnd])`
**Parameters**
*indexStart*
An integer between 0 and the length of the string, specifying the offset into the string of the first character to include in the returned substring.
*indexEnd*
Optional. An integer between 0 and the length of the string, which specifies the offset into the string of the first character not to include in the returned substring.
```JavaScript
var anyString = 'Mozilla';

// Displays 'Moz'
console.log(anyString.substring(0, 3));
console.log(anyString.substring(3, 0));

// Displays 'lla'
console.log(anyString.substring(4, 7));
console.log(anyString.substring(4));
console.log(anyString.substring(7, 4));

// Displays 'Mozill'
console.log(anyString.substring(0, 6));

// Displays 'Mozilla'
console.log(anyString.substring(0, 7));
console.log(anyString.substring(0, 10));
```

### Repeat a string repeat a string
Repeat a given string (first argument) `num` times (second argument). Return an empty string if `num` is not a positive number.
```JavaScript
function repeatStringNumTimes(str, num) {
  // repeat after me
  var newStr;
  if (num<0){
    newStr = "";
    return newStr;
  }else{
    newStr = str.repeat(num);
  }
  return newStr;
}

repeatStringNumTimes("abc", 3);
```
**String.prototype.repeat()**
The `repeat()` method constructs and returns a new string which contains the specified number of copies of the string on which it was called, concatenated together.
```JavaScript
/**
 * str: String
 * count: Number
 */

let resultString = str.repeat(count);
```
example:
```JavaScript
'abc'.repeat(-1);   // RangeError
'abc'.repeat(0);    // ''
'abc'.repeat(1);    // 'abc'
'abc'.repeat(2);    // 'abcabc'
'abc'.repeat(3.5);  // 'abcabcabc' (count will be converted to integer)
'abc'.repeat(1/0);  // RangeError
```

### Truncate a string
Truncate a string (first argument) if it is longer than the given maximum string length (second argument). Return the truncated string with a `...` ending.

Note that inserting the three dots to the end will add to the string length.

However, if the given maximum string length `num` is less than or equal to 3, then the addition of the three dots does not add to the string length in determining the truncated string.

```JavaScript
function truncateString(str, num) {
  // Clear out that junk in your trunk
  var newStr;
  if(str.length>num){
    if(num>3){
      newStr=str.slice(0,num-3) + "...";
    }else{
      newStr=str.slice(0,num) + "...";
    }
    return newStr;
  }else{
    return str;
  }
}

truncateString("A-tisket a-tasket A green and yellow basket", 11);
```

### Chunky Monkey
Write a function that splits an array (first argument) into groups the length of `size` (second argument) and returns them as a two-dimensional array.
```JavaScript
function chunkArrayInGroups(arr, size) {
  // Break it up.
  var tempArr = new Array();
  var resultArr = new Array();
  arrLength = Math.ceil(arr.length/size);
  for (var i =0;i<arr.length/size;i++){
    tempArr=arr.slice(size*i,size*i+size);
    resultArr.push(tempArr);
  }
  return resultArr;
}

chunkArrayInGroups([0, 1, 2, 3, 4, 5], 4);
```

### Slasher Flick
Return the remaining elements of an array after chopping off `n` elements from the head.
The head means the beginning of the array, or the zeroth index.

```JavaScript

function slasher(arr, howMany) {
  // it doesn't always pay to be first
  arr.splice(0,howMany);
  return arr;
}

slasher([1, 2, 3], 2);
```

**Array.prototype.splice()**
The `splice()` method changes the contents of an array by removing existing elements and/or adding new elements.

```JavaScript
array.splice(start)
array.splice(start, deleteCount)
array.splice(start, deleteCount, item1, item2, ...)
```
`splice() `方法向/从数组中添加/删除项目，然后返回被删除的项目。
注释：该方法会改变原始数组。
`splice()` 方法可删除从 `index` 处开始的零个或多个元素，并且用参数列表中声明的一个或多个值来替换那些被删除的元素。
如果从 `arrayObject` 中删除了元素，则返回的是含有被删除的元素的数组。

`arrayObject.splice(index,howmany,item1,.....,itemX)`

|参数|描述|
|---|---|
|index|	必需。整数，规定添加/删除项目的位置，使用负数可从数组结尾处规定位置。|
|howmany|	必需。要删除的项目数量。如果设置为 0，则不会删除项目。|
|item1, ..., itemX|	可选。向数组添加的新项目。|


### Mutations
Return true if the string in the first element of the array contains all of the letters of the string in the second element of the array.
For example, `["hello", "Hello"]`, should return true because all of the letters in the second string are present in the first, ignoring case.

```JavaScript
function mutation(arr) {
  for(var i=0;i<arr[1].length;i++){
    if (arr[0].toLowerCase().indexOf(arr[1][i].toLowerCase()) == -1){
      return false;
    }
  }
  return true;
}

mutation(["hello", "hey"]);
```

**String.prototype.indexOf()** （有些难度）
The `indexOf()` method returns the index within the calling String object of the first occurrence of the specified value, starting the search at fromIndex. Returns `-1` if the value is not found.
`str.indexOf(searchValue[, fromIndex])`

**Parameters**
*searchValue*
A string representing the value to search for.
*fromIndex*  Optional
The index at which to start the searching forwards in the string. It can be any integer. The default value is 0. If `fromIndex <= 0` the entire string is searched. If `fromIndex >= str.length`, the string is not searched and -1 is returned. Unless searchValue is an empty string, then str.length is returned.

The `indexOf()` method is case sensitive.

### Falsy Bouncer
Remove all falsy values from an array.
Falsy values in JavaScript are `false`, `null`, `0`, `""`, `undefined`, and `NaN`.

这个例子比较好，特别是null的判断上，花了很长时间
```JavaScript
function bouncer(arr) {
  // Don't show a false ID to this bouncer.
  var falsyValue=[false, null, 0, "", undefined, NaN];
  var newArr = new Array();
  newArr = arr.filter(function(val){
    if (falsyValue.indexOf(val) == -1 ){
      return true;
    }
  });
  for (var i=0;i<newArr.length;i++){
    if(!newArr[i]||newArr[i]==''||newArr[i] === undefined){//难点
      newArr.splice(i,1);
    }
  }
  return newArr;
}

bouncer([false, null, 0, NaN, undefined, ""]);
```


### Seek and Destroy
You will be provided with an initial array (the first argument in the destroyer function), followed by one or more arguments. Remove all elements from the initial array that are of the same value as these arguments.
```JavaScript
function destroyer(arr) {
  // Remove all the values
  var args = [].slice.call(arguments);
  var otherArr = args.slice(1);
  var newArr = new Array();
  var tempArr = args[0];
  newArr = tempArr.filter(function (val){
    for (var i=0;i<otherArr.length;i++){
      if(val===otherArr[i]){
        return false;
      }
    }
    return true;  //important
  });
  return newArr;
}

destroyer([1, 2, 3, 1, 2, 3], 2, 3);
```

**Arguments object** (有些难度)
The `arguments` object is an Array-like object corresponding to the arguments passed to a function.
The arguments object is a local variable available within all functions. You can refer to a function's arguments within the function by using the arguments object. This object contains an entry for each argument passed to the function, the first entry's index starting at 0. For example, if a function is passed three arguments, you can refer to them as follows:
```JavaScript
arguments[0]
arguments[1]
arguments[2]
```
The arguments object is not an Array. It is similar to an Array, but does not have any Array properties except length. For example, it does not have the pop method. However it can be converted to a real Array:
```JavaScript
var args = Array.prototype.slice.call(arguments);
var args = [].slice.call(arguments);
```

JavaScript允许一个函数传递个数可变的参数，因为有`arguments`这个内置对象，它一个函数传递的所有参数的数组。


**Array.prototype.filter()**
The `filter()` method creates a new array with all elements that pass the test implemented by the provided function.
`var new_array = arr.filter(callback[, thisArg])`

```JavaScript
function isBigEnough(value) {
  return value >= 10;
}

var filtered = [12, 5, 8, 130, 44].filter(isBigEnough);
// filtered is [12, 130, 44]
```
**Parameters**
*callback*
Function is a predicate, to test each element of the array. Return true to keep the element, false otherwise, taking three arguments:
*element*
The current element being processed in the array.
*index*
The index of the current element being processed in the array.
*array*
The array filter was called upon.

**Return value**
A new array with the elements that pass the test.


### Where do I belong 注意
Return the lowest index at which a value (second argument) should be inserted into an array (first argument) once it has been sorted. The returned value should be a number.

For example, `getIndexToIns([1,2,3,4], 1.5)` should return 1 because it is greater than `1` (index 0), but less than `2` (index 1).

```JavaScript
function getIndexToIns(arr, num) {
  // Find my place in this sorted array.
  var arrSorted = arr.sort(function(a,b){
    return a-b;
  });
  if (arrSorted[0]>=num){
    return 0;
  }
  for (var i=0;i<arr.length-1;i++){
    if(arrSorted[i]<num && arrSorted[i+1]>=num){
      return i+1;
    }
  }
  if (arrSorted[arr.length-1]<num){
    return arr.length;
  }
}

getIndexToIns([5, 3, 20, 3], 5);
```
注意sort的用法，
> 如果调用该方法时没有使用参数，将按字母顺序对数组中的元素进行排序，说得更精确点，是按照字符编码的顺序进行排序。要实现这一点，首先应把数组的元素都转换成字符串（如有必要），以便进行比较。
> 如果想按照其他标准进行排序，就需要提供比较函数，该函数要比较两个值，然后返回一个用于说明这两个值的相对顺序的数字。比较函数应该具有两个参数 a 和 b，其返回值如下：
  + 若 a 小于 b，在排序后的数组中 a 应该出现在 b 之前，则返回一个小于 0 的值。
  + 若 a 等于 b，则返回 0。
  + 若 a 大于 b，则返回一个大于 0 的值。


### Caesars Cipher
One of the simplest and most widely known ciphers is a `Caesar cipher`, also known as a `shift cipher`. In a `shift cipher` the meanings of the letters are shifted by some set amount.

A common modern use is the `ROT13` cipher, where the values of the letters are shifted by 13 places. Thus 'A' ↔ 'N', 'B' ↔ 'O' and so on.

```JavaScript
function rot13(str) { // LBH QVQ VG!
  var result="";
  for(var i=0;i<str.length;i++){
    if(str[i]>="A" && str[i]<="Z"){
      result += String.fromCharCode((str.charCodeAt(i) -65+13)%26+65);
    }else{
      result += str[i];
    }
  }
  return result;
}

// Change the inputs below to test
rot13("SERR PBQR PNZC");
```

**String.prototype.charCodeAt()**
The `charCodeAt()` method returns an integer between 0 and 65535 representing the UTF-16 code unit at the given index .
`str.charCodeAt(index)`

**Parameters**
*index*
An integer greater than or equal to 0 and less than the length of the string; if it is not a number, it defaults to 0.
**Return value**
A number representing the UTF-16 code unit value of the character at the given index; NaN if index is out of range.
```JavaScript
'ABC'.charCodeAt(0); // returns 65
```

**String.fromCharCode()**
The static String.fromCharCode() method returns a string created by using the specified sequence of Unicode values.
`String.fromCharCode(num1[, ...[, numN]])`

**Parameters**
*num1, ..., numN*
A sequence of numbers that are Unicode values.
**Return value**
A string containing the characters corresponding to the sequence of Unicode values.

```JavaScript
String.fromCharCode(65, 66, 67);  // "ABC"
```

### Can I pass an array into fromCharCode?
You could use the function's `apply()` method
```JavaScript
document.write(String.fromCharCode.apply(null, array));
```
