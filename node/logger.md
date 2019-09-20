##### winston日志插件使用

```javascript
const winston = require('winston');
require('winston-daily-rotate-file');
const {format} = require('winston');
const {combine, printf} = format;

//	调整默认的json格式为message
const stringFormat = printf(({message}) => {
    return `${message}`;
});

//  日志相关配置
const logFile = path.join(args.logDir, 'server-%DATE%.log');
const logger = winston.createLogger({
    format: combine(
        stringFormat
    ),
    transports: [
        new (winston.transports.DailyRotateFile)({
            filename: logFile,
            //  日志按天分割
            datePattern: 'YYYY-MM-DD',
            zippedArchive: true,
            //  单个日志最大为20M
            maxSize: '20m',
        })
    ]
});
```

