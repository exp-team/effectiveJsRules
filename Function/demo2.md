#掌握高阶函数
高阶函数就是那些将函数作为参数或返回值的函数。

典型的就是数组的sort方法，它需要调用者决定如何比较数组中的任意两个元素。

    function compareNumbers(x,y) {
        if (x < y) {
            return -1;
        }
        if (x > y) {
            return 1;
        }
        return 0;
    }
    [3,1,4,1,5,9].sort(compareNumbers); // [1,1,3,4,5,9]

上边例子当然可以直接用一个匿名函数进一步简化。

高阶函数通常可以简化代码，并消除繁琐的样板代码。比如，一个简单的转换字符串数组的操作，我们可以使用循环实现：

    var names = ["fred", "wilma", "pebbles"];
    var upper = [];
    for (var i = 0, n = names.length; i < n; i++) {
        upper[i] = names[i].toUpperCase();
    };
    console.log(upper); // ["FRED", "WILMA", "PEBBLES"];

使用数组便利的map方法，可以完全消除循环，仅仅使用一个局部函数就可以实现对元素的逐个转换：

    var names = ["fred", "wilma", "pebbles"];
    var upper = names.map(function(name) {
            return name.toUpperCase();
        });
    console.log(upper); // ["FRED", "WILMA", "PEBBLES"];

使用高阶函数完成抽象：
1）使用英文字母构造一个字符串:

    var aIndex = 'a'.charCodeAt(0); // 97
    var str = '';
    for (var i = 0; i < 26; i++) {
        str += String.fromCharCode(aIndex + i)
    }
    console.log(str); // abcdefghijklmnopqrstuvwxyz

2) 同时，程序另一部分代码生成一个包含数字的字符串:

    var digits = '';
    for (var i = 0; i < 10; i++) {
        digits += i;
    }
    console.log(digits);

3) 同时，程序的其他部分还存在创建一个随机的字符串:

    var random = '';
    for (var i = 0; i < 8; i++) {
        random += String.fromCharCode(Math.random(*26) + aIndex);
    }
    console.log(random); // different result each time

每个例子都创建了不同的字符串，我们可以观察他们的共同逻辑：每个循环通过连接每个独立部分的计算结果来创建一个字符串。
我们可以提取公共部分，将他们移到一个工具函数里：

    function buildString (n, callback) {
        var result = '';
        for (var i = 0; i < n; i++) {
            result += callback(i);
        }
        return result;
    }

最后，我们可以实现1）

    buildString(26, function(i) {
        return String.fromCharCode(aIndex + i);
    });   

实现2）

    buildString(10, function(i) {
        return i;
    })

实现3）

    buildString(26, function(i) {
        return String.fromCharCode(Math.random(*26) + aIndex);
    }); 























