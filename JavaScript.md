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

可用'' 或 ""来标注，\可以用来转义特殊字符，换行一般用+连接。</br>
**注意不要把字符串声明为对象**</br>

**字符串方法**

- length：返回字符串长度
- indexOf(): 返回方法指定文本**首次**出现的索引，未找到返回-1
- lastIndexOf(): 返回方法指定文本**最后一次**出现的索引，未找到返回-1
- search(): 搜索特定值字符串，并返回出现位置
- indexOf()和search()比较：search()方法无法设置第二个开始位置参数；indexOf() 方法无法设置更强大的搜索值（正则表达式）。</br>
**提取部分字符串**
- slice(start, end)：提取并返回新字符串，如果参数为负数，则从末尾往前提取；若第二个参数为空，则直接提取剩余部分。
- substring(start, end)：与slice()类似，但无法接受负数参数。
- substr(start, length)：与slice()类似，但第二个参数是提取字符串的长度。
- charAt(position): 方法返回字符串中指定下标（位置）的字符串。
- charCodeAt(position): 方法返回字符串中指定索引的字符 unicode 编码。</br>
**替换字符串内容**
- replace(): 用另一个值替换在字符串中指定的值，replace() 方法不会改变调用它的字符串，它返回新的字符串，且默认只替换首个匹配。</br>
replace()默认对大小写敏感。
- replace()中的正则式使用: /i 忽略大小写 ； /g 替换所有。</br>
**字符串大小写转换**
- toUpperCase(): 把字符串转化为大写
- toLowerCase(): 把字符串转化为小写</br>
**拼接字符串**
- concat(): 连接两个字符串
- trim(): 去除字符串两端空白</br>
**把字符串转换为数组**
- split(): 
txt.split(",");          // 用逗号分隔 </br>
txt.split(" ");          // 用空格分隔 </br>
txt.split("|");          // 用竖线分隔 </br>
若分隔符为"" ，返回的数组是间隔单个字符的数组</br>
### JavaScript数字
**JavaScript 数值始终是 64 位的浮点数</br>**

|值(aka Fraction/Mantissa)	|指数|	符号|
|------------------------------|----|-----|
|52 bits(0 - 51)	|11 bits (52 - 62)	|1 bit (63)|

**注意 使用+ 的时候，字符和数值的级联**</br>
**NaN 非数值**</br>
**Infinity**</br>
Infinity （或 -Infinity）是 JavaScript 在计算数时超出最大可能数范围时返回的值。</br>
**十六进制**</br>
JavaScript 会把前缀为 0x 的数值常量解释为十六进制。利用toString()可以把数字转化为十六进制、八进制或二进制。

### JavaScript数字方法
- toString():  以字符串返回数值
- toExponential(): 返回字符串值，它包含已被四舍五入并使用指数计数法的数字，参数定义小数点后的字符数
- toFixed(): 返回字符串值，它包含了指定位数小数的数字
- toPrecision(): 返回字符串值，它包含了指定长度的数字
- valueOf(): 以数值返回数值
- Number(): 可用于把 JavaScript 变量转换为数值
- parseFloat(): 解析其参数并返回浮点数
- parseInt(): 解析其参数并返回整数

### 数组
数组是对象，访问其属性时，用元素的下标数字。</br>

**数组属性和方法** </br>
- length : 属性返回数组的长度（数组元素的数目）
- Array.foreach(): 也可以进行数组遍历
- push() : 向数组添加新元素
- toString() : 把数组转换为数组值（逗号分隔）的字符串
- join() : 将所有数组元素结合为一个字符串。它的行为类似 toString()，但可以规定分隔符
- pop() : 删除最后一个元素，返回“被弹出”的值，即这个被删除的元素
- push() : 在数组结尾处向数组添加一个新的元素，返回新数组的长度
- shift() : 删除首个数组元素，并把所有其他元素“位移”到更低的索引，返回被位移出的元素
- unshift() : 在数组开头添加新元素，并“反向位移”旧元素，返回新数组长度 
- delete:一般删除时不用delete，因为会留下空洞，要用pop或者push替代
- splice() : 方法可用于向数组添加新项，经常用来不留空洞地替换元素</br>
```JavaScript
var fruits = ["Banana", "Orange", "Apple", "Mango"];
fruits.splice(2, 0, "Lemon", "Kiwi");
/*第一个参数（2）定义了应添加新元素的位置（拼接）。
第二个参数（0）定义应删除多少元素。
其余参数（“Lemon”，“Kiwi”）定义要添加的新元素。
splice() 方法返回一个包含已删除项的数组*/
```
- concat() : 合并（连接）现有数组来创建一个新数组，参数数量任意
- slice() : 方法用数组的某个片段切出新数组，也可接受两个参数，从开始参数选取元素，直到结束参数（不包括）为止
```JavaScript
var fruits = ["Banana", "Orange", "Lemon", "Apple", "Mango"];
var citrus = fruits.slice(1); 
/*结果 Orange, Lemon, Apple, Mango*/
```

