#理解函数调用、方法调用以及构造函数调用之间的不同

##最简单的函数调用

    function hello(username) {
        return "hello, " + username;
    }
    hello('hc'); // hello, hc

##方法调用

    var obj = {
        username: 'hc',
        hello: function() {
            return: "hello, " + this.username;
        }
    }
    obj.hello(); // hello, hc

在方法调用中，是由调用表达式自身来确定this变量的绑定

    var obj2 = {
        username: 'hw',
        hello: obj.hello
    }
    obj2.hello(); // hello, hw

我们可以预定义一个在多个项目中共享的函数来使用

    function hello() {
        return "hello, " + this.username;
    };
    var obj1 = {
        hello: hello,
        username: 'hc'
    };
    obj1.hello(); //hello, hc

    var obj2 = {
        hello: hello,
        username: 'hw'
    };
    obj2.hello(); //hello, hw

但是单独调用

    hello(); // "hello, undefined";

如果在严格模式下, this变量的默认绑定值为undefined：

    function hello() {
        'use strict';
        return "hello, " + this.username;
    }
    hello(); // error: cannot read property "username" of undefined

##构造函数调用
构造函数调用将一个全新的对象作为this变量的值，并隐式返回这个新对象作为调用结果。

    function User(name, passwordHash) {
        this.name = name;
        this.passwordHash = passwordHash;
    };
    var u = new User('hc', '12345');
    u.name // hc











