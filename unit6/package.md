## 面向对象的程序设计 
	ECMA-262把对象定义为:"无序属性的集合，其属性可以包含基本值、对象或者函数"。对象的每个属性或方法都有一个名字，而每个名字都映射到一个值。正因为这样，我们可以把ECMAScript的对象想象成散列表：无非就是一组名值对，其中值可以是数据或函数。
### 理解对象
##### 属性类型
	ECMAScript中有两种属性：数据属性和访问器属性
1. 数据属性
  数据属性包含一个数据值的位置。数据属性有4个描述其行为的特性
  + Configurable:表示能否通过delete删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为访问器属性。默认为true
  + Enumerable:表示能否通过for-in循环遍历属性。默认为true
  + Writable:表示能否修改属性的值。默认为true
  + value:包含这个属性的数据值。
    直接在对象上定义的属性，它的Configurable、 Enumerable和Writable特性都被设置为true，而value特性被设置为制定的值。
    在调用Object.defineProperty（）方法时，如果不指定Configurable、 Enumerable和Writable特性的默认值都是false.
2. 访问器属性
  访问器属性不包含数据值，它们包含一对儿getter和setter函数（不过，这两个函数都不是必需的）。在读取访问器属性是，会调用getter函数，这个函数负责返回有效的值；在写入访问器属性时，会调用setter函数并传入新值，这个函数负责决定如何处理数据。访问器属性有4个特性
  + Configurable:表示能否通过delete删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为访问器属性。默认为true
  + Enumerable:表示能否通过for-in循环遍历属性。默认为true
  + Get:在读取属性时调用的函数。默认为undefined
  + Set:在写入属性时调用的函数。默认为undefined

##### 定义多个属性
	ECMAScript5又定义了一个Object.defineProperties（）方法，利用这个方法可以通过描述符一次定义多个属性。这个方法接收两个对象参数：第一个对象是要添加和修改其属性的对象，第二个对象的属性与第一个对象中要添加或修改的属性一一对应。

##### 读取属性的特性
	使用ECMAScript5 的Object.getOwnPropertyDescriptor()方法，可以取得给定属性的描述符。这个方法接收两个参数：属性所在的对象和要读取其描述符的属性名称。
### 创建对象

##### 工厂模式
	工厂模式是软件工程领域一种广为人知的设计模式，这种模式抽象了创建具体对象的过程。考虑到ECMAScript中无法创建类，开发人员就发明了一种函数，用函数来封装以特定接口创建对象的细节。
	工厂模式虽然解决了创建多个相似对象的问题，但却没有解决对象的识别的问题（即怎样知道一个对象的类型）。随着javascript的发展，又一个新模式出现了。

##### 构造函数模式
	ECMAScript中的构造函数可用来创建特定类型的对象。像Object和Array这样的原生构造函数，在运行时会出现在执行环境中。此外，也可以创建自定义的构造函数，从而定义自定义对象类型的属性和方法。
	构造函数要创建新实例，必须使用new操作符。这种方式调用构造函数实际上会经历4个步骤
	1. 创建一个新对象
	2. 将构造函数的作用域赋给新对象（因此this就指向了这个新对象）
	3. 执行构造函数中的代码（为这个新对象添加属性）
	4. 返回新对象。

  对象的constructor属性最初是用来标识对象类型的。创建自定义的构造函数意味着将来可以将它的实例标识为一种特定的类型，而这正是构造函数模式胜过工厂模式的地方。
1. 将构造函数当作函数
  构造函数与其他函数的唯一区别，就在于调用它们的方式不同。不过，构造函数毕竟也是函数，不存在定义构造函数的特殊语法。任何函数，只要通过new操作符来调用，那它就可以作为构造函数；而任何函数，如果不通过new操作符来调用，那它跟普通函数也不会有什么两样。

2. 构造函数的问题

  构造函数模式虽然好用，但也并非没有缺点。使用构造函数的主要问题，就是每个方法都要在每个实例上重新创建一遍。ECMAScript中的函数是对象，因此每定义一个函数，也就是实例化了一个对象。

