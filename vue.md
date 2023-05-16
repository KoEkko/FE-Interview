# vue路由模式

vue路由模式有两种：Hash模式和History模式
1. hash模式：在url中使用#来表示当前路由，hash模式不会向服务器发起请求，因此在前端页面切换时不会刷新整个页面，只会改变url的hash部分，这样可以实现单页面应用的效果

2. history模式：在url中不再使用#，而是使用html5新特性pushState和replaceState来改变url，history模式会向服务器发起请求，因此在前端页面切换时会发起http请求，服务器需要返回对应的html文件，这样可以更好的SEO优化，并且用户体验更加自然。但是需要服务器端进行一些配置，以避免在直接访问页面时出现404错误


> 原理

hash：在hash模式下，当url的hash发生变化时，浏览器并不会向服务器发起请求，而是通过监听浏览器窗口的hashChange事件来更新页面的内容，当用户点击链接或者执行JS代码改变URL的hash时，浏览器会触发hashChange事件，从而实现无刷新更新页面的效果


history：在history模式下，当用户点击链接或者执行js代码改变url时，浏览器向服务器发起请求，服务器返回对应的html文件，浏览器将返回的html文件渲染到页面上，在html5中新增了两个API，pushState和replaceState，这两个API可以修改浏览器历史记录中的当前页面状态，但不会触发浏览器向服务器发送请求，当用户点击浏览器的前进或后退按钮时，浏览器会根据历史记录中的状态渲染对应的页面


# 虚拟dom


vue3中的虚拟DOM是指一种用JS对象来表示当前页面上的DOM结构的抽象概念。Vue3中的虚拟DOM与vue2的实现原理基本相同，即将整个页面抽象为一个树形结构，每个节点都是一个js对象，包括节点的标签名，属性，子节点等信息。  

当Vue3的组件的状态发生变化时，Vue会重新渲染虚拟DOM，并将新的虚拟DOM与旧的虚拟DOM进行比较，找出需要更新的节点，最后只更新需要更新的节点，从而避免了不必要的DOM操作，提高了页面性能  

Vue3中的虚拟DOM相比于Vue2有一些改进：  
 - 更快的渲染速度：Vue3中的虚拟DOM可以通过编译时优化来减少运行时的开销，从而提高渲染速度  
 - 更小的包体积：Vue3中的虚拟DOM采用了更加轻量级的设计
 - 更好的组合性：Vue3中的虚拟DOM可以方便地与其他库进行继承，从而实现更好的组合型


> Vue3中的虚拟DOM如何优化渲染速度

1. 编译时优化：Vue3中的编译器可以通过静态分析，识别出静态节点和静态属性，并将他们编译为常量，在渲染时直接使用，从而减少了DOM的比较和渲染的次数，提高了渲染速度  

2. 针对性更新：Vue3中，当组件的状态发生变化时，Vue会先进行标记，然后只更新需要更新的部分，从而减少了不必要的虚拟DOM比较和渲染，提高了渲染速度  

3. Fragments：Vue3中引入了Fragment的概念，可以将多个子节点包裹在一个父节点内，从而减少了虚拟DOM树的深度，提高了渲染速度  

4. Teleport：Vue3中引入了Teleport的概念，可以将子组件渲染到父组件之外的DOM节点中，从而避免了不必要的DOM操作

5. suspense：可以在异步组件加载时显示占位符


> Vue3中的编译器如何识别静态节点和静态属性


Vue3中的编译器可以通过静态提升技术(Static Hoisting)来识别静态节点和静态属性。  

静态节点指的是在编译时就已经确定不会发生变化的节点，例如纯文本节点或只包含常量表达式的节点，静态属性指的是在编译时就已经确定不会发生变化的节点属性，例如class，style，id等  

