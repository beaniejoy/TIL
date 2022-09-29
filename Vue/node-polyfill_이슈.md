# node-polyfill 이슈

<br>

## 상황
- kakao api 사용을 위해 `kakao.min.js` 파일 import
- 여기서 개발환경 실행시 다음과 같은 warning 표시

```bash
Module not found: Error: Can't resolve 'crypto' in '../kakao-api-test/kakao-web/src/utils'

BREAKING CHANGE: webpack < 5 used to include polyfills for node.js core modules by default.
This is no longer the case. Verify if you need this module and configure a polyfill for it.

If you want to include a polyfill, you need to:
        - add a fallback 'resolve.fallback: { "crypto": require.resolve("crypto-browserify") }'
        - install 'crypto-browserify'
If you don't want to include a polyfill, you can use an empty module like this:
        resolve.fallback: { "crypto": false }
```
- 내용에는 webpack 5버전 미만에서는 polyfills를 포함해서 사용했지만 5버전부터는 포함하지 않는다.
- polyfills 모듈이 필요하면 직접 설정해야 한다. 그런 내용

<br>

## 해결
- [stackoverflow에서 해결책을 제시해줌](https://stackoverflow.com/questions/68206050/breaking-change-webpack-5-used-to-include-polyfills-for-node-js-core-modules)
- [node-polyfill-webpack-plugin](https://www.npmjs.com/package/node-polyfill-webpack-plugin) 플러그인으로 해결 가능

```console
$ npm install node-polyfill-webpack-plugin
```

```js
//...
const NodePolyfillPlugin = require("node-polyfill-webpack-plugin");
//...
module.exports = defineConfig({
  //...
  configureWebpack: {
    plugins: [
      new NodePolyfillPlugin()
    ]
  },
  //...
});
```
- `vue.config.js`파일에 위와 같이 `node-polyfill-webpack-plugin` 내용을 설정하면 된다.