#使用Object的直接实例构造轻量级的字典
例如，我们创建一个自定义的字典类型并将其元素作为该字典对象自身的属性来存储：

    function NativeDict () {

    };
    NativeDict.prototype.count = function () {
        var i = 0;
        for (var name in this) {
            // counts every property
            i++;
        }
        return i;
    };
    NativeDict.prototype.toString = function () {
        return "[object NativeDict]";
    }

    var dict = new NativeDict();

    dict.alice = 34;
    dict.bob = 24;
    dict.chris = 10;

    dict.count();  // 5

这样我们使用同一个对象来存储NativeDict数据结构的固定属性：count和toString和特点的字典变化条目：alice, bob, chris。
因此，当调用count来枚举字典的所有属性时，它会枚举出所有的属性，而不是仅仅枚举出我们关心的条目。

这就叫做原型污染。如果我们尝试：

    var dict = {};
    dict.alice = 34;
    dict.bob = 24;
    dict.chris = 10;
    
    var names = [];
    for (var name in dict) {
        names.push(name);
    }

    console.log(names); // ["alice", "bob", "chris"];

这么做仍然不能保证对于原型污染是安全的。
任何人都可以增加属性到Object.prototype中，但是好的一点是所有人都不应当增加属性到Object.prototype中。

#使用null原型以防止原型污染
ES5之前，并没有标准的方式创建一个空原型的新对象：

    function C() {};
    C.prototype = null;

当实例化该构造函数仍然得到的是Object的实例：

    var o = new C();
    Object.getPrototypeOf(o) === null;   // false
    Object.getPrototypeOf(o) === Object.prototype;   // true

ES5之后，我们可以有一个标准方法来创建一个没有原型的对象。
Object.create函数能够使用一个用户指定的原型链和一个属性描述符动态地构造对象：

    var x = Object.create(null);
    Object.getPrototypeOf(x) === null;  // true

一些不支持Object.create函数，在许多环境中，我们可以通过特殊的属性__proto__提供了对对象内部原型链的读写访问：

    var x = {__proto__: null}
    x instanceOf Object; // false


#使用hasOwnProperty方法以避免原型污染
我们可以通过在属性查找时使用一个测试来防止其受污染的影响：
    
    dict.hasOwnProperty("alice")?dict.alice:undefined;
    dict.hasOwnProperty(x)?dict[x]:undefined;

但是，当调用hasOwnProperty时，我们请求查找dict对象的hasOwnProperty的方法。通常情况下，该方法会继承自Object.hasOwnProperty对象。但是如果在字典中有一个同名的“hasOwnProperty”条目，那么原型中的hasOwnProperty方法不能再被获取到：

    dict.hasOwnProperty = 10;
    dict.hasOwnProperty("alice");
    // error: dict.hasOwnProperty is not a function 

为此，我们可以：

    var hasOwn = {}.hasOwnProperty;

或者：

    var hasOwn = Object.prototype.hasOwnProperty;
    var dict = {};
    dict.alice = "24";
    hasOwn.call(dict, "alice"); // true;
    hasOwn.call(dict, "hasOwnProperty"); // false;

为了避免在所有查找属性的地方都插入这样的代码，我们可以将该模式抽象到Dict的构造函数中：

    function Dict (elements) {
        // allow an optional initial table
        this.elements = elements || {};
    };
    Dict.prototype.has = function (key) {
        // own property only
        return {}.hasOwnProperty.call(this.elements, key)
    };
    Dict.prototype.get = function (key) {
        // own property only
        return this.has(key):this.elements[key]:undefined;
    };
    Dict.prototype.set = function (key, val) {
        this.elements(key) = val;
    };
    Dict.prototype.remove = function (key) {
        delete this.elements[key];
    };

但是在一些环境中，存在__proto__属性，即：

    var dict = new Dict();
    dict.has("__proto__");  // 不同环境不一致

所以为了最大限度的安全性和可移植性，我们可以：

    function Dict (elements) {
        this.elements = elements;
        this.hasSpecialProto = false;
        this.specialProto = undefined;
    };
    Dict.prototype.has = function (key) {
        if (key === "__proto__") {
            return this.hasSpecialProto;
        }
        return {}.hasOwnProperty.call(this.elements, key);
    };
    Dict.prototype.get = function (get) {
        if (key === "__proto__") {
            return this.specialProto;
        }
        return this.has(key)?this.elements[key]:undefined;
    };
    Dict.prototype.set = function (key, val) {
        if (key === "__proto__") {
            this.hasSpecialProto = true;
            this.specialProto = val;
        }
        else {
            this.elements[key] = val;
        }
    };
    Dict.prototype.remove = function (key) {
        if (key === "__proto__") {
            this.hasSpecialProto = false;
            this.specialProto = undefined;
        }
        else {
            delete this.elements[key];
        }
    }









