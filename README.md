
Airbnb Javascript Style Guide
===================
*用更合理的方式写JavaScript*

目录
-----
#####[1.类型](#a)#####

#####[2.对象](#b)#####

#####[3.数组](#c)#####

#####[4.字符串](#d)#####

#####[5.函数](#e)#####

#####[6.属性](#f)#####

#####[7.变量](#g)#####

#####[8.提升](#h)#####

#####[9.比较运算符&符号](#i)#####

#####[10.块](#j)#####

#####[11.注释](#k)#####

#####[12.空白](#l)#####

#####[13.逗号](#m)#####

#####[14.分号](#n)#####

#####[15.类型转化](#o)#####

#####[16.命名规则](#p)#####

#####[17.存取器](#q)#####

#####[18.构造函数](#r)#####

#####[19.事件](#s)#####

#####[20.模块](#t)#####

#####[21.jQuery](#u)#####

#####[22.ECMAScript5兼容性](#v)#####

#####[23.测试](#w)#####

<a name="a"><font size=5>类型</font></a>
-------------------------------------------------------

* <font size=3><h3>原始值：</h3></font>存取直接作用于它自身。
  * string
  * number
  * boolean
  * null
  * undefined

    ```js
      var foo = 1;
      var bar = foo;
      bar = 9;
      console.log(foo, bar); //=>1, 9
    ```
* <font size=3><h3>复杂类型：</h3></font>存取时作用于它自身值的引用
  * object
  * array
  * function
    ```js
      var foo = [1, 2];
      var bar = foo;
      bar[0] = 9;
      console.log(foo[0], bar[0]); //=>9,  9（传递的是指针的地址，并不是真正的赋值）
    ```

<a name="b"><font size=5>对象</font></a>
-------------------------------------------------------
* 使用直接量创建对象
```js
  //bad
  var item = new Object();

  //good
  var item = {};
```
* 不要使用[保留字](http://es5.github.io/#x7.6.1 "保留字")作为键名，他们在IE8下不工作。[更多信息](https://github.com/airbnb/javascript/issues/61 "更多信息")。

```js
  //bad
  var superman = {
    default: { clark: 'kent' },
    private: true
  };

  //good
  var superman = {
    defaults: { clark: 'kent' },
    hidden: true
  };
```
* 使用同义词替换需要使用的保留字。

```js
  //bad
  var superman = {
    class: 'alien'
  };

  //bad
  var superman = {
  klass: 'alien'
  };

  //good
  var superman = {
    type: 'alien'
  };
```

<a name="c"><font size=5>数组</font></a>
-------------------------------------------------------
* 使用直接量创建数组
```js
  //bad
  var items = new Array();

  //good
  var items = [];
```
* 向数组增加元素使用Array#push来替代直接赋值。
```js
  var someStack = [];

  //bad
  someStack[someStack.length] = 'abracadabra';

  //good
  someStack.push('abracadabra');
```
* 当你需要拷贝数据时，使用Array#slice。[jsPerf](https://jsperf.com/converting-arguments-to-an-array/7 "jsPerf")
```js
  var len = items.length;
  var itemsCopy = [];
  var i;

  //bad
  for(var i = 0; i<len;i++){
    itemsCopy[i] = items[i];
  }

  //good
  itemsCopy = items.slice();
```
* 使用Array#slice将类数组对象转换成数组。[类数组对象介绍](http://www.jb51.net/article/59169.htm "类数组对象介绍")
```js
  function trigger() {
    var args = Array.prototype.slice.call(arguments);
    ...
  }
```

<a name="d"><font size=5>字符串</font></a>
---------------------------------------------------------
* 使用单引号''包裹字符串
```js
  //bad
  var name = "Bob Parr";

  //good
  var name = 'Bob Parr';

  //bad
  var fullName = "Bob " + this.lastName;

  //good
  var fullName = 'Bob ' + this.lastName;
```
* 超过100个字符的字符串应该使用连字符写成多行
* 注：若过度使用，通过连字符链接的长字符串可能影响性能。[jsPerf](https://jsperf.com/ya-string-concat "英文的")&[讨论](https://github.com/airbnb/javascript/issues/40 "又是英文的")

```js
  // bad
  var errorMessage = 'This is a super long error that was thrown because of Batman. When you stop to think about how Batman had anything to do with this, you would get nowhere fast.';

  // bad
  var errorMessage = 'This is a super long error that was thrown because \
  of Batman. When you stop to think about how Batman had anything to do \
  with this, you would get nowhere \
  fast.';

  // good
  var errorMessage = 'This is a super long error that was thrown because ' +
    'of Batman. When you stop to think about how Batman had anything to do ' +
    'with this, you would get nowhere fast.';
```

* 程序化生成的字符串使用Array#join连接而不是使用连字符。尤其是IE下：[jsPerf](https://jsperf.com/string-vs-array-concat/2 "详解")。

```js
  var items;
  var messages;
  var length;
  var i;

  messages = [{
    state: 'success',
    message: 'This one worked.'
  }, {
    state: 'success',
    message: 'This one worked as well.'
  },{
    state: 'error',
    message: 'This one did not work.'
  }];

  length = message.length;

  //bad
  function inbox(messages) {
    items = '<ul>';

    for (i = 0; i< length; i++){
      items += '<li>' + message[i].message + '</li>';
    }

    return items + '</ul>';
  }

  //good
  function inbox(messages) {
    item = [];

    for (i = 0; i< length; i++) {
      //use dirsct assignment in this case beause we`re micro-optimizing
      item[i] = '<li>' + message[i].message + '</li>';
    }

    return '<ul>' + items.join('') + '</ul>';
  }
  //join方法接受一个可选参数，作为连接(分隔符)
