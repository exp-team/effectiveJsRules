#永远不要修改arguments对象

我们来看一个bug程序：
利用call方法，我们从数组中提取出shift方法，并在arguments对象上调用他。借此，我们实现了一个callMethod方法，
该函数需要一个对象和一个方法名，并尝试使用剩余的函数参数调用该对象方法。

    function callMethod (obj, method) {
        // 提取shift方法
        var shift = [].shift;
        // 剔除第一个参数（obj）
        shift.call(arguments);
        // 剔除第二个参数（method）
        shift.call(arguments);

        return obj[method].apply(obj, arguments);
    };
    // 使用
    var obj = {
        add: function (x, y) {
            return x + y;
        }
    };
    callMethod(obj, 'add', 17, 25);
    // error: cannot read property "apply" of undefined

该函数出错的原因是因为arguments对象并不是参数函数的副本。
obj始终表示arguments[0], method始终表示arguments[1]。这意味着，我们似乎是想提取obj['add'], 实际情况却是17[25]!

这个例子告诉我们arguments对象和函数参数之间的关系极其脆弱。修改arguments对象要承担使函数的参数失去意义的风险。
在严格模式下，函数参数不支持对其arguments对象取别名。比如：

    function strict (x) {
        "use strict";
        arguments[0] = 'modified';

        return x === arguments[0];
    };

    function nonstrict (x) {
        arguments[0] = 'modified';
        return x === arguments[0];
    };

    strict('test'); // false
    nonstrict('test');  //true

因此，永远不要修改arguments对象是更为安全的做法。我们可以实现一份复制：

    var args = [].slice.call(arguments);

最终，修复版的callMethod：

    
    function callMethod (obj, method) {
        var args = [].slice.call(arguments, 2);
        return obj[method].call(obj, args);
    };
    var obj = {
        add: function (x, y) {
            return x + y;
        }
    };
    callMethod(obj, "add", 12, 14);














