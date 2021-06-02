# koa-http-proxy-server
koa2 http-proxy-middleware fixed bodyParser preposition

## Installatiion
```bash
$ npm install koa-http-proxy-server --save
```

## Example
example with koa server
```js
// include dependencies
const Koa=require('koa')
const {createProxyMiddleware} = require('koa-http-proxy-server');

// proxy middleware options
const options = {
  target: 'http://www.example.org', // target host
  changeOrigin: true, // needed for virtual hosted sites
  ws: true, // proxy websockets
  pathRewrite: {
    '^/api/old-path': '/api/new-path', // rewrite path
    '^/api/remove/path': '/path', // remove base path
  },
  router: {
    // when request.headers.host == 'dev.localhost:3000',
    // override target 'http://www.example.org' to 'http://localhost:8000'
    'dev.localhost:3000': 'http://localhost:8000',
  },
};

// create the proxy (without context)
const exampleProxy = createProxyMiddleware('/api',options);

// mount `exampleProxy` in web server
const app = new Koa();
app.use(exampleProxy);
app.listen(3000);
```

## Fix hung up with bodyParser
Currently the only pre-provided request interceptor is fixRequestBody, which is used to fix proxied POST requests when bodyParser is applied before this middleware.

Example:
```js
const bodyParser = require('koa-bodyparser');
const {createProxyMiddleware,fixRequestBody} = require('koa-http-proxy-server');

const exampleProxy = createProxyMiddleware('/api',{
  /**
   * Fix bodyParser
   **/
  onProxyReq: fixRequestBody,
});

app.use(bodyParser())
   .use(exampleProxy)
   .listen(3000);
```
   
## Options
All options are passed to http-proxy-middleware.
See [http-proxy-middleware](https://github.com/chimurai/http-proxy-middleware#options) for full list of accepted options.