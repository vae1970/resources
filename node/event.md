##### event

node的异步依赖于event，所以需要学习event

```javascript
const EventEmitter = require('events').EventEmitter;
const event = new EventEmitter();

//	为事件注册监听器
event.on('test event', function () {
    console.log('event received');
});

//	发送事件
event.emit('test event');
```

