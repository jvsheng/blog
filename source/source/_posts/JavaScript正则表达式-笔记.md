---
title: JavaScript正则表达式 - 笔记
date: 2017-09-29 17:40:22
tags:
---
- 正则表达式括号的作用  
- 回溯  

<!-- more -->
## **可视化工具**

<iframe frameborder="0" width="348" height="208" src="https://jex.im/regulex/#!embed=true&flags=&re=%5E(a%7Cb)*%3F%24"></iframe>

## **正则表达式括号的作用**
<code>提取数据、JS中引用、正则表达式中反向引用</cdoe>

#### **1.1 分组**
```javascript
/(ab)+/
```
这里括号的作用在于，使 <code>+</code> 量词作用于 ab 这个整体
  
#### **1.2 分支结构**
```javascript
/(a|b)/
```
提供分支表达式的所有可能

#### **2.1 分组引用**

![正则表达式分组](/blog/img/17.9.30.1.jpg)
正则引擎在匹配过程中，会为每一个分组开辟一个空间，用来存储每一个分组匹配到的数据。

#### **2.2 API中引用数据**
```javascript
var regex = /(\d{4})-(\d{2})-(\d{2})/;
var string = "2017-06-12";
console.log(string.match(regex));
// =>  ["2017-06-12", "2017", "06", "12", index: 0, input: "2017-06-12"]
```
> <code>match</code> 返回的一个数组，第一个元素是整体匹配结果，然后是各个分组（括号里）匹配的
  内容，然后是匹配下标，最后是输入的文本。另外，正则表达式是否有修饰符 <code>g</code>，match
  返回的数组格式是不一样的。

在运行一次正则操作后，RegExp对象上就可以通过<code>$1</code>访问匹配到的分组数据。我们可以在replace的第二个参数中，通过<code>函数</code>使用这些数据，进行替换等操作。
```javascript
var regex = /(\d{4})-(\d{2})-(\d{2})/;
var string = "2017-06-12";
var result = string.replace(regex, function () {
return RegExp.$2 + "/" + RegExp.$3 + "/" + RegExp.$1;
});
console.log(result);
// => "06/12/2017

var regex = /(\d{4})-(\d{2})-(\d{2})/;
var string = "2017-06-12";
var result = string.replace(regex, function (match, year, month, day) {
return month + "/" + day + "/" + year;
});
console.log(result);
// => "06/12/2017"
```
特别注意下上面第二个示例中，函数的参数和match返回值的顺序与对应关系。

#### **2.3 正则中反向引用**

除了使用相应API来引用分组，也可以在正则本身里引用分组，但只能引用之前出现的分组，即反向引用。
示例，需要一个正则表达式，匹配分隔符前后一致的时间字符串：
![反向引用](/blog/img/17.9.30.2.jpg)

- <code>括号嵌套</code>
可以理解成，反向引用是引用的括号分组起来的数据，最外层的括号级别最高，所以，\1代表的是最外层的分组

- <code>\10的含义</code>
\10表示第10个分组，如果要匹配\1 和 0，使用(?:\1)0

- <code>引用不存在的分组</code>
在正则里引用不存在的分组时，不会报错，只是匹配反向引用的字符本身（\2就匹配'\2'）

- <code>分组后有量词</code>
分组后有量词的话，分组最终捕获到的数据是最后一次的匹配。
```javascript
var regex = /(\d)+ \1/;
console.log( regex.test("12345 1") );
// => false
console.log( regex.test("12345 5") );
// => true
```

- <code>非捕获括号</code>
正则中出现的括号，都会捕获它们匹配到的数据，以便后续引用，称之为捕获型分组
如果只想要括号最原始的功能，不引用它，既不在API里引用，也不在正则里反向引用。