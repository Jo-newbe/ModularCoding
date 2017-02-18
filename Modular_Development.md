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