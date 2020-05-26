## 1. Create a function for strings validation as:

- **+** [{()}]
- **+** [{({}[])}]
- **-** [(])
- **-** [()

### Solution
We'll use **stack**. We'll put all brackets into stack and once we see closing bracket we remove appropriate opening bracket.
```
var brackets = [{
open: '(', 
close: ')'}]

var check = (string) => {
  var arr = [];
  for (var i = 0; i < string.length; i++) {
    var br = brackets.find(x => x.open == string[i] || x.close == string[i]);

    if (br && arr.length > 0 && arr[arr.length - 1] == br.open && arr[arr.length - 1] != string[i]) {
      arr.pop()
    } else if (br) {
      arr.push(string[i]);
    }
  }

  return arr.length == 0;
}
check('[<[[[[test>]')
```

## 2. Simplify the function.

function func(s, a, b) {
  var match_empty = /^$/;
  if (s.match(match_empty)) 
  {
    return -1;
  } 
  else 
  {
    var i = s.length - 1;
    var aindex = -1;
    var bindex = -1;
    while ((aindex == -1) && (bindex == -1) && (i >= 0)) 
    {
      if (s.substring(i, i + 1) == a)
        aindex = i;
      if (s.substring(i, i + 1) == b)
        bindex = i;

      i--;
    }
    if (aindex != -1) 
    {
      if (bindex == -1) 
      {
        return aindex;
      } 
      else
        	return Math.max(aindex, bindex);
    } 
    else 
    {
      if (bindex != -1) {
        return bindex;
      } 
      else 
      	return -1;
    }
  }
}

### Solution.
This function returns max value between last index of 'a' symbol and 'b' symbol.

```
function func(s, a, b) 
{
	if(!s)
  	return -1;
  
  return Math.max(a && a.length == 1 ? s.lastIndexOf(a) : -1, b && b.length == 1 ? s.lastIndexOf(b) : -1);
}
```

## 3. What will be on the screen. What's wrong with the function and how to fix it.

```
var o = {
    x: 10,
    foo: function() {
        for (var i = 0; i < 10; ++i){
            setTimeout(function() {
                console.log(this.x + i);
            }, 10);
        }
    }
```
### Result:
Result: 20 times 'undefined'

### What's wrong:
This is a task for closure understanding.
```
var o = {
    x: 10,
    foo: function() {
        for (var i = 0; i < 10; ++i){
            setTimeout(function() {
                console.log(this.x + i); //// 'this' is not defined int this scope. Also when this method is performed 'for' is already executed and i value is already 10;
            }, 10);
        }
    }
```

### Fix:    
```
var o = {
    x: 10,
    foo: function() {
        for (let i = 0; i < 10; ++i){ //// 'let' construction creates a variable for each step od 'for'
            setTimeout(() => console.log(this.x + i), 0); //// ()=> construction doesn't have it's own scope and uses the scope from the caller function, thus 'this' will be accessable here.
        }
    }
}
```

## 4. Implement simple Promise-like function on javascript.
```
function Defer(func) {
	this.mainFunc = func;
  this.callbacks = [];  
  this.statusObj = {
  	status: '',
	 	pauseDef: {}
  }
}

Defer.prototype.then = function(callback) {
	this.callbacks.push(callback);
  return this;
}

Defer.prototype.pause = function() {
  this.statusObj.status = 'paused';
  this.statusObj.pauseDef = new Defer();
}

Defer.prototype.play = function() {
  this.statusObj.status = '';
  if(this.statusObj.pauseDef)
  	this.statusObj.pauseDef.resolve();
}

Defer.prototype.cancel = function() {
  this.status = 'cancelled';
  this.statusObj.pauseDef = null;  
  this.callbacks = [];    
}

Defer.prototype.resolve = function() {
	let execFunction = (prev, now) => {
  	if(prev instanceof Defer){
    	prev.statusObj = this.statusObj
      
      switch(prev.statusObj.status)
      {
      	case 'paused': 
        	return prev.statusObj.pauseDef.then(() => prev.then(now));
        case 'cancelled':
        	return false;
        
        default: return prev.then(now); 
      }
    }    
    
    switch(this.statusObj.status)
      {
      	case 'paused': 
        	return this.statusObj.pauseDef ? this.statusObj.pauseDef.then(() => now(prev)) : false;
        case 'cancelled':
        	return false;
        
        default: return now(prev);
      }
  }
  
	this.callbacks.reduce((prev, now) => {    
  	return execFunction(prev, now);
  }, this.mainFunc ? this.mainFunc() : this.callbacks[0]);
}



let f = new Defer(() => {
  console.log('init', 100)
	return 100;
})

f.then(res => {
	res += 200;
  console.log('1', res)
  	return res;
})
.then(res => {
	let newF = new Defer(() => {
  	console.log('new 2', res + 100)
  	return res + 100;
  });
  
  setTimeout(() => { newF.resolve() }, 300)
  
  return newF;
})
.then(res => {
	res += 500
  console.log('3', res)
  return res;
})

f.resolve();
f.pause();
setTimeout(() => { f.play(); }, 1000)
f.cancel();


```

## 5. Implement array.find() like it never exists in JS. Do not use strings methods.
```
Array.prototype.testFind = function(f) {
	for(x of this) {
    if(f(x)){
    	return x;
    }
  }
  return null;
}
```

## 6. Implement factorial function in JS.

## 7. Implement function which returns summ of 2 arguments like follows: f(2,3) == f(2)(3) == 5

## 8. Implement a function which is executed after some timeout. The timeout should be reseted each time when the fuction is called.

```
function deb() {
    const func = arguments[0];
    const time = arguments[1];
    return function() {
        clearTimeout(this.timeout);
        this.timeout = setTimeout(() => { func(); }, time);
    }
}
```


## 9. Implement str cmp function

```
function strCmp(str1, str2){
	str1 = '' + str1;
  str2 = '' + str2;
  
  let length = Math.min(str1.length,str2.length)
  
  for(let i=0;i<length; i++){
  	if(str1.charCodeAt(i) > str2.charCodeAt(i))
    	return 1;
  	
    if(str1.charCodeAt(i) === str2.charCodeAt(i))
    	continue;
     
    return -1; 
  }
  
  return str1.length - str2.length;
}
```



##Change sync to async

function fizzle(number) {
  return number - 50;
}

function buzzle(v1, v2) {
  return v2 + v2;
}

function fizzleAsync(number, callback) {
  callback(number - 50);
}

function buzzleAsync(v1, v2, callback) {
  callback(v2 + v2);
}

function calculateSync(input){
  let qux = fizzle(input);
  if(qux < input)
    qux +=buzzle(input,qux);
  return qux;
}


function calculateWithWhileSync(input){
  let qux = fizzle(input);
  while(qux < input)
    qux +=buzzle(input,qux);
  return qux;
}

//console.log(calculateWithWhileSync(100))


function calculateAsync(input, callback){
	fizzleAsync(input, (qux) => {
  	 if(qux < input) {
    		buzzleAsync(input,qux, (res_buz) => {
        	callback(qux+res_buz);
        });
        return;
     }
     
     callback(qux);
     
  });
}


function calculateWithWhileAsync(input, callback){
	fizzleAsync(input, (quxInput) => {
  	let cb = (qux) => {
    	if(qux >= input) 
      {
      	callback(qux);
        return;
      }      
      
    		buzzleAsync(input,qux, (res_buz) => { cb(qux+res_buz); });     
    }
    
    cb(quxInput);
     
  });
}


calculateWithWhileAsync(100, (res) => {console.log(res)})




