#使用bind方法提取具有确定接受者的方法
假设一个字符串缓冲对象使用数组来存储字符串，该数组稍后会被连接起来：

    var buffer = {
        entries: [],
        add: function(s) {
            this.entries.push(s);
        },
        concat: function() {
            return this.entries.join("");
        }
    };

    var source = ["867", "-", "5309"];
    source.forEach(buffer.add);
    buffer.join();
    // error: entries is undefined

事实上，我们传递给forEach方法的函数没有确定接受者，它会使用全局对象作为默认的接受者。由于全局对象没有entries属性，因此会抛出错误。
幸运的是，forEach方法允许调用者提供一个可选的参数作为回调函数的接受者，所以我们可以这样修复：

    var source = ["867", "-", "5309"];
    source.forEach(buffer.add, buffer);
    buffer.join();
    // 867-5309

同样，我们可是使用bind方法：

    var source = ["867", "-", "5309"];
    source.forEach(buffer.add.bind(buffer));
    buffer.join();
    // 867-5309

需要注意的是：buffer.add.bind(buffer)创建了一个新的函数而不是修改了buffer.add函数。
所以：

    buffer.add.bind(buffer) === buffer.add;   // false
    