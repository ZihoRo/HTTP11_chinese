HTTP不是要给MIME兼容协议。然而，消息可以包含一个单独的MIME-Version头字段来表明什么版本的MIME协议被用于构建消息。MIME-Version头字段的使用表明消息时完全与MIME协议一致的（如RFC2045定义）。当出口HTTP消息到严格MIME环境时，发送者有责任保持完全一致性。