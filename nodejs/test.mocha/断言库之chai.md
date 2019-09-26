## 断言库之chai-expect

本文用于记录和讲解expect风格的测试api。should断言库和风格在IE浏览器中有兼容性的问题，暂不做说明。

### BDD

expect和should都是BDD风格的测试库，二者都使用链式语言来组织断言，不同在于他们初始化断言的方式：expect通过构造函数的方式来初始化，而should是通过为Object.prototype拓展方法的方式来初始化断言（所以should不支持IE）。

```javascript
const expect = require('chai').expect;
const should = require('chai').should();
```



### 语言链

下面的接口方法单纯只是为了提供断言的可读性，使得断言更接近自然语言。所以除非被插件重新改写，否则不具有任何测试功能：

* to
* be
* been
* is
* that
* which
* and 
* has
* have
* with
* at
* of
* same
* but
* does
* still

### not

对链式之后的断言结果取反

```javascript
it('#not', function(){
        expect('bar').to.not.be.equal('foo');
        expect(function(){}).to.not.throw();
        expect({bar: 'bar'}).to.have.property('bar').and.not.equal('foo');
    });
```



### deep

设置`deep`标记，然后链式之后的.equal`, `.include`, `.members`, `.keys`, and `.property断言都使用不严格的深度相等性(递归比较键值)进行比较。严格相等性可以参考[这里](https://github.com/chaijs/deep-eql)

```javascript
// Target object deeply (but not strictly) equals `{a: 1}`
expect({a: 1}).to.deep.equal({a: 1});
expect({a: 1}).to.not.equal({a: 1});

// Target array deeply (but not strictly) includes `{a: 1}`
expect([{a: 1}]).to.deep.include({a: 1});
expect([{a: 1}]).to.not.include({a: 1});

// Target object deeply (but not strictly) includes `x: {a: 1}`
expect({x: {a: 1}}).to.deep.include({x: {a: 1}});
expect({x: {a: 1}}).to.not.include({x: {a: 1}});

// Target array deeply (but not strictly) has member `{a: 1}`
expect([{a: 1}]).to.have.deep.members([{a: 1}]);
expect([{a: 1}]).to.not.have.members([{a: 1}]);

// Target set deeply (but not strictly) has key `{a: 1}`
expect(new Set([{a: 1}])).to.have.deep.keys([{a: 1}]);
expect(new Set([{a: 1}])).to.not.have.keys([{a: 1}]);

// Target object deeply (but not strictly) has property `x: {a: 1}`
expect({x: {a: 1}}).to.have.deep.property('x', {a: 1});
expect({x: {a: 1}}).to.not.have.property('x', {a: 1});

```



### .nested

使得链式后面的所有的.property和.include断言可以使用`.`	和 `[]`。

```javascript
expect({a: {b: ['x', 'y']}}).to.have.nested.property('a.b[1]');
expect({a: {b: ['x', 'y']}}).to.nested.include({'a.b[1]': 'y'});
```

如果点`.`和括号'[]'是属性名中的一部分，那么可以通过双斜杠`\\`来转义：

```javascript
expect({'.a': {'[b]': 'x'}}).to.have.nested.property('\\.a.\\[b\\]');
expect({'.a': {'[b]': 'x'}}).to.nested.include({'\\.a.\\[b\\]': 'x'});
```

`.nested`不能和`.own`一起使用



### .own

使得链式后所有的.property和.include断言都忽略继承而来的属性

```
Object.prototype.b = 2;

expect({a: 1}).to.have.own.property('a');
expect({a: 1}).to.have.property('b');
expect({a: 1}).to.not.have.own.property('b');

expect({a: 1}).to.own.include({a: 1});
expect({a: 1}).to.include({b: 2}).but.not.own.include({b: 2});
```



### .ordered

使得链式后的所有的.members断言中actual和expected必须就有相同的顺序

```
expect([1, 2]).to.have.ordered.members([1, 2])
  .but.not.have.ordered.members([2, 1]);
```

如果.include和.order配合一起使用，那么actual和expected开始位置必须一样。

```
expect([1, 2, 3]).to.include.ordered.members([1, 2])
  .but.not.include.ordered.members([2, 3]);
```



### .any

使得链式中所有的.keys断言中目标具有至少一个给定的键。这个和.all相反。

```javascript
expect({a: 1, b: 2}).to.not.have.any.keys('c', 'd');
```

### .all

使得链式后所有的.keys断言，目标对象必须包含给定的所有键。

```javascript
expect({a: 1, b: 2}).to.have.all.keys('a', 'b');
```

### .a(type [,message])/.an(type [,message])

参数：

* type <string> 
* message <string>

检查目标的类型是否和给定的type一致。type忽略大小写。

```javascript
expect('foo').to.be.a('string');
expect({a: 1}).to.be.an('object');
expect(null).to.be.a('null');
expect(undefined).to.be.an('undefined');
expect(new Error).to.be.an('error');
expect(Promise.resolve()).to.be.a('promise');
expect(new Float32Array).to.be.a('float32array');
expect(Symbol()).to.be.a('symbol');
```

### .include(val[, msg])/.contains(val[, msg])

参数：

* val <any> 
* msg <string> 

如果目标是字符串，那么断言val是目标的子串（即目标包含字符串val）

```javascript
expect('foobar').to.include('foo');
```

如果目标是数组，那么val是目标中的其中一项(val不能是数组)：

```javascript
 expect([1,2,3,4]).to.include(3);
```

如果目标是对象，那么检查val的属性是不是目标属性的子集：

```javascript
expect({a: 1, b: 2, c: 3}).to.include({a: 1, b: 2});
```

如果目标是一个Set或者WeakSet,那么检查val是不是目标的其中一项：

```javascript
expect(new Set([1, 2])).to.include(2);
```

如果目标是一个Map对象，那么检查val是否是其中值之一：

```javascript
expect(new Map([['a', 1], ['b', 2]])).to.include(2);
```

建议：因为.include针对不同类型的目标，其断言含义不同，所以在使用之前先判断其类型：

```javascript
expect([1, 2, 3]).to.be.an('array').that.includes(2);
```

注意：默认情况下，val和目标属性或值进行比较时使用的严格比较相等性（===)，所以如果需要的话在链式前加入.deep关键字使用深度不严格相等性比较。

