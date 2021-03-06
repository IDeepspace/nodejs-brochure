## 文件系统

在 `Node.js` 中，文件系统的交互是非常重要的，服务器的本质其实就是将本地的文件发送给客户端。

`Node.js` 通过 `fs` 模块来和文件系统进行交互。该模块提供了一个 `API`，用于以模仿标准 `POSIX` 函数的方式与文件系统进行交互。

要使用 `fs` 模块：

```javascript
const fs = require('fs');
```



### 一、打开文件

**语法：**

```javascript
fs.open(path, flags[, mode], callback)
```

**参数：**

- `path` - 文件的路径。
- `flags` - 文件打开的行为，默认为 `r`。
- `mode` - 设置文件模式(权限)，文件创建默认权限为 `0666` (可读，可写)。
- `callback` - 回调函数，带有两个参数如：`callback(err, fd)`。

`flags` 参数可以参考：http://nodejs.cn/api/fs.html#fs_file_system_flags

接下来我们创建 `index.js` 文件，并打开 `input.txt` 文件进行读写，代码如下所示：

```javascript
const fs = require("fs");

// 异步打开文件
console.log("准备打开文件！");
fs.open('input.txt', 'r+', function (err, fd) {
  if (err) {
    return console.error(err);
  }
  console.log("文件打开成功！");
});
```

代码执行结果如下：

```javascript
> node index.js
准备打开文件！
文件打开成功
```



### 二、获取文件信息

**语法：**

```javascript
fs.stat(path[, options], callback)
```

**参数：**

参数使用说明如下：

