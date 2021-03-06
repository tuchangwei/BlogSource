title: "[译]如何用Node.js实现SSL/TLS"
date: 2015-09-25 21:56:51
tags: HTTPS
categories: Node.js
---
最近iOS9发布，强制App使用HTTPS传输,做为一个入坑多年的iOS程序员，还真是不了解这其中的来龙去脉，刚好订阅的Node.js周报中推送了这篇[文章](http://www.sitepoint.com/how-to-use-ssltls-with-node-js/),赶紧翻译过来，既搞清了HTTPS又学习了Node.js，一举两得。

---
使用HTTPS正在变的越来越普遍，因此我们应该知道怎么在Node.js程序中实现SSL/TSL——不论是为了访问HTTPS资源还是为了提供加密的资源。HTTPS到底是什么意思呢？它暗示了什么？有什么限制和约束？我们将试着为所有的这些问题找到答案。

另外，我们不应该仅仅通过提供HTTPS来保护我们的客户端，而且我们也应该严格要求来自服务器的是加密过的连接。我们将会看到激活SSL/TLS层的可能性的存在，即使默认情况下不能激活。让我们先来浏览一下HTTPS的当前情况。

### HTTPS Everywhere
在2015年2月17日，HTTP/2协议被IESG许可作为推荐的标准发布。这是一个大的里程碑。现在我们可以更新我们的服务器去使用HTTP/2。一个重要的方面是它与HTTP1.1和握手机制的向后的兼容性。虽然这个标准并没有指明要强制加密，但大部分浏览器将仅仅支持TLS之上的HTTP/2协议。这给了HTTPS另一方面的促进。最终，HTTPS everywhere!

那我们的协议栈看起来是怎样的呢？从一个在浏览器中跑的网站的透视来看，我们大致有下面的一些层到达IP层。

1. 用户浏览器
2. HTTP
3. SSL/TLS
4. TCP
5. IP

对比SSL/TLS顶端的HTTP协议来说，HTTPS啥也不多。因此，所有对于HTTP的规则仍然适用。那额外的层给了我们什么呢？给了很多好处。我们通过拥有Keys和证书来获得授权。当连接以一种不对称的加密时，还有某种隐私和机密被保护。最后但不仅仅还有数据的真实性也会得到保护，比如，那些传输完成的数据在传输过程中是不能被改变的。

其中一个最普遍的错误观念是使用SSL/TLS需要太多的资源并且会拖慢服务器的速度。这无异是错误的。我们也不需要任何特制的拥有密码单元的硬件。甚至对于Google来说，SSL/TLS层的消耗也小于百分之一的CPU的加载。更进一步的讲，HTTPS的网络开支会低于HTTP的百分之二。总而言之，如果因为一点网络开支而放弃HTTPS，这是没有道理的。

{%asset_img 1442392075istlsfastyet.png %}

最新的版本是TLS1.2（目前1.3版本作为工作草案是可用的）。TLS是SSL的继承者，在最新发布的SSL3.0中可以使用。从SSL到TLS的改变杜绝的互通性。但是基本的流程是不变的。我们有三种不同的加密途径。第一个是证书链公钥基础结构，第二个是提供公钥密码作为Key交换。最后，第三种是对称。这里我们对于数据传输有密码逻辑。


