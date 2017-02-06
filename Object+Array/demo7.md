#绝不要在Object.prototype中增加可枚举的属性
例如，如果我们增加一个产生对象属性名数组的allKeys方法：

    Object.prototype.allKeys = function () {
        var result = [];
        for (var key in this) {
            result.push(key);
        }
        return result;
    }

遗憾的是，该方法特污染了自身：

    ({a:1, b:2, c:3}).allKeys();
    // ["allKeys", "a", "b", "c"]

更有好的方式是讲allKeys设定为一个函数而不是方法：

    function allKeys (obj) {
        var result = [];
        for (var key in obj) {
            result.push(key);
        }
        return result;
    }

如果确实需要在Object.prototype中增加属性，ES5提供了一种更有好的方式：

    Object.defineProperty(Object.prototype, "allKeys", {
        value: function () {
            var result = [];
            for (var key in this) {
                result.push(key);
            }
            return result;
        },
        writable: true,
        enumetable: false,
        configurable: true
    });

我们通过设置其可枚举属性为false使其在for...in循环中不可见。