416（范围不满足）状态码表明请求的Range头字段中的范围没有一个与当前状态的被选资源有交叠部分，或请求的一组范围已经因无效的范围或一个小或交叠范围过多的请求而被拒绝。

对于字节范围，未能重叠当前范围意味着所有byte-range-spec值的first-byte-pos都比当前被选表示的长度大。当这个状态码在字节范围请求的响应中被生成时，发送者**应该**生成确定当前被选表示长度的Content-Range头字段（4.2节）。

例如：

> ```
>      HTTP/1.1 416 Range Not Satisfiable
>      Date: Fri, 20 Jan 2012 15:41:54 GMT
>      Content-Range: bytes */47022
> ```

*注意：因为服务器可以自由忽略Range，很多实现将简单地以200响应来响应整个被选表示。这部分是因为大多数客户端准备接收一个200来完成任务（尽管它很低效）而部分是因为客户端可能不会停止进行一个无效地部分请求知道他们接收了一个完整地表示。因此，客户端不能依赖于接收416响应解释在它是最适当地时候。*