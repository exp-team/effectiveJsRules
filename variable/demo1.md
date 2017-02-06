#避免使用with
程序有时候需要对单个对象依次调用一系列方法，使用with语句可以方便地避免对对象的重复引用：

    function status(info) {
        var widget = new Widget();
        with (widget) {
            setBackground('blue');
            setForeground('red');
            setText("status: " + info);
            show();
        }
    }

有时程序决定所有的widget对象都用该有一个info属性。更糟糕的是，有时候给Widget的原型对象在运行时加入info属性。这样将会使得status函数变的不可预测：

    status("connecting");
    // status: connecting
    Widget.prototype.info = "[[widget info]]";
    status('connected');
    // status: [[widget info]]

使用with语句从模块对象中“导入”变量：

    function f(x, y) {
        with (Math) {
            return min(round(x), sqrt(y));
        }
    }

同样，如果某人添加名为x或y的属性到Math对象上，那么上述例子中的f函数也会出错：

    Math.x = 0;
    Math.y = 0;
    f(2, 9); // 0

最好的代替方法是将对象绑定到一个简短的变量名上：

    function status(info) {
        var w = new Widget();
        w.setBackground('blue');
        w.setForeground('red');
        w.setText("status: " + info);
        w.show();
    }

该版本的行为更具有可预测性。没有任何变量引用对于w对象的内容是敏感的。所以即使一些代码修改了Widget的原型对象，status函数的行为依旧与预期一致。

    status("connecting");
    // status: connecting
    Widget.prototype.info = "[[widget info]]";
    status('connected');
    // status: connected

同样，

    function f(x, y) {
        var min = Math.min;
        var round = Math.round;
        var sqrt = Math.sqrt;
        return min(round(x), sqrt(y));
    }
    Math.x = 0;
    Math.y = 0;
    f(2, 9); // 2


