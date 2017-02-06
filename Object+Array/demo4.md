#避免继承标准类
比如，一个操作文件系统的库可能希望创建一个抽象的目录，该目录继承了数组的所有行为：

    function Dir (path, entries) {
        this.path = path;
        for (var i = 0, n = entries.length; i < n; i++) {
            this[i] = entries[i];
        }
    }
    Dir.prototype = Object.create(Array.prototype);
    // extends Array

遗憾的是，这种方式破坏了数组的length属性的预期行为：

    var dir = new Dir("/tmp/mysite", ["index.html", "script.js", "style.css"]);
    dir.length;   // 0

失败的原因是length属性只对在内部标记为“真正的”数组的特殊对象起作用。对于这些对象，javascript保持length属性与该对象的索引属性的数量同步。如果给对象添加了更多的属性，length属性就会自动增加；减少了length属性，也自动删除任何索引号大于该新值的索引属性。

但是，当我们扩展Array类时，子类的实例并不是通过new Array()或者字面量[]语法创建的。

我们可以检测：

    var dir = new Dir("/", []);
    Object.prototype.toString.call(dir);  // "[object Object]"
    Object.prototype.toString.call([]);  // "[object Array]"

更好地实现是定义一个entries数组的实例属性

    function Dir (path, entries) {
        this.path = path;
        this.entries = entries; // array property
    }

在原型中重新定义Array的方法，我们可以把这些相应的方法委托给entries属性来完成：

    
    Dir.prototype.forEach = function (f, thisArg) {
        if (typeof thisArg === 'undefined') {
            thisArg = this;
        }
        this.entries.forEach(f, thisArg);
    }


#避免使用轻率的猴子补丁
猴子补丁举例：
数组缺少一个有用的方法吗？我们可以这么添加：

    Array.prototype.split = fucntion (i) {
        return [this.slice(0, i), this.slice(i)];
    }

问题在于，如果有另外一个库，也给Array.prototype打猴子补丁：

    Array.prototype.split = fucntion (i) {
        var i = Math.floor(this.length / 2);
        return [this.slice(0, i), this.slice(i)];
    }

解决方案一：把所有的这些修改放一个函数中，用户可以选择调用或者忽略：

    function addArrayMethods () {
        Array.prototype.split = fucntion (i) {
            return [this.slice(0, i), this.slice(i)];
        }
    }

这种方法只有在程序库提供了addArrayMethods函数时才能工作，而实际上并不依赖于Array.prototype.split函数。

也可以通过带有测试条件的守护猴子补丁来实现：

    if (typeof Array.prototype.map !== 'function') {
        Array.prototype.map = function (callback, thisArg) {
            var result = [];
            for (var i = 0, n = this.length; i < n; i++){
                result[i] = callback.call(thisArg, this[i], i);
            }
            return result;
        }
    }







