# 第十四课 HTTP server
在很久很久以前，javascript只能用来编写动态网页，因此只能在浏览器内运行。后来有了node.js，javascript于是就前后端都能写了。再到后来，有了electron，javascript开始能写桌面应用程序了。后来又出现了React Native，javascript可以写手机应用了。现在，有了nodemcu，javascript连嵌入式都占领了。这一课，给大家介绍如何用javascript写web服务端应用。

1. ## KOA
    KOA是一款领先的，优秀的web服务端框架。它轻，小，快，而且使用方便，可以迅速搭建起一个小型的服务端应用。而且它还有大量的中间件，可以支持丰富的功能。
    ```javascript
    import Koa from 'koa';
    
    const app = new Koa();
    app.use(async ctx => {
      ctx.body = 'Hello World';
    });
    
    const port = 3000;
    app.listen(port, () => console.log(`listening port ${port}...`));
    ```
    上面就是一个最简单的服务端应用，只有寥寥数行。启动该服务之后，在浏览器地址栏里敲入"localhost:3000"，然后回车，就能够看到"Hello World"的字样。
    
    如果我们希望写一个Web API，那么我们必须返回JSON。
    ```javascript
    import Koa from 'koa';
    
    const app = new Koa();
    app.use(async ctx => {
      ctx.body = {
        time: Date.now(),
        bookname: 'The thin javascript',
        bookclass: 'Computer'
      };
      ctx.type = 'application/JSON';
    });
    const port = 3000;
    app.listen(port, () => console.log(`listening port ${port}...`));
    ```
    我们不需要把对象转换成JSON，再赋值给ctx.body。直接把对象赋值给ctx.body，KOA会帮我们把对象转换成JSON。当然，在真实的服务器应用中，这样的对象是通过查询各种数据库（MySQL, MongoDB等）后计算出来的。
    
    如果要做一个小网站，那么返回HTML即可。
    ```javascript
    import Koa from 'koa';
    
    const app = new Koa();
    app.use(async ctx => {
      ctx.body = `
        <!DOCTYPE HTML>
        <html>
          <head>
            <title>The thin javascript</title>
          </head>
          <body><h1>Hello World</h1></body>
        </html>
      `;
      ctx.type = 'text/html';
    });
    
    const port = 3000;
    app.listen(port, () => console.log(`listening port ${port}...`));
    ```

1. ## 静态服务器
    我们不需要把网页字符串都放到代码里。这样的字符串应该写成一个文件，然后用静态服务器中间件把文件通过http开放出去。
    ```HTML
    <!-- ./static/index.html -->
    <!DOCTYPE HTML>
    <html>
      <head>
        <title>The thin javascript</title>
      </head>
      <body><h1>Hello World</h1></body>
    </html>
    ```
    ```javascript
    // app.mjs
    import Koa from 'koa';
    import serve from 'koa-static';
    
    const app = new Koa();
    app.use(serve('./static'));
    const port = 3000;
    app.listen(port, () => console.log(`listening port ${port}...`));
    ```

1. ## Router
    一个网站/API不会只有根目录可以访问。我们经常看到的是，同一个网站支持好几个页面/API。它们通过URL来做区分。这就需要用到路由中间件。
    ```javascript
    import Koa from 'koa';
    import Router from 'koa-router';
    
    const app = new Koa();
    var router = new Router();
    
    router.get('/', async ctx => {
      ctx.body = '/';
    });
    router.get('/a', async ctx => {
      ctx.body = '/a';
    });
    router.get('/b', async ctx => {
      ctx.body = '/b';
    });
    app.use(router.routes());
    
    const port = 3000;
    app.listen(port, () => console.log(`listening port ${port}...`));
    ```
    在浏览器地址栏输入localhost:3000, localhost:3000/a, localhost:3000/b，你会看到不同的结果。
    
    Router还支持URL的路径作为参数。
    ```javascript
    import Koa from 'koa';
    import Router from 'koa-router';
    
    const app = new Koa();
    var router = new Router();
    
    router.get('/', async ctx => {
      ctx.body = '/';
    });
    router.get('/a', async ctx => {
      ctx.body = '/a';
    });
    router.get('/b/:id', async ctx => {
      ctx.body = `path=/b; id=${ctx.params.id}`;
    });
    app.use(router.routes());
    
    const port = 3000;
    app.listen(port, () => console.log(`listening port ${port}...`));
    ```
    在KOA router里，冒号开头的路径表示命名参数，冒号之后的内容就是参数名。在上面的例子中，路径名是'/b/:id'，参数名就是id。在代码中，URL路径参数被捕获在ctx.params中，因此我们用ctx.params.id来获取这个参数的值。在浏览器地址栏里输入localhost:3000/b/123试试看吧。

[上一课](lesson13.md) &#124; [目录](README.md) &#124; [下一课](appendixA.md)
