# vue 路由模式

vue 路由模式有两种：Hash 模式和 History 模式

1. hash 模式：在 url 中使用#来表示当前路由，hash 模式不会向服务器发起请求，因此在前端页面切换时不会刷新整个页面，只会改变 url 的 hash 部分，这样可以实现单页面应用的效果

2. history 模式：在 url 中不再使用#，而是使用 html5 新特性 pushState 和 replaceState 来改变 url，history 模式会向服务器发起请求，因此在前端页面切换时会发起 http 请求，服务器需要返回对应的 html 文件，这样可以更好的 SEO 优化，并且用户体验更加自然。但是需要服务器端进行一些配置，以避免在直接访问页面时出现 404 错误

> 原理

hash：在 hash 模式下，当 url 的 hash 发生变化时，浏览器并不会向服务器发起请求，而是通过监听浏览器窗口的 hashChange 事件来更新页面的内容，当用户点击链接或者执行 JS 代码改变 URL 的 hash 时，浏览器会触发 hashChange 事件，从而实现无刷新更新页面的效果

history：在 history 模式下，当用户点击链接或者执行 js 代码改变 url 时，浏览器向服务器发起请求，服务器返回对应的 html 文件，浏览器将返回的 html 文件渲染到页面上，在 html5 中新增了两个 API，pushState 和 replaceState，这两个 API 可以修改浏览器历史记录中的当前页面状态，但不会触发浏览器向服务器发送请求，当用户点击浏览器的前进或后退按钮时，浏览器会根据历史记录中的状态渲染对应的页面

# 虚拟 dom

vue3 中的虚拟 DOM 是指一种用 JS 对象来表示当前页面上的 DOM 结构的抽象概念。Vue3 中的虚拟 DOM 与 vue2 的实现原理基本相同，即将整个页面抽象为一个树形结构，每个节点都是一个 js 对象，包括节点的标签名，属性，子节点等信息。

当 Vue3 的组件的状态发生变化时，Vue 会重新渲染虚拟 DOM，并将新的虚拟 DOM 与旧的虚拟 DOM 进行比较，找出需要更新的节点，最后只更新需要更新的节点，从而避免了不必要的 DOM 操作，提高了页面性能

Vue3 中的虚拟 DOM 相比于 Vue2 有一些改进：

- 更快的渲染速度：Vue3 中的虚拟 DOM 可以通过编译时优化来减少运行时的开销，从而提高渲染速度
- 更小的包体积：Vue3 中的虚拟 DOM 采用了更加轻量级的设计
- 更好的组合性：Vue3 中的虚拟 DOM 可以方便地与其他库进行继承，从而实现更好的组合型

> Vue3 中的虚拟 DOM 如何优化渲染速度

1. 编译时优化：Vue3 中的编译器可以通过静态分析，识别出静态节点和静态属性，并将他们编译为常量，在渲染时直接使用，从而减少了 DOM 的比较和渲染的次数，提高了渲染速度

2. 针对性更新：Vue3 中，当组件的状态发生变化时，Vue 会先进行标记，然后只更新需要更新的部分，从而减少了不必要的虚拟 DOM 比较和渲染，提高了渲染速度

3. Fragments：Vue3 中引入了 Fragment 的概念，可以将多个子节点包裹在一个父节点内，从而减少了虚拟 DOM 树的深度，提高了渲染速度

4. Teleport：Vue3 中引入了 Teleport 的概念，可以将子组件渲染到父组件之外的 DOM 节点中，从而避免了不必要的 DOM 操作

5. suspense：可以在异步组件加载时显示占位符

> Vue3 中的编译器如何识别静态节点和静态属性

Vue3 中的编译器可以通过静态提升技术(Static Hoisting)来识别静态节点和静态属性。

静态节点指的是在编译时就已经确定不会发生变化的节点，例如纯文本节点或只包含常量表达式的节点，静态属性指的是在编译时就已经确定不会发生变化的节点属性，例如 class，style，id 等

Vue3 的编译器可以将静态节点和静态属性在编译时提取出来，作为常量存储在渲染函数中，在渲染时，只需要直接使用这些常量，而不需要重新创建虚拟 DOM 节点和属性对象，从而减少了虚拟 DOM 的比较和渲染次数，提高了渲染速度。

# vue2 和 vue3 的区别

# vue 中的几类 watcher

# diff 算法原理

# nexttick 原理

在 Vue3 中，nextTick 的实现原理基于 MutationObserver 和 Promise.resolve().then()。如果浏览器不支持原生 Promise，则使用 setTimeout 来模拟 Promise 的行为，并在回调函数执行前添加一个空的定时器来强制推迟执行。

