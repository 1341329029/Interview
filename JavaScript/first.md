## **HTML+JS 考试**  

1. 列举出5个块状元素和3个内联元素

  ```
  div,ul,ol,li,table,from             em,b,a
  ```

2. css都有哪些选择器

   id选择器(例: #banner)

   class选择器(例: .side)

   标签选择器(例: p{})

   层级选择器 例: div p

   通配选择器 例 *

   伪类选择器 a:hover

   CSS3中还增加一些选择器,

   如: last_child

   ul li:last-child, 选中最后一个li

   ul li:first-child,选中第1个li

   ul li:nth-chid(3) 选中第3个li

3. 介绍CSS盒模型

   ![1265396-20171119143703656-1332857321](images\1265396-20171119143703656-1332857321.png)

4. /img目录下有a.js文件 
   请在页面中引入a.js

   ```html
   <script src = "./img/a.js"></script>
   ```

5. 利用js,让当前页面跳转到http://www.baidu.com 

   ```javascript
   window.location.href="你所要跳转的页面";
   window.open="www.baidu.com";
   ```

6. ```html
   <div id=”test”>some thing</div>,写一段原生Js,让此div点击后消失.
   ```

   ```javascript
   var dv=document.getElementById("test");
   dv.onclick=function(){
     //dv.parentNode.removeChild(dv);  
     this.style.display = ‘none’;
   }
   ```

7. ```html
     <input type="text" id="username" value="请输入用户名">
       写一段js,让此表单获得焦点进,自动清空值.
     ```

      ```javascript
    <body>
        <input type="text" name="username" value="请输入用户名"＞
    </body>
    <script>
    var inp = document.getElementsByName('username')[0];
    inp.onfocus = function () {
        this.value = '';
    }

    inp.onblur = function() {
        if(this.value == '' ) {
            this.value = '请输入用户名';
        }
    }
    </script> 
     ```

  

8. ```html
     原生js写如下效果,点击”增”按钮,给ul中加一个<li>冬</li>
       <input type="button" value="增">
       <ul id=”season”>
         <li>春</li>
         <li>夏</li>
         <li>秋</li>
       </ul>
     ```
      ```javascript
    <script type="text/javascript">
        var uld = document.getElementById("season");
        var btn = document.getElementsByTagName("input")[0];
        btn.onclick=function () {
            var lid=document.createElement("li");
            lid.innerText="冬";
            uld.appendChild(lid);
    }
    </script>
     ```

9. ```html
   <form id=”reg”></form>,为此表单加一个提交事件,用至少3种办法.
   ```
    ```javascript
    DOM  level 0级的内联写法
    <form id=”reg” onsubmit=”return xx()”>

    DOM level 0级的外部写法
    document.getElementById(‘reg’).onsubmit = function() {
    }

    DOM level 2级的写法
    document.getElementById(‘reg’).addEventListener(‘sumit’ , function()	{});
    ```



10. 请描述JavaScript事件委托机制，jQuery提供哪个函数实现了事件委托机制
    指在某些对象的上级元素中,绑定事件, 达到绑定一个函数,能够监听下级DOM对象的变化.

    ```javascript
    用on函数
    
    $(‘ul’).on(‘click’ , ‘li’ , function() {
    });
    
    问,为什么直接绑定在li上?
    答:如果绑定在li上, 需要多个li对象各绑定事件,效率低.
    其实,如果动态生成了一个新的li, 此li没有事件.
    而利用委托,新增的li依然有事件.
    
    ```

11. 用jquery,让上题中的ul隐藏(提示,隐藏和显示函数,一行代码就行

    ```javascript
    $("#season").hide();
    ```

12 . var stu = ‘{“name”:”lily” , “age”: 29}’;问 stu是什么类型的变量?  如何把stu变成对象?

```javascript
答: 字符串, 
1.eval(‘(’ +stu + ‘)’),  
2.JSON.parse(stu)
```

13. ```html
    <input type="text" name="username"><span id="info"></span>
    另有check.php?username=xxx, 即可检验用户名是否存在,存在返回1,不存在返回0
    用jquery完成ajax检测用户名, 检测结果输出在span中
    ```

    ```javascript
    $(‘input’).blur(function(){
    var url = ‘check.php?username=’ + this.value;   
    $.get(url , function(rs) {
       if(rs == ‘1’) {
       } else {
       }
    });
    });
    
    ```