```javascript
// Target array deeply (but not strictly) includes `{a: 1}`
expect([{a: 1}]).to.deep.include({a: 1});
expect([{a: 1}]).to.not.include({a: 1});

// Target object deeply (but not strictly) includes `x: {a: 1}`
expect({x: {a: 1}}).to.deep.include({x: {a: 1}});
expect({x: {a: 1}}).to.not.include({x: {a: 1}});
```



.include同样可以作为链式关键字，使得链式后的.members和.keys断言中，目标是val的超集而不是一样的

```javascript
// Target object's keys are a superset of ['a', 'b'] but not identical
expect({a: 1, b: 2, c: 3}).to.include.all.keys('a', 'b');
expect({a: 1, b: 2, c: 3}).to.not.have.all.keys('a', 'b');

// Target array is a superset of [1, 2] but not identical
expect([1, 2, 3]).to.include.members([1, 2]);
expect([1, 2, 3]).to.not.have.members([1, 2]);

// Duplicates in the subset are ignored
expect([1, 2, 3]).to.include.members([1, 2, 2, 2]);
```

.any会使得.keys忽略.include.

### .ok

断言目标是一个真实的值（可以转换成boolean值中的true），但通常建议使用严格相等性进行比较

```javascript
expect(1).to.be.ok;
expect('1').to.be.ok;
expect([]).to.be.ok;
expect({}).to.be.ok;
expect(true).to.be.ok;

expect(0).to.not.be.ok;
expect('').to.not.be.ok;
expect(null).to.not.be.ok;
expect(undefined).to.not.be.ok;
expect(NaN).to.not.be.ok;
```



