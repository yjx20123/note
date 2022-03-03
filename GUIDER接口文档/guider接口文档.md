[TOC]

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

---

###### 1、用户登录

	功能：登录是便于用户管理自己的个人设备，查看设备实时数据
	
	请求方式：`post`
	
	请求路径: localhost:[端口号]/user/login
	
	请求参数：username/password
	
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

	功能：用户首次登录时，需要注册账号
	
	请求方式：`post`
	
	请求路径：localhost:[端口号]/user/register
	
	请求参数：username、password、mobile
	
	请求返回：

```json
[
  "注册成功"
]
```

> 注意事项：同一个手机号无法注册两次



###### 3、验证码

	功能：当用户注册时，返回手机验证
	
	请求功能：`post`
	
	请求路径：localhost:[端口号]/user/code
	
	请求参数：mobile

>注意事项：验证码不得超过60秒



###### 4、上传头像

	功能：修改头像
	
	请求功能：`post`
	
	请求路径：localhost:[端口号]/user/avatar?userid=1
	
	请求参数：id 
	
	请求返回：

```json
[
  "修改成功"
]
```



###### 5、获取所有用户

	功能：获取所有已注册的用户
	
	请求功能：`get`
	
	请求路径：localhost:[端口号]/user
	
	请求参数：page/count
	
	请求返回：

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

	功能：获取用户信息，携带token
	
	请求功能：`get`
	
	请求路径：localhost:[端口号]/user/info
	
	请求参数：token



###### 7、修改用户信息

	功能：更改用户资料
	
	请求功能：`put`
	
	请求路径：localhost:[端口号]/user
	
	请求参数：id
	
	请求返回：

```json
{
  "code": 200,
  "message": "更新成功"
}
```



###### 8、删除用户

	功能：当用户注册时，返回手机验证
	
	请求功能：`delete`
	
	请求路径：localhost:[端口号]/user
	
	请求参数：id
	
	请求返回：

```json
{
  "code": 200,
  "message": "删除成功"
}
```





###### 9、修改用户密码

	功能：修改密码
	
	请求功能：`put`
	
	请求路径：localhost:[端口号]/user/pwd
	
	请求参数：id
	
	请求返回：

```json
{
  "code": 200,
  "message": "修改成功"
}
```
| 字段名称    | 数据类型     | 是否为空 | 备注           |
| ----------- | ------------ | -------- | -------------- |
| id          | int          | N        | PK主键         |
| userid      | int          | Y        | 外键关联userId |
| devicecard  | varchar(255) | Y        | 设备编号       |
| devicename  | varchar(255) | Y        | 设备名         |
| longitude   | varchar(255) | Y        | 经度           |
| latitude    | varchar(255) | Y        | 纬度           |
| status      | varchar(255) | Y        | 状态           |
| create_time | datetime     | Y        | 激活时间       |
| start_time  | datetime     | Y        | 开启时间       |
| last_time   | datetime     | Y        | 离线时间       |

---

###### 10、添加设备

​	请求方式:`post`

​	请求路径：localhost:[端口号]/cropsdevice/add

​	请求返回：

```json
{
  "status_code": 200,
  "detail": "添加设备成功",
  "headers": null
}
```



###### 11、查询单个设备

​	请求方式：`get`

​	请求参数：id

​	请求路径：localhost:[端口号]/cropsdevice/one

​	请求返回：

```json
{
  "code": 200,
  "message": "Success",
  "data": {
    "userid": 1,
    "devicecard": "321",
    "create_time": "2021-10-13T17:07:54",
    "latitude": null,
    "last_time": null,
    "id": 5,
    "devicename": "312",
    "longitude": null,
    "start_time": null,
    "status": null
  }
}
```



###### 12、查询当前用户所有设备

​	请求方式：`get`

​	请求路径：localhost:[端口号]/cropsdevice

​	请求参数：id，page，count

>示例：localhost:[端口号]/cropsdevice?id=1&page=1&count=5

​	请求返回：