```
<a name="e"><font size=5>函数</font></a>
---------------------------------------------------

* 函数表达式：

```js
  //匿名函数表达式
  var anonymous = function() {
    return true;
  };

  //命名函数表达式
  var named = function named() {
    return true;
  };

  //立即调用的函数表达式（IIFE）
  (function() {
    console.log('Welcome to the Internet. Please follow me.');
  } ());
```
* 永远不要在一个非函数代码块（if、while等）声明一个函数，把那个函数赋值给一个变量。浏览器允许你这么做，但它们的解析表现不一致。
* 注：ECMA-262把块定义为一组语句。函数声明不是语句。[阅读对ECMA-262这个问题的说明](http://www.ecma-international.org/publications/files/ECMA-ST/Ecma-262.pdf#page=97 "PDF")。

```js
  // bad
  if (currentUser) {
    function test() {
      console.log('Nope.');
    }
  }

  // good
  var test;
  if (currentUser) {
    test = function test() {
      console.log('Yup');
    };
  }
```

* 永远不要把参数命名成arguments。这将取代函数作用于内的arguments对象。

```js
  // bad
  function nope(name, options, arguments) {
    // ... stuff...
  }
```

<a name="f"><font size=5>属性</font></a>
----------------------------------------------------

* 使用 . 来访问对象的属性。
```js
  var luke = {
    jedi: true,
    age: 28
  };

  // bad
  var isJedi = luke['jedi'];

  // good
  var isJedi = like.jedi;
```
* 当通过变量访问属性时使用中括号 []。

```js
  var luke = {
    jedi: true,
    age: 28
  };

  function getProp(prop) {
    return luke[prop];
  }

  var isJedi = getProp('jsdi');
```
<a name="g"><font size=5>变量</font></a>
---------------------------------------------------
* 总是使用 var 来声明变量。不这么做将导致产生全局变量。我们要避免污染全局的命名空间。

```js
  // bad
  superPower = new superPower();

  //good
  var superPower - new superPower();
```

* 使用 var 声明没一个变量。这样做的好处是增加心凉凉将变得更容易，而且你永远不用担心调换错 ； 根 ， 。

```js
  // bad
  var items = getItems(),
      goSportsTeam = true,
      dragonball = 'z';

  // bad
  // （跟上面的代码比较一下，看看哪里错了）
  var items = getItems(),
      goSportsTeam = true;
      dragonball = 'z';

  // good
  var items = getItems();
  var goSportsTeam = true;
  var dragonball = 'z';
```

* 最后再声明为赋值的变量。当你需要引入前面的变量赋值时这将変的很有用。

```js
  // bad
  var i, len, dragonball,
      items = getItems(),
      goSportsTeam = true;

  // bad
  var i;
  var items = getItems();
  var dragonball;
  var goSportsTeam = true;
  var len;

  // good
  var items = getItems();
  var goSportsTeam = true;
  var dragonball;
  var length;
  var i;
