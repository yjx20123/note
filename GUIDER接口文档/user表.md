user表设计

|  字段名称   |    数据类型    | 是否为空 |       备注       |
| :---------: | :------------: | :------: | :--------------: |
|     id      |      int       |    N     | 用户编号，PK主键 |
|  username   | varchar（255） |    N     |      用户名      |
|  password   | varchar (255)  |    N     | 登录密码（加密） |
|   mobile    |  varchar(255)  |    Y     |     手机号码     |
|   avatar    | varchar（255） |    Y     |       头像       |
| create_time |    datetime    |    Y     |     创建时间     |
| update_time |    datetime    |    Y     |     更新时间     |

------

[TOC]

---

###### 1、用户登录

​	功能：登录是便于用户管理自己的个人设备，查看设备实时数据

​	请求方式：`post`

​	请求路径: http://agric.lo3.cn/user/login

​	请求参数：username/password

 	请求返回：

```json
{
  "message": "登录成功",
  "code": 200,
  "user": {
    "username": "alger",
    "password": "$2b$12$VRdsElq0qseGNXeFeDs2n.XGdeeRrfoJ.NW8E0L5/avmskR.QwFgy",
    "id": 13,
    "avatar": "https://portrait.gitee.com/uploads/avatars/user/1750/5250172_algerkong_1623313500.png!avatar100",
    "mobile": "15234562389"
  },
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJhbGdlciIsInVzZXJfaWQiOjEzLCJ1c2VybmFtZSI6ImFsZ2VyIiwiYXZhdGFyIjoiaHR0cHM6Ly9wb3J0cmFpdC5naXRlZS5jb20vdXBsb2Fkcy9hdmF0YXJzL3VzZXIvMTc1MC81MjUwMTcyX2FsZ2Vya29uZ18xNjIzMzEzNTAwLnBuZyFhdmF0YXIxMDAiLCJleHAiOjE2MjgwNjU1ODV9.ClLHUM8wGGyD8kXybfCMXuNlgKh8Hamftkcyxlxi3uw"
}
```



​	

###### 2、 用户注册

​	功能：用户首次登录时，需要注册账号

​	请求方式：`post`

​	请求路径：http://agric.lo3.cn/user/register

​	请求参数：username、password、mobile

​	请求返回：

```json
[
  "注册成功"
]
```

> 注意事项：同一个手机号无法注册两次



###### 3、验证码

​	功能：当用户注册时，返回手机验证

​	请求功能：`post`

​	请求路径：http://agric.lo3.cn/user/code

​	请求参数：mobile

>注意事项：验证码不得超过60秒



###### 4、上传头像

​	功能：修改头像

​	请求功能：`post`

​	请求路径：http://agric.lo3.cn/user/avatar?userid=1

​	请求参数：id 

​	请求返回：

```json
[
  "修改成功"
]
```



###### 5、获取所有用户

​	功能：获取所有已注册的用户

​	请求功能：`get`

​	请求路径：http://agric.lo3.cn/user

​	请求参数：page/count

​	请求返回：

```json
{
  "code": 200,
  "message": "Success",
  "data": [
    {
      "id": 1,
      "password": "$2b$12$wgSrgbDnItERASJ55VAlceVmsazY7HXA3OTYW4PQG.oqWvuZR0aWi",
      "avatar": "/static/33ff5c96c4764f9fb27a54e62632d389.jpg",
      "update_time": "2021-12-21T15:03:27",
      "username": "ljj",
      "mobile": "19121900181",
      "create_time": "2021-09-24T13:00:54"
    }
  ]
}
```





###### 6、用户token令牌

​	功能：获取用户信息，携带token

​	请求功能：`get`

​	请求路径：http://agric.lo3.cn/user/info

​	请求参数：token



###### 7、修改用户信息

​	功能：更改用户资料

​	请求功能：`put`

​	请求路径：http://agric.lo3.cn/user

​	请求参数：id

​	请求返回：

```json
{
  "code": 200,
  "message": "更新成功"
}
```



###### 8、删除用户

​	功能：当用户注册时，返回手机验证

​	请求功能：`delete`

​	请求路径：http://agric.lo3.cn/user

​	请求参数：id

​	请求返回：

```json
{
  "code": 200,
  "message": "删除成功"
}
```





###### 9、修改用户密码

​	功能：修改密码

​	请求功能：`put`

​	请求路径：http://agric.lo3.cn/user/pwd

​	请求参数：id

​	请求返回：

```json
{
  "code": 200,
  "message": "修改成功"
}
```



