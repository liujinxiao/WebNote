## Egg知识整理系列(一)

### Koa

Koa是由Express框架原班人马打造的全新web框架，与Express风格类似，底层都是沿用同一套Http基础库，但是有几项显著差别：

- Middleware洋葱圈模型：Koa中间件采用洋葱圈模型，所有的请求经过中间件都会执行两次，可以非常方便的实现后置处理逻辑；
- Context：与Express中只有Requese和Response不同，Koa增加了Context作为上下文对象，可以在请求中将相关字段挂载在Context上。
- 异常处理：用户可以很方便的自定义错误处理中间件放在其他中间件之前，就可以捕获到异步或同步代码发生的错误。如：
```shell
    async function onerror(ctx, next) {
        try {
            await next();
        } catch (err) {
            ctx.app.emit('error', err);
            ctx.body = 'server error';
            ctx.status = err.status || 500;
        }
    }
```

### Egg继承Koa

众所周知，Koa框架是一个极简框架扩展性非常强，而Egg在此基础上做了增强。

##### 扩展
在Egg框架中，可以通过定义`app/extend/{application,context,request,response}.js`来扩展Koa框架中四个框架的原型，如在`app/extend/context.js`中定义如下代码：
```shell
module.exports = {
    get isXXX() {
        return xxx;
    }
}
```
在controller.js中，可以利用刚才的定义：
```shell
exports.handler = ctx => {
  ctx.body = ctx.isXXX ? 'is xxx' : 'is not xxx';
};
```

#### 插件

Egg提供了更加强大的插件机制，一个插件可以包含：

- extend: 扩展基础对象的上下文，提供各种工具类、属性。
- middleware: 增加一个或多个中间件，提供请求的前置、后置处理逻辑。
- config: 配置各个环境下插件自身的默认配置项。