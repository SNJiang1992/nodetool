# nodetool

之前的工作有很多的部分是写cms的模板，对应于不同的页面，这些页面的头部和尾部大多一样，每次复制粘贴很麻烦，还容易出错。考虑到node.js关于fs模块的部分，应该可以做一个工具来进行操作，所以有了这样一个想法。

**so,let's start!**

首先，要引入node.js里面两个最重要的模块*fs*和*path*

```
var fs  = require('fs');
var path = require('path');
```
读写操作的过程中，不得不说是踩过一些坑的，上来就是想当然的使用了fs.readFile()方法，读取文件，然后fs.writeFile()加上fs.appendFile()，在测试的过程中发现，结果不能保证拼接的顺序。

事实上，node提供了同步的方法来进行操作。

```
var headString = fs.readFileSync('head.html').toString();
var footString = fs.readFileSync('foot.html').toString();
```

这样用了两个变量就存储了头部和尾部的部分，然后就是appendFile()，正当我准备这么做的时候，我想到了既然都是字符串，先做好拼接再写入会很妥当一些。另外生成的页面名称也需要和中间主要的部分做关联。

所以有了

```
function buildPage(pagename) {
    var pageString = fs.readFileSync(pagename).toString();
    var page = headString + pageString + footString;
    fs.writeFileSync(`./pages/${pagename}`, page);
}
```
*注：这里生成的文件目录我写成了pages，你也可以用其他*

然后就是读取当前文件夹下的文件名了，并且要去掉**head.html**和**foot.html**这两个文件，做个遍历，然后筛选。

```
var nameArr = fs.readdirSync(__dirname);

for (let index = 0; index < nameArr.length; index++) {
    if (nameArr[index].endsWith('.html') && nameArr[index] != 'head.html' && nameArr[index] != 'foot.html') {
        buildPage(nameArr[index])
    }
}
```
打开命令行，执行

```
node build.js
```
![](https://github.com/SNJiang1992/nodetool/blob/master/target.png
)
大功告成！

当然，现在这个工具也有一些有待优化的地方：

1. 生成后自动删除原来的拼接文件。
2. 将拼好的文件取出来放到原目录下，以便能正确引入css和js文件。



