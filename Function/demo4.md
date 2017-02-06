#使用apply方法通过不同数量的参数来调用方法

我们来实现一个函数，计算任意数量数字的平均值：

    function average () {
        for (var i = 0, sum = 0, n = arguments.length; i < n; i++) {
            sum += arguments[i];
        }
        return sum / n;
    }

    average(1, 2, 3); // 3
    average(1); // 1
    average(3, 3, 4, 3, 3) // 4

average函数可称为可变参数或者可变元的函数。

同样，我们也可固定元，那么我们将用单个数组作为参数：

    function averageOfArray (array) {
        for (var i = 0, sum = 0, n = array.length; i < n; i++) {
            sum += a[i]
        }
        return sum / n
    }

    averageOfArray([1, 2, 3]); // 3
    averageOfArray([1]); // 1
    averageOfArray([3, 3, 4, 3, 3]) // 4

相对比，可变参数函数具有更便捷的语法，更简洁优雅。

如果我们有这样一个数字数组：

    var scores = getAllScore();

我们该怎么使用average()方法呢？
函数对象配有一个内置的apply方法，他与call方法相似，但该方法需要一个参数数组，然后将数组的每一个元素作为调用的单独参数调用该函数。我们可以这么使用：

    var scores = getAllScore();
    average.apply(null, scores);

如果scores有三个元素，这与average(scores[0], scores[1], scores[2])效果是完全一致的。









