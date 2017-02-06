#熟练掌握闭包
##Javascript允许你引用在当前函数以外定义的变量
##即使外部函数已经返回，当前函数仍然可以引用在外部函数所定义的变量
##必报可以更新外部变量的值。实际上，闭包存储的是外部变量的引用，而不是他们的值的副本。

    function box() {
        var val = undefined;
        return {
            set: function(newVal) {
                val = newVal;
            },
            get: function() {
                return val;
            },
            type: function() {
                return typeof val;
            }
        }
    }
    var b = box();
    b.type();  // undefined;
    b.set(98.6);
    b.get();  // 98.6
    b.type();  // number