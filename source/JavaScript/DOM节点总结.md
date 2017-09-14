---
title: DOM节点总结
date: 2015-6-22 9:36:05
type: JavaScript
---


>节点包括三种：属性节点、元素节点、文本节点


        元素类型	    NodeType
        元素              1
        属性              2
        文本	        3
        注释	        8
        文档	        9
***


### 1、查询

在对DOM进行增删改之前，首先要找到对应的元素。具体的查找方法如下：

        getElementByID()       // 得到单个节点
        getElementsByTagName()    // 得到节点数组 NodeList
        getElementsByName()      // 得到节点数组 NodeList
         
#### 1.1、子节点
        Node.childNodes  //获取子节点列表NodeList; 注意换行在浏览器中被算作了text节点，如果用这种方式获取节点列表，需要进行过滤
        Node.firstChild  //返回第一个子节点
        Node.lastChild  //返回最后一个子节点
        
#### 1.2、 父节点
        Node.parentNode   // 返回父节点
        Node.ownerDocument  //返回祖先节点（整个document）
        
#### 1.3、同胞节点
        Node.previousSibling    // 返回前一个节点，如果没有则返回null
        Node.nextSibling       // 返回后一个节点
        
### 2、增加
>新增节点首先要创建节点，然后将新建的节点插入DOM中，所以下面分别介绍创建节点和插入节点的方法，复制节点的方法也在创建节点中进行介绍。

#### 2.1、创建节点
        createElement()    // 按照指定的标签名创建一个新的元素节点
        
        document.createAttribute(attrName);
        对某个节点创建属性
        
        document.createTextNode(text);
        创建文本节点
        
创建代码片段（为避免频繁刷新DOM，可以先创造代码片段，完成所有节点操作之后统一添加到DOM中）
        
        createDocumentFragment()

#### 2.2、复制节点
        clonedNode = Node.cloneNode(boolean) // 只有一个参数，传入一个布尔值，true表示复制该节点下的所有子节点；false表示只复制该节点
        
#### 2.3插入节点
        /*插入node*/
        parentNode.appendChild(childNode);  // 将新节点追加到子节点列表的末尾
        parentNode.insertBefore(newNode, targetNode);  //将newNode插入targetNode之前
        
        /*插入html代码*/
        node.insertAdjacentHTML('beforeBegin', html);  //在该元素之前插入代码
        node.insertAdjacentHTML('afterBegin', html);  //在该元素的第一个子元素之前插入代码
        node.insertAdjacentHTML('beforeEnd', html);  //在该元素的最后一个子元素之后插入代码
        node.insertAdjacentHTML('afterEnd', html);  //在该元素之后插入代码
        
### 3、替换节点
        parentNode.replace(newNode, targetNode);    //使用newNode替换targetNode
        
### 4、删除节点
#### 4.1、移除节点
        parentNode.removeChild(childNode);  // 移除目标节点
        node.parentNode.removeChild(node);    //在不清楚父节点的情况下使用
        
#### 4.2、修改文本节点
        appendData(data);
        将data加到文本节点后面
        
        deleteData(start,length);
        将从start处删除length个字符
        
        insertData(start,data)
        在start处插入字符,start的开始值是0;
        
        replaceData(start,length,data)
        在start处用data替换length个字符
        
        splitData(offset)
        在offset处分割文本节点
        
        substringData(start,length)
        从start处提取length个字符
        
### 5、属性节点
操作属性节点，就是对DOM样式进行增删改查。对于行内样式、内联样式、外部样式有不同的操作方法；各种方法获得的样式也有可读可写和只读之分。

#### 5.1、直接获取CSS样式
        node.style.color     // 可读可写
        
#### 5.2、Style本身的属性和方法
         node.style.cssText     //获取node行内样式字符串
         node.style.length      //获取行内样式个数
         node.style.item(0)     //获取指定位置的样式
         
#### 5.3、获取和修改元素样式
        HTML5为元素提供了一个新的属性：classList 来实现对元素样式表的增删改查。操作如下：
                node.classList.add(value);     //为元素添加指定的类
                node.classList.contains(value);  // 判断元素是否含有指定的类，如果存在返回true
                node.classList.remove(value);  // 删除指定的类
                node.classList.toggle(value);  // 有就删除，没有就添加指定类
        
#### 5.4、修改DOM特性的方法
        Node.getAttribute('id')    // 获取
        Node.setAttribute('id')    // 设置
        Node.removeAttribute()     // 移除
        Node.attributes        // 获取DOM全部特性
        
#### 5.5、只读方法
>getComputedStyle是window的方法。它能够获取当前元素所有最终使用的CSS属性值，但是是只读的。它有两个参数，第一个为传入的节点，第二个可以传入:hover, :blur等获取其伪类样式，如果没有则传入null。

然而IE并不支持getComputedStyle方法，可以使用currentStyle来保持兼容性：

        window.getComputedStyle ? window.getComputedStyle(node, null) : node.currentStyle



<br>







