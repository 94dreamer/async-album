## Async 函数

ES2017 标准引入了 async 函数 ，更方便了异步操作。

async 函数是 Generator 函数的语法糖。

前文有一个 Generator 函数，依次读取两个文件。

```
var fs = require('fs');

var readFile = function(fileName){
	return new Promise(function(resolve,reject){
		fs.readFile(fileName,function(error,data){
			if(error) reject(error);
			resolve(data);
		})
	})
}

var gen = function* (){
	var f1 = yield readFile('/etc/fstab');
	var f2 = yield readFile('/etc/shells');
	console.log(f1.toString());
	console.log(f2.toString());
}
```

写成 asycn 函数，就是下面这样。

```
var asycnReadFile = async function(){
	var f1 = await readFile('/etc/fstab');
	var f2 = await readFile('/etc/shells');
	console.log(f1.toString());
	console.log(f2.toString());
}
```

一比较发现，async 函数就是将 Generator 函数的星号 （*）替换成了 async，将 yield 替换成 await ，仅此而已。

#### async 函数的优点

1. 内置执行器，不需要想Generator一样 在外部next()调用。
2. 更好的语义。
3. 更广的适用性。

