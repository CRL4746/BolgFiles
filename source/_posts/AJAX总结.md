---
 title: AJAX总结
 date: 2015-12-22 07:31:12
 type: "JavaScript"
---

# 1、ajax是什么?

>asynchronous javascript and xml:异步的javascript和xml。

ajax是一种用来改善用户体验的技术，其本质是利用浏览器内置的一种特殊的对象 (XMLHttpRequest)异步(即发送请求时，浏览器不会销毁当前页面，用户可以继续在当前页面做其它的操作)的向服务器发送请求,并且利用服务器返回的数据(不再是一个完整的页面，只是部分的数据，一般使用文本或者xml返回)来部分更新当前页面。使用ajax技术之后，页面无刷新，并且不打断用户的操作。

## 1.1、ajax技术的优点：

(1).页面无刷新

(2).不打断用户的操作，用户的体验好。

(3).按需获取数据，浏览器与服务器之间数据的传输量减少。

(4).是一个标准技术，不需要下载任何的插件。

(5).可以利用客户端(浏览器)的计算能力。

# 2、ajax对象

## 2.1、如何获得ajax对象?

>XMLHttpRequest并没有标准化，要区分浏览器:

    function getXhr(){
          var xhr;
    if(window.XMLHttpRequest){
          //非ie浏览器
          xhr = new XMLHttpRequest();
    }else{
          //ie
          xhr = new ActiveXObject('Microsoft.XMLHttp');
      }
    }

## 2.2、ajax对象的属性
### onreadystatechange
绑订一个事件处理函数(注册监听器),当ajax对象的readyState值发生了改变(比如，从0-->1)，就会产生readystatechange事件。

### responseText
获得服务器返回的文本

### responseXML
获得服务器返回的xml数据

### status
获得状态码

### readyState
ajax对象在与服务器进行通讯时，通过readyState属性值可以获取该对象的通讯的状态，其属性有5个(0,1,2,3,4),当属性值为4时，表示ajax对象已经获得了服务器返回的所有的数据。

    0:(未初始化)对象已建立（获得），但是尚未初始化（尚未调用open方法）
    
    1：（初始化）对象已建立，调用了open方法但尚未调用send方法。

    2：（发送数据）send方法一调用。
    
    3：（数据传送中）已接收部分数据。
    
    4：（响应结束）接收了所有的数据。

## 2.3、ajax编程的基本步骤

### step1, 先获得ajax对象

    var xhr = getXhr();

### step2, 发送请求

    xhr.open(请求方式,请求地址,异步还是同步);
    
    请求方式: get/post
    
    请求地址:如果是get请求，请求参数要添加到请求地址的后面。
    
    true表示异步请求 ：ajax对象发请求的同时，用户可以对当前页面做其它的操作。
    
    false表示同步请求:ajax对象发请求的同时,浏览器会锁订当前页面，用户需要等待处理完成之后，浏览器才会解除锁定，才能做下一步操作。

#### 方式一：get请求

    xhr.open('get','check_username.do?username=zs',true);
    
    xhr.onreadystatechange=f1;
    
    xhr.send(null);

#### 方式二：post请求

    xhr.open('post');

### step3,编写服务器端的代码

>服务器端一般不需要返回完整的页面，只需要返回部分的数据，比如一个简单的字符串。

### step4, 编写监听器

    function f1(){
      if(xhr.readyState == 4){
      //获得服务器返回的数据
      var txt = xhr.responseText;
      //dom操作
      }
    }

**eg:**

    function check_username() {
    //step1，获得ajax对象
        var xhr = getXhr();
        document.write(xhr.readyState + "<br/>");//0
        
        //step2, 发请求
        xhr.open('get', 'check_username.do?username=' + $F('username'), true);//建立连接
        document.write(xhr.readyState + "<br/>");//1
        xhr.onreadystatechange = function () {//绑定事件处理函数（注册监听器）
        
            //只有ajax对象的readyState值为4时，才能获得服务器返回的完整数据
            document.write(xhr.readyState + "<br/>");//依次输出2、3、4
            if (xhr.readyState == 4) {
                //获得服务器返回的文本数据
                var txt = xhr.responseText;
                
                //更新页面
                $('username_msg').innerHTML = txt;
            }
        };
        xhr.send(null);//向服务发送请求
    }

>XMLHttpRequest对象xhr通过onreadystatechange属性绑定了事件处理函数，在整个会话内，onreadystatechange属性会发生多次变化，依次从0-1-2-3-4，每一次属性值的变化都会触发绑定事件函数的执行。

