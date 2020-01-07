# Koa2

## 简介

基于 node.js，使用 ES2017 async 函数的 HTTP 中间件框架。

### 关键词

- async

- middleware

### 特点

Koa 的中间件栈以类似堆栈的方式流动，允许你对 HTTP 请求和响应进行过滤等操作。

只有对几乎所有 HTTP 服务器都通用的方法才会直接集成到 Koa 的代码库中。包括内容协商、节点不一致的规范化、重定向等。

Koa 没有预先绑定任何中间件。

### 中间件 

Koa 可以接收两类函数作为中间件：

- async 函数
- 普通函数

```javascript
// async 函数
app.use(async (ctx, next) => {
  const start = Date.now();
  await next();
  const ms = Date.now() - start;
  console.log(`${ctx.method} ${ctx.url} - ${ms}ms`);
})
```

```javascript
// 普通函数

// 中间件默认接收两个参数 (ctx, next)，ctx 是请求的上下文，
// next 是一个函数，它调用下一个中间件，返回一个 Promise

app.use((ctx, next) => {
  const start= Date.now();
  return next().then(() => {
    const ms = Date.now() - start;
    console.log(`${ctx.method} ${ctx.url} - ${ms}ms`);
  })
})
```

## 安装

为了支持 ES2015 语法 和 async 函数，Koa 需要 node 版本为 v7.6.0 或更高。

```bash
npm install koa
```

## 应用

一个 Koa 应用是一个包含很多中间件函数的对象。

```javascript
cosnt Koa = require('koa');
const app = new Koa();

app.use((ctx, next) => {
  ctx.body = 'Hello Koa';
});

app.listen(3000);
```

### 级联

Koa 的中间件以一种传统的方式级联。级联连接的实现是，通过传入控制的系列函数直到遇见一个 return。Koa 调用下游，然后控制流返回上游。

### 设置

应用的设置是 app 实例的属性，目前支持以下属性：

- app.env 默认为 NODE_ENV 或 “development”
- app.proxy 当真正的代理报头字段将被信任
- app.subdomainOffset .subdomains的偏移量以忽略[2]

### app.listen()

Koa 应用不是HTTP 服务器的 1 对 1 表示 。可以将一个或多个 Koa 可以挂载到一起去，形成使用单个HTTP 服务器的大型应用程序。

下面是一个绑定到 3000 端口的 Koa 应用：

```javascript
const Koa = require('koa');
const app = new Koa();
app.listen(3000);
```

app.listen(...) 方法是下面的语法糖：

```javascript
const http = require('http');
const Koa = require('koa');
const app = new Koa();
http.createServer(app.callback()).listen(3000);
```

这意味着你可以在多个地址同时启动 HTTP 和 HTTPS 或者相同的应用：

```javascript
const http = require('http');
const https = require('https');
const Koa = require('koa');
const app = new Koa();
http.createServer(app.callback().listen(3000));
https.createServer(app.callback().listen(3001));
```

### app.callback()

返回一个回调函数，该函数适用于处理请求的 http.createServer()方法。你也可以使用这个回调函数将 Koa 应用挂载到 Connet/Express 应用中。

### app.use(function)

给应用注册中间件。

### app.keys=

设置签名 cookie 秘钥。

这些被传递给 KeyGrip，但是你也可以传递你自己的 KeyGrip 实例。

```javascript
app.keys = ['im a newer secret', 'i like turtle'];
app.keys = new KeyGrip(['im a newer secret', 'i like turtle'], 'sha256');
```

当设置了 `{signed: true}` 这些 key 可以被旋转和使用：

```javascript
ctx.cookies.set('name', 'tobi', {signed: true});
```

### app.context

`app.context` 是创建 `ctx` 的原型。你可以通过编辑 `app.context`  给 `ctx` 添加额外的属性。当你需要给整个应用添加属性或者方法时非常有用，这些属性或方法可能更有性能（没有中间件）和更简单（更少的需求），但代价是更多地依赖 `ctx` ，这可能被认为是一种反模式。

例如，你可以通过 `ctx` 对你的数据库添加一个应用：

```javascript
app.context.db = db();

app.use(async ctx => {
  console.log(ctx.db);
})
```

### 错误处理

默认情况下，除非 `app.silent` 为 `true`，否则将所有错误输出到 `stderr`。默认错误处理器在 `err.status` 为 404 或 `err.expose` 为 `true` 时不会报错。运行自定义的错误处理逻辑如集中处理日志，你可以添加一个 “error” 事件监听器：

```javascript
app.on('error', err => {
  log.error('server error', err);
});
```

如果错误发生在 req/res 循环中，并且无法响应客户端，`Context` 实例任会被传递过去：

```javascript
app.on('error', (err, ctx) => {
  log.error('server error', err, ctx);
})
```

当错误发生时，仍然可以响应客户端，但没有数据被写入套接字，Koa 将响应 500 服务器内部错误。在这两种情况下，都会发出一个 app-level 的“error”，用于日志记录。

## Context

Koa Context 封装了 node 的 `request` 和 `response` 对象到一个单独的对象。它提供了许多 Web 应用的方法和 APIs。

`Context` 被每个请求创建，在中间件被引用为接收方，或者 `ctx` 标识符：

 ```javascript
app.use(async ctx => {
  ctx; // is the Context
  ctx.request; // is a Koa Request
  ctx.response; // is a Koa Response
});
 ```

许多上下文的访问器和方法委托了它们的 `ctx.request` 或 `ctx.response`。例如 `ctx.type` 和 `ctx.length` 委托了 `response` 对象，`ctx.path` 和 `ctx.method` 委托了 `request` 对象。

### API

`Context` 专用方法和访问器。

