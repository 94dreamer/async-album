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

暴露一个








