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
            setTimeout(() => console.log(this.x + i), 0); //// ()=> construction doesn't have each own scode and uses the scope from the caller function, thus 'this' will be accessable here.
        }
    }
}
```
