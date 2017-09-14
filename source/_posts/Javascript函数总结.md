---
title: Javascript中函数的总结
date: 2015-5-22 20:34:14
type: "JavaScript"
---
### 函数定义

>函数是由这样的方式进行声明的：关键字 function、函数名、一组参数，以及置于括号中的待执行代码。 
 函数的构造语法有这三种： 
 
代码如下:

    1.function functionName(arg0, arg1, ... argN) { statements }//function语句 
    2.var function_name = new Function(arg1, arg2, ..., argN, function_body);//Function()构造函数 
    3.var func = function(arg0, arg1, ... argN) { statements };//函数直接量
    
    示例：
    1.function f(x){return x*x};//function语句 
    2.var f = new Function("x","return x*x;");//Function()构造函数 
    3.var f = function(x){return x*x;};//函数直接量
     
**如果函数无明确的返回值，或调用了没有参数的 return 语句，那么它真正返回的值是 undefined。** 

### 1、Function()构造函数 

* 函数实际上是功能完整的对象 。Function类可以表示开发者定义的任何函数。用Function类直接创建函数的语法如下：

    var function_name = new function(arg1, arg2, ..., argN, function_body)

    在上面的形式中，每个 arg 都是一个参数，最后一个参数是函数主体（要执行的代码）。这些参数必须是字符串。
    
    var sayHi = new Function("sName", "sMessage", "alert('Hello ' + sName + sMessage);"); 
    sayHi("jzj,", "你好!");//Hello jzj，你好！ 
      
* 函数名只是指向函数的变量，那么可以把函数作为参数传递给另一个函数吗？答案是可以的，请看：
           
   代码如下:
   
        function callAnotherFunc(fnFunction, vArgument) {
            fnFunction(vArgument);
        }
        var doAdd = new Function("iNum", "alert(iNum + 10)"); 
        callAnotherFunc(doAdd, 10); //输出 "20"

* 注意：尽管可以使用 Function 构造函数创建函数，但最好不要使用它，因为用它定义函数比用传统方式要慢得多。不过，所有函数都应看作 Function 类的实例 。

>如果你定义的函数没有参数，那么可以只需给构造函数传递一个字符串（即函数的主体）即可。 

* 注意：传递给构造函数Function()的参数中没有一个用于说明它要创建的函数名。用Function()构造函数创建的未命名函数有时被称为“匿名函数”。
 
>Function()函数允许我们动态地建立和编译一个函数，它不会将我们限制在function语句预编译的函数体中。

### 2、函数直接量 

* 函数直接量是一个表达式，它可以定义匿名函数。函数直接量的语法和function语句非常相似，只不过它被用作表达式，而不是用作语句，而且也无需指定函数名。语法：
 
代码如下:

    var func = function(arg0, arg1, ... argN) { statements };//函数直接量 
    
* 虽然函数直接量创建的是未命名函数，但是它的语法也规定它可以指定函数名，这在编写调用自身的递归函数时非常有用，例如：
 
代码如下:

    var f = function fact(x) { 
    if (x <= 1) { 
    return 1; 
    } else { 
    return x * fact(x - 1); 
    } 
    }; 
>注：它并没有真正创建一个名为fact()函数，只是允许函数体用这个名字来引用自身。JavaScript1.5之前的版本中没有正确实现这种命名的函数直接量。

#### 函数引用 

**函数名并没有什么实质意义，它不过是用来保存函数的变量名字，可以将这个函数赋给其他变量，它仍然会以相同方式起作用：** 

代码如下:

    function square(x){return x*x;} 
    var a = square; 
    var b = a(5);//b 为25 
    
### 3、Function()构造函数和函数直接量差别 

>Function()构造函数和函数直接量之间的差别有一点就是：使用构造函数Function()创建的函数不使用词法作用域，相反的，它们总是被顶级函数来编译，如：

    var y = "global"; 
    function constructFunction() { 
    var y = "local"; 
    //Function()构造函数 
    return new Function("return y;");//不使用局部作用域 
    } 
    function constFunction() { 
    var y = "local"; 
    //函数直接量 
    var f = function () { 
    return y;//使用局部作用域 
    }; 
    return f; 
    } 
    //显示 global，因为Function()构造函数返回的函数并不使用局部作用域 
    alert(constructFunction()()); 
    //显示 lobal，因为函数直接量返回的函数并使用局部作用域 
    alert(constFunction()()); 
    
