##### 获取命令行传递的参数

当程序通过命令行传递参数的时候，需要做以下操作

```javascript
//	启动程序
node example/parse.js -a beep -b boop
//	获取参数，并转化为对象
const args = require('minimist')(process.argv.slice(2));
//	断言，强制传入参数a，否则跑出异常
const assert = require('assert');
assert(args.a !== undefined, 'can\'t find symbol \'maxProcess\'');
```

##### 数组转string

```javascript
const data = [];
const dataString = Array.prototype.join.call(data);
```

##### 获取时间

```javascript
//	formate过的时间字符串
new Date().toLocaleString();
//	unix时间戳
Math.round(new Date().getTime() / 1000);
```

##### 手动抛出异常

```javascript
throw new Error("exception")
```

##### 获取指定文件的文件夹

```javascript
const path = require('path');
const dir = path.dirname(arg.path);
```

##### 创建文件夹

```
//	同步创建文件夹
const fs = require('fs');
if (!fs.existsSync(dir)) {
    try {
        fs.mkdirSync(dir);
    } catch (e) {
        // 有可能存在并发生成文件夹的异常，可以忽略
    }
}
```

##### 拼接文件路径

```javascript
const path = require('path');
path.join(args.logDir, 'abc.pdf');
```

##### json与jsonString互转

```javascript
//	jsonString转Json
const message = '';
const arg = JSON.parse(message);
//	json转jsonString
const data = {};
const str = JSON.stringify(data)
```

