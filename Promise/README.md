## Promise

### 1. 含义

所谓`Promise`，简单来说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。从语法来说，Promise是一个对象，从它可以获取异步操作的消息。Promise 提供统一的API，各种异步操作都可以用同样的方法进行处理。

Promise 对象有两个特点。

1. 对象的状态不受外界影响。Promise 对象代表一个异步操作，有三种状态：Pending、Resolved 和 Rejected。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。这也是Promise这个名字的由来。

2. 一旦状态改变，就不会再变，任何时候都可以得到这个结果。Promose对象的状态改变，只有两种情况发生：从 Pending 变为 Resolved 和从  Pending 变为 Rejected，状态从此不可逆转。如果改变已经发生，你再对 Promise 对象添加回调函数，也会立即得到这个结果。这与事件（Event）完全不同，事件的特点是，如果你错过了它，再去监听，是得不到结果的。

有了 Promise 对象，就可以将异步操作以同步操作的流程表达出来，避免了层层嵌套的回调函数。此外，Promise 对象提供统一的接口，使得控制异步操作更加容易。

Promise 也有一些缺点。首先，无法取消 Promise，一旦新建它就会立即执行，无法中途取消。其次，如果不设置回调函数，Promise内部抛出的错误，不会反应到外部。第三，当处于Pending状态时，无法得知目前进展到那一个阶段。

### 2. 基本用法

ES6规定，Promise对象是一个构造函数，用来生成Promise实例。
下面代码创造了一个Promise实例。

```
var promise = new Promise(function(resolve,reject){
	// ... some code 
	
	if(/*异步操作成功*/){
		resolve(value);
	}else{
		reject(error);
	}
});
```

下面是一个我们最熟悉的Ajax操作，我们用Promise对象来实现试试：

```
function getJSON(url){
	return new Promise((resolve,reject)=>{
		var XHR=new XMLHttpRequest();
		XHR.open("GET",url);
		XHR.onreadystatechange = handler;
		XHR.responseType='json';
		XHR.setRequestHeader('Accept','application/json');
		XHR.send();
		
		function handler() {
			if(this.readyState !== 4){
				return;
			}
			if(this.status ===200){
				resolve(this.response);
			}else{
				reject(new Rrror(this.statusText));
			}
		};
	})
}

getJSON('/posts.json').then(json=>{
	console.log('Contents:'+json);
},error=>{
	console.error('出错了',error);
})
```

### 3. 应用

#### 加载图片
我们可以将图片的加载写成一个Promise，一旦加载完成，Promise的状态就发生变化。

```
const preloadImage=function(path){
	return new Promise(function(resolve,reject){
		var img= new Image();
		img.onload=resolve;
		img.onerror=reject;
		img.src=path;
	});
}
```



