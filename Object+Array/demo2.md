# 使构造函数与new操作符无关

    function User (name, passworHash) {
        this.name = name;
        this.passworHash = passworHash;
    };

如果调用者忘记使用new关键字，那么函数的接受者将是全局对象：
    
    var u = User('hc', 'efregr4rfioeghioe');
    console.log(u);   // undefined
    this.name;   // hc
    this.passworHash; // efregr4rfioeghioe

该函数不但会返回毫无疑义的undefined，而且会灾难性地创建（如果这些全局变量已经存在，则会修改）全局变量name和passworHash。
在严格模式下，这样的调用会导致一个错误。

一个更为健壮的方式是提供一个不管怎样调用都工作如构造函数的函数：

    function User (name, passworHash) {
        if( !(this instanceOf User) ) {
            return new User(name, passworHash);
        }
        this.name = name;
        this.passworHash = passworHash;
    };

这样的方式缺点时他需要额外的函数调用，因此代价有点高。而且，它很难适用于可变参数。
另外一种更加怪异的方式,它使用ES5的Object.create：

    function User (name, passworHash) {
        var self = this instanceOf User ? this : Object.create(User.prototype);
        self.name = name;
        self.passworHash = passworHash;
        return self;
    }

Object.create只有在ES5环境下有效，就环境下可以通过创建一个局部的构造函数并使用new操作并初始化该构造函数来代替：

    if ( typeof Object.create === 'undefined') {
        Object.create = function (prototype) {
            function C() {};
            c.prototype = prototype;
            return new C();
        }
    }

请注意，这只实现了但参数版本的Object.create。
真实版本的Object.create函数还接受一个可选的参数，该参数描述了一组定义在新对象上的属性描述符。





