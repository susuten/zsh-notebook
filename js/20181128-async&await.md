> time：2018-11-28

## async

### 什么是async

async函数就是Generator函数的语法糖

```
var fs = require('fs');

var readFile = function (fileName) {
    return new Promise(function (resolve, reject) {
        fs.readFile(fileName, function(error, data) {
            if (error) reject(error)
            resolve(data);
        });
    });
}
```

```
var gen = functon* () {
    var f1 = yield readFile('/etc/fstab');
    var f2 = yield readFile('/etc/shells);
    console.log(f1.toString());
    console.log(f2.toString());
}
```

```
var asyncReadFile = async function () {
    var f1 = await readFile('/etc/fstab');
    var f2 = await readFile('/etc/shells);
    console.log(f1.toString());
    console.log(f2.toString());
}
```

比较上面的代码会发现，async函数就是将 Generator 函数的星号（*）替换成 async，将 yield 替换成 await

> a function always returns a `promise`
> if the code has return `<non-promise>` in it, then javascript automatically wraps it into a resolved promise with the value.

```
async function f() {
    return 2;
}
f().then(console.log);  // 2
```

```
async function f() {
    return Promise.resolve(3);
}
f().then(console.log);  // 3
```

### async 函数的优点
> async ensures that the function returns a promise, and `wraps` non-promises in it. 

对Generator 函数的改进，主要体现在以下三点
- 内置执行器。
- 更好的语法。async 表示函数里有异步操作，await 表示紧跟在后面的表达式需要等待结果。
- 更广的适用性。co函数库规定，yield 命令后面只能是 Thunk 函数或 Promise 对象，而 async 函数的 await 命令后面，可以跟Promise对象和原始类型的值（数值、字符串和布尔值，但这时等同于同步操作）


### async 函数的实现
将Generator函数和自动执行器，包装在一个函数里

```
async function fn(args) {
    // ...
}

// 等同于

function fn(args) {
    return spawn(function*() {
        // ...
    });
}
```
其中 spawn 函数就是自动执行器

下面是 spawn 函数的实现

```
function spawn(genF) {
    return new Promise(function(resolve, reject) {
        var gen = genF();
        function step(nextF) {
            try {
                var next = nextF();
            } catch (e) {
                return reject(e);
            }
            if (next.done) {
                return resolve(next.value);
            }
            Promise.resolve(next.value).then(function(v) {
                step(function () {
                    return gen.next(v);
                });
            }, function(e) {
                step(function() {
                    return gen.throw(e);
                })
            });
        }
        step(function() {
            return gen.next(undefined);
        });
    });
}
```

async是非常新的语法功能，不属于ES6，属于ES7

### async 函数的用法
当函数执行的时候，一遇到 await 就会先返回，等到触发的异步操作完成，再接着执行函数体内后面的语句。

```
async function getStockPriceByName (name) {
    var symbol = await getStockSymbol(name);
    var stockPrice = await getStockPrice(symbol);
    return stockPrice;
}

getStockPriceByName('goog').then(function (result) {
    console.log(result);
});
```

```
function timeout(ms) {
    return new Promise((resolve) => {
        setTimeout(resolve, ms);
    });
}

async function asyncPrint(value, ms) {
    await timeout(ms);
    console.log(value);
}

asyncPrint('hello world', 1000);
```
上面代码指定1s 以后，输出 "hello world"

### take care
- await 命令后面的 Promise 对象，运行结果可能是 rejected，所以最好把 await 命令放在 try...catch代码块中
```
async function myFunction () {
    try {
        await somethingThatReturnsAPromise();
    } catch (err) {
        console.log(err);
    }
}

// 另一种写法
async function myFunction() {
    await somethingThatReturnAPromise().catch(function(err) {
        console.log(err);
    });
}
```
- await 命令只能用在 async 函数中，如果用在普通函数，就会报错

- 如果希望多个请求并发执行，可以使用 Promise.all 方法

## await
### 什么是 await
> the keyword `await` makes javascript wait until that promise settles and returns its result.

```
async function f() {
    let promise = new Promise((resolve, reject) => {
        setTimeout(() => resolve('done!'), 1000);
    });
    
    let result = await promise; // wait till the promise resolve
    console.log(result);    // "done!"
}

f();
```

### await 的作用
> await makes js wait until the promise settles, and then go on with the result. 
> That doesn't cost any `CPU` resources, because the engine can do other jobs meanwhile.
> It's just more elegant syntax of getting the promise result than `promise.then`, easier to read and write.

### 使用 await 需要注意的地方
- can't use await in `regular` function 
- await won't work in the `top-level` code
- await accepts `thenables`

## 总结
`async`的作用
- 使得函数返回 promise
- 允许在函数里面使用 await

## 参考
[阅读](http://www.ruanyifeng.com/blog/2015/05/async.html)
[阅读](https://javascript.info/async-await)