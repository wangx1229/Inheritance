看了这么多关于继承的文章，好像都是一个模子里面出来的...所以总结一下自己的认识，希望能够让自己或者看到这篇文的你都有所收获。有什么不对的地方，请指正，我也会后续修改。

关于为什么使用继承，直白的讲，我的理解就是为了复用。比如我一个项目里面多次使用子类继承与父类或者实例化这种情况，总不能每次都重新写一大堆属性方法吧，相对而已，使用继承，可以直接得到相关的属性和方法，简单了很多

我会尽量写的清楚一些，来让这篇文章更容易理解。但是仍然需要你对以下几个方面有一定的基础。
```
1. '继承'(请注意，这可是带有引号的！)
2. call()和apply()
3. 原型链
4. 构造函数
```
首先来了解一下我们开始前的工作

## 继承
这里继承带有引号，因为继承是类中的说法，他的意思是复制！复制！复制！而JS中的'继承'，其实指的是通过原型链进行委托。不管怎么说，它本质上都不是复制，会和类的行为不一致。
## call()和apply()
call和apply方法可以将函数中的this指向我们需要的对象，比如说：
```JavaScript
function balabala() {
  this.name = 'balabala'
};
const obj = {};
balabala.call(obj)
obj.name // balabala
```
调用函数的时候使用call可以讲函数中的this指向obj，这个时候，obj就有了name属性，属性值是balabala。

## 原型链
JS万物皆是对象（如果你非要拿4种简单基本类型来怼我，对不起，请出门右转），而每个对象都有一个prototype属性。

举个栗子：

我们有一个A对象，A.prototype属性指向的其实也是一个对象，他就是我们委托的对象。所有的对象会通过prototype链一节一节的委托到起，一直到他的尽头Object.prototype，因为当我们的链子到达这一节的时候，此时的Object.prototype对象已经不再委托给任何对象了。

## 构造函数

JS中通常使用new关键字调用构造函数来创建一个对象。  
通过new操作，主要实现了以下4个行为
```
1. new调用一个函数会生成一个新对象。
2. 新对象的原型对象会委托到我们函数的原型对象上来。
3. 新对象会指向被调用的函数中的this。
4. 如果被调用的函数中没有返回别的对象，那么就会返回这个新对象。
```
具体用法和理解参考以下栗子

网上给出很多种继承方式，有的六七种，这些个代号，反正我的脑细胞是记不下来...

我认为具体使用哪种继承，还是要看你业务的实际情况，你需要那种继承，就使用哪种继承。起名字是一件很复杂的事情，所以我这里不会给他们起任何名字。但是我会根据业务需要进行分门别类。

首先我们来创建一个父类
```javascript
function father() {
  this.name = 'Daddy';
}
father.prototype.say = function() {
  console.log('from father');
}
const obj = new father();
```
father类中属性，会通过new调用以后，成为实例obj的属性，因此这个时候obj就有了name和speak属性。

同时由于obj的原型对象会指向father.prototype对象，因此obj可以通过原型使用father.prototype中的spell属性。

下面我们加入子类的情况，其中子类是通过父类实现的。
## 如果你仅仅希望子类的实例继承于子类但又不继承与父类时

#### 假如你希望子类使用父类中的属性，你可以：
```javascript
function child() {
  father.call(this)  //神奇的call()方法
}
child.prototype = new father();
child.prototype.say = father.prototype.say;
const obj = new child();
```
子类通过father.call(this) 会将father函数中this帮到child上，这是就相当于child.name = 'Daddy'，也就相当于在child中写成this.name = 'Daddy'。这个时候在创建子类实例的时候，name会被添加到obj上，obj可以通过原型指向say。注意child.prototype.say = father.prototype.say并没有让子类和父类相关联。这连个方法只是指向了同一个函数引用。因此更改father原型中的say属性并不会影响child原型中的say属性。
## 如果你希望子类的实例继承于子类但又继承与父类时
```javascript
function child() {
  father.call(this)  //神奇的call()方法 
}
child.prototype = new father();

child.prototype.speak = function() {
 console.log('from child')
}

const obj = new child();
```
这个时候，会首先在child.prototype上创建一个name属性，然后将child.prototype.__proto___指向father.prototype。

obj.__proto___会指向child.prototype， 而obj.__proto___.__proto__会指向child.prototype.__proto__，也就是我们创建的father.prototype。

这样实例对象obj既继承与子类又继承与父类。当你修改子类或者父类原型中的方法时，实例都会受到影响。

有一点需要注意的时，当你使用child.prototype = new father(); 他会破坏原来child.prototype以及其指向，所以你必须在这之后对child.prototype添加你需要的属性，比如本例中的say属性，或者修改constructor属性。

#### 也可以使用ES6中新添加的方法 a = Object.create(b) 创建一个对象指向a，并使a的原型关联到b对象上。类比可以将b看作时例子中的father.prototype或者child.prototype。


