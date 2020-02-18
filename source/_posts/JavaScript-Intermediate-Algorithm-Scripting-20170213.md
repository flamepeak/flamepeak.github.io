---
title: JavaScript Intermediate Algorithm Scripting 20170213
date: 2017-02-13 15:38:30
tags: [JavaScript]
categories: [Web前端]
---



### Sum All Numbers in a Range
We'll pass you an array of two numbers. Return the sum of those two numbers and all numbers between them.
The lowest number will not always come first.

```JavaScript
function sumAll(arr) {
  max = Math.max.apply(null, arr);
  min = Math.min.apply(null, arr);
  var sum=0;
  for (var i=min;i<=max;i++){
    sum += i;
  }
  return sum;
}

sumAll([1, 4]);
```

The following function uses `Function.prototype.apply()` to find the maximum element in a numeric array.
```JavaScript
function getMaxOfArray(numArray) {
  return Math.max.apply(null, numArray);
}
```

### Diff Two Arrays
Compare two arrays and return a new array with any items only found in one of the two given arrays, but not both. In other words, return the symmetric difference of the two arrays.

```JavaScript
function diffArray(arr1, arr2) {
  var newArr = [];
  // Same, same; but different.
  newArr = arr1.filter(function (val){
    return arr2.indexOf(val) ==-1;
  });
  newArr= newArr.concat(arr2.filter(function(val){
    return arr1.indexOf(val)==-1;
  }));
  return newArr;
}

diffArray([1, 2, 3, 5], [1, 2, 3, 4, 5]);
```

### Roman Numeral Converter (Great)
Convert the given number into a roman numeral.All roman numerals answers should be provided in upper-case.

```JavaScript
function convertToRoman(x) {
  var n=['I', 'IV', 'V', 'IX', 'X', 'XL', 'L', 'XC', 'C', 'CD', 'D', 'CM', 'M'];
  var v=[1, 4, 5, 9, 10, 40, 50, 90, 100, 400, 500, 900, 1000];
  if( x<1 || x>4999 )
  {
      alert(x+' is not valid');
      return '';
  }
  y = '';
  for(i=12; i>=0; )
      if( v[i]<=x )
      {
          y += n[i];
          x -= v[i];
      }
      else
      {
          i--;
          k = 0;
      }

 return y;
}

convertToRoman(36);
```
