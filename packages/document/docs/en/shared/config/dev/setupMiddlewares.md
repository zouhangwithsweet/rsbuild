- **Type:**

```js
Array<
  (
    middlewares: {
      unshift: (...handlers: RequestHandler[]) => void;
      push: (...handlers: RequestHandler[]) => void;
    },
    server: {
      sockWrite: (
        type: string,
        data?: string | boolean | Record<string, any>,
      ) => void;
    },
  ) => void
>;
```

- **Default:** `undefined`

Provides the ability to execute a custom function and apply custom middlewares.

The order among several different types of middleware is: `unshift` => internal middlewares => `push`.

```js
export default {
  dev: {
    setupMiddlewares: [
      (middlewares, server) => {
        middlewares.unshift((req, res, next) => {
          next();
        });

        middlewares.push((req, res, next) => {
          next();
        });
      },
    ],
  },
};
```

It is possible to use some server api to meet special scenario requirements:

- sockWrite. Allow send some message to hmr client, and then the hmr client will take different actions depending on the message type. If you send a "content changed" message, the page will reload.

```js
export default {
  dev: {
    setupMiddlewares: [
      (middlewares, server) => {
        // add custom watch & trigger page reload when change
        watcher.on('change', (changed) => {
          server.sockWrite('content-changed');
        });
      },
    ],
  },
};
```
