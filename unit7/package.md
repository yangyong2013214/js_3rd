## 函数表达式


### 递归
	递归函数是在一个函数通过名字调用自身的情况下构成的。在严格模式下，不能通过脚本访问arguments.call，访问这个属性会导致错误。
	var factorial =(function f(num){
	    if(num <= 1) {
	        return 1;
	    }else{
	        return num * f(num - 1);
	    }
	});
	以上代码创建了一个名为f()的命名函数表达式，然后把它赋值给变量factorial。解决递归调用问题。
### 闭包
	闭包是指有权访问另一个函数作用域中的变量的函数。
	变量对象，每个执行环境都有一个表示变量的对象。
	作用域链本质上是一个指向变量对象的指针列表，它只引用但不实际包含变量对象。
	无论什么时候在函数中访问一个变量时，就会从作用域链中搜索具有相应名字的变量。
##### 闭包与变量
	作用域链的这种配置机制引出了一个值的注意的副作用，即闭包只能取得包含函数中任何变量的最后一个值。别忘了闭包所保存的是整个变量对象，而不是某个特殊的变量。
##### 关于this对象
	在全局函数中，this等于window，而当函数被作为某个对象的方法调用时，this等于那个对象，不过，匿名函数的执行环境具有全局性，因此其this对象通常指向window。
##### 内存泄露
	由于IE9之前的版本对JScript对象和COM对象使用不同的垃圾收集例程，因此闭包在IE的这些版本中会导致一些特殊的问题。具体来说，如果闭包的作用域链中保存着一个HTML元素，那么就意味着该元素将无法被销毁。如下面的例子：
	function assignHandler(){
	  var element = document.getElementById('someElement');
	  element.onclick = function(){
	    alert(element.id);
	  }
	}
	以上代码创建了一个作为element元素事件处理程序的闭包，而这个闭包则又创建了一个循环引用。由于匿名函数保存了一个队assignHandler()的活动对象的引用，因此就会导致无法减少element的引用数。只要匿名函数存在，element的引用数至少也是1，因此它所占用的内存就永远不会被回收。
	必须要记住：闭包会引用包含函数的整个活动对象，而其中包含着element。即使闭包不直接引用element，包含函数的活动对象中也仍然会保存一个引用。因此，有必要把element变量设置为null。这样就能解除对DOM对象引用，顺利地减少其引用数，确保正常回收其占用的内存。

### 模仿块级作用域
	javascript没有块级作用域的概念。javascript从来不会拨错多重声明同一个变量；遇到这种情况，它只会对后续的声明视而不见。匿名函数可以用来模仿块级作用域并避免这个问题。用作块级作用的匿名函数的语法如下：
	(function(){
	  //这里是块级作用域
	})();
	这种技术经常在全局作用域中被用在函数外部，从而限制向全局作用域中添加过多的变量和函数。一般来说，我们都应该尽量少向全局作用域中添加变量和函数。在一个由很多开发人员共同参与的大型应用程序中，过多的全局变量和函数很容易导致命名冲突。而通过创建私有作用域，每个开发人员既可以使用自己的变量，又不必担心搞乱全局作用域。

### 私有变量
	严格来讲，javascript中没有私有成员的概念；所有对象属性都是公有的。不过，倒是有一个私有变量的概念。任何在函数中定义的变量，都可以认为是私有变量，因为不能在函数的外部访问这些变量。
	如果在这个函数内部创建一个闭包，那么闭包通过自己的作用域链也可以访问这些变量。而利用这一点，就可以创建用于访问私有变量的公有方法。
	我们把有权访问私有变量和私有函数的公有方法称为特权方法（privileged method）。有两种在对象上创建特权方法的方式，第一种是在构造函数中定义特权方法，这个模式在构造内部定义了所有私有变量和函数。利用私有和特权成员，可以隐藏那些不应该被直接修改的数据。不过，在构造函数中定义特权方法有一个缺点，那就是你必须使用构造函数模式来达到这个目的。构造函数模式的缺点是针对每个实例都会创建同样一组新方法，而使用静态私有变量来实现特权方法就可以避免这个问题。
##### 静态私有变量
	通过在私有作用域中定义私有变量或函数，同样也可以创建特权方法，这个模式创建了一个私有作用域，并在其中封装了一个构造函数及相应的方法。在私有作用域中，首先定义了私有变量和私有函数，然后又定义了构造函数及其公有方法。公有方法是在原型上定义的。这一点体现了典型的原型模式。需要注意的是，这个模式在定义构造函数时并没有使用函数声明，而是使用了函数表达式。函数声明只能创建局部函数，但那并不是我们想要的。出于同样的原因，我们也没有在声明MyObject时使用var关键字。
##### 模块模式
	前面的模式是用于为自定义类型创建私有变量和特权方法的。而道格拉斯所说 的模块模式（module pattern）则是为单例创建私有变量和特权方法。所谓单例（singleton），指的就是只有一个实例的对象。这个模块模式使用了一个返回对象的匿名函数。在这个匿名函数内部，首先定义了私有变量和函数，然后，将一个对象字面量作为函数的值返回。返回的对象字母量中只包含可以公开的属性和方法。由于这个对象是在匿名函数内部定义的，因此它的公有方法有权访问私有变量和函数。从本质上来讲，这个对象字面量定义的是单例的公共接口。这种模式在需要对单例进行某些初始化，同时又需要维护其使用变量是是非常有用的。

​	

​	