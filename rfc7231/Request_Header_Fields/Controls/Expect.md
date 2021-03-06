请求中的“Expect”头字段表明一组确定的需要被服务器支持以正确处理该请求的行为。本规范定义的唯一期望是`100-continue`。

> ```
>      Expect  = "100-continue"
> ```

Expect 字段值是不区分大小写的。

接收到一个Expect字段值而不是`100-continue`服务器**可能**以417（期望失败）状态码来响应以表明意向不到的期望是无法实现的。

`100-continu`期望通知接收者客户端将要在这个请求中发送一个消息体，并且如果请求行和头字段不足以制造一个直接的成功，重定向或错误响应时，希望接收100（继续）的临时响应。这允许客户端等待一个迹象，这么做之前发送一个消息体时值得的，它可以在消息体很大或客户端预期可能有错误时提供效率（例如，当第一次发送一个状态改变方法而没有预先验证认证凭证）。

例如，一个请求这样开始

> ```
>      PUT /somewhere/fun HTTP/1.1
>      Host: origin.example.com
>      Content-Type: video/h264
>      Content-Length: 1234567890987
>      Expect: 100-continue
> ```

允许服务器在客户开始填充管道进行不必要的数据传输之前立刻响应一个错误消息，如401（未授权）或（405方法不被允许）。

对客户端的要求：

- 客户端**不得**在不包含消息体的请求中生成`100-continue`期望。
- 在发送请求消息主体前将会等待一个100（Continue）响应的客户端**必须**发送一个包含`100-continu`期望的Expect头字段。
- 发送`100-continue`期望的客户端不被要求等待任何特定的时间长度，这样的客户端**可能**继续发送消息体，即使时它没有接收到响应。此外，由于100（继续）响应不能通过HTTP/1.0的中介发送，这样的客户端**不应该**在发送消息体之前等待一段不确定的时间。
- 在包含`100-continue`期望的请求的响应中接收到417（期望失败）状态码的客户端**应该**不带`100-continue`期望的重复那个请求，因为417响应只表示响应链不支持期望（例如，它通过了一个HTTP/1.0的服务器）。

对服务器的要求：

- 在HTTP/1.0请求中接收到`100-continue`期望的服务器**必须**忽略期望。
- 如果服务器已经接收到请求的一部分或所有的消息体，或者结构说明没有消息体，它**可能**忽略发送100（继续）响应。
- 一旦消息体被接收到并处理，发送100（继续）响应的服务器**必须**发送一个最终响应码，除非连接被过早关闭。
- 在读取整个消息体之前发送最终响应状态码的服务器**应该**在响应中指明它是否想要关闭连接或继续读取和丢弃请求消息（RFC7230，6.6节）。

源服务器**必须**在接收HTTP/1.1（或更新）的请求行以及包含`100-continue`期望并指出请求消息带有消息主体的完整头字段时用最终响应状态码发送即时响应（如果状态可以通过请求行和头字段的检测确定），或者发送一个100（继续）即时响应以鼓励客户端发送请求消息体。源服务器**不得**在发送100（继续）响应前等待消息主体。

代理**必须**在接收到HTTP / 1.1（或更高版本）请求行和包含100个继续期望并且指示请求消息主体将遵循的完整报头部分时发送具有最终状态代码的即时响应（如果状态可以通过请求行和头字段的检测确定），或者通过向下一个入站服务器发送相应的请求行和头部分来开始将请求转发到源服务器。如果代理相信（从配置或过去的交互）下一个入站服务器只支持HTTP/1.0，代理**可能**生成一个100（继续）响应来鼓励客户端开始发送消息主体。

*注意：Expect头字段在原版HTTP/1.1（RFC2068）发布之后才被添加，作为请求临时100（继续）响应的手段和指示必须理解的扩展的一般机制。但是这个扩展机制还没有被客户端使用并且必须理解的要求也没有被很多服务器实现，除非翻译扩展实现。本规范已经移除了这个扩展机制以简化100-continue的处理和定义。*