### .true

断言目标严格等于(===)true

```javascript
expect(true).to.be.true;
expect(!0).to.be.true;

expect(1).to.not.be.true;
expect('1').to.not.be.true;
expect([]).to.not.be.true;
expect({}).to.not.be.true;

expect(false).to.not.be.true; //不推荐
expect(false).to.be.false; //推荐
```

### .false

断言目标值严格等于(===)false

```javascript
expect(false).to.be.false;
expect(!1).to.be.false;
```

### .null

严格等于null

```
expect(null).to.be.null;
```

### .undefined

严格等于undefined

```javascript
expect(undefined).to.be.undefined;
```

### .NaN

严格等于NaN

```
expect(NaN).to.be.NaN;
```

### .exist

断言目标值存在，即不为null或un defined的

```javascript
expect(null).to.be.null; // Recommended
expect(null).to.not.exist; // Not recommended

expect(undefined).to.be.undefined; // Recommended
expect(undefined).to.not.exist; // Not recommended

expect(1).to.equal(1); // Recommended
expect(1).to.exist; // Not recommended

expect(0).to.equal(0); // Recommended
expect(0).to.exist; // Not recommended
```



### .empty

检查目标是否为空

1. 目标是数组或字符串时，严格比较`length`属性是否为0

   ```
   expect([]).to.be.empty;
   expect('').to.be.empty;
   ```

2. 如果目标是map或set，养个比较size是否为0

   ```
   expect(new Set()).to.be.empty;
   expect(new Map()).to.be.empty;
   ```

3. 如果目标是非函数（即对象），那么检查其*`自身`*是否没有属性

   ```
   Object.prototype.a=1;
   expect({}).to.have.property('a');
   expect({}).to.be.empty;
   ```

### .arguments/.Arguments

检查目标是不是参数

```javascript
function test () {
  expect(arguments).to.be.arguments;
}

test();
```



### .equal(val[,msg]) / .equals(val[,msg])  / .eq(val[,msg]) 

参数：

* val <any>
* msg <string>

检查目标和val是否严格相等(===)

```javascript
expect(1).to.equal(1);
expect('foo').to.equal('foo');
```



### .eql(obj[, msg]) / .eqls(obj[, msg]

参数：

- val <any>
- msg <string>

目标和val深度不严格相等（==），等价于链式.deep和.equal组合使用。

```javascript
// Target object is deeply (but not strictly) equal to {a: 1}
expect({a: 1}).to.eql({a: 1}).but.not.equal({a: 1});

// Target array is deeply (but not strictly) equal to [1, 2]
expect([1, 2]).to.eql([1, 2]).but.not.equal([1, 2]);
```

###.above(val[,msg])

参数：

- val <number>
- msg <string>

目标大于val

```
expect(2).to.be.above(1);
```

###.least(val[,msg])

参数：

- val <number>
- msg <string>

目标最小等于val（大于等于val）

```
expect(2).to.be.at.least(1); 
expect(2).to.be.at.least(2);
```

### .below(val[, msg])

参数：

- val <number>
- msg <string>

目标小于val

```
expect(1).to.be.below(2)
```

### .most(val[,msg])

参数：

- val <number>
- msg <string>

目标不大于val（小于等于val）

```javascript
expect(1).to.be.at.most(2); 
expect(1).to.be.at.most(1);
```

### .within(start, finish[, msg])

参数：

* start <number>
* finish <number>
* msg <string>

目标值在start和finish区间（闭区间）

```javascript
expect(2).to.be.within(1, 3); 
expect(2).to.be.within(2, 3); 
expect(2).to.be.within(1, 2); 
```



### .instanceof(constructor[, msg]) / .instanceOf(constructor[, msg])

参数：

* constructor <*Constructor*>
* msg <string>

检查目标是构造函数constructor的实例