```json
{
  "code": 200,
  "message": "Success",
  "data": [
    {
      "userid": 1,
      "devicecard": "321",
      "create_time": "2021-10-13T17:07:54",
      "latitude": null,
      "last_time": null,
      "id": 5,
      "devicename": "312",
      "longitude": null,
      "start_time": null,
      "status": null
    },
    {
      "userid": 1,
      "devicecard": "GUIDER20211127123428",
      "create_time": "2021-11-27T12:34:27",
      "latitude": "0",
      "last_time": "2021-11-27T12:34:28",
      "id": 22,
      "devicename": "Mark Garcia",
      "longitude": "0",
      "start_time": "2021-11-27T12:34:28",
      "status": "0"
    }
  ]
}
```



###### 13、修改设备

​	请求方式：`put`

​	请求路径：localhost:[端口号]/cropsdevice

​	请求参数：id

> 示例：localhost:[端口号]/cropsdevice?id=1

​	请求返回：

```json
{
  "code": 200,
  "message": "Success",
  "data": 1
}
```



###### 14、删除设备

​	请求方式：`delete`

​	请求路径：localhost:[端口号]/cropsdevice

请求参数：id

> 示例：localhost:[端口号]/cropsdevice?id=1

​	请求返回：

```json
{
  "code": 200,
  "message": "Success",
  "data": 1
}
```
feedback表

|  字段名称   | 数据类型 | 是否为空 |             备注              |
| :---------: | :------: | :------: | :---------------------------: |
|     id      |   int    |    N     |            PK主键             |
|   userid    |   int    |    Y     |        关联外键userId         |
|   content   | varchar  |    Y     |           意见反馈            |
| create_time | datetime |    Y     |           创建时间            |
| solve_time  | datetime |    Y     |           解决时间            |
| bool_solve  |   int    |    Y     | 解决状态:0为未解决，1为已解决 |

---

###### 15、添加意见反馈

​	功能：用户提出问题

​	请求方式：`post`

​	请求路径: localhost:[端口号]/feedback/add

​	请求参数：userId,content

​	请求返回：

```json
{
  "code": 200,
  "detail": "意见反馈成功!!!"
}
```

###### 16、查看当前用户全部反馈

​	功能：反馈分页

​	请求方式：`get`

​	请求路径: localhost:[端口号]/feedback

​	请求参数：id:用户id/page：页数/count :条数

​	请求返回：

```json
{
  "code": 200,
  "message": "Success",
  "data": [
    {
      "solve_time": "2021-09-28T10:15:06",
      "userid": 1,
      "create_time": "2021-09-28T10:15:06",
      "bool_solve": 0,
      "id": 14,
      "content": "666"
    }
  ]
}
```

###### 17、是否解决

​	功能：修改状态码

​	请求方式：`put`

​	请求路径: localhost:[端口号]/feedback

​	请求参数：id: 意见反馈id

​	请求返回：

```json
{
  "status_code": 200,
  "detail": "修改成功",
  "headers": null
}
```

###### 18、删除意见反馈

​	功能：删除反馈记录

​	请求方式：`delete`

​	请求路径: localhost:[端口号]/feedback

​	请求参数：id: 意见反馈id

​	请求返回：

```json
{
  "status_code": 200,
  "detail": "删除成功",
  "headers": null
}
```

|  字段名称  |    数据类型    | 是否为空 |   备注   |
| :--------: | :------------: | :------: | :------: |
|     id     |      int       |    N     |  PK主键  |
| cropstype  | varchar（255） |    Y     | 作物分类 |
| type image |  varchar(255)  |    Y     | 分类图片 |
| cropsrole  |  varchar(255)  |    Y     | 作物作用 |

---

###### 19、添加种类

​	请求方式：`post`

​	请求路径：localhost:[端口号]/cropstype/add

###### 20、查看所有种类

​	请求方式：`get`

​	请求路径：localhost:[端口号]/cropstype

​	请求返回：

```json
[
  {
    "id": 1,
    "cropstype": "测试",
    "cropsrole": "修改",
    "db_cropsnames": [
      {
        "cropsname": "小白菜",
        "cropsrole": null,
        "sTMP_MAX": null,
        "sTMP_MIN": null,
        "sHR_MAX": null,
        "sHR_MIN": null,
        "sPH_MAX": null,
        "sPH_MIN": null,
        "PP_MAX": null,
        "PP_MIN": null,
        "NN_MAX": null,
        "NN_MIN": null,
        "SEC_MAX": null,
        "SEC_MIN": null,
        "KK_MAX": null,
        "KK_MIN": null
      }
    ]
  }
 
]
```



