## 一 Node连接mysql
```JavaScript
let db=mysql.createConnection({host, port, user, password, database});
db.query('select * from user', (err, data)=>{});
```
连接池配置：
```JavaScript
const express = require('express');
const mysql = require('mysql');

let mysqlDB = mysql.createPool({
    host:'localhost',
    post:3306,
    user:'root',
    password:'root',
    database:'suibian'
});

let app = express();

app.get('/api',function (req,res) {
    let query = 'select * from students';
    mysqlDB.query(query,function (err,data) {
        let name = data[0].name;
        res.send(name);
    });
});

app.listen(3000);

```
## 二 Node连接mongodb
```JavaScript
const MongoClient = require('mongodb').MongoClient;
const MongoURL = 'mongodb://localhost:27017/';
function _connectDB(callback) {
    MongoClient.connect(MongoURL,function (err,db) {
        if(err){
            console.log('Mongo未连接');
            callback(err,'Mongo未连接');
            return;
        }
        console.log('Mongo已连接');
        callback(null, db);
    });
}

//插入一个数据
exports.insertOne = function (dbname,collectionName,json,callback) {
    _connectDB(function (err,db) {
        if(err){
            throw err;
            return;
        }
        let dbObj = db.db(dbname);
        dbObj.collection(collectionName).insertOne(json, function (err, result) {
            if (err) {
                throw err;
                db.close();
                return;
            }
            callback(null, result);
            db.close();
        });
    });
};

```
//使用：
```JavaScript
const db = require('./db');
db.insertOne('testdb','teacher',{'name':'lisi'},function (err,result) {
    if(err){
        console.log(err);
        return;
    }
    console.log('sucess');
});

```
## 三 ORM框架sequlize
```
sequelize框架需要额外安装mysql2 包
npm install mysql2 --save
npm install sequelize --save
```
```JavaScript
const Sequelize = require('sequelize');
const mysqlConfig = {
    database: 'lib1', // 使用哪个数据库
    username: 'root', // 用户名
    password: 'root', // 口令
    host: 'localhost', // 主机名
    port: 3306 // 端口号，MySQL默认3306
};
let sequelize = new Sequelize(
	mysqlConfig.database,
	mysqlConfig.username,
 	mysqlConfig.password, 
	{
    		host: mysqlConfig.host,
    		dialect: 'mysql',
    		pool: {
        		max: 5,
        		min: 0,
        		idle: 30000
    }
});

//sequelize与数据库直连
// (async ()=>{
//     let table = await sequelize.query("SELECT * FROM users");
//     console.log(table);
// })();

//ORM
let User = sequelize.define('user', {      
 //sequelize会给user自动添加s后缀
    id: {
        type: Sequelize.STRING(50),
        primaryKey: true
    },
    name: Sequelize.STRING(100),
    age: Sequelize.STRING(10),
}, {
    timestamps: false       //关闭sequelize默认的timestamp的功能
});

let name = 'ha';
let age = 22;
(async ()=>{
    let dog = await User.create({
        id:0,
        name:name,
        age:age
    });
})();


```