- `path` - 文件路径。
- `callback` - 回调函数。回调有两个参数 `(err, stats)`，其中 `stats` 是一个 [`fs.Stats`](http://nodejs.cn/s/NMuvVx) 对象。

如果出现错误，则 `err.code` 将是[常见系统错误](http://nodejs.cn/s/5wcdGc)之一。

不建议在调用 `fs.open()`、 `fs.readFile()` 或 `fs.writeFile()` 之前使用 `fs.stat()` 检查文件是否存在。 而是应该直接打开、读取或写入文件，如果文件不可用则处理引发的错误。

要检查文件是否存在但随后并不对其进行操作，则建议使用 [`fs.access()`](http://nodejs.cn/s/NCPsM3)。

例如，给定以下的文件夹结构：

```
- txtDir
  -- file.txt
- index.js
```

`index.js` 中的代码内容如下：

```javascript
const fs = require('fs');

const pathsToCheck = ['./txtDir', './txtDir/file.txt'];

for (let i = 0; i < pathsToCheck.length; i++) {
  fs.stat(pathsToCheck[i], function (err, stats) {
    console.log(stats.isDirectory());
    console.log(stats);
  });
}
```

得到的输出将会类似于：

```
true
Stats {
  dev: 16777220,
  mode: 16877,
  nlink: 3,
  uid: 501,
  gid: 20,
  rdev: 0,
  blksize: 4096,
  ino: 12895090162,
  size: 96,
  blocks: 0,
  atimeMs: 1567049441318.2173,
  mtimeMs: 1567049448640.432,
  ctimeMs: 1567049448640.432,
  birthtimeMs: 1567049441217.446,
  atime: 2019-08-29T03:30:41.318Z,
  mtime: 2019-08-29T03:30:48.640Z,
  ctime: 2019-08-29T03:30:48.640Z,
  birthtime: 2019-08-29T03:30:41.217Z }
false
Stats {
  dev: 16777220,
  mode: 33188,
  nlink: 1,
  uid: 501,
  gid: 20,
  rdev: 0,
  blksize: 4096,
  ino: 12895090175,
  size: 17,
  blocks: 8,
  atimeMs: 1567049483181.4712,
  mtimeMs: 1567049481957.6978,
  ctimeMs: 1567049481957.6978,
  birthtimeMs: 1567049448640.4126,
  atime: 2019-08-29T03:31:23.181Z,
  mtime: 2019-08-29T03:31:21.958Z,
  ctime: 2019-08-29T03:31:21.958Z,
  birthtime: 2019-08-29T03:30:48.640Z }
```



### 三、读取文件

`Node.js` 文件系统（`fs` 模块）模块中的方法均有异步和同步版本，例如读取文件内容的函数有异步的  `fs.readFile()`  和同步的 `fs.readFileSync()`。

- 同步文件系统会阻塞程序的执行，也就是除非操作完毕，否则不会向下执行代码。
- 异步文件系统不会阻塞程序的执行，而是在操作完成时，通过回调函数将结果返回。
- 比起同步，异步方法性能更高，速度更快，而且没有阻塞。

下面看个 `demo` ：

先创建一个 `input.txt` 文件，写入以下内容：

```
node.togoblog.cn
```

`index.js`：

```javascript
const fs = require("fs");

// 异步读取
fs.readFile('input.txt', function (err, data) {
  if (err) {
    return console.error(err);
  }
  console.log("异步读取: " + data.toString());
});

// 同步读取
const data = fs.readFileSync('input.txt');
console.log("同步读取: " + data.toString());

console.log("程序执行完毕。");
```

代码运行结果：

```
> node index.js
同步读取: node.togoblog.cn

程序执行完毕。
异步读取: node.togoblog.cn
```



### 四、写入文件

**语法：**

以下为异步模式下写入文件的语法格式：

```
fs.writeFile(filename, data[, options], callback)
```

如果文件存在，该方法写入的内容会覆盖旧的文件内容。

**参数：**

参数使用说明如下：

- `path` - 文件路径。
- `data` - 要写入文件的数据，可以是 `String`(字符串) 或 `Buffer`(流) 对象。
- `options` - 该参数是一个对象，包含 `{encoding, mode, flag}`。默认编码为 `utf8`, 模式为 `0666` ， `flag`  为 `'w'`
- `callback` - 回调函数，回调函数只包含错误信息参数(`err`)，在写入失败时返回。

**实例：**

创建 `file.js` 文件，代码如下所示：

```javascript
const fs = require("fs");

console.log("准备写入文件");
fs.writeFile('input.txt', '我是通过写入的文件内容！', function (err) {
  if (err) {
    return console.error(err);
  }
  console.log("数据写入成功！");
  console.log("--------我是分割线-------------")
  console.log("读取写入的数据！");
  fs.readFile('input.txt', function (err, data) {
    if (err) {
      return console.error(err);
    }
    console.log("异步读取文件数据: " + data.toString());
  });
});
```

代码执行结果：

```
> node file.js
准备写入文件
数据写入成功！
--------我是分割线-------------
读取写入的数据！
异步读取文件数据: 我是通过写入的文件内容
```

我们会发现程序会帮我们创建一个 `input.txt`，并写入相应的内容。



### 五、关闭文件

需要用到 `close` 方法。

`close` 方法有两个参数，第一个参数为关闭文件的文件描述符 `fd`，第二参数为回调函数，回调函数有一个参数 `err`（错误），关闭文件后执行。

异步关闭文件：

```javascript
const fs = require("fs");

fs.open("test.txt", "r", (err, fd) => {
    fs.close(fd, err => {
        console.log("关闭成功");
    });
});

// 关闭成功
```




### 六、截取文件

**语法**

以下为异步模式下截取文件的语法格式：

```javascript
fs.ftruncate(fd, len, callback)
```

该方法使用了文件描述符来读取文件。

**参数**

参数使用说明如下：

- `fd` - 通过 `fs.open()` 方法返回的文件描述符。
- `len` - 文件内容截取的长度。
- `callback` - 回调函数，没有参数。

**实例**

input.txt 文件内容为：

```
site:togoblog.cn
```

接下来我们创建 `file.js` 文件，代码如下所示：

```javascript
var fs = require("fs");
var buf = new Buffer.alloc(1024);

console.log("准备打开文件！");
fs.open('input.txt', 'r+', function(err, fd) {
   if (err) {
       return console.error(err);
   }
   console.log("文件打开成功！");
   console.log("截取10字节内的文件内容，超出部分将被去除。");
   
   // 截取文件
   fs.ftruncate(fd, 10, function(err){
      if (err){
         console.log(err);
      } 
      console.log("文件截取成功。");
      console.log("读取相同的文件"); 
      fs.read(fd, buf, 0, buf.length, 0, function(err, bytes){
         if (err){
            console.log(err);
         }

         // 仅输出读取的字节
         if(bytes > 0){
            console.log(buf.slice(0, bytes).toString());
         }

         // 关闭文件
         fs.close(fd, function(err){
            if (err){
               console.log(err);
            } 
            console.log("文件关闭成功！");
         });
      });
   });
});
```

以上代码执行结果如下：

```
$ node file.js 
准备打开文件！
文件打开成功！
截取10字节内的文件内容，超出部分将被去除。
文件截取成功。
读取相同的文件
site:togob
文件关闭成功！
```



### 八、删除文件

语法

以下为删除文件的语法格式：

```
fs.unlink(path, callback)
```

参数

参数使用说明如下：

- `path` - 文件路径。
- `callback` - 回调函数，没有参数。

实例

`input.txt` 文件内容为：

```
site:togoblog.cn
```

接下来我们创建 `file.js` 文件，代码如下所示：

```javascript
const fs = require("fs");

console.log("准备删除文件！");
fs.unlink('input.txt', function (err) {
  if (err) {
    return console.error(err);
  }
  console.log("文件删除成功！");
});
```

以上代码执行结果如下：

```
$ node file.js 
准备删除文件！
文件删除成功！
```

再去查看 `input.txt` 文件，发现已经不存在了。



### 九、创建目录

**语法**

以下为创建目录的语法格式：

```
fs.mkdir(path[, options], callback)
```

**参数**

参数使用说明如下：

- `path` - 文件路径。
- `options` 参数可以是：
  - `recursive` - 是否以递归的方式创建目录，默认为 `false`。
  - `mode` - 设置目录权限，默认为 `0777`。
- `callback` - 回调函数，没有参数。

**实例**

接下来我们创建 `file.js` 文件，代码如下所示：

```javascript
const fs = require("fs");
// tmp 目录必须存在
console.log("创建目录 /tmp/test/");
fs.mkdir("/tmp/test/", function (err) {
  if (err) {
    return console.error(err);
  }
  console.log("目录创建成功。");
});
```

以上代码执行结果如下：

```
$ node file.js 
创建目录 /tmp/test/
目录创建成功。
```

可以添加 `recursive: true` 参数，不管创建的目录 `/tmp` 和 `/tmp/a` 是否存在：

```javascript
fs.mkdir('/tmp/a/apple', { recursive: true }, (err) => {
  if (err) throw err;
});
```



### 十、读取目录

**语法：**

```javascript
fs.readdir(path, callback)
```

参数使用说明如下：

- `path` - 文件路径。
- `callback` - 回调函数，回调函数带有两个参数 `err`， `files`，`err` 为错误信息，`files` 为目录下的文件数组列表。

创建 `file.js`，内容如下：

```javascript
const fs = require("fs");

console.log("查看 /tmp 目录");
fs.readdir("/tmp/", function (err, files) {
  if (err) {
    return console.error(err);
  }
  files.forEach(function (file) {
    console.log(file);
  });
});
```

执行结果如下：

```
$ node file.js
查看 /tmp 目录
boost_interprocess
com.apple.launchd.Z85o7UhSY5
com.sogou.inputmethod
media_tmp
```



### 十一、删除目录

**语法：**

```javascript
fs.rmdir(path, callback)
```

**参数：**

- `path` - 文件路径。
- `callback` - 回调函数，没有参数。

创建 `file.js` 文件，代码如下所示：

```javascript
const fs = require("fs");

// 执行前创建一个空的 /tmp/test 目录
console.log("准备删除目录 /tmp/test");
fs.rmdir("/tmp/test", function (err) {
  if (err) {
    return console.error(err);
  }
  console.log("读取 /tmp 目录");
  fs.readdir("/tmp/", function (err, files) {
    if (err) {
      return console.error(err);
    }
    files.forEach(function (file) {
      console.log(file);
    });
  });
});
```

运行结果：

```
$ node file.js
准备删除目录 /tmp/test
读取 /tmp 目录
boost_interprocess
com.apple.launchd.Z85o7UhSY5
com.sogou.inputmethod
media_tmp
```



### 十二、更多

http://nodejs.cn/api/fs.html