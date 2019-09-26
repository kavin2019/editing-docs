## 断言库之assert

Assert是nodejs自带的一个断言库，其优点是方便快捷。不需要安装额外的包，而且能配合mocha测试框架使用。

`assert`模块提供了一组简单的断言测试，可用于测试不变式

assert有strict（严格）和legacy（遗留/旧版）两种模式，建议使用strict模式

### 类：assert.AssertError

该类是Error的子类，表示断言失败，所有的Assert的断言失败后实际上都是抛出这个错误。

构造函数：`assert.AssertError(options)`

`options`:

1. message <string> 如果提供，则错误消息被设置为此值。
2. actual <any> 错误实例包含该属性。一般由断言发生错误时，断言内部提供。
3. expected <any> 错误实例包含该属性，一般由断言内部提供
4. operator <string> 错误实例包含该属性,一般用于区分哪个断言发生错误
5. stackStartFn <Function> 如果提供，那么从堆底到提供的stackStartFn中间的帧全部删除



AssertError包含Error的所有属性（message和name），而且额外包含：

1. `actual` <any> 
2. `expected` <any>
3. `generatedMessage` <boolean>指示消息是否是自动生成的（true）
4. `code` <string> 始终将其设置为字符串`ERR_ASSERTION`以指示该错误实际上是断言错误
5. `operator` <string> 设置为传入的运算符值

例如：

```javascript
it('assert.AssertError', function(){
        const { message } = new assert.AssertionError({
            actual: 1,
            expected: 2,
            operator: 'strictEqual'
        });

        try{
            assert.strictEqual(1,2);
        } catch (err) {
            assert(err instanceof assert.AssertionError);
            assert.strictEqual(err.message, message);
            assert.strictEqual(err.name, 'AssertionError [ERR_ASSERTION]');
            assert.strictEqual(err.actual, 1);
            assert.strictEqual(err.expected, 2);
            assert.strictEqual(err.code, 'ERR_ASSERTION');
            assert.strictEqual(err.operator, 'strictEqual');
            assert.strictEqual(err.generatedMessage, true);
        }

    });
```



### 严格模式

在严格模式下，任何assert的断言函数都将严格使用相等性，因此，严格模式下的`assert.deepEqual()`等价于`assert.deepStrictEqual()`

```javascript
var assert = require('assert').strict;
```

### 旧版模式

*不建议使用，建议使用严格模式*

在该模式下 `抽象平等比较`将用于所有名称中没有严格限制的任何函数，例如： `assert.deepEqual()`

```javascript
var assert = require('assert')	
```



### assert(value [, message])

value转换为boolean值后是否为true

参数：

* value <any> 	被检查的实际值
* message <string>|<error>

别名：assert.ok();

例如：

```javascript
it('#assert()', function(){
        assert(true);
        assert([]);
        assert('a');
        assert({});
        assert(1);

        assert.throws(() => { assert(false) });
        assert.throws(() => { assert(0) });
        assert.throws(() => { assert('') });
        assert.throws(() => { assert(undefined) });
        assert.throws(() => { assert(null) });
    });
```

### assert.deepEqual(actual, expected [, message])

等价于将原始值和期望值通过抽象比较（ `==`）进行比较。

PS：对于此类比较优先考虑assert.deepStrictEqual,因为assert.deepEqual可能带来意外的结果

参数：

* actual <any>
* expected <any>
* message <string>|<error>

严格模式：

assert.deepStrictEqual()

例如：

```javascript
it('#deepEqual()', function(){
        const obj1 = {
            a: 1,
            b: {
                c: 1
            }
        };
        const obj2 = {
            a: 1,
            b: {
                c: 2
            }
        };
        const obj3 = {
            a: 1,
            b: {
                c: '1'
            }
        };
  			//新建对象{}
        const obj4 = Object.create(obj1);

        assert.deepEqual(1, '1');
        assert.deepEqual(!0, 1);
        assert.deepEqual(!1, '');
        assert.deepEqual(!1, 0);
        assert.deepEqual(obj1, obj1);
        assert.deepEqual(obj1, obj3);
        
        assert.throws(() => { assert.deepEqual(!1, undefined); });
        assert.throws(() => { aassert.deepEqual(!1, null); });
        assert.throws(() => { assert.deepEqual(obj1, obj2) });
        assert.throws(() => { assert.deepEqual(obj1, obj4) });
    });
```



### assert.deepStrictEqual(actual, expected [, message])

测试`actual`和`expected`参数之间的深度相等性。“深层”相等意味着子对象的可枚举“自身”属性也将通过以下规则进行递归求值。

