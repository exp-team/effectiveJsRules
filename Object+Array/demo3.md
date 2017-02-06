#使用闭包存储私有数据
我们可以利用闭包在对象中存储真正的私有数据。并不将数据作为对象的属性来存储，而是在构造函数中以变量的方式存储他，并将对象的方法转变为引用这些变量的闭包。

    function User (name, passwordHash) {
        this.toString = function () {
            return "[User] " + name + "]";
        };
        this.checkPassword = function (password) {
            return hash(password) === passwordHash;
        }
    }

现在User的实例根本不包含任何实例属性，因此外部的代码不能直接访问User实例的name和passwordHash变量。

#认识到this变量的隐式绑定问题
CSV（逗号分隔型取值）文件格式是一种表格数据的简单文本表示：

    HC,1989,hebei,China
    GRY,1996,liaoning,China
    William,1988,paris,FR

我们可以编写一个简单的、可定制的读取CSV数据的类：

    function CSVReader (separators) {
        this.separators = separators || [","];
        this.regexp = new RegExp(this.separators.map(function(sep){
                return "\\" + sep[0]
            }).join("|"));
    }

实现一个简单的read方法可以分为两步来处理：
第一步，将输入字符串分为按行划分的数组；
第二部，将数组的每一行再分为按单元格划分的数组。
因此，结果应该是一个二位的字符串数组。
使用map方法最为合适：

    CSVReader.prototype.read = function(str) {
        var lines = str.trim().split(/\n/);
        return lines.map(function(line) {
                return line.split(this.regexp); // wrong this! 
            })
    };
    var reader = new CSVReader();
    reader.read("a,b,c\nd,e,f\n");

这样有一个bug，传递给line.map的回调函数引用了this，它期望得到CSVReader对象的regexp属性。然而，map函数将其回调函数的接收者绑定到了lines数组。其结果是， this.regexp产生undefined值，使得调用line.split陷入混乱。

修正方法1：

    CSVReader.prototype.read = function(str) {
        var lines = str.trim().split(/\n/);
        return lines.map(function(line) {
                return line.split(this.regexp);
            }, this);
    }
    var reader = new CSVReader();
    reader.read("a,b,c\nd,e,f\n");

修正方法2：

    CSVReader.prototype.read = function(str) {
        var lines = str.trim().split(/\n/);
        var self = this;
        return lines.map(function(line) {
                return line.split(self.regexp);
            });
    }
    var reader = new CSVReader();
    reader.read("a,b,c\nd,e,f\n");

修正方法3：

    CSVReader.prototype.read = function(str) {
        var lines = str.trim().split(/\n/);
        return lines.map(function(line) {
                return line.split(this.regexp);
            }.bind(this));
    }
    var reader = new CSVReader();
    reader.read("a,b,c\nd,e,f\n");













