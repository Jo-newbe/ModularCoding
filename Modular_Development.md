# JavaScript模块化编程

随着前端技术的发展，多数网站逐渐演变成“互联网应用程序”（Internet Applications），同时要求前端处理复杂的逻辑以及完成更多的功能，从而导致嵌入在HTML页面上的JavaScript代码就越来越多，越来越复杂了。

前端项目也更像是传统后台应用程序一样，需要多人协同开发、项目控制、单元测试等。因此JavaScript模块化开发成为一个迫切
的需求。开发者只需关注自己的核心业务功能，其他的可以加载别人已经开发好的模块即可。

但是很不幸，JS并不是一种支持模块化编程的语言，因为在JS中不支持“类class”，当然更谈不上支持“模块module”了。可喜的是在ES6中，将支持“类” 和 “模块”，只不过还需要等上一段时间呢。

经过多年的努力，JavaScript已实现在现有的环境下，实现模块化的效果。

## 原始写法

刚诞生模块化编程时，模块的定义是 实现特定功能的一组函数（方法）。

```javascript
	//	将多个不同功能的函数放在一起，完成特定功能，那么这一组函数就被称为模块。
	function mod1() {
		// mod1
	}
	function mod2() {
		// mod2
	}
```

通常将上面一组函数写在一个js文件上，这样就形成一个模块，在使用该模块时，在页面上加载该js文件，调用相应的函数即可。
但是这种写法具有很明显的缺点：a 污染全局变量以及全局对象；b 体现不出模块内函数之间的关联性

## 对象写法

后来开发人员发现原始写法的缺点后，为解决该问题。就将实现特定功能的函数都放到一个对象上，用一个对象来定义模块。

```javascript
	var module = {
		_data: 1,
		mod1: function() {},
		mod2: function() {}
	}
```

用一个对象来定义一个模块，在使用时可以通过该对象调用mod1和mod2方法即可。虽然解决了原始写法的缺点，但是也同时产新的缺点：会暴露模块内的所有成员，而且所有成员都是可读可写。外部可以随意修改。

## 立即执行函数：Immediately-Invoked Function Expression，IIFE

使用立即执行函数（通常匿名）写法，可以避免暴露一些私有的成员。这也是JavaScript模块的基本写法。但也需要改进。

```javascript
var module = (function() {
	// 私有成员
	var _data = 1;
	return {
		mod1: function() {},
		mod2: function() {}
	}
}());
```

## Augmentation（增大）模式

很多时候，一个功能需要定义多个模块来实现，那么也就存在一个模块依赖另一个模块。或者一个模块要继承其他模块。

```javascript
	// module2 模块2依赖于jQuery
	// 同时继承与模块1
	var module2 = (function(m, $) {
		m.mod3 = function() {};
		return m;
	}(module1, jQuery));
```

这种写法可以在一定程度上解决，模块之前的依赖，但是却无法保证被依赖的模块会先加载。那么就有可能导致module1为undefined值。

## 宽松的增大模式（Loose Augmentation）

如果module1没被先加载，导致值为undefined。那么就使module1参数值空对象

```javascript
	var module2 = (function(m, $) {
		m.mod3 = function() {};
		return m;
	}(window.module1 || {}, jQuery)); 
```

## 传入全局变量

为了保证模块的独立性，最好将模块内用到的全局变量，当做实参传入变量内。同时可以体现出模块之间的依赖性。

## 模块编写规范

因为有了模块，开发者可以更加方便的使用别人已完成的模块。大大的提高了开发效率。但是想想，如果定义模块没有统一的规范，每个人想怎么写就怎么写，那么加载别人的模块，就很有可能因为规范问题，导致无法使用他人的模块。那岂不是失去了模块的意义了吗！因此制定模块的编写规范是必要的。

目前，JavaScript模块规范有三种：CommonJS、AMD（Asynchronous Module Definition）和CMD（Common Module Definition）。

## CommonJS