参数：

- actual <any>
- expected <any>
- message <string>|<error>

比较规则：

- 使用Object.is()判定两个值是否是具有相同的值。
- 对象的的类型必须相同。
- [[Prototype]]也使用严格相等比较(`===`)对对象进行比较。
- 对象比较仅考虑可对属性枚举和遍历的。
- `Error` 始终比较名称和消息，即使它们不是不可枚举的属性也是如此。
- `Symbol`还比较了可枚举的自身属性。
- `将对象包装器`作为对象和未包装的值进行比较。
- `Object` 属性进行无序比较。
- `Map`键和`Set`项目进行无序比较。
- 当双方不同或双方遇到环时参考时，递归停止。
- `WeakMap`而`WeakSet`比较不依赖于它们的值。

例如：

```javascript
it('#deepStrictEqual()', function(){
        assert.deepStrictEqual({ a: 1}, {a:1});
        assert.throws(() => { assert.deepStrictEqual({ a: 1 }, { a: '1' }); });

        // The following objects don't have own properties
        const date = new Date();
        const object = {};
        const fakeDate = {};
        Object.setPrototypeOf(fakeDate, Date.prototype);
        // Different [[Prototype]]:
        assert.throws(() => { assert.deepStrictEqual(object, fakeDate)});
        // Different type tags:
        assert.throws(() => { assert.deepStrictEqual(date, fakeDate) });

        // OK, because of the SameValue comparison
        assert.deepStrictEqual(NaN, NaN);
        // Different unwrapped numbers:
        assert.throws(() => { assert.deepStrictEqual(new Number(1), new Number(2)) });

        // OK because the object and the string are identical when unwrapped.
        assert.deepStrictEqual(new String('foo'), Object('foo'));

        //ok
        assert.deepStrictEqual(-0, -0);
        // Different zeros using the SameValue Comparison:
        assert.throws(() => { assert.deepStrictEqual(0, -0) });

        const symbol1 = Symbol();
        const symbol2 = Symbol();
        // OK, because it is the same symbol on both objects.
        assert.deepStrictEqual({ [symbol1]: 1 }, { [symbol1]: 1 });
    
        assert.throws(() => { assert.deepStrictEqual({ [symbol1]: 1 }, { [symbol2]: 1 }) });
    });
```

### assert.doesNotReject(asyncFn [, error, message ])

参数：

* `asyncFn` <Function>|<Promise> 异步函数或返回Promise的函数
* `error` <RegExp>|<Function>
* `message` <string>

asyncFn需要为异步函数（async）或者是一个返回Promise的同步函数，然后判断检查Promise是否调用reject。如果调用则抛出错误。

如果asyncFn是同步函数并且引发错误， 将直接抛出一个系统错误。如果返回不是一个Promise对象，那么返回一个[`ERR_INVALID_RETURN_VALUE`]的错误。以上两者都不会进入测试的错误处理程序。

### assert.doesNotThrow(fn [, error, message ])

参数：

- `fn` <Function>|<Promise>
- `error` <RegExp>|<Function>
- `message` <string>

断言函数`fn`不会引发错误。如果抛出错误和error一样，那么抛出一个assertionError错误，否则，抛出系统错误给调用方。

```javascript
assert.doesNotThrow(()=>{
            throw new TypeError();
        },
        TypeError
        );
```



### assert.equal（actual，Expected [，message]）

参数：

- `actual` <any>
- `expeted` <any>
- `message` <string>

严格模式：assert.strictEqual()

使用[抽象相等比较]测试`actual`和`expected`参数之间的浅强制性相等。`==`

```javascript
it('#equal()',function(){
        assert.equal(1, 1);
        assert.equal(1, '1');
        assert.equal(false, 0);
        assert.equal(true, 1);

        var obj = { a: 1, b: { c: 1 } };
        assert.equal(obj, obj);

        assert.throws(() => { assert.equal({ a: 1, b: { c: 1 } }, { a: 1, b: { c: 1 } }); });
    } );
```



### assert.strictEqual（actual，expected [，message]）

参数：

- `actual` <any>
- `expeted` <any>
- `message` <string>|<Error>

