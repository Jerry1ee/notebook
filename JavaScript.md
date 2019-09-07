# JS中的一些知识
## 基础
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

### 一些特殊符号的应用
- ==== 号：等值等型。        
- ！== 号：不等值或不等型。
- typeof:返回变量的类型。
- instanceof：返回true，如果对象是对象类型的实例。
- x ** y 产生的结果与 Math.pow(x,y) 相同。
### JS数据类型
```javascript
var length = 7;                             // 数字
var lastName = "Gates";                      // 字符串
var cars = ["Porsche", "Volvo", "BMW"];         // 数组
var x = {firstName:"Bill", lastName:"Gates"};    // 对象 
//   js拥有动态类型，相同变量可用作不同类型
```
数组和对象</br>
- 数组：用方括号书写，表示方法为
```javascript
var cars=['bens','porsche','BMW']
```
- 对象：用花括号书写，属性采用键值对的形式，表示方法为
```javascript
var student={
number:111,
name:"Lee",
age:22,
country:"China"
}
```
- null 的特殊性：js中null是对象类型，可以通过把对象设置为null来清空对象，清空后类型仍为对象
### 函数
- 函数的定义
```javascript
function myFunction(p1, p2) {
    return p1 * p2;              // 该函数返回 p1 和 p2 的乘积
}
```
注意如果函数后不加()，将会返回函数定义，而不是调用结果。
### 对象
- JavaScript 对象是被命名值的容器
- 函数可以作为对象的一个属性
```javascript
var person={
     firstName : "Jerry",
     lastName :"Lee",
     fullName : function () { return this.firstName + " " + this.lastName ;}
};
```
- this 关键词：</br>
函数定义中常表示引用该函数的“拥有者”
- 访问对象属性：</br>
objectName.propertyName 或 objectName["propertyName"]</br>
### 事件
HTML 事件是发生在 HTML 元素上的“事情”，当在 HTML 页面中使用 JavaScript 时，JavaScript 能够“应对”这些事件。
- 例如元素对某些事件的发生，使用JavaScript来进行相应的反应：
```JavaScript
<p id = "demo"></p>
<button onclick = displayDate()></button>
<script>
function displayDate(){
     document.getElementById("demo").innerHTML = Date();
}
</script>
```
| 事件	| 描述 |
|-----|------|
|onchange|     HTML元素已被改变|
|onclick| 用户点击了 HTML 元素|
|onmouseover|  用户把鼠标移动到 HTML 元素上|
|onmouseout|   用户把鼠标移开 HTML 元素|
|onkeydown|    用户按下键盘按键|
|onload|	浏览器已经完成页面加载|
### 字符串

可用'' 或 ""来标注，\可以用来转义特殊字符，换行一般用+连接。；
**注意不要把字符串声明为对象**

### 字符串方法

- length：返回字符串长度
- indexOf(): 返回方法指定文本**首次**出现的索引，未找到返回-1
- lastIndexOf(): 返回方法指定文本**最后一次**出现的索引，未找到返回-1
- search(): 搜索特定值字符串，并返回出现位置
- indexOf()和search()比较：search()方法无法设置第二个开始位置参数；indexOf() 方法无法设置更强大的搜索值（正则表达式）。
### 提取部分字符串
- slice(start, end)：提取并返回新字符串，如果参数为负数，则从末尾往前提取；若第二个参数为空，则直接提取剩余部分。
- substring(start, end)：与slice()类似，但无法接受负数参数。
- substr(start, length)：与slice()类似，但第二个参数是提取字符串的长度。
### 替换字符串内容
- replace(): 用另一个值替换在字符串中指定的值，replace() 方法不会改变调用它的字符串，它返回新的字符串，且默认只替换首个匹配。