```
* 在作用域顶部声明变量。这将帮你避免变量声明提升相关问题。

```js
  // bad
  function () {
    test();
    console.log('doing stuff..');

    //..other stuff..

    var name = getName();

    if (name === 'test') {
      return false;
    }

    return name;
  }

  // good
  function () {
    var name = getName();

    test();
    console.log('doing stuff..');

    //..other stuff..

    if (name === 'test') {
      return false;
    }

    return name;
  }

  // bad - 不必要的函数调用
  function () {
    var name = getName();

    if (!arguments.length) {
      return false;
    }

    this.setFirstName(name);

    return true;
  }

  // good
  function () {
    var name;

    if (!arguments.length) {
      return false;
    }

    name = getName();
    this.setFirstName(name);

    return true;
  }
```

<a name="h"><font size=5>提升</font></a>
----------------------------------------------------
* 变量声明会提升至作用域顶部，但不会赋值。

```js
  // 我们知道这样不能正常工作（假设这里没有名为 notDefined 的全局变量）
  function example() {
    console.log(notDefined); // => throws a ReferenceError
  }

  // 但由于变量声明提升的原因，在一个变量引用后再创建它的变量声明将可以正常工作。
  // 注：变量赋值为 `true` 不会提升。
  function example() {
    console.log(declaredButNotAssigned); // => undefined
    var declaredButNotAssigned = true;
  }

  // 解释器会把变量声明提升到作用域顶部，意味着我们的例子将被重写成：
  function example() {
    var declaredButNotAssigned;
    console.log(declaredButNotAssigned); // => undefined
    declaredButNotAssigned = true;
  }
```

* 匿名函数表达式会提升他们的变量名，但不会提升函数的赋值。

```js
  function example() {
    console.log(anonymous); // => undefined

    anonymous(); // => TypeError anonymous is not a function

    var anonymous = function () {
      console.log('anonymous function expression');
    };
  }
```
* 命名表达式会提升变量名，但不会提升函数名和函数体。

```js
  function example() {
    console.log(named); // => undefined

    named(); // => TypeError named is not a function

    superPower(); // => ReferenceError superPower is not defined

    var named = function superPower() {
      console.log('Flying');
    };
  }

  // 当函数名跟变量名一样时，表现也是如此。
  function example() {
    console.log(named); // => undefined

    named(); // => TypeError named is not a function

    var named = function named() {
      console.log('named');
    }
  }
```
* 函数声明提升他们的名字和函数体。

```js
  function example() {
    superPower(); // => Flying

    function superPower() {
      console.log('Flying');
    }
  }
```
* 了解更多信息在 [Javascript Scoping & Hosting ](http://www.adequatelygood.com/JavaScript-Scoping-and-Hoisting.html "English") by [Ben Cherry](http://www.adequatelygood.com/ "English")。

<a name="i"><font size=5>比较运算符&等号</font></a>
------------------------------------------------------------------

* 优先使用 === 和 ！== 而不是 == 和 ！=
* 条件表达式例如 if 语句通过抽象方法 ToBoolean 强制计算他们的表达式并且总是遵循下面的规则：
  * 对象被计算为true
  * Undefined被计算为false
  * Null被计算为false
  * 布尔值被计算为布尔值
  * 数字如果是+0、-0或NaN被计算为false，否则为true
  * 字符串如果是空字符串‘’被计算为false，否则为true
```js
  if([0]) {
    // true
    // 一个数组就是一个对象，
  }
```

* 使用快捷键。

```js
  // bad
  if (name !== '') {
    // ...stuff...
  }

  // good
  if (name) {
  }

  // bad
  if (collection.length > 0) {
    // ...stuff...
  }

  // good
  if (collection.length) {
    // ...stuff...
  }
```
* 了解更多信息在[ Truth Equality and JavaScript](http://javascriptweblog.wordpress.com/2011/02/07/truth-equality-and-javascript/#more-2108 "English")by Angus Croll。

<a name="j"><font size=5>块</font></a>
-------------------------------------------------

* 使用大括号包裹所有的多行代码块。

```js
  // bad
  if (test)
    return false;

  // good
  if (test) return false;

  // good
  if (test) {
    return false;
  }

  // bad
  function () { return false; }

  // good
  function () {
    return false;
  }
