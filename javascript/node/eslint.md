# ESlint

- code convention check

## package.json

```json
"devDependencies" {
  "@babel/eslint-parser": "^7.12.16",
  "@vue/cli-plugin-eslint": "~5.0.0",
  "babel-eslint": "^10.1.0",
  "eslint": "^7.32.0",
  "eslint-plugin-vue": "^8.0.3"
}
```
- `eslint`
- `eslint-plugin-vue`
- `@babel/eslint-parser`와 `babel-eslint` 차이

## config

```js
// .eslintrc.js
module.exports = {
  root: true,
  parserOptions: {
    parser: '@babel/eslint-parser',
  },
  env: {
    node: true,
    browser: true,
  },
  extends: [
    // 'plugin:vue/essential',
    'plugin:vue/vue3-essential',
    'eslint:recommended',
  ],
};
```
- `eslint:recommended`
  - able to use eslint rules (ESlint rules API 참고)
- `plugin:vue/essential`
  - Use this if you are using Vue.js 2.x.
- `plugin:vue/vue3-essential`
  - Vue CLI version >= 3.x 

## rules

```js
// .eslintrc.js
rules: {
  'semi': ["error", "always"],
  'indent': ["error", 2],
  // 'no-mixed-spaces-and-tabs': "error",
  'no-unsafe-finally': "error",
  'no-return-assign': "error",
  'space-before-function-paren': ["error", {
    "anonymous": "always", 
    "named": "never", 
    "asyncArrow": "always"
  }]
},
```
- `eslint:recommended` 설정시 사용할 수 있는 rules (https://eslint.org/docs/rules/)