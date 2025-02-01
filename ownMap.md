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