```

* 如果通过 if 和 else 使用多行代码， 把 else 放在 if代码块关闭括号的同一行。

```js
  // bad
  if (test) {
    thing1();
    thing2();
  }
  else {
    thing3();
  }

  // good
  if (test) {
    thing1();
    thing2();
  } else {
    thing3();
  }
```

<a name="k"><font size=5>注释</font></a>
---------------------------------------------------

* 使用 /**.......*/作为多行注释。包含描述、指定所有参数和返回值的类型和值。

```js
  // bad
  // make() returns a new element
  // based on the passed in tag name
  //
  // @param {String} tag
  // @return {Element} element
  function make(tag) {

    // ...stuff...

    return element;
  }

  // good
  /**
   * make() returns a new element
   * based on the passed in tag name
   *
   * @param {String} tag
   * @return {Element} element
   */
  function make(tag) {

    // ...stuff...

    return element;
  }
```

* 使用 // 作为单行注释。在评论对象上面另起一行使用单行注释。在注释前面插入空行。

```js
  // bad
  var active = true;  // is current tab

  // good
  // is current tab
  var active = true;

  // bad
  function getType() {
    console.log('fetching type...');
    // set the default type to 'no type'
    var type = this.type || 'no type';

    return type;
  }

  // good
  function getType() {
    console.log('fetching type...');

    // set the default type to 'no type'
    var type = this.type || 'no type';

    return type;
  }
```
* 给注释增加 FIXME 或 TODO 的前缀可以帮助其他开发者快速了解这是一个需要复查的问题，或者给需要实现的功能提供一个解决方式。这将有别于常见的注释，因为他们是可操作的。使用 FIXME - need to figure this out 或者 TODO -- need to implement 。
* 使用 // FIXME: 标注问题

```js
  function Calculator() {

    // FIXME: shouldn't use a global here
    total = 0;

    return this;
  }
```
* 使用 // TODO: 标注问题的解决方式。

```js
  function Calculator() {

    // TODO: total should be configurable by an options param
    this.total = 0;

    return this;
  }
```
<a name="l"><font size=5>空白</font></a>
---------------------------------------------------
* 使用2个空格作为缩进
```js
  // bad
  function () {
  ∙∙∙∙var name;
  }

  // bad
  function () {
  ∙var name;
  }

  // good
  function () {
  ∙∙var name;
  }
```

* 在大括号前面放一个空格

```js
  // bad
  function test(){
    console.log('test');
  }

  // good
  function test() {
    console.log('test');
  }

  // bad
  dog.set('attr',{
    age: '1 year',
    breed: 'Bernese Mountain Dog'
  });

  // good
  dog.set('attr', {
    age: '1 year',
    breed: 'Bernese Mountain Dog'
  });
```

* 在控制语句（if、while等）的小括号前面放一个空格。在函数调用及生命中，不在函数的参数列表前加空格。

```js
  // bad
  if(isJedi) {
    fight ();
  }

  // good
  if (isJedi) {
    fight();
  }

  // bad
  function fight () {
    console.log ('Swooosh!');
  }

  // good
  function fight() {
    console.log('Swooosh!');
  }
```

* 使用空格把运算符隔开

```js
  // bad
  var x=y+5;

  // good
  var x = y + 5;
```

* 在文件末尾插入一个空行。

```js
  // bad
  (function (global) {
    // ...stuff...
  })(this);
```
```js
  // good
  (function (global) {
    // ...stuff...
  })(this);↵
```

* 在使用长方法链时进行缩进。使用前面的点 . 强调这是方法调用而不是新语句。

```js
  // bad
  $('#items').find('.selected').highlight().end().find('.open').updateCount();

  // bad
  $('#items').
    find('.selected').
      highlight().
      end().
    find('.open').
      updateCount();

  // good
  $('#items')
    .find('.selected')
      .highlight()
      .end()
    .find('.open')
      .updateCount();

  // bad
  var leds = stage.selectAll('.led').data(data).enter().append('svg:svg').classed('led', true)
      .attr('width', (radius + margin) * 2).append('svg:g')
      .attr('transform', 'translate(' + (radius + margin) + ',' + (radius + margin) + ')')
      .call(tron.led);

  // good
  var leds = stage.selectAll('.led')
      .data(data)
    .enter().append('svg:svg')
      .classed('led', true)
      .attr('width', (radius + margin) * 2)
    .append('svg:g')
      .attr('transform', 'translate(' + (radius + margin) + ',' + (radius + margin) + ')')
      .call(tron.led);
