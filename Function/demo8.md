#使用bind方法实现函数颗粒化
一个装配URL字符串的函数：

    function simpleURL (protocol, domain, path) {
        return protocol + "://" + domain + "/" + path;
    }

通常情况下的一个场景是： 程序可能需要将特定站点的路径字符串构建为绝对路径URL： 

    var urls = paths.map(function(path) {
            return simpleURL("http", siteDomain, path);
        })

这个例子中，前两个参数对于每次迭代都是固定的，仅仅是第三个参数是变化的。我们可以：

    var urls = paths.map(simpleURL.bind(null, "http", siteDomain);

由于simpleURL不需要引入this变量，所以可以提供给任何值，一般null和undefined是常用做法。
simpleURL.bind的其余参数（"http", siteDomain）和提供给新函数的所有参数共同组成了传递给simpleURL的参数。