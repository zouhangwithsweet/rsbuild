- **类型：** `boolean`
- **默认值：** `false`

是否捕获每个模块的耗时信息，对应 Rspack 的 [profile](https://webpack.js.org/configuration/other-options/#profile) 配置。

### 示例

```js
export default {
  performance: {
    profile: true,
  },
};
```

开启后，Rspack 在生成一些关于模块统计数据的 JSON 文件时，会将模块构建的耗时信息也包含进去。
