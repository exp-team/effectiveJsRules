#使用立即调用的函数表达式创建局部作用域

这段BUG程序会输出什么？

    function wrapElements (a) {
        var result = [], i, n;
        for (i = 0, n = a.length; i < n; i++){
            result[i] = function () {
                return a[i];
            }
        }
        return result;
    }

    var wrapped = wrapElements([10, 20, 30, 40, 50]);
    var f = wrapped[0];
    f(); // ?

程序员可能希望这段程序输出10，但实际上他输出undefined。
运行时进入一个作用域，javascript会为每一个绑定到该作用域的变量在内存中分配一个slot（槽）。wrapElements函数绑定了三个局部变量：result, i和n。
因此，当他被调用时，wrapElements函数回味这三个变量分配slot。在循环的每次迭代中，循环体都会为嵌套函数分配一个闭包。改程序的bug在于这样一个事实：程序员似乎期望该函数存储的是嵌套函数创建时变量i的值。但事实上，他存储的是变量i的引用。由于每次函数创建后变量i的值都发生了变化，因此内部函数最终看到的是变量i的最后的值。
值得注意的是：闭包存储的是其外部变量的引用而不是值。
所以，所有由wrapElements函数创建的闭包都引用在循环之前创建的变量i的同一个共享slot。由于每次循环迭代都递增变量i直到运行到数组结束。因此，这时候其实当我们调用其中任何一个闭包时，他都会查找数组的索引5并返回undefined值。

解决办法是通过船舰一个嵌套函数并立即调用他来强制创建一个局部作用域：

    function wrapElements (a) {
        var result = [], i, n;
        for (i = 0, n = a.length; i < n; i++){
            (function(){
                var j = i;
                result[i] = function () {
                    return a[i];
                }
            })();
        }
        return result;
    }

另外变种是将作为行参的局部变量绑定到IIFE并将其值作为实参传入：

    function wrapElements (a) {
        var result = [], i, n;
        for (i = 0, n = a.length; i < n; i++) {
            (function(j){
                result[j] = fucntion() {
                    return a[j];
                }
            })(i);
        }
    }