###### 21、修改种类

​	请求方式：`put`

​	请求路径：localhost:[端口号]/cropstype

​	请求参数：Id

>示例：localhost:[端口号]/cropstype?id=1

​	请求返回：

```json
{
  "code": 200,
  "message": "Success",
  "data": 1
}
```



###### 22、删除种类

​	请求方式：`delete`

​	请求路径：localhost:[端口号]/cropstype

​	请求参数：Id

> 示例：localhost:[端口号]/cropstype?id=1

​	请求返回：

```json
{
  "code": 200,
  "message": "Success",
  "data": 1
}
```

|  字段名称   |   数据类型   | 是否为空 |       备注       |
| :---------: | :----------: | :------: | :--------------: |
|     id      |     int      |    N     |      PK主键      |
| cropstypeid |     int      |    Y     |     外键关联     |
|  cropsname  | varchar(255) |    Y     |     作物名称     |
|  nameimage  | varchar(255) |    Y     |     作物图片     |
|  cropsrole  | varchar(255) |    Y     |     作物作用     |
|  sTMP_MAX   | varchar(255) |    Y     |  土壤温度最大值  |
|  sTMP_MIN   | varchar(255) |    Y     |  土壤温度最小值  |
|   sHR_MAX   | varchar(255) |    Y     |  土壤湿度最大值  |
|   sHR_MIN   | varchar(255) |    Y     |  土壤湿度最小值  |
|   sPH_MAX   | varchar(255) |    Y     |   土壤PH最大值   |
|   sPH_MIN   | varchar(255) |    Y     |   土壤PH最小值   |
|   PP_MAX    | varchar(255) |    Y     |   土壤磷最大值   |
|   PP_MIN    | varchar(255) |    Y     |   土壤磷最小值   |
|   NN_MAX    | varchar(255) |    Y     |   土壤氮最大值   |
|   NN_MIN    | varchar(255) |    Y     |   土壤氮最小值   |
|   SEC_MAX   | varchar(255) |    Y     | 土壤导电率最大值 |
|   SEC_MIN   | varchar(255) |    Y     | 土壤导电率最小值 |
|   KK_MAX    | varchar(255) |    Y     |   土壤钾最大值   |
|   KK_MIN    | varchar(255) |    Y     |   土壤钾最小值   |
| create_time |   datetime   |    Y     |     创建时间     |

---

###### 23、添加农作物参数

​	请求方式：`post`

​	请求路径：localhost:[端口号]/cropsname/add

###### 24：查看所有作物参数

​	请求方式：`get`

​	请求路径：localhost:[端口号]/cropsname

​	请求返回：

```json
{
  "code": 200,
  "message": "Success",
  "data": [
    {
      "id": 1,
      "NN_MIN": null,
      "sHR_MIN": null,
      "cropstypeid": 1,
      "SEC_MAX": null,
      "sPH_MAX": null,
      "cropsname": "小白菜",
      "SEC_MIN": null,
      "sPH_MIN": null,
      "KK_MAX": null,
      "nameimage": null,
      "PP_MAX": null,
      "cropsrole": null,
      "KK_MIN": null,
      "PP_MIN": null,
      "sTMP_MAX": null,
      "create_time": null,
      "NN_MAX": null,
      "sTMP_MIN": null,
      "sHR_MAX": null
    }
  ]
}
```



###### 25：修改作物参数

​	请求方式：`put`

​	请求路径：localhost:[端口号]/cropsname

​	请求参数：id

> 示例：localhost:[端口号]/cropsname?id=1

###### 26：删除作物参数

​	请求方式：`delete`

​	请求路径：localhost:[端口号]/cropsname

​	请求参数：id

