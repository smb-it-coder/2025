## Own map function

Array.prototype.myMap = function(callback){
  let len = this.length;
let newArry = [];

  for(let i = 0; i <= len; i++){
      newArry.push(callback(this(i)))
  }

rew newArry;
}

et arr = [1, 2, 3];
arr = arr.myMap(e => e * 2);
console.log(arr);

You're on the right track trying to create a custom `myMap` function on `Array.prototype`, but there are a few **bugs** and **syntax issues** in your code.

Let's break it down and **fix it properly**.

---

### ❌ Issues in your code:
1. `this(i)` → should be `this[i]` (arrays are accessed with `[]`, not function call syntax)
2. `for(let i = 0; i <= len; i++)` → should be `i < len` (or else you get `undefined` at the end)
3. `rew newArry;` → typo, should be `return newArry;`
4. `et arr = [1, 2, 3];` → typo, should be `let arr = [1, 2, 3];`

---

### ✅ Corrected Version:

```javascript
Array.prototype.myMap = function(callback) {
  let len = this.length;
  let newArry = [];

  for (let i = 0; i < len; i++) {
    newArry.push(callback(this[i], i, this));
  }

  return newArry;
};

let arr = [1, 2, 3];
arr = arr.myMap(e => e * 2);
console.log(arr); // [2, 4, 6]
```

---

### 🧠 Bonus Tip:
If you're mimicking the real `Array.prototype.map`, pass the second and third arguments (`index`, and the original array) to the callback, like:

```js
callback(currentValue, index, array)
```

Which is what I did above 👆.

Let me know if you want to try writing your own `filter`, `reduce`, or even `Promise.all` polyfill next!
