## 异步测试以及promise测试

### 异步测试

异步测试的要点在于mocha框架在调用it时提供的回调函数是带参的，第一个参数done回调函数，表示异步测试。如果done接受一个一个参数，那么表示该测试失败，否则成功。

注意：done必须且仅调用一次。

````javascript
var user ={
    save: function(finished){
        setTimeout(function(){
            finished && finished('','保存成功');
        }, 1000);
    }
};

describe('用户',function(){
     //测试异步必须使用带参数的函数，否则被认为是同步执行的方法，那么所有的测试结果都为正确
    it('用户信息保存', function(done){
        user.save(function(error, msg){
            if(error){ return done(error);  }
            done();
        });
        /*
        因为done接受第一个参数和save一样都是error，所以可以简写为：
        user.save(done);
        */
    });
});
/****测试结果******/
 用户
    ✓ 用户信息保存 (1005ms)
````





### 测试promise

测试promise的过程就按同步过程测试即可。

```javascript
var assert = require('assert');
var bluebird =require('bluebird');
var user ={
    save: function(finished){
        setTimeout(function(){
            finished && finished('','保存成功');
        }, 1000);
    }
};
//利用bluebird构建Promise对象。
bluebird.promisifyAll(user);

describe('用户',function(){
    it('用户信息保存', function(){
        user.saveAsync().then(function(msg) {
            assert.equal(!!msg, true);
        });
    });
});


/***结果***/
用户
    ✓ 用户信息保存
```

PS：借助should风格的断言库，上面测试用例中的测试体可以简写为：

```javascript
//应用should断言库
var should =require('should');
//简写
user.saveAsync().should.eventually.be.ok();
```

