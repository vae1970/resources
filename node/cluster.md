##### 多进程实现

```javascript
//	指定work进程数
const maxProcess = 8;
const cluster = require('cluster');
if (cluster.isMaster) {
	//	主线程执行的代码
    //	修改主进程在ps命令下显示的进程名称
    process.title = "pdf_render_main_process";
    for (let i = 0; i < maxProcess; i++) {
        cluster.fork();
    }
} else {
	//	work进程执行的代码
    //	获取当前work进程是第几个
    const index = cluster.worker.id;  
}
```

