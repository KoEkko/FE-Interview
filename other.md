# 跨域


# web安全


# 组件化

# 设计模式


# 浏览器渲染过程

浏览器的基本工作原理可以分为以下几个步骤：

1. 用户输入URL。当用户在浏览器地址栏中输入URL时，浏览器会进行解析，将其分解为协议、主机、路径等相关信息。

2. 发送HTTP请求。浏览器使用HTTP协议向服务器发送请求，请求资源文件，如HTML、CSS、JavaScript等。

3. 接收响应。服务器接收到请求后，会返回相应的资源文件，浏览器接收响应后进行处理。

4. 解析HTML。浏览器将HTML解析成DOM树，CSS解析成CSSOM树并与DOM树合成渲染树。

5. 执行JavaScript。如果HTML中包含JavaScript代码，则浏览器会执行JavaScript代码，对DOM树进行修改或是向服务器发送异步请求。

6. 渲染页面。渲染引擎使用渲染树来布局和绘制页面，最终呈现给用户。

7. 监听用户事件。浏览器会监听用户的各种事件，如点击、滚动、输入等，对这些事件进行响应，如跳转页面、弹出对话框等。

8. 清理垃圾。浏览器会定期清理内存中的无用对象和资源，以保证浏览器的运行效率和用户体验。

总的来说，浏览器的基本工作原理是将用户输入的URL解析成请求，接收响应后解析HTML、执行JavaScript、渲染页面并监听用户事件，最终展示给用户。


# http和https的区别

# 对称加密和非对称加密


# token 和 cookie 的区别


1. 存储位置：cookie存储在浏览器的cookie中，而token通常存储在客户端的localStorage 和 sessionStorage

2. 数据大小：cookie的数据大小通常限制在4KB左右，而token可以存储更多的数据

3. 安全性：cookie的安全性相对较差，容易受到跨站脚本攻击(XSS)和跨站请求伪造(CSRF)等攻击，token通过使用数字签名或加密算法来确保安全性，可以有效的防止这些攻击

4.跨域支持：cookie在跨域请求时需要进行额外的配置才能生效，而token可以跨域使用，可以方便地在不同域名之间传递身份信息

5.服务端存储：cookie是由服务器生成并存储在客户端，每次请求时会自动携带到服务端。而token由客户端生成并存储在本地，每次请求时需要手动将token添加到请求头中。


# token存储的位置


Token一般存在localStorage中而不是sessionStorage中。

localStorage和sessionStorage都是HTML5提供的存储API，可以在浏览器端存储数据。它们的主要区别在于数据的生命周期和作用域。

localStorage的数据会一直存在，除非被手动删除或浏览器清除缓存。而sessionStorage的数据只在当前会话中存在，一旦关闭浏览器窗口或标签页，数据就会被清除。

由于Token通常用于身份验证和授权，需要在多个会话中保持持久性，因此选择localStorage存储更为合适。而sessionStorage适用于临时保存数据，只在当前会话中有效。

需要注意的是，将Token存储在localStorage中存在一些安全风险，例如受到XSS攻击或窃取Token的风险。因此，应该使用加密算法对Token进行加密，或者使用HttpOnly标记的Cookie存储Token，以提高安全性。