- **Type:** `Record<string, string> | Record<string, ProxyDetail>`
- **Default:** `undefined`

Proxying some URLs.

```js
export default {
  dev: {
    proxy: {
      '/api': 'http://localhost:3000',
    },
  },
};
```

A request to /api/users will now proxy the request to http://localhost:3000/api/users.

If you don't want /api to be passed along, we need to rewrite the path:

```js
export default {
  dev: {
    proxy: {
      '/api': {
        target: 'http://localhost:3000',
        pathRewrite: { '^/api': '' },
      },
    },
  },
};
```

The DevServer Proxy makes use of the [http-proxy-middleware](https://github.com/chimurai/http-proxy-middleware/tree/2.x) package. Check out its documentation for more advanced usages.

The full type definition of DevServer Proxy is:

```ts
import type { Options as HttpProxyOptions } from 'http-proxy-middleware';

type ProxyDetail = HttpProxyOptions & {
  bypass?: (
    req: IncomingMessage,
    res: ServerResponse,
    proxyOptions: ProxyOptions,
  ) => string | undefined | null | false;
  context?: string | string[];
};

type ProxyOptions =
  | Record<string, string>
  | Record<string, ProxyDetail>
  | ProxyDetail[]
  | ProxyDetail;
```

In addition to the http-proxy-middleware option, we also support the bypass and context configuration:

- bypass: bypass the proxy based on the return value of a function.
  - Return `null` or `undefined` to continue processing the request with proxy.
  - Return `false` to produce a 404 error for the request.
  - Return a path to serve from, instead of continuing to proxy the request.
- context: If you want to proxy multiple, specific paths to the same target, you can use an array of one or more objects with a context property.

```js
// custom bypass
export default {
  dev: {
    proxy: {
      '/api': {
        target: 'http://localhost:3000',
        bypass: function (req, res, proxyOptions) {
          if (req.headers.accept.indexOf('html') !== -1) {
            console.log('Skipping proxy for browser request.');
            return '/index.html';
          }
        },
      },
    },
  },
};
```

```js
// proxy multiple
export default {
  dev: {
    proxy: [
      {
        context: ['/auth', '/api'],
        target: 'http://localhost:3000',
      },
    ],
  },
};
```
