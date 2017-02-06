#当心局部快函数声明笨拙的作用域

    function f() {
        return "global";
    };

    function test(x) {
        function f() {
            return "local";
        }

        var result = [];
        if (x) {
            result.push(f());
        }
        result.push(f());

        return result;
    };

    test(true); // ["local", "local"]
    test(false); // ["local"]

下面的问题时，如果我们把函数f移到条件块里面：

    function f() {
        return "global";
    };

    function test(x) {
        var result = [];
        if (x) {
            function f() {
                return "local";
            };
            result.push(f());
        }
        result.push(f());

        return result;
    };

    test(true); // ?
    test(false); // ?

事实上，合理的猜测是：["local", "local"]， ["local"]。事实上，也是如此。但是需要注意的是：在某些javascript环境中，在运行时
根据包含函数f的条件块是否被执行来有条件地绑定函数f。

如果你需要有条件地选择函数，最好的办法是使用 var 声明和函数表达式来实现：

    function f() {
        return "global";
    };

    function test(x) {
        var result = [];
        var g = f;
        if (x) {
            g = function () {
                return "local"
            }
            result.push(f());
        }
        result.push(g());

        return result;
    };


#当心明明函数表达式笨拙的作用域

命名函数表达式：

    var f = function double(x) {
        return x * 2;
    }

 匿名函数表达式：

    var f = function (x) {
        return x * 2;
    }

这两者的区别在于后者会绑定到与其函数名相同的变量上，该变量将作为该函数内的一个局部变量。例如完成这样的递归：

    var f = function find(tree, key) {
        if (!tree) {
            return null;
        }
        if (tree.key === key) {
            return tree.value;
        }
        return find(tree.left, key) || find(tree.right, key)
    }

注意，变量find的作用域只在其自身函数中。不想函数声明，命名函数表达式不能通过其内部的函数名在外部被引用：

    find(myTree, "foo");
    // error: find is not defined







    