```
function Cat () { }
expect(new Cat()).to.be.an.instanceof(Cat);
expect([1, 2]).to.be.an.instanceof(Array);
```



### .property(name[, val[, msg]])

参数：

* name <string>
* val <any>
* msg <string>

检查目标是否具有属性name

```javascript
expect({a: 1}).to.have.property('a');
```

如果val有值，那么通过严格相等比较，目标具有name属性并且值等于val：

```
expect({a: 1}).to.have.property('a', 1);
```

在链式前面加入.deep可以将比较方式修改为不严格比较相等：

```javascript
// Target object deeply (but not strictly) has property `x: {a: 1}`
expect({x: {a: 1}}).to.have.deep.property('x', {a: 1});
expect({x: {a: 1}}).to.not.have.property('x', {a: 1});
```

在链式前加入.own可以检查自身的属性（不包括继承来的属性）：

```javascript
Object.prototype.b = 2;
expect({a: 1}).to.have.own.property('a', 1);
expect({a: 1}).to.have.property('b');
expect({a: 1}).to.not.have.own.property('b');
```

注意：`.property`会修改目标对象，所以.property后面链式上的断言都是以.property的值为目标对象：

```javascript
expect({a: 1}).to.have.property('a').that.is.a('number');
```

PS:断言`.ownProperty`和`.haveOwnProperty`等价于`.own.property`

### .ownPropertyDescriptor(name[, descriptor[, msg]])

参数：

* name <string>
* descriptor <Object>
* msg <string>

检查目标指定name属性是否有属性descriptor，例如：

```javascript
expect({a: 1}).to.have.ownPropertyDescriptor('a', {
  configurable: true,
  enumerable: true,
  writable: true,
  value: 1,
});
```



### .lengthOf(n[, msg])

参数：

* n <Number>
* msg <string>

检查目标的`length`和`size`等于指定的n：

```javascript
expect([1, 2, 3]).to.have.lengthOf(3);
expect('foo').to.have.lengthOf(3);
expect(new Set([1, 2, 3])).to.have.lengthOf(3);
expect(new Map([['a', 1], ['b', 2], ['c', 3]])).to.have.lengthOf(3);
```

`lengthof`可以用作链式中的一个断言，这种情况可以使用`length`代替，表示目标的length或size。后续可以接.above`, `.below`, `.least`, `.most`, 以及`.within用来检查lenghtof值：

```javascript
// Recommended
expect([1, 2, 3]).to.have.lengthOf(3);

// Not recommended
expect([1, 2, 3]).to.have.lengthOf.above(2);
expect([1, 2, 3]).to.have.lengthOf.below(4);
expect([1, 2, 3]).to.have.lengthOf.at.least(3);
expect([1, 2, 3]).to.have.lengthOf.at.most(3);
expect([1, 2, 3]).to.have.lengthOf.within(2,4);
```



### .match(re[, msg]) / .matches(re[, msg]) 

参数：

* re <RegExp>
* msg <string>

检查目标是否符合给定的正则表达式re：

```javascript
expect('foobar').to.match(/^foo/);
```



### .string(str[, msg])

参数：

* str <string>
* msg <string>

检查目标字符串是否包含给定的字符串str

```javascript
expect('foobar').to.have.string('bar');
```



### .keys(key1[, key2[, …]])

参数：

* key <string>|<Array>|<object>

断言目标包含给定的属性名。

与.any、.all、contains及have前缀结合使用会影响断言结果：

当与.any结合使用时，无论使用have还是contains前缀，目标中至少包含给定的属性中的一个。注意：.any和.all必须两者选其一，默认.all。当与.all结合使用时，目标属性必须包含所有给定的属性名

当.all和.contains结合使用时，目标对象必须拥有所有传入的属性，但它还可以拥有其它的属性名。

当.all和.have结合使用时，目标对象必须且仅拥有全部传入的属性名。

