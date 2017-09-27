---
title: Element-ui源码分析 - 单元测试
date: 2017-09-24 00:27:07
tags:
---
> 1.测试的工具函数  
> 2.如何测试自定义指令  
> 3.Mocha测试框架  
> 4.Chai断言库  
<!-- more -->

## 测试的工具函数

## 如何测试自定义指令
```javascript
it('create', () => {
  let count = 0;
  const el = document.createElement('div');
  const vnode = {
    context: {
      handleClick: () => ++count
    }
  };
  const binding = {
    expression: 'handleClick'
  };

  Clickoutside.bind(el, binding, vnode);
  expect(el[ctx]).to.exist;
});
```
这需要跳出Vue的思维，观察源代码，可以发现，自定义指令的js文件中只是export了bind、update、unbind方法，但是绑定指令到Vue是通过<code>directives: { Clickoutside }</code>。  

所以，<code>可以直接调用bind等方法，进行元素指令的绑定</code>，就可以编写相应的测试用例了。

## Mocha测试框架
```javascript
describe('Array', function() {
  describe('#indexOf()', function() {
    it('should return -1 when the value is not present', function() {
      expect([1,2,3].indexOf(5)).to.equal(-1);
    });
  });
});
```
<code>describe函数</code>的第一个参数会被输出在控制台中，作为一个用例集的描述，而且这个描述是可以根据自己的需求来嵌套输出的，称之为：<code>用例集定义函数</code>。

<code>it函数</code>第一个参数用来输出一个用例的描述，前边打个对勾代表测试通过，第二个参数是一个函数，用来编写用例内容，用断言模块来判断结果的正确性，称之为：<code>用例函数</code>。
![测试运行结果](/blog/img/17.9.27.png)

## Chai断言库
```javascript
expect(4 + 5).to.be.equal(9);
```
下面是一些目前遇到的断言语法：
+ equal(value) - 断言目标严格等于(===)value 
+ exist - 断言目标存在，即非null也非undefined  

断言的使用都是相同的套路，<code>to</code>是链接词，没有其他的作用，只是提高可读性。