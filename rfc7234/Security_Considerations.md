本节旨在告知开发者，信息提供者，以及用户了解特定于HTTP缓存的安全性问题。更多的一般性安全注意事项在HTTP消息（RFC7230）和语义（RFC7231）中介绍。

缓存暴露了额外的潜在漏洞，因为缓存的内容代表了一个对恶意开发有吸引力的目标。因为缓存的内容在HTTP请求完整后依然存在，在缓存上的攻击可以暴露一个在用户认为其已经被从网络上删除很久的信息。

特别的，各种攻击还可能被存储到共享缓存而被放大；如”缓存污染“攻击使用缓存来分发一个恶意负载体到许多客户端，并且当攻击可以使用执行缺陷，提升权限或其他技术来将一个响应插入到缓存中的时候，这将特别有效。一个长江的缓存污染攻击是利用消息解析在代理和用户代理之间的区别；查看RFC7230，3.3.3节了解相关要求。

同样，执行缺陷（也包括缓存操作的误解）可能导致本应为私密的敏感信息的缓存（例如，认证凭证），将其暴露给未授权的部分。

进一步的，缓存的使用可能带来隐私问题。例如，如果两个用户共享一个缓存，并且第一个浏览了一个站点，第二个可能能够发现已经有人浏览过那个站点，因为由于缓存的存在，资源从缓存载入将更快速。

注意Set-Cookie响应头字段（RFC6265）没有禁止缓存；一个带有Set-Cookie头字段的可缓存响应可以被（通常是）用来满足后续到缓存的请求。希望控制这些响应的缓存的服务器被鼓励发送适合的Cache-Control响应头字段。