```javascript
// 结合any使用
expect({ foo: 1, bar: 2, baz: 3 }).to.have.any.keys('foo', 'bar')
expect({ foo: 1, bar: 2, baz: 3 }).to.contains.any.keys('foo', 'bar')

// 结合all使用
expect({ foo: 1, bar: 2, baz: 3 }).to.have.all.keys('foo', 'bar', 'baz')
expect({ foo: 1, bar: 2, baz: 3 }).to.contains.all.keys('foo', 'bar')

// 传入string
expect({ foo: 1, bar: 2, baz: 3 }).to.have.any.keys('foo')
// 传入Array
expect({ foo: 1, bar: 2, baz: 3 }).to.have.all.keys(['foo', 'bar', 'baz'])
// 传入Object
expect({ foo: 1, bar: 2, baz: 3 }).to.have.any.keys({ bar: 2, foo: 1 })
```

### .throw([errorLike], [errMsgMatcher], [msg])

参数：

* errorLike <Error>|<ErrorConstructor>
* errMsgMatcher <String>|<RegExp>
* msg <string>

当没有参数传入时，.throw调用目标的函数并且断言有错误抛出：

```javascript
var badFn = function () { throw new TypeError('Illegal salmon!'); };
expect(badFn).to.throw();
```

如果传入一个参数而且是一个错误构造函数，.throw调用目标函数并断言抛出的错误并且该错误是该构造函数的实例。

```javascript
var badFn = function () { throw new TypeError('Illegal salmon!'); };
expect(badFn).to.throw(TypeError);
```

如果传入一个参数而且是一个错误的实例，那么.throw调用目标函数并断言抛出的错误严格等于(===)错误实例：

```javascript
var err = new TypeError('Illegal salmon!');
var badFn = function () { throw err; };
expect(badFn).to.throw(err);
```

如果传入一个参数并且是一个字符串，那么.throw调用目标函数并断言抛出的错误信息中包含指定的字符串：

```javascript
var badFn = function () { throw new TypeError('Illegal salmon!'); };
expect(badFn).to.throw('salmon');
```

如果传入一个参数并且是一个正则表达式，那么.throw调用目标函数并且断言抛出的错误信息符合正则表达式：

```javascript
var badFn = function () { throw new TypeError('Illegal salmon!'); };
expect(badFn).to.throw(/salmon/);
```

如果传入两个参数，第一个参数是错误实例或者错误构造函数，并且第二个参数是字符串或正则表达式，那么.throw调用目标函数符合上述所有描述：

```javascript
var err = new TypeError('Illegal salmon!');
var badFn = function () { throw err; };

expect(badFn).to.throw(TypeError, 'salmon');
expect(badFn).to.throw(TypeError, /salmon/);
expect(badFn).to.throw(err, 'salmon');
expect(badFn).to.throw(err, /salmon/);
```

注意：.throw调用会改变目标，由目标函数转变成错误实例。例如：

```javascript
var err = new TypeError('Illegal salmon!');
err.code = 42;
var badFn = function () { throw err; };

expect(badFn).to.throw(TypeError).with.property('code', 42);
```



### .respondTo(method[, msg]) / .respondsTo(method[, msg])
参数：

* method <string>
* msg <string>

如果目标是一个非函数对象，那么.respondTo断言目标具有一个名为method的方法，这个方法可以是它自身的也可以是继承而来的。

```javascript
function Cat () {}
Cat.prototype.meow = function () {};

expect(new Cat()).to.respondTo('meow');
```

如果目标是一个函数，那么.respondTo断言函数的prototype属性对象具有给定的方法名method，同样方法可以是自身的也可以是继承而来的。

```javascript
function Cat () {}
Cat.prototype.meow = function () {};
expect(Cat).to.respondTo('meow');
```

添加.itself可以将目标强制为一个非函数对象

```javascript
function Cat () {}
Cat.prototype.meow = function () {};
Cat.hiss = function () {};
expect(Cat).itself.to.respondTo('hiss').but.not.respondTo('meow');
```



### 