##### 原型模式
	我们创建的每个函数都有一个prototype（原型）属性，这个属性是一个指针，指向一个对象，而这个对象的用途是包含可以由特定类型的所有实例共享的属性和方法。如果按照字面量意思来理解，那么prototype就是通过调用构造函数而创建的那个对象实例的原型对象。使用原型对象的好处是可以让所有对象实例共享它所包含的属性和方法。换句胡说，不必在构造函数中定义对象实例的信息，而是可以将这些信息直接添加到原型对象中。
	in操作符，有两种方式使用：单独使用和在for-in循环中使用，在单独使用时，in操作符会在通过对象能够访问给定属性是返回true，无论该属性存在于实例中还是原型中。hasOwnProperty()只在属性存在于实例中时才返回true，因此只要in操作符返回true而hasOwnProperty()返回false，就可以确定属性是原型中的属性。
	在使用for-in循环时，返回的是所有能够通过对象访问的、可枚举（emumerated）属性，其中既包括存在于实例中的属性，也包括存在于原型中的属性。屏蔽了原型中不可枚举的属性（即将Enumerable标记为false的属性）的实例属性也会在for-in循环中返回。
	由于在原型中查找值的过程是一次搜索，因此我们对原型对象所做的任何修改都能过立即从实例上反映出来——即使是先创建了实例后修改原型也照样如此。

##### 组合使用
	创建自定义类型的最常见方式，就是组合使用构造函数模式与原型模式。构造函数模式用于定义实例属性，而原型模式用于定义方法和共享的属性。结果，每个实例都有自己的一份实例属性的副本，但同时又共享着对方法的引用，最大限度地节省了内存。另外，这种混合模式还支持构造函数传递参数，可谓是集两种模式之长。
##### 动态原型模式
	有其他OO语言经验的开发人员在看到独立的构造函数和原型时，很可能会感到非常困惑。动态原型模式正式致力于解决这个问题的一个方案，它把所有信息都封装在了构造函数中，而通过在构造函数中初始化原型（仅在必要的情况下），又保持了同时使用构造函数和原型的优点。换句话说，可以通过检查某个应该存在的方法是否有效，来决定是否需要初始化原型。
##### 寄生构造函数模式
	这种模式的基本思想是创建一个函数，该函数的作用仅仅是封装创建对象的代码，然后在返回新创建的对象。除了使用new操作符并把使用的包装函数叫做构造函数之外，这个模式跟工厂模式其实是一模一样的。
##### 稳妥构造函数模式
	道格拉斯.克罗克福德（Douglas Crockford）发明了javascript中的稳妥对象（durable objects）这个概念。所谓稳妥对象，指的是没有公共属性，而且其方法也不能引用this的对象。稳妥对象最适合在一些完全的环境中（这些环境中会禁止使用this和new），或者在防止数据被其他应用程序改动时使用。稳妥构造函数遵循与寄生构造函数类似的模式，但有两点不同：一个创建对象的实例方法不引用this；二是不使用new操作符构造函数。
### 继承
	继承是OO语言中的一个最为人津津乐道的概念。许多OO语言都支持两种继承方式：接口继承和实现继承。接口继承只继承方法签名，而实现继承则继承实际的方法。如前所述，由于函数没有签名，ECMAScript中无法实现接口继承。ECMAScript只支持实现继承，而且实现继承主要是依靠原型链来实现的。
##### 原型链
	ECMAScript中描述了原型链的概念，并将原型链作为实现继承的主要方法。其基本思想是利用原型让一个引用类型继承另一个引用类型的属性和方法。
	构造函数，原型和实例的关系：每个构造函数都有一个原型对象，原型对象都包含一个指向构造函数的指针，而实例都包含一个指向原型对象的内部指针。如果我们然原型对象等于另一个类型的实例。结果会怎么样。显然，此时的原型对象将包含一个指向另一个原型的指针，相应地，另一个原型中也包含着一个指向另一个构造函数的指针。

1. 默认的原型
  大家要记住，所有函数的默认原型都是Object的实例，因此默认原型都会包含一个内部指针，指向Object.prototype。这也正是所有自定义类型都会继承toString()、valueOf()等默认方法的根本原因。
