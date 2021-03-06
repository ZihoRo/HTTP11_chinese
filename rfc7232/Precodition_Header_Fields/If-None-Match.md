“If-None-Match”头字段使得接收者缓存或源服务器上的请求成为条件的，当字段值是“\*”时他们没有目标资源的任何当前表示，或由一个与字段值中列出的任何实体标签都不匹配的被选表示。

当为If-None-Match比较实体标签时，接收者**必须**使用弱比较函数，因为弱实体标签可以被用于缓存验证，即使表示数据被改变过。

> ```
>      If-None-Match = "*" / 1#entity-tag
> ```

例子

> ```
>      If-None-Match: "xyzzy"
>      If-None-Match: W/"xyzzy"
>      If-None-Match: "xyzzy", "r2d2xxxx", "c3piozzzz"
>      If-None-Match: W/"xyzzy", W/"r2d2xxxx", W/"c3piozzzz"
>      If-None-Match: *
> ```

If-None-Match主要被用于条件的GET请求使的被缓存的信息能够以最小的交易开销进行高效更新。当客户端期望更新一个或多个有实体标签的被缓存响应时，客户端**应该**在进行GET请求时生成一个包含那些实体标签的列表的If-None-Match头字段；这允许接收者服务器能发送一个304（未修改）响应来指示这些被缓存的响应中的一个匹配了被选表示。

当客户端认为资源没有当前表示的时候（RFC7231，4.2.1节），If-None-Match也可以使用“\*”值来阻止一个非安全的请求方法（如PUT）不经意的修改一个目标资源的已存在的表示。如果不止一个客户端试图为目标资源创造一个初始表示，这是“更新丢失”问题的一个可能出现的变种。

接收If-None-Match头字段的源服务器**必须**在执行方法前评估条件（第5节）。如果字段值是”\*“，源服务器有目标资源的一个当前表示，条件是假的。如果字段值是实体标签列表，列表的标签有一个匹配被选表示的实体标签，条件是假的。

如果条件评估为假的，源服务器**不得**执行被请求的方法；作为替代，源服务器**必须**以304状态码响应（如果请求时GET或HEAD）或者412状态码响应（其他所有的请求方法）。

缓存处理接收到的If-None-Match头字段的要求定义在RFC7234的4.3.2节。