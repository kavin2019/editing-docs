### .itself

设置`itself`标记，然后使用`respondTo`断言，如此目标函数被当作对象。

### .satisfy(matcher[, msg])

参数：

* matcher <Function>
* msg <string>

调用matcher函数，并将目标当作参数传入，并断言返回真。

```javascript
expect(1).to.satisfy(function(num) {
  return num > 0;
});
```



### .closeTo(expected, delta[, msg])

参数：

* expected <Number>
* delta <Number>
* msg <string>

断言目标在expected+/-delta区间内（闭合区间）

```javascript
expect(1.5).to.be.closeTo(1, 0.5);
expect(1.5).to.be.closeTo(2, 0.5);
expect(1.5).to.be.closeTo(1, 1);
```



### .members(set[, msg])

参数：

* set <Array>
* msg <String>

断言目标数组和传入的数组set具有相同的元素

```javascript
expect([1, 2, 3]).to.have.members([2, 1, 3]);
expect([1, 2, 2]).to.have.members([2, 1, 2]);
```

断言进行元素比较时，使用的严格相等(===)，可以通过使用.deep使用不严格相等深度比较：

```javascript
// Target array deeply (but not strictly) has member `{a: 1}`
expect([{a: 1}]).to.have.deep.members([{a: 1}]);
expect([{a: 1}]).to.not.have.members([{a: 1}]);
```

再没有配合.order前缀使用前，元素顺序时无关的，加入.order后元素顺序必须一致：

```javascript
expect([1, 2, 3]).to.have.ordered.members([1, 2, 3]);
expect([1, 2, 3]).to.have.members([2, 1, 3])
  .but.not.ordered.members([2, 1, 3]);
```

默认情况下，目标数组和参数传入数组size一致。通过配合.include前缀，使得目标数组成为参数数组的超集，需要注意的是参数数组中的重复数据只能算一个：

```javascript
// Target array is a superset of [1, 2] but not identical
expect([1, 2, 3]).to.include.members([1, 2]);
expect([1, 2, 3]).to.not.have.members([1, 2]);

// Duplicates in the subset are ignored
expect([1, 2, 3]).to.include.members([1, 2, 2, 2]);
```

.deep和.order以及.include可以组合使用。

```javascript
expect([{a: 1}, {b: 2}, {c: 3}])
  .to.include.deep.ordered.members([{a: 1}, {b: 2}])
  .but.not.include.deep.ordered.members([{b: 2}, {c: 3}]);
```



### .oneOf(list[, msg])

参数：

* list <Array>
* msg <string>

断言目标是参数list中的一个元素。比较使用的是严格比较(===)

```javascript
expect(1).to.be.oneOf([1, 2, 3]);
```



### .change(subject[, prop[, msg]]) / .changes(subject[, prop[, msg]])

参数：

* subject <String>|<function>
* prop <string>
* msg <string>

当只传入一个参数时，.change断言传入的函数subject会在调用目标函数之前和之后返回不同的结果：

```javascript
var dots = ''
  , addDot = function () { dots += '.'; }
  , getDots = function () { return dots; };

// Recommended
expect(getDots()).to.equal('');
addDot();
expect(getDots()).to.equal('.');

// Not recommended
expect(addDot).to.change(getDots);
```

当传入的是两个参数时，.change断言目标函数调用前subject的prop属性值不同

```javascript
var myObj = {dots: ''}
  , addDot = function () { myObj.dots += '.'; };

// Recommended
expect(myObj).to.have.property('dots', '');
addDot();
expect(myObj).to.have.property('dots', '.');

// Not recommended
expect(addDot).to.change(myObj, 'dots');
```

PS: 严格相等（===）比较目标函数调用之前和之后的值

### .increase(subject[, prop[, msg]])

参数：

- subject <String>|<function>
- prop <string>
- msg <string>

.increase使用和.change一样，不同在于，increase断言函数返回或者是对象属性的值会向上增长，通过.by接口可以断言其增长的数量：

```javascript
var myObj = {val: 1}
  , addTwo = function () { myObj.val += 2; };

expect(addTwo).to.increase(myObj, 'val').by(2); 
```

### .decrease(subject[, prop[, msg]])

参数：

- subject <String>|<function>
- prop <string>
- msg <string>

.decrease和.increase相反，其断言最终函数返回值或对象属性值相下减少，同样可以通过.by接口断言减少的数量：

```javascript
var myObj = {val: 1}
  , subtractTwo = function () { myObj.val -= 2; };

expect(subtractTwo).to.decrease(myObj, 'val').by(2);
```

### .by(delta[, msg])

参数：

* delta <Number>
* msg <string>

用于断言.increase或.decrease减少的量值。（.change也适用，只不过不建议）

### .extensible

断言目标对象可拓展

```javascript
var nonExtensibleObject = Object.preventExtensions({})
  , sealedObject = Object.seal({})
  , frozenObject = Object.freeze({});

expect({a: 1}).to.be.extensible;
expect(nonExtensibleObject).to.not.be.extensible;
expect(sealedObject).to.not.be.extensible;
expect(frozenObject).to.not.be.extensible;
expect(1).to.not.be.extensible;
```

### .sealed

断言目标对象不能被继承

```javascript
var sealedObject = Object.seal({});
var frozenObject = Object.freeze({});

expect(sealedObject).to.be.sealed;
expect(frozenObject).to.be.sealed;
expect(1).to.be.sealed;
```

### .frozen

断言目标对象被frozen

```javascript
var frozenObject = Object.freeze({});

expect(frozenObject).to.be.frozen;
expect(1).to.be.frozen;
```

### .finite

断言目标对象是一个number，并且不为NaN，也不为 positive/negative `Infinity`

```javascr
expect(1).to.be.finite;
expect(NaN).to.not.be.finite;
expect(Infinity).to.not.be.finite;
expect(-Infinity).to.not.be.finite; 
expect('foo').to.not.be.finite;
```

### .fail([message])

### .fail(actual, expected, [message], [operator])

参数：

* actual <any>
* expected <any>
* message <string>
* operator <string>

直接抛出断言错误

```javascript
expect.fail();
expect.fail("custom error message");
expect.fail(1, 2);
expect.fail(1, 2, "custom error message");
expect.fail(1, 2, "custom error message", ">");
expect.fail(1, 2, undefined, ">");

```