```

* 在块末和信语句前插入空行。

```js
  // bad
  if (foo) {
    return bar;
  }
  return baz;

  // good
  if (foo) {
    return bar;
  }

  return baz;

  // bad
  var obj = {
    foo: function () {
    },
    bar: function () {
    }
  };
  return obj;

  // good
  var obj = {
    foo: function () {
    },

    bar: function () {
    }
  };

  return obj;
```

<a name="m"><font size=5>逗号</font></a>
-----------------------------------------------------

* 行首逗号：不需要。

```js
  // bad
  var story = [
      once
    , upon
    , aTime
  ];

  // good
  var story = [
    once,
    upon,
    aTime
  ];

  // bad
  var hero = {
      firstName: 'Bob'
    , lastName: 'Parr'
    , heroName: 'Mr. Incredible'
    , superPower: 'strength'
  };

  // good
  var hero = {
    firstName: 'Bob',
    lastName: 'Parr',
    heroName: 'Mr. Incredible',
    superPower: 'strength'
  };
```
* 额外的行末逗号：不需要。这样做会在IE6/IE7和IE9怪异模式下引起问题。同样，多余的逗号在某些ES3的实现里会增加数组的长度。在ES5中已经澄清（[source](http://es5.github.io/#D "English")）：

>Edition 5 clarifies the fact that a trailing comma at the end of an ArrayInitialiser does not add to the length of the array. This is not a semantic change from Edition 3 but some implementations may have previously misinterpreted this.

```js
  // bad
    var hero = {
      firstName: 'Kevin',
      lastName: 'Flynn',
    };

    var heroes = [
      'Batman',
      'Superman',
    ];

    // good
    var hero = {
      firstName: 'Kevin',
      lastName: 'Flynn'
    };

    var heroes = [
      'Batman',
      'Superman'
    ];
```

<a name="n"><font size=5>分号</font></a>
-----------------------------------------------------

* 使用分号

```js
  // bad
  (function () {
    var name = 'Skywalker'
    return name
  })()

  // good
  (function () {
    var name = 'Skywalker';
    return name;
  })();

  // good (防止函数在两个 IIFE 合并时被当成一个参数
  ;(function () {
    var name = 'Skywalker';
    return name;
  })();
```

[了解更多](http://stackoverflow.com/questions/7365172/semicolon-before-self-invoking-function/7365214#7365214)

<a name="o"><font size=5>分号</font></a>
-------------------------------------------------------
* 在语句开始时执行类型转换。
* 字符串

```js
  // =>this.reviewScore = 9;

  // bad
  var totalScore = this.reviewScore + '';

  // good
  var totalScore = '' + this.reviewScore;

  // bad
  var totalScore = '' + this.reviewScore + 'total score';

  // good
  var totalScore = this.reviewScore + 'total score';
```

* 使用parseInt转换数字时总是带上类型转换的基数。

```js
  var inputValue = '4';

  // bad
  var val = new Number(inputValue);

  // bad
  var val = +inputValue;

  // bad
  var val = inputValue >> 0;

  // bad
  var val = parseInt(inputValue);

  // good
  var val = Number(inputValue);

  // good
  var val = parseInt(inputValue, 10);
```

* 如果因为某些原因paeseInt成为你所做的事情的瓶颈而需要使用位操作解决[性能问题](http://jsperf.com/coercion-vs-casting/3)时，留个注释说清楚原因和你的目的。

```js
  // good
  /**
   * parseInt was the reason my code was slow.
   * Bitshifting the String to coerce it to a
   * Number made it a lot faster.
   */
  var val = inputValue >> 0;
```

* 注：小心使用位操作运算符。数字没被当成[64位值](http://es5.github.io/#x4.3.19)，但是位操作运算符总是返回32位的证书（[source](http://es5.github.io/#x11.7)）。位操作处理大于32位的整数值时还会导致意料之外的行为。[讨论](https://github.com/airbnb/javascript/issues/109)。最大的32位整数是2147483647：

```js
  2147483647 >> 0 //=> 2147483647
  2147483648 >> 0 //=> -2147483648
  2147483649 >> 0 //=> -2147483647
