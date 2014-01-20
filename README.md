Velocity - Template Engine 
==========================
[![Build Status](https://secure.travis-ci.org/shepherdwind/velocity.js.png)](https://travis-ci.org/shepherdwind/velocity.js)
[![NPM version](https://badge.fury.io/js/velocityjs.png)](http://badge.fury.io/js/velocityjs)

velocityjs in npm the package name from the original velocity.js to velocityjs , feel require ("velocity.js") comparison does not look good, so renamed from the 0.3.0 version later in velocityjs next update.

View the latest edition

`$ npm info velocityjs Version`
velocity.js is the velocity template syntax javascript implementation. Velocity is a Java-based template engine. Velocity template template applies to a large number of scenarios to support complex logic operations, including basic data types, variables, and functions assigned functions.


##Features

* Supports both client and server side use
* The syntax is rich logic, language constitutes a tiny door
* Separation of parsing and rendering templates
* The basic syntax is fully supported velocity
* The browser uses the mutual support between a reference template based js module loading mechanisms, such kissy
* Three Helper, friendly data analog solutions
* Vim Syntax


##Install

via npm:

```bash
$ npm install velocityjs
```

##Broswer Support

Execution cake orders packaged velocity.js browser side script:

```bash
$ make parse
```
Installation requires cli under coffeejs, temporary package is used for the kissy, velocity.js ecma5 need some common features, such as foreach, some, isArray , etc., in the node environment comes with features, and is cross-compatible web client to solve the existing library. Need to provide their own set of cross-browser api, such as kissy package:

```js
  //api map
  var utils = {
    forEach : S.each,
    some    : S.some,
    mixin   : S.mix,
    guid    : S.guid,
    isArray : S.isArray,
    indexOf : S.indexOf,
    keys    : S.keys,
    now     : S.now
  };

```

Velocity syntax with high fault tolerance, similar to parse html structure, while the complex rules of grammar, syntax interpreter so execution performance may be slower, velocity.js perform the analysis algorithms and grammar grammatical structure independent of the two processes, the first step, grammatical structure analysis returns an array (syntax tree), describing velocity syntax, grammar and syntax tree performed using the data to calculate the final results of the template.

After executing build, get two documents, namely build / velocity / under index.js and parse.js , both independent of each other, parse.js parsing process can be placed locally, run the command:

The parsing and template stitching apart, in order to facilitate the local compiler syntax tree, reduce web side js operations. Thinking locally compiled template from KISSY of XTemplate .

Although the syntax interpreter can be executed in the browser, but it is not recommended to use.

```bash
# Using the velocity command line tools packaged
veloctiy --build *.vm
veloctiy -b *.vm
```

Source in `test/Web/JS` directory compiled as part of the line is obtained here can be directly accessed .

##Public API

###node_module

```js
var Velocity = require('../src/velocity');

//1. 直接解析
Velocity.render('string of velocity', context);

//2. 使用Parser和Compile
var Parser = Velocity.Parser;
var Compile = Velocity.Compile;

var asts = Parser.parse('string of velocity');
(new Compile(asts)).render(context);
```
####context

`context`是一个对象，可以为空，执行中`$foo.bar`，访问路径是`context.foo.bar`，
`context`的属性可以是函数，和vm中定义保持一致。

###On Broswer

1 . package under the program using the line:

```js
KISSY.use('velocity/index, web/directives', function(S, Velocity, asts){
  var compile = new Velocity(asts);
  S.all('body').html(compile.render());
});
```

2 . use the online compilation:

```js
KISSY.use('velocity/index, velocity/parse', function(S, Velocity, Parser){
  var html = (S.all('#tpl').html());
  var asts = Parser.parse(html);
  var compile = new Velocity(asts);
  console.log(compile.render());
});
```

两者的区别在于asts的获取，第一种方式，直接取asts，第二种，需要首先执行语法分析过
程。

##Syntax

具体语法请访问官网文档：[velocity user guide](http://velocity.apache.org/engine/devel/user-guide.html)。

###Directives

Directives支持`set`, `foreach`, `if|else|elseif`, `macro`, `parse`, `break`。不
支持有，`stop`, `evaluate`, `define`，感觉这些语法比较偏，用处不大，暂时没有实现。
其中`parse`，在web端，使用kissy的模块加载器加载，需要首先线下编译打包，[例子](http://shepherdwind.com/velocity/web/index.html)。

###macro

宏分为系统的宏，比如`parse, include`，和用户自定义宏，通过`#macro`在vm中定义，此
外可以使用自定义的js函数替代在vm中定义。对于系统宏和自定义宏，不做区分，对于
`#parse`和`#include`的调用，可以使用自定义函数来执行。具体见[issue #3](https://github.com/shepherdwind/velocity.js/issues/3)。

###foreach

foreach在velocity中对对象的遍历，和js有区别，java中对象是一个map，需要通过方法
`keyset`来获取map中的key，foreach循环写法等同于js的for in循环，感觉有点怪异。在
一个foreach，有一个`$foreach`的对象可以使用，此变量作用域为当前循环范围。

```
#foreach($num in [1..5])
  index => $foreach.index 
  count => $foreach.count
  #if (!$foreach.hasNext) end #end
#end
结果：
index => 0
count => 1

index => 1
count => 2
...
index => 4
count => 5
end
```

###string

velocity中字符串求值和php类似，双引号字符串里面的变量会被替换变量对应的值，单引
号原样返回，推荐尽量使用单引号，那样性能好一些。此外，双引号中变量替换，没有再次
调用语法分析器，而是使用正则，只支持简单的引用替换，比如`"$varname1 $foo.bar"`，
`"$foo.bar[1] $foo.bar()"`都不支持。如果需要完整支持字符串双引号，需要反复调用语
法分析器，考虑到性能，基本引用基本够用了，vm本身支持强大的变量赋值，可以先赋值，
在放入字符串，或者使用加法进行字符串拼接。

在java中可能大量使用双引号方式，因为java对象无法自动转换类型，双引号做类型转换用，
而在web端，js无此需要。

###velocity

```
Usage: velocity [options] [file ...]

Options:

  -h, --help         output usage information
  -V, --version      output the version number
  -b, --build        build .vm file to js ast module of kissy

Example:

  # parse vm file
  $ velocity a.vm 

  # parse vm file with json data
  $ velocity a.vm  a.json

  # build asts module of kissy
  $ velocity *.vm
```

##Helper

Helper提供一些额外的功能，主要用于解决vm数据模拟问题。

- `structure` 获取vm中所有变量的结构: `$foo.bar` => `foo: {bar: 'string'}`
- `backstep` vm逆推，根据velocity文件和解析后的结果，计算数据结构和内容
- `jsonify` 把vm转换为json结构，去除其中的html标签，比如：

jsonify文档[issue #11](https://github.com/shepherdwind/velocity.js/issues/11)

```
hello world $foo.name.
=>
{foo: { name: $foo.name }}
```

##License

(The MIT License)

Copyright (c) 2012-2013 Eward Song<eward.song@gmail.com>

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the 'Software'), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of
the Software, and to permit persons to whom the Software is furnished to do so,
subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS
FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR
COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER
IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
