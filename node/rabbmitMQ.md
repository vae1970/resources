##### 引入包

```javascript
const amqp = require('amqplib');
```

##### Consumer客户端连接MQ server

```javascript
//  MQ重连间隔时间
const MQRetryTime = 3000;
//  MQ正在连接标志位（同一时间只允许发起一次连接，否则会引发多余的错误的Consumers）
let channelConnecting = false;

/**
 * 处理job的MQ监听器
 * @returns {Promise<void>}
 */
async function listener() {
    try {
        if (!channelConnecting) {
            //  修改MQ连接标志位
            channelConnecting = true;
            const connection = await amqp.connect(URL);
            const channel = await connection.createChannel();
            await channel.assertQueue(jobQueueName);
            //	设置Prefetch count为1，阻塞MQ（每处理完一个任务，再消费下一个message）
            channel.prefetch(1);
            await channel.consume(jobQueueName, function (message) {
                try {
                    /**
                     * request args 每次请求都会携带报告的链接和需要生成PDF的文件路径
                     * @property {String} url
                     * @property {String} path
                     * @property {String} reportId
                     */
                    const arg = JSON.parse(message.content.toString());
                    //	业务逻辑
                } catch (e) {
                    channel.ack(message);
                    log(e, false, encodeURIComponent(message.content.toString()));
                }
            });
            connection.on("error", function () {
                channelConnecting = false;
                setTimeout(listener, MQRetryTime);
            });
            connection.on("close", function () {
                channelConnecting = false;
                setTimeout(listener, MQRetryTime);
            });
        }
    } catch (e) {
        channelConnecting = false;
        setTimeout(listener, MQRetryTime);
    }
}
```



##### publisher客户端连接MQ server

```javascript
//  MQ正在连接标志位（同一时间只允许发起一次连接，否则会引发多余的错误的Consumers）
let channelConnecting = false;
let connection = null;
let channel = null;

async function listener() {
    try {
        if (!channelConnecting) {
            channelConnecting = true;
            connection = await amqp.connect(URL);
            channel = await connection.createChannel();
            //  如果Queue不存在，则创建
            await channel.assertQueue(args.queueCallbackQueueName);
            connection.on("error", function () {
                channelConnecting = false;
                setTimeout(listener, MQRetryTime);
            });
            connection.on("close", function () {
                channelConnecting = false;
                setTimeout(listener, MQRetryTime);
            });
        }
    } catch (e) {
        channelConnecting = false;
        setTimeout(listener, MQRetryTime);
    }
}

//	publish message
function sendCallback(data, channel, message) {
    (async () => {
        data = typeof data === 'string' ? data : JSON.stringify(data);
        //	再次确认queue是否存在（如果当前queue已被删除，就无法发送也不会抛异常）
        await channel.assertQueue(args.queueCallbackQueueName);
        try {
            await channel.sendToQueue(args.queueCallbackQueueName, Buffer.from(data),
                {
                    persistent: true
                }
            );
            //	确认消息发送后，再确认consumer消息（如果你们的publisher不是consumer处理完消息后的回调，那么无需ack consumer的消息）
            await channel.ack(message);
        } catch (e) {
        }
    })();
}
```