```

* 布尔：
```js
  var age = 0;

  // bad
  var hasAge = new Boolean(age);

  // good
  var hasAge = Boolean(age);

  // good
  var hasAge = !!age;
```

<a name="p"><font size=5>命名规则</font></a>
-------------------------------------------------------

* 避免单字母明明。明明因具备描述性。

```javascript
  // bad
  function q() {
    // ...stuff...
  }

  // good
  function query() {
    // ..stuff..
  }
```

* 使用驼峰式命名对象/函数和实例。

```javascript
  // bad
  var OBJEcttsssss = {};
  var this_is_my_object = {};
  var o = {};
  function c() {}

  // good
  var thisIsMyObject = {};
  function thisIsMyFunction() {}
```

* 使用帕斯卡式命名构造函数或类

```javascript
  // bad
  function user(options) {
    this.name = options.name;
  }

  var bad = new user({
    name: 'nope'
  });

  // good
  function User(options) {
    this.name = options.name;
  }

  var good = new User({
    name: 'yup'
  });
```

* 不要使用下划线前/后缀。
>为什么？javascript并没有私有属性或私有方法的概念。虽然使用下划线式表示‘私有的一种共识’，但实际上这些属性完全公开，它本身就是你公共接口的一部分。这种习惯或许会导致开发者错误的认为改动它不会造成麻烦或破坏活着不需要测试。长话短说：如果你想要某处位‘私有’，它必须不能显示提出的。

```javascript
   // bad
    this.__firstName__ = 'Panda';
    this.firstName_ = 'Panda';
    this._firstName = 'Panda';

    // good
    this.firstName = 'Panda';
```

* 不要保存 this 的引用。使用Function#bind。

```javascript
  // bad
  function () {
    var self = this;
    return function () {
      console.log(self);
    };
  }

  // bad
  function () {
    var that = this;
    return function () {
      console.log(that);
    };
  }

  // bad
  function () {
    var _this = this;
    return function () {
      console.log(_this);
    };
  }

  // good
  function () {
    return function () {
      console.log(this);
    }.bind(this);
  }
```

* 给函数命名。这在做堆栈轨迹时很有帮助。

```javascript
  // bad
  var log = function (msg) {
    console.log(msg);
  };

  // good
  var log = function log(msg) {
    console.log(msg);
  };
```

* 注： IE8及以下版本对命名函数表达式的处理有些怪异。了解更多信息到[http://kangax.github.io/nfe/](http://kangax.github.io/nfe/)。
* 如果你的文件导出一个类，你的文件名应该与类名完全相同。

```javascript
  // file contents
  class CheckBox {
    // ...
  }
  module.exports = CheckBox;

  // in some other file
  // bad
  var CheckBox = require('./checkBox');

  // bad
  var CheckBox = require('./check_box');

  // good
  var CheckBox = require('./CheckBox');
```

<a name="q"><font size=5>存取器</font></a>
-------------------------------------------------------

* 属性的存取函数不是必须的。
* 如果你需要存取函数时使用 getVal() 和 setVal('hello')。

```javascript
  // bad
  dragon.age();

  // good
  dragon.getAge();

  // bad
  dragon.age(25);

  // good
  dragon.setAge(25);
```

* 如果属性时布尔值，使用isVal()或hasVal()。

```javascript
  // bad
  if (!dragon.age()) {
    return false;
  }

  // good
  if (!dragon.hasAge()) {
    return false;
  }
```

* 创建 get() 和 set() 函数是可疑的，但要保持一致。

```javascript
  function Jedi(options) {
    options || (options = {});
    var lightsaber = options.lightsaber || 'blue';
    this.set('lightsaber', lightsaber);
  }

  Jedi.prototype.set = function set(key, val) {
    this[key] = val;
  };

  Jedi.prototype.get = function get(key) {
    return this[key];
  };
```

<a name="r"><font size=5>构造函数</font></a>
-------------------------------------------------------

* 给对象原型分配方法，而不是使用一个新对象覆盖原型。覆盖原型将导致继承出现问题：重设原型将覆盖原有原型！

```javascript
  function Jedi() {
    console.log('new jedi');
  }

  // bad
  Jedi.prototype = {
    fight: function fight() {
      console.log('fighting');
    },

    block: function block() {
      console.log('blocking');
    }
  };

  // good
  Jedi.prototype.fight = function fight() {
    console.log('fighting');
  };

  Jedi.prototype.block = function block() {
    console.log('blocking');
  };