2. 确定原型和实例的关系
  可以通过两个方式来确定原型和实例直接的关系。第一种方式是使用instanceof操作符，只要用这个操作符来测试实例与原型链中出现过的构造函数，结果返回true。第二种方式是使用isPrototypeOf()方法。同样，只要是原型链中出现过的原型，都可以说是该原型链所派生的实例的原型，因此isPrototypeOf()方法也会返回true。
3. 谨慎地定义方法
  子类型有时候需要重写超类型中的某个方法，或者需要添加超类型中不存在的某个方法。但不管怎样，给原型添加方法的代码一定要放在替换原型的语句之后。
4. 原型链的问题
  原型链虽然很强大，可以用它来实现继承，但它也存在一些问题。其中，最主要的问题的来自包含引用类型值的原型。原型链的第二个问题是：在创建子类型的实例是，不能向超类型 的构造函数中传递参数。
##### 借用构造函数
	在解决原型中包含引用类型值所带来问题的过程中，开发人员开始使用一种叫做借用构造函数（constructor stealing）的技术（有时候也叫做伪造对象或经典继承）。这种技术的基本思想相当简单，即在子类型构造函数的内部调用超类型构造函数。别忘了，函数只不过是在特定环境中执行代码的对象，因此通过使用apply()和call()方法也可以在（将来）新创建的对象上执行构造函数。

1. 传递参数
  相对于原型链而言，借用构造函数有一个很大的优势，即可以在子类型构造函数中向超类型构造函数传递参数。
2. 借用构造函数的问题
  如果仅仅是借用构造函数，那么也将无法避免构造函数模式存在的问题——方法都在构造函数中定义，因此函数复用就无从谈起了。而且，在超类型的原型中定义的方法，对于在调用超类型构造函数后，再添加应该在子类型中定义的属性。

##### 组合模式
	组合继承（combination  inheritance），有时候也叫做伪经典继承，指的是将原型链和借用构造函数的技术组合到一块，从而发挥二者之长的一种继承模式。其背后的思路是使用原型链实现对原型属性和方法的继承，而通过借用构造函数来实现对实例属性的继承。这样，即通过在原型上定义方法实现了函数复用，又能够保证每个实例都有它自己的属性。
##### 原型式继承
	道格拉斯·克罗克福德在2006年写了一篇文章，题为Prototypal Inheritance in Javascript中，介绍了一种实现继承的方法，这种方法并没有使用严格意义上的构造函数。他的想法是借助原型可以基于已有的对象创建新对象，同时还不必因此创建自定义类型。函数如下：
	function object(o){
	  function  F(){};
	  F.prototype = o;
	  return new F();
	}
	在object（）函数内部，先创建了一个临时性的构造函数，然后将传入的对象作为这个构造函数的原型，最后返回了这个临时类型的一个新实例。从本质上讲，object()对传入其中的对象执行了一次浅复制。
	ECMAScript 5通过新增Object.create()方法规范化了原型式继承。这个方法接收两个参数：一个用作新对象原型的对象和（可选的）一个为新对象定义额外属性的对象。
##### 寄生式继承
	寄生式（parasitic）继承是与原型式继承紧密相关的一种思路，并且同样也是有克罗克福德推而广之的。寄生式继承的思路与寄生构造函数和工厂模式类似，即创建一个仅用于封装继承过程的函数，该函数在内部以某种方式来增强对象，最后再像真地是它做了所有工作返回对象。函数如下：
	function createAnother(original){
	  var clone = object(original);
	  clone.sayHi = function(){
	    alert('Hi');
	  }
	  return clone;
	}

##### 寄生组合式继承
	组合继承最大的问题就是无论什么情况下，都会调用两次超类型的构造函数：一次是在创建子类型原型的时候，另一次是在子类型构造函数内部。我们已经找到解决这个问题的方法——寄生组合式继承。所谓寄生组合式继承，即通过借用构造函数来继承属性，通过原型链的混成形式来继承方法。其背后的基本思路是：不必为了指定子类型的原型而调用超类型的构造函数，我们所需要的无非就是超类型原型的一个副本而已。本质上，就是使用寄生式继承来继承超类型的原型，然后再将结果指定给子类型的原型。其模式如下：
	function inheritPrototype(subType, superType){
      var prototype = object(superType.prototype);
      prototype.constructor = subType;
      subType.prototype = prototype;
	}