利用[SameValue Comparison](https://tc39.github.io/ecma262/#sec-samevalue)严格比较actual和expected的相等性。

```javascript
const assert = require('assert').strict;

assert.strictEqual(1, 2);
// AssertionError [ERR_ASSERTION]: Expected inputs to be strictly equal:
//
// 1 !== 2

assert.strictEqual(1, 1);
// OK

assert.strictEqual('Hello foobar', 'Hello World!');
// AssertionError [ERR_ASSERTION]: Expected inputs to be strictly equal:
// + actual - expected
//
// + 'Hello foobar'
// - 'Hello World!'
//          ^
```





### assert.fail（[message]）

参数：

* message <string>|<error> 默认值'Failed'

抛出一个`AssertionError`与所提供的错误消息或默认的错误消息。如果`message`参数是的实例，Error则将抛出而不是`AssertionError`。

```javascript
const assert = require('assert').strict;

assert.fail();
// AssertionError [ERR_ASSERTION]: Failed

assert.fail('boom');
// AssertionError [ERR_ASSERTION]: boom

assert.fail(new TypeError('need array'));
// TypeError: need array
```



### assert.ifError(value)

参数：

* value <any>

如果value不是null或undefine，抛出AssertionError错误。堆栈跟踪包含从错误传递到`ifError()`包括`ifError()`自身可能的新帧的 所有帧

```javascript
it('#ifError()', function(){
        assert.ifError(undefined);
        assert.ifError(null);

        assert.throws(() => { assert.ifError(NaN); });
        assert.throws(() => {assert.ifError(0); });
        assert.throws(() => { assert.ifError(1); });
        assert.throws(() => { assert.ifError('error'); });
        assert.throws(() => { assert.ifError(new Error()); });

    });
```

抛出的错误包含堆栈的所有帧：

```javascript
 // Create some random error frames.
let err;
(function errorFrame() {
  err = new Error('test error');
})();

(function ifErrorFrame() {
  assert.ifError(err);
})();
// AssertionError [ERR_ASSERTION]: ifError got unwanted exception: test error
//     at ifErrorFrame
//     at errorFrame
```





### assert.notDeepEqual(actual, expected [, message])

与assert.deepEqual（）相反， 递归测试不相等。

参数：

- actual <any>
- expected <any>
- message <string>|<error>





### assert.notDeepStrictEqual（actual，expected [，message]）

与assert.deepStrictDqual相反，在严格模式下测试不相等的情况。

参数：

- actual <any>
- expected <any>
- message <string>|<error>





### assert.notEqual（actual，expected [，message]）

与assert.Equal（）相反，使用"抽象比较相等"比较`!=`

参数：

- actual <any>
- expected <any>
- message <string>|<error>



### assert.notStrictEqual（actual，expected [，message]）

assert.strictEqual（）的相反检查。使用"严格相等比较"浅比较actual和expected是否不相等

参数：

- actual <any>
- expected <any>
- message <string>|<error>



### assert.ok（value [，message]）

参数：

* value <any>
* message <string>|<error>

测试value是否真实，等价于：assert.equal(!!value, true, message);

如果message是字符串，如果断言检查错误，则抛出AssertionError的错误，内容即为message。如果message是Error的实例，那么断言错误将抛出message这个错误。



### assert.throws(fn [, error] [, message])

参数：

* fn <Function>
* error <RegExp>|<Function>|<Object>|<Error>
* message <string>

断言fn会抛出错误。

如果error为RegExp、Function或Object，那么抛出的错误将通过严格相等性对属性递归进行比较;如果error是错误类型，那么如果抛出错误为该类型的实例，那么就符合断言。

如果指定 `message`，则当 `fn` 调用无法抛出或错误验证失败时， `message` 将附加到 `AssertionError` 提供的消息

例1: 自定义错误实例/错误实例

```javascript
const err = new TypeError('错误值');
err.code = 404;
err.foo = 'bar';
err.info = {
  nested: true,
  baz: 'text'
};
err.reg = /abc/i;

assert.throws(
  () => {
    throw err;
  },
  {
    name: 'TypeError',
    message: '错误值',
    info: {
      nested: true,
      baz: 'text'
    }
    // 仅测试验证对象上的属性。
    // 使用嵌套对象需要存在所有属性。
    // 否则验证将失败。
  }
);

// 使用正则表达式验证错误属性：
assert.throws(
  () => {
    throw err;
  },
  {
    // `name` 和 `message` 属性是字符串，使用正则表达式将匹配字符串。 
    // 如果失败，则会抛出错误。
    name: /^TypeError$/,
    message: /错误/,
    foo: 'bar',
    info: {
      nested: true,
      // 无法对嵌套属性使用正则表达式！
      baz: 'text'
    },
    // `reg` 属性包含一个正则表达式，
    // 并且只有当验证对象包含相同的正则表达式时，
    // 它才会通过。
    reg: /abc/i
  }
);

// 由于 `message` 和 `name` 属性不同而失败：
assert.throws(
  () => {
    const otherErr = new Error('未找到');
    otherErr.code = 404;
    throw otherErr;
  },
  err // 测试 `message`、 `name` 和 `code`。
);
```

例2:使用构造函数验证 instanceof：

```javascript
assert.throws(
  () => {
    throw new Error('错误值');
  },
  Error
);
```

例3: 使用RegExp验证错误信息

使用正则表达式在错误对象上运行 `.toString`，因此也将包含错误名称。

```javascript
assert.throws(
  () => {
    throw new Error('错误值');
  },
  /^Error: 错误值$/
);
```

例4: 自定验证函数

```javascript
assert.throws(
  () => {
    throw new Error('错误值');
  },
  (err) => {
    assert(err instanceof Error);
    assert(/value/.test(err));
    // 除了 `true` 之外，不建议从验证函数返回任何内容。 
    // 这样做会导致再次抛出捕获的错误。 
    // 这通常不是理想的结果。 
    // 抛出有关失败的特定验证的错误（如本例所示）。
    return true;
  },
  '不是期望的错误'
);
```



注意：`error` 不能是字符串。 如果提供了一个字符串作为第二个参数，则假定 `error` 被忽略，而字符串将用于 `message`。 这可能导致容易错过的错误。 使用与抛出的错误消息相同的消息将导致 `ERR_AMBIGUOUS_ARGUMENT` 错误。 如果使用字符串作为第二个参数

```javascript
function throwingFirst() {
  throw new Error('错误一');
}
function throwingSecond() {
  throw new Error('错误二');
}
function notThrowing() {}

// 第二个参数是一个字符串，输入函数抛出一个错误。
// 第一种情况不会抛出，因为它与输入函数抛出的错误消息不匹配！
assert.throws(throwingFirst, '错误二');
// 在下一个示例中，传入的消息类似来自错误的消息，
// 并且由于不清楚用户是否打算实际匹配错误消息，
// 因此 Node.js 抛出了 `ERR_AMBIGUOUS_ARGUMENT` 错误。
assert.throws(throwingSecond, '错误二');
// TypeError [ERR_AMBIGUOUS_ARGUMENT]

// 该字符串仅在函数未抛出时使用（作为消息）：
assert.throws(notThrowing, '错误二');
// AssertionError [ERR_ASSERTION]: Missing expected exception: 错误二

// 如果要匹配错误消息，请执行以下操作：
// 它不会抛出错误，因为错误消息匹配。
assert.throws(throwingSecond, /错误二$/);

// 如果错误消息不匹配，则不会捕获函数内的错误。
assert.throws(throwingFirst, /错误二$/);
// Error: 错误一
//     at throwingFirst (repl:2:9)
```

由于令人困惑的表示法，建议不要使用字符串作为第二个参数。 这可能会导致难以发现的错误。





### assert.rejects(asyncFn [, error] [, message])

参数：

- `asyncFn` <Function>|<Promise> 异步函数或返回Promise的函数
- `error` <RegExp>|<Function>
- `message` <string>

asyncFn是一个异步或者返回Promise的同步函数，然后检查其是否调用reject方法（被拒绝，正常会调用resolve）

如果 `asyncFn` 是一个函数并且它同步抛出一个错误，则 `assert.rejects()` 将返回一个带有该错误的被拒绝的 `Promise`。 如果函数未返回 Promise，则 `assert.rejects()` 将返回一个被拒绝的 `Promise`，其中包含 [`ERR_INVALID_RETURN_VALUE`](http://nodejs.cn/s/zKCD5R) 错误。 在这两种情况下都会跳过错误处理函数

该断言和assert.throws()行为一致，不同在于当前断言用于检查异步方法，而后者检查的是同步方法函数。

如果指定，则 `error` 可以是 [`Class`](http://nodejs.cn/s/5wDLcp)、[`RegExp`](http://nodejs.cn/s/tbQJCP)、验证函数、将测试每个属性的对象、或者将测试每个属性的错误实例（包括不可枚举的 `message` 和 `name` 属性）。

如果指定 `message`，则当 `asyncFn` 无法拒绝时 `message` 将是 `AssertionError` 提供的消息。

```javascript
(async () => {
  await assert.rejects(
    async () => {
      throw new TypeError('错误值');
    },
    {
      name: 'TypeError',
      message: '错误值'
    }
  );
})();

assert.rejects(
  Promise.reject(new Error('错误值')),
  Error
).then(() => {
  // ...
});
```

注意， `error` 不能是字符串。 如果提供了一个字符串作为第二个参数，则假定 `error` 被忽略，而字符串将用于 `message`。 这可能导致容易错过的错误。