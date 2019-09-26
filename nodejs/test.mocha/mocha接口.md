## mocha的接口

Mocha的“接口”系统允许开发者选择习惯的风格或DSL。Mocha有**BDD**，**TDD**，**Exports**，**QUnit**和**Require**风格的接口。

#### 1.1 BDD

**BDD**接口提供`describe()`，`context()`，`it()`，`specify()`，`before()`，`after()`，`beforeEach()`和`afterEach()`。

`context()`只是`describe()`的别名，二者表现也是一致的；它只是为了让测试可读性更高。同样`specify()`也是`it()`的别名。

> 前文所有的示例都是使用**BDD**接口编写的

```javascript
describe('Array', function() {
  before(function() {
    // ...
  })

  describe('#indexOf()', function() {
    context('when not present', function() {
      it('should not throw an error', function() {
        (function() {
          [1,2,3].indexOf(4)
        }).should.not.throw()
      })
      it('should return -1', function() {
        [1,2,3].indexOf(4).should.equal(-1);
      })
    })
    context('when present', function() {
      it('should return the index where the element first appears in the array', function() {
        [1,2,3].indexOf(3).should.equal(2)
      })
    })
  })
})
```

#### 1.2 TDD

**TDD**接口提供`suite()`，`test()`，`suiteSetup()`，`suiteTeardown()`，`setup`和`teardown()`：

```javascript
suite('Array', function() {
  setup(function() {
    // ...
  })

  suite('#indexOf()', function() {
    test('should return -1 when not present', function() {
      assert.equal(-1, [1,2,3].indexOf(4));
    })
  })
})
```

#### 1.3 EXPORTS

**EXPORTS**接口很像Mocha的前身[expresso](https://link.jianshu.com/?t=https://github.com/visionmedia/expresso)，键值`before`，`after`，`beforeEach`，`afterEach`是特殊用例，对象类型的属性值是测试套件，方法类型的属性值是测试用例：

```javascript
module.exports = {
  before: function() {
    // ...
  },

  'Array': {
    '#indexOf()': {
      'should return -1 when not present': function() {
        [1,2,3].indexOf(4).should.equal(-1)
      }
    }
  }
}
```

#### 1.4 QUNIT

类[QUnit](https://link.jianshu.com/?t=http://qunitjs.com/)接口与QUnit的“扁平化”外观相匹配[[14\]](https://www.jianshu.com/p/9c78548caffa#fn14)，测试套件只需要简单地在测试用例之前定义就行。和TDD类似，它使用`suite()`和`test()`，但又类似于BDD，也包含了`before()`，`after()`，`beforeEach()`和`afterEach()`。

```javascript
function ok(expr, msg) {
  if (!expr) throw new Error(msg)
}

suite('Array')

test('#length', function() {
  var arr = [1,2,3]
  ok(arr.length == 3)
})

test('#indexOf()', function() {
  var arr = [1,2,3]
  ok(arr.indexOf(1) == 0)
  ok(arr.indexOf(2) == 1)
  ok(arr.indexOf(3) == 2)
})

suite('String')

test('#length', function() {
  ok('foo'.length == 3)
})
```

#### 1.5 REQUIRE

`require`风格接口允许你直接用`require`语句引入`describe`等函数并在任意位置使用它们。当你希望在你的测试中禁用全局变量时这也会很有用。

*<small>注意，require风格接口不能通过node直接运行，必须通过Mocha运行</small>*

```javascript
var testCase = require('mocha').describe
var pre = require('mocha').before
var assertions = require('mocha').it
var assert = require('chai').assert

testCase('Array', function() {
  pre(function() {
    // ...
  });

  testCase('#indexOf()', function() {
    assertions('should return -1 when not present', function() {
      assert.equal([1,2,3].indexOf(4), -1)
    })
  })
})
```