# ESlint

- code convention check

<br>

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

> As of March 2020, babel-eslint has been deprecated, and is now @babel/eslint-parser.

- `babel-eslint`(deprecated) > `@babel/eslint-parser` 

<br>

## ESlint Config

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
    //...,
    'eslint:recommended',
  ],
};
```
- `root`
  - 특정 프로젝트로 ESlint 범위를 설정하고자 할 때
  - `true`: project root level 위치에서 ESlint 범위 설정 
- `eslint:recommended`
  - able to use eslint rules (ESlint rules API 참고)
- `env`
  - provides predefined global variables
  - `browser`: console 같은 전역변수 사용가능
  - `node`: require 같은 예약어 사용 가능


### rules

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

<br>

## Vue ESlint

- https://eslint.vuejs.org/

```js
extends: [
  // 'plugin:vue/essential',
  'plugin:vue/vue3-essential',
]
```
- `plugin:vue/essential`
  - Use this if you are using Vue.js 2.x.
- `plugin:vue/vue3-essential`
  - Vue CLI version >= 3.x 

### rules for vue cli ESlint

```js
// .eslintrc.js
rules: {
  /* plugin:vue/vue3-essential */ 
  'vue/html-closing-bracket-newline': ["error", {
    "singleline": "never",
    "multiline": "always"
  }],
  'vue/html-closing-bracket-spacing': ["error"],
  'vue/html-end-tags': ["error"],
  'vue/html-indent': ["error", 2],
  'vue/html-quotes': ["error"],
  'vue/html-self-closing': ["error", {
    "html": {
      "void": "always",
      "normal": "never"
    }
  }
}
```
- vue cli eslint rules API ([참고링크](https://eslint.vuejs.org/rules/))