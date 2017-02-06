#使用数组而不要使用字典来存储有序集合
for...in循环会挑选一定的顺序来枚举对象的属性。由于标准允许javascript引擎自由选择一个顺序，他们的选择会微妙地改变程序行为：
假设有一个映射标题和等级的字典：

    var ratings = {
        "God Will Hunting": 0.8,
        "Mystic River": 0.7,
        "21": 0.6,
        "Doubt": 0.9
    }

如果我们想计算他们等级的平均值：

    var total = 0;
    var count = 0;
    for (var key in ratings) {
        count++;
        total += ratings[key];
    }
    console.log(total/count);

事实证明，流行的Javascript环境实际上是用不同的顺序执行这个循环。
例如，一些环境根据加入对象的顺序来枚举对象的keys：

    (0.8+0.7+0.6+0.9)/4  // 0.75

有的环境（比如最新版的chrome），总是先枚举潜在数组索引，然后才是其他key。由于电影21的名字恰好是一个合法的数组索引，所以：

    (0.6+0.8+0.7+0.9)/4  // 0.749999999999