##### 引包

```javascript
const puppeteer = require('puppeteer-core');
```

##### chrome 配置

```javascript
const puppeteerOptions = {
    executablePath: executablePath,
    args: ['--no-sandbox', '--disable-setuid-sandbox'],
    ignoreDefaultArgs: ['--disable-extensions'],
    //  开启则显示chrome界面和devtools    
    devtools: false
};
```

##### puppeteer连接chrome

```javascript
//	首次启动
puppeteer.launch(puppeteerOptions);
//	重新连接
puppeteer.connect({browserWSEndpoint});
```

##### 其他操作

```javascript
//	打开新的chrome标签页
const page = await browser.newPage();
//	访问并渲染页面（dom加载完，所有请求都加载完）
await page.goto(url, {waitUntil: ['domcontentloaded', 'networkidle0']});
//	把页面打印为PDF
await page.pdf({path: path, format: 'A4'});
//	等待指定元素加载出来
const dom = await page.$('html');
```

