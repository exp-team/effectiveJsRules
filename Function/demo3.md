#使用Call方法来改变this指向
表示键值对列表的对象提供了名为forEach的方法：

    var table = {
        entries: [],
        addEntry: function (key, value) {
            this.entries.push({key: key, value: value});
        },
        forEach: function (func, thisTarget) {
            var entries = this.entries;

            for (var i = 0, n = entries.length; i < n; i++) {
                var entry = entries[i];
                func.call(thisTarget, entry.key, entry.value, i);
            }
        } 
    }

这个例子允许table对象的使用者将一个方法作为table.forEach的回调函数func, 并为该方法提供一个合理的接受者thisTarget. 
例如，可以方便的将table中的内容复制到另外一个table2中

    var table2 = table1.forEach(table2.addEntry, table2)