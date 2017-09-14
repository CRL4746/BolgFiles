---
title: HTML5 drag & drop 拖拽与拖放
date: 2015-7-13 10:23:04
type: "JavaScript"
---

### 一、相关重点

#### 1、DataTransfer 对象：退拽对象用来传递的媒介，使用一般为Event.dataTransfer。
#### 2、draggable    属性：就是标签元素要设置draggable=true，否则不会有效果，例如：
    <div title="拖拽我" draggable="true">列表1</div>
    
#### 3、ondragstart  事件：当拖拽元素开始被拖拽的时候触发的事件，此事件作用在被拖曳元素上
#### 4、ondragenter  事件：当拖曳元素进入目标元素的时候触发的事件，此事件作用在目标元素上
#### 5、ondragover   事件：拖拽元素在目标元素上移动的时候触发的事件，此事件作用在目标元素上
#### 6、ondrop       事件：被拖拽的元素在目标元素上同时鼠标放开触发的事件，此事件作用在目标元素上
#### 7、ondragend    事件：当拖拽完成后触发的事件，此事件作用在被拖曳元素上
#### 8、Event.preventDefault()  方法：阻止默认的些事件方法等执行。在ondragover中一定要执行preventDefault()，否则ondrop事件不会被触发。另外，如果是从其他应用软件或是文件中拖东西进来，尤其是图片的时候，默认的动作是显示这个图片或是相关信息，并不是真的执行drop。此时需要用用document的ondragover事件把它直接干掉。
#### 9、Event.effectAllowed     属性：就是拖拽的效果。

### 二、相关实例
       
>此demo实现的效果是把右侧的列表拖动到左侧的写着果敢的“垃圾箱”三个字的div层上，此列表元素就会从这个星球上消失。

其中HTML结构如下：   
    
    <div class="dustbin"><br />垃<br />圾<br />箱</div>
    <div class="dragbox">
        <div class="draglist" title="拖拽我" draggable="true">列表1</div>
        <div class="draglist" title="拖拽我" draggable="true">列表2</div>
        <div class="draglist" title="拖拽我" draggable="true">列表3</div>
        <div class="draglist" title="拖拽我" draggable="true">列表4</div>
        <div class="draglist" title="拖拽我" draggable="true">列表5</div>
        <div class="draglist" title="拖拽我" draggable="true">列表6</div>
    </div>
    <div class="dragremind"></div>
  
JS代码如下：

    var $ = function(selector) {
        /*简单的选择器方法*/
        ...
    };
    
    var eleDustbin = $(".dustbin")[0], eleDrags = $(".draglist"), lDrags = eleDrags.length, eleRemind = $(".dragremind")[0], eleDrag = null;
    for (var i=0; i<lDrags; i+=1) {
        eleDrags[i].onselectstart = function() {
            return false;
        };
        eleDrags[i].ondragstart = function(ev) {
            /*拖拽开始*/
            //拖拽效果
            ev.dataTransfer.effectAllowed = "move";
            ev.dataTransfer.setData("text", ev.target.innerHTML);
            ev.dataTransfer.setDragImage(ev.target, 0, 0);
            eleDrag = ev.target;
            return true;
        };
        eleDrags[i].ondragend = function(ev) {
            /*拖拽结束*/
            ev.dataTransfer.clearData("text");
            eleDrag = null;
            return false
        };
    }
    eleDustbin.ondragover = function(ev) {
        /*拖拽元素在目标元素头上移动的时候*/
        ev.preventDefault();
        return true;
    };
    
    eleDustbin.ondragenter = function(ev) {
        /*拖拽元素进入目标元素头上的时候*/
        this.style.color = "#ffffff";
        return true;
    };
    eleDustbin.ondrop = function(ev) {
        /*拖拽元素进入目标元素头上，同时鼠标松开的时候*/
        if (eleDrag) {
            eleRemind.innerHTML = '<strong>"' + eleDrag.innerHTML + '"</strong>被扔进了垃圾箱';
            eleDrag.parentNode.removeChild(eleDrag);
        }
        this.style.color = "#000000";
        return false;
    };