#### ctx.req

Node 的 `request` 对象。

#### ctx.res

Node 的 `response` 对象。

不支持绕过 Koa 的响应处理。避免使用以下 node 属性和方法：

- res.statusCode
- res.writeHead()
- res.write()
- res.end()

#### ctx.request

Koa `Request` 对象。

#### ctx.response

Koa 的 `Response` 对象。

#### ctx.state

通过中间件传递信息和你的前端视图的推荐命名空间。

```javascript
ctx.state.user = await User.find(id);
```

#### ctx.app

应用实例引用。

#### ctx.app.emit

Koa 应用程序拓展了一个内部 EventEmitter。

#### ctx.cookies.set(name, value[, options])

可以选择的 `options`：

- maxAge
- signed
- expires
- path，默认为“/”
- domain
- secure
- httpOnly，默认为“true”
- overwrite，是否覆盖同名 cookie，默认为“false”

#### ctx.cookie.get(name[, options])

获取 cookie 填的 `options`:

- signed 请求的 cookie 应该被签名

#### ctx.throw([status], [msg], [properties])

辅助函数，可以抛出一个 status 默认为 500 的错误，这将允许 Koa 做出适当的响应。

```javascript
ctx.throw(400);
ctx.throw(400, 'name required');
ctx.throw(400, 'name required', {user: user});
```

例如：`ctx.throw(400, 'name required')` 等价于：

```javascript
const err = new Error('name required');
err.status = 400;
err.expose = true;
throw err;
```

请注意，这些是用户级错误，并使用 err.expose 意味着消息适合于客户端响应，但通常不适用于错误消息，因为你不想泄漏故障细节。

Koa 使用 [http-errors](https://github.com/jshttp/http-errors) 来创建错误。状态只能作为第一个参数传递。

#### ctx.assert(value, [status], [msg], [properties])

用于抛出错误的辅助方法，当 `value` 取反时类似于 `.thow()`。与 node 的 assert() 方法相同。

```javascript
ctx.assert(ctx.state.user, 401, 'Usesr not found. Please login');
```

本方法 Koa 使用 [http-assert](https://github.com/jshttp/http-assert)。

#### ctx.respond

将 `ctx.respnd = false` 时，你可以绕过 Koa 内置的响应处理。

请注意，Koa 不支持使用此方法。它可能会导致潜在的错误。

### Request 别名

- `ctx.header`
- `ctx.headers`
- `ctx.method`
- `ctx.method=`
- `ctx.url`
- `ctx.url=`
- `ctx.originalUrl`
- `ctx.origin`
- `ctx.href`
- `ctx.path`
- `ctx.path=`
- `ctx.query`
- `ctx.query=`
- `ctx.querystring`
- `ctx.querystring=`
- `ctx.host`
- `ctx.hostname`
- `ctx.fresh`
- `ctx.stale`
- `ctx.socket`
- `ctx.protocol`
- `ctx.secure`
- `ctx.ip`
- `ctx.ips`
- `ctx.subdomains`
- `ctx.is()`
- `ctx.accepts()`
- `ctx.acceptsEncodings()`
- `ctx.acceptsCharsets()`
- `ctx.acceptsLanguages()`
- `ctx.get()`

### Response 别名

- `ctx.body`
- `ctx.body=`
- `ctx.status`
- `ctx.status=`
- `ctx.message`
- `ctx.message=`
- `ctx.length=`
- `ctx.length`
- `ctx.type=`
- `ctx.type`
- `ctx.headerSent`
- `ctx.redirect()`
- `ctx.attachment()`
- `ctx.set()`
- `ctx.append()`
- `ctx.remove()`
- `ctx.lastModified=`
- `ctx.etag=`

## Request

Koa 的 `Request` 对象是 node 请求对象的上层抽象，提供了 HTTP 服务器开发的常用辅助函数。

## Response

Koa 的 `Response` 对象是 node 请求对象的上层抽象，提供了 HTTP 服务器开发的常用辅助函数。

### API

#### response.status=

- 100 "continue"
- 101 "switching protocols"
- 102 "processing"
- 200 "ok"
- 201 "created"
- 202 "accepted"
- 203 "non-authoritative information"
- 204 "no content"
- 205 "reset content"
- 206 "partial content"
- 207 "multi-status"
- 208 "already reported"
- 226 "im used"
- 300 "multiple choices"
- 301 "moved permanently"
- 302 "found"
- 303 "see other"
- 304 "not modified"
- 305 "use proxy"
- 307 "temporary redirect"
- 308 "permanent redirect"
- 400 "bad request"
- 401 "unauthorized"
- 402 "payment required"
- 403 "forbidden"
- 404 "not found"
- 405 "method not allowed"
- 406 "not acceptable"
- 407 "proxy authentication required"
- 408 "request timeout"
- 409 "conflict"
- 410 "gone"
- 411 "length required"
- 412 "precondition failed"
- 413 "payload too large"
- 414 "uri too long"
- 415 "unsupported media type"
- 416 "range not satisfiable"
- 417 "expectation failed"
- 418 "I'm a teapot"
- 422 "unprocessable entity"
- 423 "locked"
- 424 "failed dependency"
- 426 "upgrade required"
- 428 "precondition required"
- 429 "too many requests"
- 431 "request header fields too large"
- 500 "internal server error"
- 501 "not implemented"
- 502 "bad gateway"
- 503 "service unavailable"
- 504 "gateway timeout"
- 505 "http version not supported"
- 506 "variant also negotiates"
- 507 "insufficient storage"
- 508 "loop detected"
- 510 "not extended"
- 511 "network authentication required"

---

文档：

[koajs.com](https://koajs.com/)

[Github](https://github.com/koajs/koa)