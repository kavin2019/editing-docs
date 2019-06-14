

# node之Sequelize

***

Sequelize是一个nodejs上基于Promise的ORM框架，适用于Postgres、MySQL、MariaDB、SQLite和Microsoft SQL Server。它具有可靠的事务支持、关系、快速加载和延迟加载、读取复制等特性。

>ORM: 对象关系映射（英语：**(Object Relational Mapping**，简称**ORM**，或**O/RM**，或**O/R mapping**），是一种程序技术，用于实现面向对象编程语言里不同类型系统的数据之间的转换 。从效果上说，它其实是创建了一个可在编程语言里使用的--“虚拟对象数据库”。主要用于自动完成编程中实体对象和持久化系统中实体的对应。

## 安装

安装命令

```
npm install sequelize --save 
```

关于npm安装区别

```
npm install packageName //本地安装，安装到项目目录下，不在package.json中写入依赖
npm install packageName -g //全局安装，安装在Node安装目录下的node_modules下
npm install packageName --save //安装到项目目录下，并在package.json文件的dependencies中写入依赖，简写为-S
npm install packageName --save-dev //安装到项目目录下，并在package.json文件的devDependencies中写入依赖，简写为-D
```

查看安装

```
npm ls sequelize
```

可以看到安装的sequlize的版本

```
jamesde-iMac:microletters james$ npm ls sequelize
microletters@0.0.0 /Users/james/Documents/project/NodeJs/microletters
└── sequelize@5.8.2 
```

连接数据库时需要连接数据库的模块，例如mysql：

```
npm install mysql2 --save//如果不安装mysql2 运行时会提示错误
> Error: Please install mysql2 package manually
```



## 使用sequelize连接mysql数据库

* 准备mysql数据库的连接配置：

  ```javascript
  var config ={
      mySql: {
          database: 'microletters', //数据库名
          username: 'root',  //数据库登录用户
          password: '123qwert', //数据库登录密码
          host: '127.0.0.1', //mysql所在服务器
          port: 3306 //mysql端口，默认3306
      },
  }
  
  module.exports = config;
  ```

* 初始化sequelize

  ```javascript
  var Sequelize = require('sequelize');
  var config = require('../config');
  var sequelize = new Sequelize(config.mySql.database,
      config.mySql.username,
      config.mySql.password,
      {
          host: config.mySql.host,
          port: config.mySql.port,
          dialect: 'mysql', //告诉sequelize连接的是mysql数据库
  
          //连接池配置
          pool: {
              max: 5,//最大的连接数
              min: 0,//连接池中最少连接数，就是sequelize初始化时pool中自动创建min数量的连接，在建立连接时如果当前连接数少于min，使用pool中的连接；大于min并少max时，新建连接加入pool。
              //如果当前连接数超过max，这些查询会被阻塞，直到池中有可用的连接
              idle: 30000 //最大闲置时间，超过这个时间，如果当前连接数大于min，连接会被释放；单位ms
          }
      });
  sequelize.model = Sequelize;
  module.exports = sequelize;
  ```

* 定义模型

  ```javascript
  var sequelize = require('../db/dbinstance');
  var Sequelize = sequelize.model; //这个存在的意义主要在于定义类型需要
  
  module.exports = sequelize.define('user', {
          id: {
              type: Sequelize.INTEGER(11),
              primaryKey: true,
              autoIncrement: true
          },
          name: {
              type: Sequelize.STRING(20),
              allowNull: false,
          },
          password: {
              type: Sequelize.STRING(50),
              allowNull: false
          },
          status: {
              type: Sequelize.INTEGER(1),
              allowNull: false,
          },
          create_time: {
              type: Sequelize.DATE,
              allowNull: false
          }
      }, {
              timestamps: false,
          }
  );
  ```

* 同步模型结构到数据库
	```javascript
  var userModel = require('../models/user');
  
  var initmysql = async ()=>{
      //创建表
      await userModel.sync(
      {
          force: true //每次同步都创建表,否则只有不存在时更新
      }
      );
  
      let user = await userModel.create({
          name: 'kavin',
          password: '123456',
          status: 0,
          create_time: Date.now()
      });
  
  }
  initmysql();
  
  ```

//模型同步，除了model.sync之外，sequelize.sync可以同步所有模型
	//sequelize.symc({
		force: true, //删除表后同步，false只有表不存在时同步
	  match:/reg/  //可以通过正则表达式指定同步符合正则的表名的模型
	})
	```
	
* 查询数据

  ```javascript
  var userModel = require('../models/user');

  exports.getIndex = async (req, res, next) => {
          let user = await userModel.findOne(); //查询数据
          res.render('index', { title: '微消息', user: user });
  };
  ```

## sequelize知识点

### 定义模型

#### 命令define

```
var ins = sequelize.define(modelName,obj1,obj2)
```

参数：

* modelName： 模型名称，对应到表会以复数的形式（+s）表示

* arg1： 对象结构形式

  ```
  {
    fieldName: {
      opt1: value1
      ...
      optn: valuen
    }
    ...
  }
  fieldName: 字段（属性）名，对应关系表中的字段
  opt ： 对字段的描述
  ```
  
  opt对应的描述：
  
  1. type：字段类型
  
     ```
     Sequelize.STRING                      // VARCHAR(255)
     Sequelize.STRING(1234)                // VARCHAR(1234)
     Sequelize.STRING.BINARY               // VARCHAR BINARY
     Sequelize.TEXT                        // TEXT
     Sequelize.TEXT('tiny')                // TINYTEXT
     
     Sequelize.INTEGER                     // INTEGER
     Sequelize.BIGINT                      // BIGINT
     Sequelize.BIGINT(11)                  // BIGINT(11)
     
     Sequelize.FLOAT                       // FLOAT
     Sequelize.FLOAT(11)                   // FLOAT(11)
     Sequelize.FLOAT(11, 12)               // FLOAT(11,12)
     
     Sequelize.REAL                        // REAL        PostgreSQL only.
     Sequelize.REAL(11)                    // REAL(11)    PostgreSQL only.
     Sequelize.REAL(11, 12)                // REAL(11,12) PostgreSQL only.
     
     Sequelize.DOUBLE                      // DOUBLE
     Sequelize.DOUBLE(11)                  // DOUBLE(11)
     Sequelize.DOUBLE(11, 12)              // DOUBLE(11,12)
     
     Sequelize.DECIMAL                     // DECIMAL
     Sequelize.DECIMAL(10, 2)              // DECIMAL(10,2)
     
     Sequelize.DATE                        // DATETIME for mysql / sqlite, TIMESTAMP WITH TIME ZONE for postgres
     Sequelize.DATE(6)                     // DATETIME(6) for mysql 5.6.4+. Fractional seconds support with up to 6 digits of precision 
     Sequelize.DATEONLY                    // DATE without time.
     Sequelize.BOOLEAN                     // TINYINT(1)
     
     Sequelize.ENUM('value 1', 'value 2')  // An ENUM with allowed values 'value 1' and 'value 2'
     Sequelize.ARRAY(Sequelize.TEXT)       // Defines an array. PostgreSQL only.
     
     Sequelize.JSON                        // JSON column. PostgreSQL only.
     Sequelize.JSONB                       // JSONB column. PostgreSQL only.
     
     Sequelize.BLOB                        // BLOB (bytea for PostgreSQL)
     Sequelize.BLOB('tiny')                // TINYBLOB (bytea for PostgreSQL. Other options are medium and long)
     
     Sequelize.UUID                        //   PostgreSQL 和 SQLite 中为 UUID, MySQL 中为CHAR(36) BINARY (使用 defaultValue: Sequelize.UUIDV1 或 Sequelize.UUIDV4 生成默认值)
     
     Sequelize.RANGE(Sequelize.INTEGER)    // Defines int4range range. PostgreSQL only.
     Sequelize.RANGE(Sequelize.BIGINT)     // Defined int8range range. PostgreSQL only.
     Sequelize.RANGE(Sequelize.DATE)       // Defines tstzrange range. PostgreSQL only.
     Sequelize.RANGE(Sequelize.DATEONLY)   // Defines daterange range. PostgreSQL only.
     Sequelize.RANGE(Sequelize.DECIMAL)    // Defines numrange range. PostgreSQL only.
     
     Sequelize.ARRAY(Sequelize.RANGE(Sequelize.DATE)) // Defines array of tstzrange ranges. PostgreSQL only.
     
     Sequelize.GEOMETRY                    // Spatial column.  PostgreSQL (with PostGIS) or MySQL only.
     Sequelize.GEOMETRY('POINT')           // Spatial column with geomerty type.  PostgreSQL (with PostGIS) or MySQL only.
     Sequelize.GEOMETRY('POINT', 4326)     // Spatial column with geomerty type and SRID.  PostgreSQL (with PostGIS) or MySQL only.
     ```
  
  2. primarykey： 是否是主键
  
  3. allowNull： 是否允许空
  
  4. defaultValue：默认值
  
  5. unique: 是否唯一
  
  6. autoincrement：布尔值，字段是否自增长
  
  7. comment：备注，虽然mysql现在同步时不会自动加上，但是用来查询模型时参考还是很有用的
  
  8. field：这个字段可以指定对应数据库表中的字段名称
  
  9. references：建立和其它模型（表）的外键关系。例如：
  
     ```
     references: {
     	model：'otherModel', //关联模型
     	key: 'field', //外键对应的字段
     	// 强制使用外键约束，仅适用于 PostgreSQL
       deferrable: Sequelize.Deferrable.INITIALLY_IMMEDIATE
     }
     ```
  
  
  10. values:指定字段取值范围。如：sex：{type:Sequelize.STRING,values:['feman','man']}
  
  11. get和set访问器：
  
      ```
      {
      	name{
      	  type:Sequelize.STRING,
      		get:function(){
      			var name = this.getDataValue('name');
      			var sex = this.getDataValue('sex');
      			
      			return firstName.concat('(',sex,')');
      		},
      		set: function(val){
      			return this.setDataValue('name',val.toUperCase())
      		}
      	}
      }
      ```
  
  12. validates：属性验证，下方的验证都由[validator.js](https://github.com/chriso/validator.js)实现。验证会在create，save和update时自动运行。也可也通过实例调用validate 进行校验
  
      ```
      var ValidateMe = sequelize.define('foo', {
        foo: {
          type: Sequelize.STRING,
          validate: {
            is: ["^[a-z]+$",'i'],     // 只允许字母
            is: /^[a-z]+$/i,          // 只允许字母
            not: ["[a-z]",'i'],       // 不能使用字母
            isEmail: true,            // 检测邮箱格式 (foo@bar.com)
            isUrl: true,              // 检查Url格式 (http://foo.com)
            isIP: true,               // 检查 IPv4 或 IPv6 格式
            isIPv4: true,             // 检查 IPv4
            isIPv6: true,             // 检查 IPv6
            isAlpha: true,            // 不能使用字母
            isAlphanumeric: true,     // 只允许字母数字字符
            isNumeric: true,          // 只能使用数字
            isInt: true,              // 只能是整数
            isFloat: true,            // 只能是浮点数
            isDecimal: true,          // 检查数字
            isLowercase: true,        // 检查小写字母
            isUppercase: true,        // 检查大写字母
            notNull: true,            // 不允许null
            isNull: true,             // 只能为null
            notEmpty: true,           // 不能空字符串
            equals: 'specific value', // 只能使用指定值
            contains: 'foo',          // 必须包含子字符串
            notIn: [['foo', 'bar']],  // 不能是数组中的任意一个值
            isIn: [['foo', 'bar']],   // 只能是数组中的任意一个值
            notContains: 'bar',       // 不能包含子字符串
            len: [2, 10],              // 值的长度必在 2 和 10 之间
            isUUID: 4,                // 只能是UUID
            isDate: true,             // 只能是日期字符串
            isAfter: "2011-11-05",    // 只能使用指定日期之后的时间
            isBefore: "2011-11-05",   // 只能使用指定日期之前的时间
            max: 23,                  // 允许的最大值
            min: 23,                  // 允许的最小值
            isArray: true,            // 不能使用数组
            isCreditCard: true,       // 检查是有效的信用卡
      
            // 也可以自定义验证:
            isEven: function(value) {
              if(parseInt(value) % 2 != 0) {
                throw new Error('Only even values are allowed!')
              // we also are in the model's context here, so this.otherField
              // would get the value of otherField if it existed
              }
            }
          }
        }
      });
      也可以修改参数和提示消息：
      isIn: {
        args: [['en', 'zh']], //这行注释，就意味着只修改这个验证的提示消息
        msg: "Must be English or Chinese"
      }
      ```
  
      
  
* arg2: 模型选项配置：

  1. underscored: 是否自动将驼峰表示法转换为mysql的下划线表示法。如firstName对应数据表字段first_name

  2. timestamps: 是否自动加入附加字段，createAt，updateAt。默认true，一般需要取消

  3. freezeTableName：一般将模型名映射到数据库表时会自动+s表示复数形式，如果这个选项设置为true，则直接应用模型名。

  4. indexes： 对模型建立索引,其值为数组形式，因为一张表可以建立多个索引，如

     ```javascript
     indexes: [ 
        //创建唯一索引。聚簇索引
       	{
           unique: true,
           fields: ['email']
         },
   
         // Creates a gin index on data with the jsonb_path_ops operator
       {
           fields: ['data'],
         using: 'gin',
           operator: 'jsonb_path_ops'
       },
     
         // By default index name will be [table]_[fields]
         // Creates a multi column partial index
         {
           name: 'public_by_author',
           fields: ['author', 'status'],
           where: {
             status: 'public'
           }
         },
     
         // A BTREE index with a ordered field
         {
           name: 'title_index',
           method: 'BTREE',
           fields: ['author', {attribute: 'title', collate: 'en_US', order: 'DESC', length: 5}]
       }],
     ```

     

  5. initialAutoIncrement：数字，设置主键默认自增的开始值

  6. getterMethods和setterMethods。模型选项中的get和set时用来提供哪些不需要持久化，其值来自其它属性的计算值时的模型属性，例如, 获取和设置一个人的全名：

     ```javascript
     var Foo = sequelize.define('foo',{/*attributes*/},{
     	getterMethods:{
     		fullName:function(){
     			return this.getDataValue('first_name')+' '+this.getDataValue('last_name');
     		}
     	},
     	setterMethods:{
     		fullName:function(val){
     			 var strs = val.split(" ");
     			 this.setDataValue('first_name',strs[0]);
     			 this.setDataValue('last_name', strs[1]);
     		}
     	}
     }
     //调用
     console.log(Foo.findOne().fullName); 
     Foo.findOne().fullName ="kavin hai";
                      
     //这里要很注意一个地方，自己踩过的坑。就是定义get和set函数时不要使用箭头函数如下：
     {
       getterMethods: {
         fullName:()=>{
                         return this.getDataValue('name') + ':' + this.getDataValue('id');
                     }
       }
     }
     //上面会提示“this.getDataValue is not a function”，因为箭头函数中this指向的context和function定义的不一样。特别要注意
     ```
  
     ***PS:检索底层属性值－总是使用`this.getDataValue()`和`this.setDataValue(val)`***
  
  7. tableName: 指定表名，用于新建模型和已有表进行映射
  
  8. paranoid： 是否真的从表中删除数据，如果true，不删除值增加一个deleteAt标示。这个必须timestamps启用
  
  9. validate：模型验证
  
     ```javascript
     {
       validate: {
         bothCoordsOrNone: function() {
           if ((this.latitude === null) !== (this.longitude === null)) {
             throw new Error('Require either both latitude and longitude or neither')
           }
         }
     ```
     
  10. 拓展对象和模型方法classMethods和instanceMethods：
  
      sequelize 4.0版本以下：
      
      ```javascript
      var Foo =sequelize.define('Foo',{},{
      	classMethods:{
      			modelMethod1:function(arg){
      					//ToDo something
      			}
      	},
      	instanceMethods:{
      			instanceMethod1:function(arg){
      					//ToDo something
      			}
      	}
      });
      
      Foo.modelMethod1(arg);
  Foo.build().instanceMethod1(arg);
      ```
      
      sequelzie 4.0以上版本就不能将拓展方法放到define中，而是通过
      
      ```
      module.exports = function(sequelize,dataTypes){
      	var Foo = sequelize.define('foo',{/**/},{/**/});
      	Foo.method1 = function(){/*内容*/}; //对类的拓展方法
      	Foo.prototype.method2 = function(){/*内容*/}; //对实例的方法拓展
      }
      ```
      
  11. version：是否启用乐观锁，值可以为false（默认，关闭），true和指定字段名。如果启用表示提交保存一个对象，如果对象在数据库中已经被修改则返回错误

#### 导入模型

通过导入模型方法sequelize.import方法，可以将模型定义放入一个单独的文件。例如，我们在在user.js定义一个user模型

```javascript
module.exports = function(sequelize,dataTypes){
  	return sequelize.define('user',{
      name:{dataTypes.String}
    })
}
//实质最总还是调用的define方法
```

然后在其它地方导入需要的模型

```javascript
var user = sequelize.import(__dir+'/models/user');
//或者直接使用回调函数
var user = sequelize.import((sequelize,dataTypes)=>{
  return sequelize.define('user',{
      name:{dataTypes.String}
    };
})
```



### 数据库同步

```
// 按模型同步，如果表存在，直接修改
Project.sync()
Task.sync()

// 强制创建
Project.sync({force: true}) // 这种方式会先删除表再重建

// 删除表
Project.drop()
Task.drop()

// 同步所有表不存在的模型
sequelize.sync()

// 强制同步所有模型，先删除表再重建.
// 可以通过match指定正则表达式，只强制同步匹配的表
sequelize.sync({force: true[, match:/_test/g]})

// 删除所有的模型对应的表
sequelize.drop()
```



### 数据检索和查询

假设instance为定义的模型的一个实例

* 查询单个记录

  ```
  // 按照id字段查询值为123的记录。当id字段不存在或者没有123的记录，返回null
  var ins = instance.findById(123);
  // 按照where条件(属性)查询，值返回第一个满足条件的记录。没有满足记录时返回null
  instance.findOne({
  			where:{ name:'tony' }
  			})
  ```

  

* 查询不存在是创建

  ```
  //查询名字为tony的记录，如果不存在则创建一个新的记录（名字tony，age为23）
  //返回一个数组[instance, created],数组第一个是实例，第二个是个布尔值，true表示是创建的，false表示已经存在查询返回的
  instance.findOrCreate({
  	where{ name:'tony'},
  	defaults:{ name:'tony',age:23}
  })
  
  ```

* findAll

  ```
  //查询多个满足条件的记录，如果没有条件限制，则查询所有的
  instance.findAll();//查询所有
  //限制条件
  instance.findAll({
  	where:{
  		name:'kavin'
  	}
  });
  
  //限制条件支持多种限制符
  instance.findAll(
  	where:{
  		id:{
  			[Op.and]: {a: 5}, //And {a = 5}
  			[Op.or]: [{a:5},{a:6}], //a=5 or a=6
  			[Op.gt]: 6, // id>6
  			[Op.gte]: 6, //id>=6
  			[Op.lt]: 5, //id <5
  			[Op.lte]: 5 //id<=5
  			[Op.ne]: 5, //id!=5
  			[Op.between]:[4.6], //4<id<6
  			[Op.notBetween]:[4,6], //id>=4 or id<=6.不再4-6区间的
  			[Op.in]:[4,5,6], //id是4，5，6其中一个
  			[Op.like]:'%hat', //模糊匹配，like '%hat'
  			[Op.notLike]:'%hat',// not like '%hat'
  			//下面的是PG所使用的
  			[Op.iLike]: '%hat',         // ILIKE '%hat' (case insensitive)  (PG only)
        [Op.notILike]: '%hat',      // NOT ILIKE '%hat'  (PG only)
        [Op.overlap]: [1, 2],       // && [1, 2] (PG array overlap operator)
        [Op.contains]: [1, 2],      // @> [1, 2] (PG array contains operator)
        [Op.contained]: [1, 2],     // <@ [1, 2] (PG array contained by operator)
        [Op.any]: [2,3]            // ANY ARRAY[2, 3]::INTEGER (PG only)
  		}
  	}
  );
  
  ```

  

* 复杂查询，多种限制条件组合

  ```
  instance.findAll(
  	{
  		where:{
  			name: 'kavin',
        [Op.or]:[
          { id: [1,2,3] },
          { id: { [Op.gt]: 10 } }
        ]
  		}
  	}
  );
  instance.findAll(
  	{
  		where:{
  			name: 'kavin',
        id:{
        	[Op.or]:[
        		[1,2,3],
        		{[Op.gt:10]}
        	]
        }
  		}
  	}
  );
  #最终计算出的sql：
  SELECT *
  FROM `instance`
  WHERE (
    `instance`.`name` = 'a project'
     AND (`instance`.`id` IN (1,2,3) OR `instance`.`id` > 10)
  )
  
  //not 的例子
  Project.findOne({
    where: {
      name: 'a project',
      [Op.not]: [
        { id: [1,2,3] },
        { array: { [Op.contains]: [3,4,5] } }
      ]
    }
  });
  //对应的sql
  SELECT *
  FROM `Projects`
  WHERE (
    `Projects`.`name` = 'a project'
     AND NOT (`Projects`.`id` IN (1,2,3) OR `Projects`.`array` @> ARRAY[3,4,5]::INTEGER[])
  )
  LIMIT 1
  ```

  

* 查询限制偏移和返回记录数控制

  ```
  user.findAll({
  	where:{/*condition*/},
  	limit: 8, //限制如果查询结果大于8条，只返回前8条
  	offset: 10, //查询记录跳过前10条记录，如果小于10，返回0条记录。limit在offset之后
  });
  ```

  

* 排序和分组

  ```
  something.findOne({
    order: [
      'name',
      // will return `name`
      'username DESC',
      // will return `username DESC` -- i.e. don't do it!
      ['username', 'DESC'],
      // will return `username` DESC
      sequelize.fn('max', sequelize.col('age')),
      // will return max(`age`)
      [sequelize.fn('max', sequelize.col('age')), 'DESC'],
      // will return max(`age`) DESC
      [sequelize.fn('otherfunction', sequelize.col('col1'), 12, 'lalala'), 'DESC'],
      // will return otherfunction(`col1`, 12, 'lalala') DESC
      [sequelize.fn('otherfunction', sequelize.fn('awesomefunction', sequelize.col('col'))), 'DESC']
      // will return otherfunction(awesomefunction(`col`)) DESC, This nesting is potentially infinite!
      [{ raw: 'otherfunction(awesomefunction(`col`))' }, 'DESC']
      // This won't be quoted, but direction will be added
    ]
  })
  ```

  

* findallAndCount
  findall和count的结合体，在多记录查询时返回所有满组条件的数量。配合limit 和offset可以完成分页

  ```
  Project
    .findAndCountAll({
       where: {
          title: {
            $like: 'foo%'
          }
       },
       offset: 10,
       limit: 2
    })
  ```

  

* 原始查询
  sequelize默认情况下构建的实例都是衍生自模型，通过实例可以实现对数据的删除和更新，所以有时候我们只需要显示纯数据。使用raw参数

  ```
  instance.findOne({
  	where:{},
  	raw: true  //返回的数据，没有方法
  })
  ```

* count
  统计数量

  ```
  instance.count();//统计所有
  instance.count({where:{}});//按条件统计
  ```

  

* max/min
  查询指定字段的最大/最小值

  ```
  instance.max('age') //查询整表中age字段的最大值
  instance.max（'age',{ where: {age:{[Op.lt]:20}}) //查询age小于20的最大值 
  
  instance.min('age') //查询整表中age字段的最小值
  instance.min（'age',{ where: {age:{[Op.gt]:20}}) //查询age大于20的最小值 
  ```

  

* sum
  求和

  ```
  instance.sum('age') //求整表的age之和
  instance.sum('age',{ where: {age:{[Op.gt]:20}});//求大于20的age之和
  ```

  