## Generator 函数的语法

#### 基本概念

Generator 函数是ES6提供的一种异步编程解决方案，语法行为与传统函数完全不同。

Generator 函数是一个状态机，封装了多个内部状态。

执行 Generator 函数会返回一个遍历器对象，也就是说，Generator 函数出了状态机，还是一个遍历器对象生成函数。返回的遍历器对象，可以依次遍历 Generator 函数内部的每一个状态。

#### 特征

一是，function 关键字与函数名之间有一个星号；二是，函数体内部使用 yield 表达式，定义不同的内部状态（yield 在英语里的意思就是“产出”）。

```
function* helloWorldGenerator(){
	yield 'hello';
	yield 'world';
	return 'ending'
}

var hw=helloWorldGenerator();

hw.next()
// { value: 'hello', done: false }

hw.next()
// { value: 'world', done: false }

hw.next()
// { value: 'ending', done: true }

hw.next()
// { value: undefined, done: true }

```

总结一下，调用 Generator 函数，返回一个遍历器对象，代表 Generator 函数的内部指针。以后，每次调用遍历器对象的 next方法，就会返回一个有着 value 和 done 两个属性的对象。value 属性表示当前的内部状态的值，是 yield 表达式后面那个表达式的值；done 属性是一个布尔值，表示是否遍历结束。

#### 应用

Generator 可以暂停函数执行，返回任意表达式的值。这种特点使得Generator 有多种应用场景

1. 异步操作的同步化表达

Generator 函数的暂停执行的效果，意味着可以把异步操作写在 yield 表达式里面，等到调用 next 方法时再完后执行。这实际等同宇不需要写回调函数了，因为异步操作的后续操作可以放在 yield 表达式下面，反正要等到调用 next 方法时再执行。所以 Generator 函数的一个重要实际意义就是用来处理异步操作，改写回调函数。

```
function* loadUI (){
	showLoadingScreen();
	yield loadUIDataAsynchronously();
	hideLoadingScreen();
}
var loader = loadUI();
//加载UI
loader.next();
//卸载UI
loader.next();
```

上面代码中，第一次调用loadUI函数时，该函数不会执行，仅返回一个遍历器。下一次对该遍历器调用next方法，则会显示Loading界面（showLoadingScreen），并且异步加载数据（loadUIDataAsynchronously）。等到数据加载完成，再一次使用next方法，则会隐藏Loading界面。可以看到，这种写法的好处是所有Loading界面的逻辑，都被封装在一个函数，按部就班非常清晰。

Ajax是典型的异步操作，通过 Generator 函数部署 Ajax 操作，可以用同步的方式表达。

```
function* main(){
	var result = yield request('http://some.url');
	var resp = JSON.parse(result);
	console.log(resp.value);
}

function request(url){
	makeAjaxCall(url,function(){
		it.next(response);
	});
}

var it = main();
it.next();
```