> 示例：localhost:[端口号]/cropsname?id=1

 |  字段名称   |   数据类型   | 是否为空 |    备注    |
 | :---------: | :----------: | :------: | :--------: |
 |     id      |     int      |    N     |   PK主键   |
 |  deviceid   |     int      |    N     |  外键关联  |
 | create_time |   datetime   |    Y     |  存入时间  |
 |  cropsname  | varchar(255) |    Y     |  作物名称  |
 |    sTMP     | varchar(255) |    Y     |  土壤温度  |
 |     sHR     | varchar(255) |    Y     |  土壤湿度  |
 |     sPH     | varchar(255) |    Y     |   土壤PH   |
 |     PP      | varchar(255) |    Y     |   土壤磷   |
 |     NN      | varchar(255) |    Y     |   土壤氮   |
 |     sEC     | varchar(255) |    Y     | 土壤导电率 |
 |     KK      | varchar(255) |    Y     |   土壤钾   |

---

###### 27、添加数据

​	请求方式：`post`

​	请求路径：localhost:[端口号]/data/add

​	请求返回：

```json
{
  "code": 200,
  "message": "Success",
  "data": {}
}
```



###### 28、获取所有数据

​	请求方式：`get`

​	请求路径：localhost:[端口号]/data

​	请求参数：id,page,count

​	请求返回：

```json
{
  "code": 200,
  "message": "Success",
  "data": [
    {
      "id": 11980,
      "create_time": "2021-12-22T15:05:04",
      "sTMP": "string",
      "sPH": "string",
      "NN": "string",
      "KK": "string",
      "deviceid": 1,
      "cropsname": "花生",
      "sHR": "string",
      "PP": "string",
      "sEC": "string"
    }
      ]
}
```



###### 29、 修改数据

​	请求方式：`put`

​	请求路径：localhost:[端口号]/data

​	请求参数：id

​	请求返回：

```json
{
  "code": 200,
  "message": "Success",
  "data": 1
}
```



###### 30、删除数据

​	请求方式：`delete`

​	请求路径：localhost:[端口号]/data

​	请求返回：

```json
{
  "code": 200,
  "message": "Success",
  "data": 1
}
```



###### 31、查询当天所有数据

​	请求方式：`get`

​	请求路径：localhost:[端口号]/data/add

​	请求返回：

```json
{
  "code": 200,
  "message": "Success",
  "data": [
    {
      "id": 1,
      "count": 1,
      "percent": 0
    }
  ]
}
```



---

###### 32、模拟添加设备

​	请求方式：`post`

​	请求路径：localhost:[端口号]/simulatation/adddevice

​	请求参数：num(生成条数)

> 示例:localhost:[端口号]/simulatation/adddevice?num=1

​	请求返回：

```json
{
  "status_code": 200,
  "detail": "添加设备成功",
  "headers": null
}
```



###### 33、模拟添加数据

​	请求方式：`post`

​	请求路径：localhost:[端口号]/simulatation/adddata

​	请求参数：num(生成条数)

> 示例：localhost:[端口号]/simulatation/adddata?num=1

​	请求返回：

```json
{
  "status_code": 200,
  "detail": "添加数据成功",
  "headers": null
}
```



###### 34、获取设备状态数量

请求方式：`get`

​	请求路径：localhost:[端口号]/echart/num

​	请求参数：id

> 示例：localhost:[端口号]/echart/num?id=1

请求返回：

```json
{
  "code": 200,
  "message": "Success",
  "data": {
    "online": 2,
    "stanby": 0,
    "fault": 1,
    "offline": 0
  }
}
```

**online**:在线

__stanby__:待机

__fault__:损坏

**offline**:离线

###### 35、天气查询

​	请求方式：`get`

​	请求路径：localhost:[端口号]/weather

​	请求参数：longitude/latitude（经纬度）

> 示例：localhost:[端口号]/weather?longitude=1&latitude=1

​	请求返回：

```json
{
  "endtime": "截至 15:24 CST",
  "findadd": "东城区 天气",
  "temperature": "5°",
  "weathercon": "晴朗",
  "sunrise": "07:33",
  "sunset": "16:52",
  "humidity": "40%",
  "precipitation": "0%",
  "airpressure": "1024.0 毫巴",
  "airquality": "60",
  "airquality_l": "良"
}
```

