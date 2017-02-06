#数组迭代要优先使用for循环而不是for...in循环
参考下边bug代码

    var scores = [98, 74, 85, 77, 93, 100, 89];
    var total = 0;
    for (var score in scores) {
        total += score;
    }
    var mean = total/scores.length;

for...in循环始终枚举所有key，下一个看似合理的猜测是：（0+1+2+3+...+6）/7 = 21,
但是这也不对，即使是数组的索引属性，对象属性key也始终是字符串。total值意想不到的为“00123456”，mean为17636.571428571428

正确方法是使用传统的for循环：

    var scores = [98, 74, 85, 77, 93, 100, 89];
    var total = 0;
    for (var i = 0, n = scores.length; i < n; i++) {
        total += score;
    }
    var mean = total/scores.length;

#迭代方法优于循环


    var trimmed = [];
    for (var i = 0, n = input.length; i < n; i++) {
        trimmed.push(input[i].trim());
    }

使用forEach实现：

    var trimmed = [];
    input.forEach(function(s) {
        trimmed.push(s.trim());
    })

这段代码不仅见简单可读，而且还消除了中止条件和任何数组索引。

通过现有的数组建立一个新的数组的模式：

    var trimmed = input.map(function(s) {
        return s.trim();
    })

另一种常见的模式是计算一个新数组，该数组只包含现有数组的一些元素：

    listings.filter(function(listing) {
        return listing.price >= min && listing.price <= max;
    })

当然，这都是ES5的默认方法，我们完全可以定义自己的迭代抽象：

    function takeWhile (a, callback) {
        var result = [];
        for (var i = 0, n = a.length; i < n; i++) {
            if (!callback(a[i], i)) {
                break;
            }
            result[i] = a[i];
        }
        return result
    }

    var prefix = takeWhile([1, 2, 4, 8, 16, 32], function(n) {
        return n < 10;
    })

循环只有一点优于迭代函数，就是前者有控制流程操作：如break和continue;










