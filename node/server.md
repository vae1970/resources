##### 启动server

```
const http = require('http');
const url = require('url');
currentPort = 8080;
await http.createServer((request, response) => {
    //	url传参
    const arg = url.parse(request.url, true).query;
}).listen(currentPort);
```