CommonJS是为了JS的表现而制定的规范，由于早期JS没有模块的功能，因此CommonJS应运而生。但是其强大的地方是，CommonJS希望JavaScript可以在任何地方执行，不仅仅是在浏览器上。

### 概述

目前非常流行的项目NodeJS就是CommonJS规范的一种实现。

根据CommonJS规范，一个单独的文件就是一个模块，有自己的作用域。在一个文件里边定义的变量、函数、类都是私有的，对其他文件是不可见的。

```javascript
	// mod1.js
  var n = 1;
  var add = function(val) {
  	return val + n;
  };
```

在上面代码中，变量n 以及 函数add 是mod1.js文件私有，对其他文件是不可见的。
如果想在多文件中共享变量，可以使用global对象

```javascript
	global.share = 'this is a share data';
```

上面代码中，share变量就是共享的，可以被所有文件读取，但是这种做法并不推荐。

CommonJS规范规定，每个模块内部，module变量表示当前模块。该变量是一个对象，其exports属性（即module.exports）是对外的接口。在加载某个模块时，本质就是加载该模块module.exports属性。

```javascript
	var n = 1;
	var add = function(val) {
		return val + n;
	};

	module.exports.n = n;
	module.exports.add = add;
```

上述代码，通过module.exports输出变量n 以及 函数add。

require()方法用于加载指定模块。

```javascript
	var mod = require('./mod.js');
	console.log(mod.n); // 1
	console.log(mod.add(1)); // 2
```

CommonJS模块的特点如下：
	* 所有代码都运行在模块的作用域下，不会污染全局作用域（全局变量以及全局对象）
	* 模块可以多次健在加载，但只会在第一次加载时运行一次，然后将运行后的结果缓存起来，以后再加载就直接读取缓存结果，想要模块再次运行，必须清楚缓存。
	* 模块加载顺序，是按照其在代码中出现的顺序。

### module对象

NodeJS内部提供一个Module构建函数，所有模块都是Module的实例。

```javascript
	function Module(id, parent) {
		this.id = id;
		this.exports = {};
		this.parent = parent;
		// some thing else
	}
```

每一个模块内部都有一个module对象，代表当前模块。具有以下属性
	* module.id 模块的标识符，通常是带有绝对路径的模块文件名。
	* module.filename 模块的文件名，带有绝对路径。
	* module.loaded 返回一个bool值，表示该模块是否已加载完成。
	* module.parent 返回一个对象，表示调用该模块的模块。
	* module.children 返回一个数组，表示该模块要用到的其他模块。 
	* module.exports 表示模块对外输出的值。

```javascript
	// mod2.js
	var jQuery = require('jquery');
	exports.$ = jQuery;
	console.log(module);
```
上述代码，在命令行输入结果如下：

```javascript
	{ id: '.',
	  exports: { '$': [Function] },
	  parent: null,
	  filename: '/example.js',
	  loaded: false,
	  children:
	    [ { id: '/node_modules/jquery/dist/jquery.js',
	       exports: [Function],
	       parent: [Circular],
	       filename: '/node_modules/jquery/dist/jquery.js',
	       loaded: true,
	       children: [],
	       paths: [Object] 
	    } ],
	  paths: ['/node_modules' ]
	}
```

#### module.exports属性

module.exports属性表示当前模块对外输入的接口，其他文件（模块）加载该模块，实际上就是读取module.exports变量。

#### exports变量

为了方便，NodeJS为每个模块提供一个exports变量，指向module.exports。这就等同于在每个模块头部，有一行这样的代码。

```javascript
	var exports = module.exports;
```

造成的结果就是，在对外输出模块接口时，可以向exports对象添加方法。

```javascript
	exports.area = function(r) {
		return Math.PI * r * r;
	};
	exports.circumference = function(r) {
		return 2 * Math.PI * r;
	};
```

注意：不要直接给exports赋值，这样做会切断exports与module.exports之间的关联。