Vue3的编译器可以将静态节点和静态属性在编译时提取出来，作为常量存储在渲染函数中，在渲染时，只需要直接使用这些常量，而不需要重新创建虚拟DOM节点和属性对象，从而减少了虚拟DOM的比较和渲染次数，提高了渲染速度。


# vue2 和 vue3 的区别


# vue中的几类watcher


# diff算法原理



# nexttick原理


在 Vue3 中，nextTick 的实现原理基于 MutationObserver 和 Promise.resolve().then()。如果浏览器不支持原生 Promise，则使用 setTimeout 来模拟 Promise 的行为，并在回调函数执行前添加一个空的定时器来强制推迟执行。  

我以vue源码中的nextTick的实现方式来解释其原理，nexttick接受一个参数，名为fn，是一个回调函数，在nextTick中有一个变量p，p 是Promise.resolve()函数，fn在函数p执行完成之后调用。  

p要么是当前刷新的异步操作**currentFlushPromise**，要么是普通的异步操作**resolvedPromise**，resolvedPromise是一个常量，而currentFlushPromise是一个变量，默认是null，而且可以被改变，在queueFlush中被重新赋值，当不处于刷新列表(!isFluing && !isFlushPending)时，会把resolvedPromise.then(flushJobs)赋值给currentFlushPromise，所以currentFlushPromise实际上是Promise.resolve().then(flushJobs)  

而flushJobs同样也是一个回调函数，主要用于刷新任务队列queue，其实就是执行任务队列中的任务，如果当前存在任务列表，会在Promise.resolve()之后执行任务队列中的任务，直到把列表清空为止，然后才执行传入的回调函数fn，也就是说整个过程就相当于**Promise.resolve().then(FlushJobs).then(fn)**


无论是在Vue2还是Vue3中，nextTick都是用来处理DOM更新完毕后执行回调函数的方法。在Vue2中，nexttick的实现基于浏览器的异步任务队列和微任务队列，而在vue3中，为了解决浏览器对Promise的缺陷问题，如果Promise在当前事件循环中被解决，那么在then回调函数之前添加的任务将不能在同一个任务中执行，采用MutationObserver和Promise.resolve().then()来解决。


而MutationObserver可以用于监测DOM树的变化，并在发生变化时执行回调函数，其作用在于能够高效的监测DOM变化，从而能够更快的响应DOM变化并进行响应的操作



# 响应式原理

Vue3的响应式原理是基于ES6中的Proxy实现的，在Vue3中，每个响应式对象都是用Proxy对象来实现的。  

当Vue3创建一个响应式对象时，它会使用reactive函数将这个对象转换成一个Proxy对象，当访问响应式对象的属性时，Proxy对象会自动拦截这个操作，收集该属性的响应式依赖，并返回该属性的值，如果修改了响应式对象的属性，Proxy对象会自动侦测到这个变化，并触发响应式更新。  

Vue2的响应式原理是基于Object.defineProperty实现的，同样可以拦截对象属性的访问和设置，但是该实现方法有缺陷：  
1. Object.defineProperty只能监听已经存在的属性的变化，无法监听新添加的属性和已经删除的属性的变化。这意味着，如果在Vue2中添加或删除了一个对象的属性，需要手动的调用Vue.set或Vue.delete方法来触发响应式更新  
2. Object.defineProperty无法监听数组的变化，在Vue2中需要使用Vue.set或者Vue.delete方法来触发响应式更新  
3. Object.defineProperty对于嵌套对象的监听比较麻烦，需要递归的对每个对象的属性进行监听，这会增加开发的复杂度，并且会降低响应式系统的性能，Vue3中用Proxy实现的响应式，则对嵌套对象有更好的处理方式，它是你用到哪个属性就会监听到哪个属性，而不是直接对所有属性都进行监听。  
4. Object.defineProperty能监听到属性的操作比Proxy少很多，比如属性是个函数的时候，可以监听其bind操作等



# computed 原理




# keep-alive 原理


# 插槽使用及渲染原理