**数组排序** </br>
- sort() :方法以字母顺序对数组进行排序
- reverse() :方法反转数组中的元素
- 比值函数 : 比较函数的目的是定义另一种排序顺序，它返回一个正、负或零值。当 sort() 函数比较两个值时，会将值发送到比较函数，并根据所返回的值（负、零或正值）对这些值进行排序
- 随机排序数组 :
```JavaScript
var points = [40, 100, 1, 5, 25, 10];
points.sort(function(a, b){return 0.5 - Math.random()}); 
```
- 查找最高最低数组值 : Math.max.apply() 或者 Math.min.apply()

**数组迭代** </br>
- forEach() : 为数组的每个元素调用一次函数（回调函数）
```JavaScript
var txt = "";
var numbers = [45, 4, 9, 16, 25];
numbers.forEach(myFunction);

function myFunction(value, index, array) {
  txt = txt + value + "<br>"; 
}
/*若将txt作为 <p>标签内容输出，则为：
45
4
9
16
25*/
该函数接受 3 个参数：项目值，项目索引，数组本身
```
- Array.map() : 对每个数组元素执行函数来创建新数组;不会对没有值的数组元素执行函数;不会更改原始数组。
```JavaScript
var numbers1 = [45, 4, 9, 16, 25];
var numbers2 = numbers1.map(myFunction);

function myFunction(value, index, array) {
  return value * 2;
}
/*numbers2 数组应该为 90，8，18，32，50
该函数有 3 个参数：项目值，项目索引，数组本身
当回调函数仅使用 value 参数时，可以省略索引和数组参数*/
```
- Array.filter() : 创建一个包含通过测试的数组元素的新数组
```JavaScript
var numbers = [45, 4, 9, 16, 25];
var over18 = numbers.filter(myFunction);

function myFunction(value, index, array) {
  return value > 18;
}/* numbers数组为 45,25
```
-Array.reduce() :  在每个数组元素上运行函数，以生成（减少它）单个值；从左到右工作，如果要从右到左使用reduceRight()；reduce() 方法不会减少原始数组
```JavaScript
/*下面是一个确定数组元素总和的例子
var numbers1 = [45, 4, 9, 16, 25];
var sum = numbers1.reduce(myFunction);

function myFunction(total, value, index, array) {
  return total + value;
}
/*reduce函数接受一个初始值，例如reduce(myFunction,100)，则从100开始添加元素值
```
- Array.every() : 方法检查**所有**数组值是否通过测试
- Array.some() : 方法检查**某些**数组值是否通过测试
- Array.indexOf() 方法在数组中搜索元素值并返回其位置</br>
array.indexOf(item, start):item必需。要检索的项目, start可选。从哪里开始搜索。负值将从结尾开始的给定位置开始，并搜索到结尾。
- Array.lastIndexOf() : 与 Array.indexOf() 类似，但是从数组结尾开始搜索
- Array.find() : 方法返回通过测试函数的第一个数组元素的值
- Array.findIndex() : 方法返回通过测试函数的第一个数组元素的索引
### 日期
**日期**</br>
var d =</br>
- new Date()
- new Date(year, month, day, hours, minutes, seconds, milliseconds) : 参数可以省略部分
- new Date(milliseconds)
- new Date(date string)
- new Date(dateString) : 从日期字符串创建一个新的日期对象：
```javaScript
var d = new Date("October 13, 2014 11:13:00");
```
- new Date(milliseconds) : 创建一个零时加毫秒的新日期对象
- toDateString() : 方法将日期转换为更易读的格式

**日期格式化**</br>
- ISO 日期（完整的日期加时、分和秒）: </br>
写日期也可以添加时、分和秒 (YYYY-MM-DDTHH:MM:SS)：
```javaScript
var d = new Date("2018-02-19T12:00:00");
```
- JavaScript 短日期 : 短日期通常使用 "MM/DD/YYYY" 这样的语法 "09/11/2019"
- JavaScript 长日期 : 长日期通常以 "MMM DD YYYY" 这样的语法来写 "Sep 11 2019", 月和天可以互换












