#理解prototype、getPrototypeOf、__proto__之间的不同
 
##C.prototype用于建立由new C()创建的对象的原型
##Object.getPrototypeOf(obj)是ES5中用来获取obj对象的原型对象的标准方法
##obj.__proto__用来获取obj对象的原型对象的非标准方法

比如：
    function User (name, passworHash) {
        this.name = name;
        this.passworHash = passworHash;
    };

    User.prototype.toSring = function () {
        return "[User " + this.name + "]";
    };

    User.prototype.checkPassword = function (password) {
        return hash(password) === this.passworHash;
    };

    var u = new User('hc', 'dsfrgtrbrtb');

    Object.getPrototypeOf(u) === User.prototype;  // true
    u.__proto__ === User.prototype;


# 使用Object.getPrototypeOf函数而不要使用__proto__
因为__proto__属性并不是完全兼容的。例如，对于null原型的对象，不同环境处理的可能不同：

    var empty = Object.create(null); // object with no prototype
    "__proto__" in empty;   // false (in some environments)
    "__proto__" in empty;   // true (in some environments)

利用__proto__属性来实行Object.getPrototypeOf：

    if (typeof Object.getPrototypeOf === 'undefined') {
        Object.getPrototypeOf = function (obj) {
            var t = typeof obj;
            if (!obj || (t !== 'object' && t !== "function")) {
                throw new TypeError('not an object');
            }
            return obj.__proto__;
        }
    }