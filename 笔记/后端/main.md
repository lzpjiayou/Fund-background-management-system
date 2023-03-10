资金后台管理系统
1.创建后端接口文档：node + express + jwt +mongodb

2.构建前端页面：vue +  element-ui

3.数据请求拦截：Axios


1.创建一个服务器端口
利用express创建一个Web的服务器，在server.js
安装express:  npm i express -s

const express = require('express')
const app = express()

const port = process.env.PORT || 5000;

绑定并监听指定主机和端口上的连接
app.listen(port, () => {
  console.log(`server running on port ${port}`);
});

用get方法来指定路径
app.get("/", (req, res) => {
  res.send("hello world");
});

使用nodemon来监听文件的改动
npm install -g nodemon
好处是：代码被修改之后，会被 nodemon 监听到，从而实现自动重启项目的效果。


2.连接本地数据库MongoDB
npm安装mongoose来操作数据库，并引入到server.js文件中
npm i mongoose
const mongoose = require("mongoose");

在server.js同目录下创建config文件夹来存放数据库的地址信息
key.js下
module.exports = {
  mongoURI: "mongodb://127.0.0.1:27017/test",
  secretOrKey: "secret",
};

在server.js中引入数据库的地址信息
const db = require("./config/keys").mongoURI;
使用mongoose连接数据库
mongoose
  .connect(db)
  .then(() => {
    console.log("MongoDB connected!");
  })
  .catch((err) => {
    console.log(err);
  });

在server.js同目录下创建model文件夹来存放对应的数据库变量User和Profiles

//in server.js
//引入body-parser,bodyparser是一类处理request的body的中间件函数,对传入的请求体进行解析
const bodyParser = require("body-parser");

//这是常用的方法，常见的前端请求解决方案如表单post提交、axios、fetch等库的post请求都需要这个中间件进行解析，返回json的格式数据。
当请求的数据类型是application/x-www-form-urlencoded时才会进入这个中间件进行处理。
app.use(bodyParser.urlencoded({ extended: false }));

//解析并返回 json格式的数据，这是常用的方法。内部会查看content-type，只有是正确的content-type默认是application/json才进入这个中间件解析处理。
app.use(bodyParser.json());

////引入登录和路由模块
const users = require("./routes/api/users");

const profiles = require("./routes/api/profiles");

//在指定的路径上挂载指定的中间件函数:当请求的路径的基础部分与路径匹配时，中间件函数将执行。
app.use("/api/users", users);

app.use("/api/profiles", profiles);

引入passport来验证token，初始化
const passport = require("passport");
在config文件夹下创建passport.js
require("./config/passport")(passport);
app.use(passport.initialize());
