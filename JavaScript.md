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
id 属性定义 HTML 元素。innerHTML 属性定义 HTML 内容</br>
多次使用的同一脚本，可以放在外部文件中，如放在 myScript.js中，引用时方法如下，src也可以是具体的网址：
  
 ```javascript
  <script src="myScript.js"></script>
```
### JavaScript 能够以不同方式“显示”数据

- 使用 window.alert() 写入警告框
- 使用 document.write() 写入 HTML 输出 (在 HTML 文档完全加载后使用 document.write() 将删除所有已有的 HTML) 
- 使用 innerHTML 写入 HTML 元素
- 使用 console.log() 写入浏览器控制台

## 2019.9.4
### 
