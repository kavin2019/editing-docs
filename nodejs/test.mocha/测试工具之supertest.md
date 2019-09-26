### supertest

supertest是一个高度封装了http请求的测试工具包，其是[superagent](https://github.com/visionmedia/superagent)的孪生兄弟，其底层都是通过superagent实现，同样supertest也支持superagent的所有接口。

### 安装

```shell
npm i supertest --save-dev
```

### 引用

```
const request = require('supertest');
```

### 开始使用

supertest接受一个http.server对象或者Function，或者是url地址。如果server没有启动监听，那么supertest会随机绑定一个端口并启动。

supertest可以和任意一个测试框架配合。也可以独立测试框架之外运行

supertest的接口调用是按照定义时的顺序执行的，所以在断言之前可以对response的header或body进行修改

下例配合mocha框架一起工作：

```javascript
const request = require('supertest');
const express = require('express');

describe('test supertest', function(){
    let app = express();
    app.get('/user', function (req, res) {
        res.status(200).json({ user: 'peter' });
    });

    it('expect resturn 200 status', function(done){
        request(app).get('/user')
        .expect(200)
        .expect('Content-Type', /json/)
        .expect({user:  'peter'})
        .end(function(err, res){
            done(err);
        });
    });

    //supertest的接口调用是按照定义时的顺序执行的，所以在断言之前可以对response的header或body进行修改
    it('expect return body have property password and equal "hello"', function(done){
        request(app).get('/user')
        .expect(200)
        .expect(function(res){
            expect(res.body).be.not.have.property('password');
        })
        .expect(function(res){
            res.body.password='hello';
        })
        .end(function(err, res){
            expect(res.body).be.have.property('password', 'hello');
            done(err);
        });
    });
});
```

supertest可以设置和持久化cookie，

```javascript
describe('test supertest use cookie', function(){
    let app = express();
    app.use(cookieParser());

    app.get('/', function (req, res) {
         res.cookie('cookie', 'hey');
         res.send();
    });

    app.get('/return', function(req, res){
        console.log(req.cookies);
        if(req.cookies.cookie){
            res.send(req.cookies.cookie);
        }else{
            res.send('none');
        }
    });

    const agent = request.agent(app);
    it('should save cookie', function(done){
        agent.get('/')
        .expect(200)
        .expect('set-cookie', 'cookie=hey; Path=/')
        .end(function(err, res){
            done(err);
        });
    });

    it('should send cookie', function(done){
        agent.get('/return')
        .expect(200)
        .expect('hey', done);
    });
});
```

supertest发送请求时也可以通过设置请求头带上指定的cookie

```javascript
describe('test supertest', function () {
    let app = express();
    app.use(cookieParser());
    app.get('/', function (req, res) {
        res.send(req.cookies);
    });

    it('should have cookie', function (done) {
        request(app).get('/')
        .set('Cookie', ['nameOne=valueOne;nameTwo=valueTwo'])
        .expect(200)
        .expect('Content-Type', /json/)
        .expect({ nameOne: 'valueOne', nameTwo: 'valueTwo' })
        .end(function(err,res){
            done(err);
        });
    });
});
```



实现登录状态，需要用到cookie时有两种思路

第一种：使用"const agent = request.agent(app)"这样，agent在获取cookie后所有的请求都会自动带上cookie，常用于进行登录操作后执行其他请求测试。

```javascript
var supertest = require('supertest');
  var app = express();
  var agent = supertest.agent(app);

  agent.post('login').end(...);
  // then ..
  agent.post('create_topic').end(...); // 此时的 agent 中有用户登陆后的 cookie
```



第二种：登录后使用变量记录cookie，然后每次请求都重新设置请求头的cookie

```javascript
var supertest = require('supertest');
  var userCookie;
  supertest.post('login').end(function (err, res) {
      userCookie = res.headers['Cookie']
    });
  // then ..

  supertest.post('create_topic')
    .set('Cookie', userCookie)
    .end(...)
```





