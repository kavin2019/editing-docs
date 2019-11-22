## eslint

### 简介

> ESLint 是在 ECMAScript/JavaScript 代码中识别和报告模式匹配的工具，它的目标是保证代码的一致性和避免错误

- ESLint 使用 [Espree](https://github.com/eslint/espree) 解析 JavaScript。
- ESLint 使用 AST 去分析代码中的模式
- ESLint 是完全插件化的。每一个规则都是一个插件并且你可以在运行时添加更多的规则。

### 安装

#### 先决条件

* nodejs(>=6.4)
* npm(version 3+)

### 安装

```shell
$ npm install --save-dev eslint
或
$ npm install -g eslint
#全局安装下时，使用的任何插件或可共享配置都必须安装在本地
```

### 生成配置

```shell
#全局安装情况下：
$ eslint --init
#本地安装后
$./nodes_modules/eslint/bin/eslint. --init
```

运行之后会自动在目录下生成一个.eslintrc.*的文件，文件配置形式如下：

```JSON
module.exports = {
    'env': {
        'browser': true,
        'es6': true,
        'node': true,
        'mocha': true
    },
    'extends': 'eslint:recommended',
    'globals': {
        'Atomics': 'readonly',
        'SharedArrayBuffer': 'readonly',
    },
    'parserOptions': {
        'ecmaVersion': 8
    },
    'rules': {
        'indent': [
            'error',
            4
        ],
        'linebreak-style': [
            'error',
            'unix'
        ],
        'quotes': [
            'error',
            'single'
        ],
        'semi': [
            'error',
            'always'
        ]
    }
};
```



### 配置

> ESlint 被设计为完全可配置的，这意味着你可以关闭每一个规则而只运行基本语法验证，或混合和匹配 ESLint 默认绑定的规则和你的自定义规则

eslint有两种配置方式：

* configuration comments 使用javascript注释把配置信息直接嵌入到一个代码源文件中

* configuration files使用javascript、JSOn或YAML文件为整个目录和它的子目录指定配置信息，可以配置一个独立的.eslintrc.*文件或者直接在package.json文件的`eslintConfig`字段指定配置，再者也可以在命令执行时指定一个任意的配置文件。

  ```json
  //在pacakge.json中配置
  {
      "name": "mypackage",
      "version": "0.0.1",
      "eslintConfig": {
          "env": {
              "browser": true,
              "node": true
          }
      }
  }
  ```
  
  ```shell
  $ eslint -c myconfig.json myfiletotest.js
  #如果你使用一个配置文件，想要 ESLint 忽略任何 .eslintrc.* 文件，请确保使用 --no-eslintrc 的同时，加上 -c 标记
  ```
  
  
  
  PS：主目录下的配置文件，只有ESLint在找不到其它配置文件时才使用
  
  ESLint支持配置文件格式及优先级：
  
  > 1. `.eslintrc.js`
  > 2. `.eslintrc.yaml`
  > 3. `.eslintrc.yml`
  > 4. `.eslintrc.json`
  > 5. `.eslintrc`
  > 6. `package.json`

配置内容：

* **Environment** 指定脚本的运行环境。每种环境都有一组特定的预定义全局变量。
* **Globals** - 脚本在执行期间访问的额外的全局变量
* **Rules** - 启用的规则及其各自的错误级别。

#### Environment

一个环境定义了一组预定义的全局变量。可用的环境包括：

>- `browser` - 浏览器环境中的全局变量。
>- `node` - Node.js 全局变量和 Node.js 作用域。
>- `commonjs` - CommonJS 全局变量和 CommonJS 作用域 (用于 Browserify/WebPack 打包的只在浏览器中运行的代码)。
>- `shared-node-browser` - Node.js 和 Browser 通用全局变量。
>- `es6` - 启用除了 modules 以外的所有 ECMAScript 6 特性（该选项会自动设置 `ecmaVersion` 解析器选项为 6）。
>- `worker` - Web Workers 全局变量。
>- `amd` - 将 `require()` 和 `define()` 定义为像 [amd](https://github.com/amdjs/amdjs-api/wiki/AMD) 一样的全局变量。
>- `mocha` - 添加所有的 Mocha 测试全局变量。
>- `jasmine` - 添加所有的 Jasmine 版本 1.3 和 2.0 的测试全局变量。
>- `jest` - Jest 全局变量。
>- `phantomjs` - PhantomJS 全局变量。
>- `protractor` - Protractor 全局变量。
>- `qunit` - QUnit 全局变量。
>- `jquery` - jQuery 全局变量。
>- `prototypejs` - Prototype.js 全局变量。
>- `shelljs` - ShellJS 全局变量。
>- `meteor` - Meteor 全局变量。
>- `mongo` - MongoDB 全局变量。
>- `applescript` - AppleScript 全局变量。
>- `nashorn` - Java 8 Nashorn 全局变量。
>- `serviceworker` - Service Worker 全局变量。
>- `atomtest` - Atom 测试全局变量。
>- `embertest` - Ember 测试全局变量。
>- `webextensions` - WebExtensions 全局变量。
>- `greasemonkey` - GreaseMonkey 全局变量。

这些环境并不是互斥的，所以你可以同时定义多个。
可以在源文件里、在配置文件中或使用 [命令行](http://eslint.cn/docs/user-guide/command-line-interface) 的 `--env` 选项来指定环境。
要在你的 JavaScript 文件中使用注释来指定环境，格式如下：

```
/* eslint-env node, mocha */
```

如果你想在一个特定的插件中使用一种环境，确保提前在 `plugins` 数组里指定了插件名，然后在 env 配置中不带前缀的插件名后跟一个 `/` ，紧随着环境名。例如：

```
{
    "plugins": ["example"],
    "env": {
        "example/custom": true
    }
}
```

#### Paser Options

ESLint允许用户选择想要支持的js语法，默认情况下，ESLint支持ECMAScript5语法。用户可以覆盖该设置，已启用ECMAScript和jsx的支持。

对ES6的语法：`{ "parserOptions": { "ecmaVersion": 6 }}`

PS:**支持ES6的语法并不意味着支持ES6的全局变量，需要引入es6的环境**`{"env": { "es6": true}`

解析器选项可以在 `.eslintrc.*` 文件使用 `parserOptions` 属性设置。可用的选项有：

- `ecmaVersion` - 默认设置为 3，5（默认）， 你可以使用 6、7、8、9 或 10 来指定你想要使用的 ECMAScript 版本。你也可以用使用年份命名的版本号指定为 2015（同 6），2016（同 7），或 2017（同 8）或 2018（同 9）或 2019 (same as 10)
- `sourceType` - 设置为 `"script"` (默认) 或 `"module"`（如果你的代码是 ECMAScript 模块)。
- ecmaFeatures \- 这是个对象，表示你想使用的额外的语言特性:
  - `globalReturn` - 允许在全局作用域下使用 `return` 语句
  - `impliedStrict` - 启用全局 **strict mode** (如果 `ecmaVersion` 是 5 或更高)
  - `jsx` - 启用 JSX
  - `experimentalObjectRestSpread` - 启用实验性的 [object rest/spread properties](https://github.com/sebmarkbage/ecmascript-rest-spread) 支持。(**重要：**这是一个实验性的功能,在未来可能会有明显改变。 建议你写的规则 **不要** 依赖该功能，除非当它发生改变时你愿意承担维护成本。)

#### Globals

当访问当前源文件内未定义的变量时，[no-undef](http://eslint.cn/docs/rules/no-undef) 规则将发出警告。如果你想在一个源文件里使用全局变量，推荐你在 ESLint 中定义这些全局变量，这样 ESLint 就不会发出警告了。你可以使用注释或在配置文件中定义全局变量

在配置文件中:

```
{
    "globals": {
        "var1": "writable",
        "var2": "readonly"，
        "var3":"off"//关闭全局变量，出现后会提示no-undef错误
    }
}
```

在源文件中：

```
/* global var1, var2 */
或
/* global var1:writable, var2:writable */
```

#### Plugs

ESLint 支持使用第三方插件。在使用插件之前，你必须使用 npm 安装它。插件名称可以省略 `eslint-plugin-` 前缀。

在配置文件中：

```
{
    "plugins": [
        "plugin1",
        "eslint-plugin-plugin2"
    ]
}
```



#### Configuring Rules

ESLint 附带有大量的规则。你可以使用注释或配置文件修改你项目中要使用的规则:

- `"off"` 或 `0` - 关闭规则
- `"warn"` 或 `1` - 开启规则，使用警告级别的错误：`warn` (不会导致程序退出)
- `"error"` 或 `2` - 开启规则，使用错误级别的错误：`error` (当被触发的时候，程序会退出)

在源文件中使用注释配置：

```
/* eslint quotes: ["error", "double"], curly: 2 */
```

在单独的配置文件中配置：

```json
{
    "rules": {
        "eqeqeq": "off",
        "curly": "error",
        "quotes": ["error", "double"]
    }
}
```

如果配置定义一个在插件中的规则时：

```json
{
    "plugins": [
        "plugin1"
    ],
    "rules": {
        "eqeqeq": "off",
        "curly": "error",
        "quotes": ["error", "double"],
        "plugin1/rule1": "error"
    }
}
```



Disable Rules

通过源文件注释来禁止规则：

```javascript
/* eslint-disable */
alert('foo');
/* eslint-enable */
```

你也可以对指定的规则启用或禁用警告:

```javascript
/* eslint-disable no-alert, no-console */
alert('foo');
console.log('bar');
/* eslint-enable no-alert, no-console */
```

如果在整个文件范围内禁止规则出现警告，将 `/* eslint-disable */` 块注释放在文件顶部：

```javascript
/* eslint-disable */
alert('foo');
```

你也可以对整个文件启用或禁用警告:

```javascript
/* eslint-disable no-alert */
alert('foo');
```

可以在你的文件中使用以下格式的行注释或块注释在某一特定的行上禁用所有规则：

```javascript
alert('foo'); // eslint-disable-line
// eslint-disable-next-line
alert('foo');
```

在某一特定的行上禁用某个指定的规则(一个或多个)：

```javascript
alert('foo'); // eslint-disable-line no-alert, quotes, semi
// eslint-disable-next-line no-alert, quotes, semi
alert('foo');
```

在配置文件中禁用，使用overrides和files：

```javascript
{
  "rules": {...},
  "overrides": [
    {
      "files": ["*-test.js","*.spec.js"],
      "rules": {
        "no-unused-expressions": "off"
      }
    }
  ]
}
```



#### extends

Using eslint:recommended

值为 `"eslint:recommended"` 的 `extends` 属性启用一系列核心规则，这些规则报告一些常见问题，在 [规则页面](http://eslint.cn/docs/rules/) 中被标记为 绿色勾 。这个推荐的子集只能在 ESLint 主要版本进行更新。

如果你的配置集成了推荐的规则：在你升级到 ESLint 新的主版本之后，在你使用[命令行](http://eslint.cn/docs/user-guide/command-line-interface#fix)的 `--fix` 选项之前，检查一下报告的问题，这样你就知道一个新的可修复的推荐的规则将更改代码

### Using a shareable configuration package

`shareable configuration package`是一个 npm 包，它输出一个配置对象。要确保这个包安装在 ESLint 能请求到的目录下。

例如：

```shell
$ npm i eslint-config-standard --save-dev
```

之后在配置文件使用：

```json
{
  extends:'standard'
}
```

### Using the configuration from a plugin

[插件](https://cn.eslint.org/docs/developer-guide/working-with-plugins) 是一个 npm 包，通常输出规则。一些插件也可以输出一个或多个命名的 [配置](https://cn.eslint.org/docs/developer-guide/working-with-plugins#configs-in-plugins)。要确保这个包安装在 ESLint 能请求到的目录下。

例如：

```json
{
    "plugins": [
        "react"
    ],
    "extends": [
        "eslint:recommended",
        "plugin:react/recommended"
    ],
    "rules": {
       "no-set-state": "off"
    }
}
```

### Using a configuration file

使用本地的配置文件：

```json
{
    "extends": [
        "./node_modules/coding-standard/eslintDefaults.js",
        "./node_modules/coding-standard/.eslintrc-es6",
        "./node_modules/coding-standard/.eslintrc-jsx"
    ],
    "rules": {
        "eqeqeq": "warn"
    }
}
```

### Using `"eslint:all"`

这个选项可以让eslint启用所有核心规则。核心规则集可以在ESLint的任何次要或主要版本上更改。