```

* 方法可以返回 this 来实现方法的链式调用。

```javascript
  // bad
  Jedi.prototype.jump = function jump() {
    this.jumping = true;
    return true;
  };

  Jedi.prototype.setHeight = function setHeight(height) {
    this.height = height;
  };

  var luke = new Jedi();
  luke.jump(); // => true
  luke.setHeight(20); // => undefined

  // good
  Jedi.prototype.jump = function jump() {
    this.jumping = true;
    return this;
  };

  Jedi.prototype.setHeight = function setHeight(height) {
    this.height = height;
    return this;
  };

  var luke = new Jedi();

  luke.jump()
    .setHeight(20);
```

* 写一个自动逸的 toString() 方法是可以的，但是确保它可以正常工作且不会产生副作用。

```jsvascript
  function Jedi(options) {
    options || (options = {});
    this.name = options.name || 'no name';
  }

  Jedi.prototype.getName = function getName() {
    return this.name;
  };

  Jedi.prototype.toString = function toString() {
    return 'Jedi - ' + this.getName();
  };
```

<a name="s"><font size=5>事件</font></a>
-------------------------------------------------------

* 挡给事件附加数据时（无论时DOM事件还是私有事件），传入一个哈希而不是原始值。这样可以让后面的贡献者增加更多的数据到事件数据而无需找出并更新事件的每一个处理器。例如，不好的写法：

```javascript
  // bad
  $(this).trigger('listingUpdated', listing.id);

  ...

  $(this).on('listingUpdated', function (e, listingId) {
    // do something with listingId
  });
```

更好的写法：

```javascript
  // good
  $(this).trigger('listingUpdated', { listingId : listing.id });

  ...

  $(this).on('listingUpdated', function (e, data) {
    // do something with data.listingId
  });
```

<a name="t"><font size=5>模块</font></a>
-------------------------------------------------------

* 模块应用以 ！ 开始。这样确保了挡一个不好的模块忘记包含最厚的分还时，在合并代码到生产环境后不会产生错误。[详细说明](https://github.com/airbnb/javascript/issues/44#issuecomment-13063933)。
* 文件应该以驼峰式命名，并放在同名的文件夹里，且与导出的名字一直。
* 增加一个名为noConfict() 的方法来设置导出的模块为前一个版本并返回它。
* 永远在模块顶部生命 'use strict';。

```javascript
  // fancyInput/fancyInput.js

  !function (global) {
    'use strict';

    var previousFancyInput = global.FancyInput;

    function FancyInput(options) {
      this.options = options || {};
    }

    FancyInput.noConflict = function noConflict() {
      global.FancyInput = previousFancyInput;
      return FancyInput;
    };

    global.FancyInput = FancyInput;
  }(this);
```

<a name="u"><font size=5>jQuery</font></a>
-------------------------------------------------------

* 使用$ 作为存储jQuery对象的变量名前缀。

```javascript
  // bad
  var sidebar = $('.sidebar');

  // good
  var $sidebar = $('.sidebar');
```

* 缓存Jquery 查询。
```javascript
  // bad
  function setSidebar() {
    $('.sidebar').hide();

    // ...stuff...

    $('.sidebar').css({
      'background-color': 'pink'
    });
  }

  // good
  function setSidebar() {
    var $sidebar = $('.sidebar');
    $sidebar.hide();

    // ...stuff...

    $sidebar.css({
      'background-color': 'pink'
    });
  }
```

* 对DOM查询使用层叠\$('.sliderbar ul') 或父元素\> 子元素 $('.sliderbar ul')。[jsPerf](https://jsperf.com/jquery-find-vs-context-sel/16)
* 对作用域的jQuery对象查询使用find。

```javascript
  // bad
  $('ul', '.sidebar').hide();

  // bad
  $('.sidebar').find('ul').hide();

  // good
  $('.sidebar ul').hide();

  // good
  $('.sidebar > ul').hide();

  // good
  $sidebar.find('ul').hide();
```

<a name="v"><font size=5>ECMAScript 5 兼容性</font></a>
-------------------------------------------------------

* 参考Kangax的ES5[兼容表](http://kangax.github.io/compat-table/es5/)。

<a name="w"><font size=5>测试</font></a>

* Yup

```javascript
  function () {
    return true;
  }
```


