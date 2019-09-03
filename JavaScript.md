# JS中的一些知识
## 2019.9.3 
### 脚本的位置和使用
脚本可以出现在<head></head>，<body></body>之间，把脚本放在body元素底部，可改善显示速度

```javascript
<script>
function myFunction() {
     document.getElementById("标签的ID").innerHTML = "触发函数后更改的文本";
  }
 </script>
  ```
  多次使用的同一脚本，可以放在外部文件中，如放在 myScript.js中，引用时方法如下：
  
 ```javascript
  <script src="myScript.js"></script>
```
