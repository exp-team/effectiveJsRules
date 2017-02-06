# 使用变量保存arguments的引用
迭代器(iterator)是一个可以顺序存取数据集合的对象。一个典型的API是next方法，该方法获得序列中的下一个值。
比如：

    var it = values(1, 4, 1, 4, 2, 1, 3, 5, 6);
    it.next(); // 1
    it.next(); // 4
    it.next(); // 1

一个bug实现：

    function values () {
        var i = 0;
        var n = arguments.length;
        return {
            hasNext: fucntion () {
                return i < n;
            },
            next: function () {
                if (i > n) {
                    throw new Error('end of iteration');
                }
                return arguments[i++];
            }
        }
    };

    var it = values(1, 4, 1, 4, 2, 1, 3, 5, 6);
    it.next(); // undefined
    it.next(); // undefined
    it.next(); // undefined

上述错误原因在于一个新的arguments变量被隐式地绑定在函数体当中。我们感兴趣的arguments对象是与values函数相关的那个，但是迭代器的next方法含有自己的arguments变量。

修正实现：

    function values () {
        var i = 0;
        var n = arguments.length;
        var a = arguments;
        return {
            hasNext: fucntion () {
                return i < n;
            },
            next: function () {
                if (i > n) {
                    throw new Error('end of iteration');
                }
                return a[i++];
            }
        }
    };

    var it = values(1, 4, 1, 4, 2, 1, 3, 5, 6);
    it.next(); // 1
    it.next(); // 4
    it.next(); // 1