**eg:同步、异步问题：**

    // 验证验证码是否正确
    function check_code() {
        var flag = false;
        // step1，获得ajax对象
        var xhr = getXhr();
        // step2, 发请求
        var uri = 'check_code.do?checkcode=' + $F('checkcode');
        xhr.open('get', encodeURI(uri), false);// 建立连接
        xhr.onreadystatechange = function () {// 绑定事件处理函数（注册监听器）
        // 只有ajax对象的readyState值为4时，才能获得服务器返回的数据。
            if (xhr.readyState == 4) {
                if (xhr.status == 200) {
                    // 获得服务器返回的文本数据
                    var txt = xhr.responseText;
                    // 更新页面
                    if (txt == "error") {
                        $('checkcode_msg').innerHTML = "验证码错误！";
                        flag = false;
                    } else {
                        $('checkcode_msg').innerHTML = "验证码正确！";
                        flag = true;
                    }
                } else {
                    $('checkcode_msg').innerHTML = '系统繁忙，验证出错';
                    flag = false;
                }
            }
        };
        $('checkcode_msg').innerHTML = "正在验证......";// 当服务器响应比较慢时可以看到此提示
        xhr.send(null);// 向服务发送请求
        //当ajax对象向服务器发送同步请求时,
        //浏览器不会向下执行，会等待服务器的响应。
        //如果是异步，send后，并不会先执行绑定事件，而是先向下执行
        //在监听事件中返回值有问题，所以没在其中返回
        return flag;
    }

>当为异步方式时，返回的flag始终是false，flag在监听事件中被有被改变（监听事件在return之前没有执行），所以要设置为同步方式，这样在return falg之前，监听事件就会处理完毕，flag的值就会发生相应的改变。

# 3、ajax编程中的编码问题

### (1)发送get请求:

ie浏览器内置的ajax对象，对中文参数使用gbk编码，而其它浏览器(firefox,chrome)使用utf8编码,服务器端默认使用iso-8859-1去解码。

解决方案:

**step1**,让服务器对get请求中的参数使用指定的编码格式进行解码。

比如，对于tomcat，可以修改 conf/server.xml，只对get方式有效

    <Connector port="8080" protocol="HTTP/1.1" 
    
              connectionTimeout="20000" 
    
              URIEncoding="utf-8"（要加入的代码）
    
              redirectPort="8443" />
    
          <!-- A "Connector" using the shared thread pool-->
    
         ajax经典总结

**step2**,对请求地址，使用encodeURI函数进行统一的编码(utf-8)

    var uri = 'check_username.do?username='+ $F('username');

    xhr.open('get',encodeURI(uri),true);

### (2)发送post

如果发送方式为post，那么所有浏览器内置的ajax对象都会对请求参数使用utf-8进行编码，那么只要在服务器端设置：request.setCharacterEncoding("utf-8");



# 4、发送post请求

    xhr.open('post','check_username.do',true);
    
    xhr.setRequestHeader('content-type','application/x-www-form-urlencoded');
    
    xhr.onreadystatechange=f1;
    
    xhr.send('username=' + $F('username'));

>注意：因为按照http协议的要求，如果发送的post请求，那么请求数据包里面，应该有一个消息头 content-type。但是，ajax对象默认没有，所以，需要调用setRequestHeader方法。

# 5、json (javascript object notation)

### (1)json是什么?

>是一种轻量级的数据交换标准。

#### a,什么是数据交换?
简单地讲，就是将要交换的数据先转换成一个与平台无关的数据格式(比如xml或者json字符串)发送给接受方，接受方再进行相应的转换。

#### b,轻量级
相对于xml，json解析的速度更快，数据量更小。

### (2)json语法格式

#### a)如何表示一个对象
{属性名:属性值,属性名:属性值...}

**要注意:**

 a,属性值的类型可以是string,number,null,boolean, object。

 b,属性名必须使用引号括起来

 c,属性值如果是string，也必须使用引号括起来

#### b)如何表示一个对象组成的数组

[{},{},{}...]

### (3)如何使用json做数据交换

#### 1)java对象(java对象组成的数组或者集合)转换成对应的json字符串

可以从www.json.org去下载对应语言的工具。

使用的json-lib中提供的两个类:

JSONObject, JSONArray

#### 2)json字符串转换成javascript对象

    可以使用prototype提供的evalJSON函数。
    
    prototype.js提供了很多有用的函数:
    
    a, $(id):  document.getElementById(id);
    
    b, $F(id): document.getElementById(id).value;
    
    c,  $(id1,id2,...): 分别查找id为id1,id2...的节点，然后返回一个数组。
    
    d, strip(): 除掉字符串两端的空格。
    
    e, evalJSON():将json字符串转换成对应的javascript对象或者数 组。

# 6、ajax应用中的缓存问题:

当使用IE浏览器时，如果使用get方式发请求，浏览器会先缓存之前

访问的数据，如果访问的地址不变，不会向服务器发请求。

解决方式1:使用post方式发请求。

解决方式2: 在请求地址后面添加一个随机数。

# 7、发送同步请求

    xhr.open('post', 'check_username.do', false);
    function check_username() {
        var xhr = getXhr();
        xhr.open('post', 'show.do', true);
        xhr.setRequestHeader('content-type', 'application/x-www-form-urlencoded');
        xhr.onreadystatechange = function () {
    
            if (xhr.readState == 4) {
                var txt = xhr.responseText;
                alert(txt);
                $('username_msg').innerHTML = txt;
            }
    
        };
        xhr.send('username=' + $F('username'));
    //当ajax对象向服务器发送同步请求时,浏览器不会向下执行，会等待服务器的响应。在完整的收到服务器返回的数据之前，浏览器便处于锁定状态，用户不可进行操作。
    }

