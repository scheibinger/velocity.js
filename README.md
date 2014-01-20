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

context

`context` is an object that can be empty, running `$foo.bar` , the access path is `context.foo.bar`,

`context` attributes can be functions, and define vm consistent.

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

The difference is that asts acquisition, the first approach, direct access asts, second, you need to first perform parsing process.

##Syntax

Concrete syntax, please visit the official website document: [velocity user guide](http://velocity.apache.org/engine/devel/user-guide.html)。

###Directives

Directives support `set`, `foreach`, `if|else|elseif`, `macro`, `parse`, `break`。The following directives are not supported，`stop`, `evaluate`, `define`，feeling the syntax more biased, less useful, there is no implementation. Which `parse`， the web side, use kissy module loader loads, you need to compile the line package, examples .

###macro

Acer Acer into the system, such as `parse`, `include` , and user-defined macros by `# macro` is defined in vm, in addition you can use a custom js function instead defined in the vm. For system macros and custom macros, no distinction, for the # `parse` and # `include` calls, you can use a custom function to perform. See specific.[issue #3](https://github.com/shepherdwind/velocity.js/issues/3)。

###foreach

foreach in the velocity of the object traversal, and js differentiated, java objects in a map, through methods `keyset` to get the map of the key, foreach loop wording identical to the js for in the cycle, I feel a little weird. In a foreach, there is a `$ foreach` object can be used, this variable is scoped to the current cycle range.

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

velocity evaluated and php string similar variables inside double-quoted string value of the variable will be replaced with the corresponding single quotes as it returns, it is recommended to make use of single quotation marks, as good as some of the properties. In addition, variable substitution in double quotes, the parser does not call again, but the use of regular, replace only support simple references, such as `"$ varname1 $ foo.bar"` , `"$ foo.bar [1] $ foo.bar ( ) "` is not supported. If you need the full support of the string in double quotes, you need to repeatedly call the parser, taking into account the performance of the basic references basic enough, vm itself supports powerful variable assignment, you can first assignment in put string, or use the addition for character string stitching.

In a lot of java may use double quotes way because not automatically convert java object type, use double quotes do type conversion, and in the web client, js no such need.

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

Helper provides some additional features, mainly used to solve the problem vm simulation data.

- `structure` to get all the variables vm structures: `$ foo.bar` => `foo: {Bar: 'String'}`
- `backstep` VM backstepping, according to the results of velocity documents and parsed to calculate the data structure and content
- `jsonify` the vm converted to json structure, removing one of the html tag, for example:

jsonify document [issue #11](https://github.com/shepherdwind/velocity.js/issues/11)

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
