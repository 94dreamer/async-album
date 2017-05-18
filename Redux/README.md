## Redux

[以前的一篇Redux的博客，内附分享PPT](https://github.com/94dreamer/Note/tree/master/Redux的subscribe的困境);

现在我们来分析一下redux的代码结构，和重点实现。

src 目录结构

```
- utils/
- applyMiddleware.js
- bindActionCreators.js
- combineReducers.js
- compose.js
- createStore.js
- index.js
```

### 我们先来看 index.js：

在顶部引入各种依赖，然后暴露引入的几个API，其中中间有一段代码很有意思

```
function isCrushed() {}

if (
  process.env.NODE_ENV !== 'production' &&
  typeof isCrushed.name === 'string' &&
  isCrushed.name !== 'isCrushed'
) {
  warning(
    'You are currently using minified code outside of NODE_ENV === \'production\'. ' +
    'This means that you are running a slower development build of Redux. ' +
    'You can use loose-envify (https://github.com/zertosh/loose-envify) for browserify ' +
    'or DefinePlugin for webpack (http://stackoverflow.com/questions/30030031) ' +
    'to ensure you have the correct code for your production build.'
  )
}
```

首选声明了一个名为 isCrushed 的空函数，他的作用在于通过验证函数自身的 name属性是否还是 isCrushed 来判断自身文件代码是否被压缩丑化。



### createStore.js

这个是Redex最主要的一个 API了，它创建了一个 store来存放应用中所有的state，并且应用中有且仅有一个store。

```
export default function createStore(reducer, preloadedState, enhancer) {}
```

createStore 接受三个参数 reducer函数，初始状态，enhancer增强器，其中reducer为必传项。

```
  if (typeof preloadedState === 'function' && typeof enhancer === 'undefined') {
    enhancer = preloadedState
    preloadedState = undefined
  }

  if (typeof enhancer !== 'undefined') {
    if (typeof enhancer !== 'function') {
      throw new Error('Expected the enhancer to be a function.')
    }

    return enhancer(createStore)(reducer, preloadedState)
  }

  if (typeof reducer !== 'function') {
    throw new Error('Expected the reducer to be a function.')
  }
```

```
  let currentReducer = reducer
  let currentState = preloadedState
  let currentListeners = []
  let nextListeners = currentListeners
  let isDispatching = false
```
接收当前reducer，state，设置监听器空数组，储存下一个监听列表，标示一个isDispatching变量。

```
  function ensureCanMutateNextListeners() {
    if (nextListeners === currentListeners) {
      nextListeners = currentListeners.slice()
    }
  }
```
这个函数根据当前监听器复制出新的下一个监听列表为新的引用。

```
  function getState() {
    return currentState
  }
```
getState函数返回 当前state的引用。

```
  function subscribe(listener) {
    if (typeof listener !== 'function') {
      throw new Error('Expected listener to be a function.')
    }

    let isSubscribed = true

    ensureCanMutateNextListeners()
    nextListeners.push(listener)

    return function unsubscribe() {
      if (!isSubscribed) {
        return
      }

      isSubscribed = false

      ensureCanMutateNextListeners()
      const index = nextListeners.indexOf(listener)
      nextListeners.splice(index, 1)
    }
  }
```

传入到subscribe方法的参数的必须是一个函数。闭包保存一个 isSubscribed 变量，初始化为true。把参数listner push进nextListeners数组。
返回一个函数，用来卸载当前保存listener。


```
  function dispatch(action) {
    if (!isPlainObject(action)) {
      throw new Error(
        'Actions must be plain objects. ' +
        'Use custom middleware for async actions.'
      )
    }

    if (typeof action.type === 'undefined') {
      throw new Error(
        'Actions may not have an undefined "type" property. ' +
        'Have you misspelled a constant?'
      )
    }

    if (isDispatching) {
      throw new Error('Reducers may not dispatch actions.')
    }

    try {
      isDispatching = true
      currentState = currentReducer(currentState, action)
    } finally {
      isDispatching = false
    }

    const listeners = currentListeners = nextListeners
    for (let i = 0; i < listeners.length; i++) {
      const listener = listeners[i]
      listener()
    }

    return action
  }

```

dispatch函数调用时需要传入一个action对象，其中type属性不能为空。
尝试把当前currentState和action对象传入currentReducer，并标记isDispatching为true。
如果运行成功没有抛出错误，把 isDispatching 重置为false。
然后遍历监听器数组，运行监听器函数，返回action。