我以 vue 源码中的 nextTick 的实现方式来解释其原理，nexttick 接受一个参数，名为 fn，是一个回调函数，在 nextTick 中有一个变量 p，p 是 Promise.resolve()函数，fn 在函数 p 执行完成之后调用。

p 要么是当前刷新的异步操作**currentFlushPromise**，要么是普通的异步操作**resolvedPromise**，resolvedPromise 是一个常量，而 currentFlushPromise 是一个变量，默认是 null，而且可以被改变，在 queueFlush 中被重新赋值，当不处于刷新列表(!isFluing && !isFlushPending)时，会把 resolvedPromise.then(flushJobs)赋值给 currentFlushPromise，所以 currentFlushPromise 实际上是 Promise.resolve().then(flushJobs)

而 flushJobs 同样也是一个回调函数，主要用于刷新任务队列 queue，其实就是执行任务队列中的任务，如果当前存在任务列表，会在 Promise.resolve()之后执行任务队列中的任务，直到把列表清空为止，然后才执行传入的回调函数 fn，也就是说整个过程就相当于**Promise.resolve().then(FlushJobs).then(fn)**

无论是在 Vue2 还是 Vue3 中，nextTick 都是用来处理 DOM 更新完毕后执行回调函数的方法。在 Vue2 中，nexttick 的实现基于浏览器的异步任务队列和微任务队列，而在 vue3 中，为了解决浏览器对 Promise 的缺陷问题，如果 Promise 在当前事件循环中被解决，那么在 then 回调函数之前添加的任务将不能在同一个任务中执行，采用 MutationObserver 和 Promise.resolve().then()来解决。

而 MutationObserver 可以用于监测 DOM 树的变化，并在发生变化时执行回调函数，其作用在于能够高效的监测 DOM 变化，从而能够更快的响应 DOM 变化并进行响应的操作

# 响应式原理

Vue3 的响应式原理是基于 ES6 中的 Proxy 实现的，在 Vue3 中，每个响应式对象都是用 Proxy 对象来实现的。

当 Vue3 创建一个响应式对象时，它会使用 reactive 函数将这个对象转换成一个 Proxy 对象，当访问响应式对象的属性时，Proxy 对象会自动拦截这个操作，收集该属性的响应式依赖，并返回该属性的值，如果修改了响应式对象的属性，Proxy 对象会自动侦测到这个变化，并触发响应式更新。

Vue2 的响应式原理是基于 Object.defineProperty 实现的，同样可以拦截对象属性的访问和设置，但是该实现方法有缺陷：

1. Object.defineProperty 只能监听已经存在的属性的变化，无法监听新添加的属性和已经删除的属性的变化。这意味着，如果在 Vue2 中添加或删除了一个对象的属性，需要手动的调用 Vue.set 或 Vue.delete 方法来触发响应式更新
2. Object.defineProperty 无法监听数组的变化，在 Vue2 中需要使用 Vue.set 或者 Vue.delete 方法来触发响应式更新
3. Object.defineProperty 对于嵌套对象的监听比较麻烦，需要递归的对每个对象的属性进行监听，这会增加开发的复杂度，并且会降低响应式系统的性能，Vue3 中用 Proxy 实现的响应式，则对嵌套对象有更好的处理方式，它是你用到哪个属性就会监听到哪个属性，而不是直接对所有属性都进行监听。
4. Object.defineProperty 能监听到属性的操作比 Proxy 少很多，比如属性是个函数的时候，可以监听其 bind 操作等

# computed 原理

# keep-alive 原理

# 插槽使用及渲染原理

# vue 的模板编译原理

Vue 的模版编译原理主要分为以下几个步骤：

1. 解析模版：Vue 的编译器会将模版字符串解析成 AST（抽象语法树）。

2. 静态优化：Vue 会遍历 AST，尝试找到静态节点，即不需要根据数据变化进行更新的节点，将其标记为静态节点，从而避免不必要的更新操作。

3. 生成渲染函数：将 AST 转换为 JavaScript 代码，生成渲染函数。渲染函数接收一个参数，即渲染上下文，根据上下文中的数据和模版生成 VNode（虚拟节点）。

4. 执行渲染函数：当数据变化时，执行渲染函数生成新的 VNode，并通过对比新旧 VNode，更新 DOM。

这个过程中，最关键的步骤是将 AST 转换为 JavaScript 代码的过程。在这个过程中，编译器会根据节点类型生成不同的代码。例如，对于文本节点，编译器会生成一个函数调用，该函数接受一个参数，即文本内容，生成一个文本节点的 VNode。而对于元素节点，编译器会生成一段代码，该代码先创建一个元素节点的 VNode，然后递归处理子节点，最后将子节点添加到父节点上。
