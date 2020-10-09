# 第十三课 HTTP client
## Fetch
当我们想要发送HTTP请求的时候，我们就需要HTTP client。Javascript最新的HTTP client API就是fetch了。它直接返回promise，可以很方便的使用async/await来处理各种HTTP请求。在浏览器端，javascript可以直接使用fetch API。但在node.js里面，我们需要导入'isomorphic-unfetch'库。选择这个库是因为它在前端（浏览器）和后端（Node.js）都能用。
```javascript
import fetch from 'isomorphic-unfetch';
```
## 使用Web API获取当前汇率
```javascript
import fetch from 'node-fetch';

(async () => {
  const fromCurrency = 'USD'; // 美元
  const toCurrency = 'CNY'; // 人民币

  const res = await fetch(`https://www.alphavantage.co/query?function=CURRENCY_EXCHANGE_RATE&from_currency=${fromCurrency}&to_currency=${toCurrency}&apikey=LAPO12JF1DA7RU3A`);
  console.log(await res.json());
})();
```
这个API相对比较简单。直接把参数填在URL里，然后发出默认的GET请求即可。响应结果是一个JSON字符串。但由于我们使用了fetch的Response.json函数，它已经把JSON字符串转换成javascript对象了。

## 使用Web API获取当地时间
```javascript
import fetch from 'node-fetch';

(async () => {
  try {
    const res = await fetch(`http://api.timezonedb.com/v2/get-time-zone?key=VN4LIJM0J8I6&format=json&by=zone&zone=America/Chicago`);
    if (!res.ok) {
      console.log(res.statusText);
    }
    console.log(await res.json());
  } catch (e) {
    console.log(e);
  }
})();
```
我们用上面的代码就可以获取芝加哥时间。把最后的zone的名字改成"Asia\/Shanghai"就能获取上海的本地时间。想看到所有zone的名字，用下面的URL：http://api.timezonedb.com/v2/list-time-zone?key=VN4LIJM0J8I6&format=json。

TimezoneDB的API文档请参考：<https://timezonedb.com/api>。

[上一课](lesson12.md) &#124; [目录](README.md) &#124; [下一课](lesson14.md)
