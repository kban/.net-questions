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

5) Упростите функцию
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

Решение: 

function func(s, a, b) 
{
	if(!s)
  	return -1;
  
  return Math.max(a && a.length == 1 ? s.lastIndexOf(a) : -1, b && b.length == 1 ? s.lastIndexOf(b) : -1);
}


console.log(func(null,'d','sa'))

6) что будет выведено на экран? что не так с кодом и как решить
var o = {
    x: 10,
    foo: function() {
        for (var i = 0; i < 10; ++i){
            setTimeout(function() {
                console.log(this.x + i);
            }, 0);
        }
    }

Решение
20 раз выведет 20

var o = {
    x: 10,
    foo: function() {
        for (let i = 0; i < 10; ++i){
            setTimeout(() => console.log(this.x + i), 0);
        }
    }
}

o.foo();
