# 广东国地对接

## 获取UOCS Token

业务系统需要与UOCS通讯，需要先获取UOCS Token。

- 请求uri

```text
/basic/user/getToken
```

- 请求方法

```text
POST
```

- 请求参数类型

```text
application/json
```

- 请求示例

```json
{
  "username": "xxx",
  "password": "xxx"
}
```

- 请求参数说明

| 参数       | 类型     | 是否允许为空 | 说明  |
|----------|--------|--------|-----|
| username | String | 否      | 用户名 |
| password | String | 否      | 密码  |

- 响应参数类型

```text
application/json
```

- 响应结果示例

```json
{
  "token": "xxx"
}
```

## UOCS文件预处理

这一步并非必要操作，主要是为了优化用户体验。即使业务系统不操作，未初始化的文件在批签页面也会自动就行文件初始化操作。

- 请求uri

```text
/gd/preprocess/initAttachment
```

- 请求方法

```text
POST
```

- 请求示例

```json
[
  {
    "businessFileId": "",
    "name": "",
    "fileUrl": ""
  },
  {
    "businessFileId": "",
    "name": "",
    "fileUrl": ""
  }
]
```

- 请求参数说明

| 参数             | 类型     | 是否允许为空 | 说明     |
|----------------|--------|--------|--------|
| businessFileId | String | 否      | 业务系统id |
| name           | String | 否      | 文件名    |
| fileUrl        | String | 否      | 文件下载地址 |

- 响应参数类型

```text
application/json
```

- 响应结果示例

```json
{
  "code": 200,
  "message": "文件初始化消息发送成功",
  "data": [
    {
      "status": 1,
      "msg": null,
      "businessFileId": "1896749367729123330",
      "fileId": "67c6986c274fe5674fd36eb7"
    },
    {
      "status": 1,
      "msg": null,
      "businessFileId": "1896749391347249153",
      "fileId": "67c6986d274fe5674fd36eba"
    }
  ]
}
```

- 响应结果说明

| 参数             | 类型     | 是否允许为空 | 说明              |
|----------------|--------|--------|-----------------|
| businessFileId | String | 否      | 业务系统id          |
| fileId         | String | 否      | UOCS系统文件id      |
| status         | Int    | 否      | 预处理结果，1为成功，2为失败 |
| msg            | String | 是      | 错误消息            |

## 打开批签页面

业务系统打开批签页面。

- 示例url

```text
http://106.14.254.175:17003/?callback=http%3A%2F%2F106.14.254.175%3A18301%2Ffile%2FloadSignList
```

其中callback为批签页面回调地址，需要业务系统提供。该地址可以携带业务系统Token保证数据安全。

- 回调接口负责响应UOCS系统Token和待签章文件列表。示例如下：

```json
{
  "code": 200,
  "message": "批签文件目录获取成功",
  "data": {
    "uocsToken": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJ1c2VyIjoiMTg1MzcxNjA4Mzk3MzczNDQwMSIsImV4cCI6MTc0MTA4NjkyOH0.EFVDT2TiK5yowdztAANwxo91EMf7eE6OVGoyoK5_-ffPAWwWb5R_jHtf9Uvyktdzcl4fideSz4ypPktj2-Z6oT5MzkdZobXx57JhS21y3GL5zRgvuDaDV3j8sx1wxnTF1M7wDdKAy-0gu0pSjRPWMYIN1u2hL-NKeHzc88Zt-DqTPnSVl6y61rgd5aRoI0XVL6toCFBn8794CcCkB1iCKszVVMz4Vv5aNjo8KIyXjVVem51iVrRFSEiofoZxe-wXOb8qgUQWzJmQpCcTPKuKlziGkQmADuY3si78q5KSkX5Y33duJPAg7IyMDMYUXs3VYEqUnbKWTcrInYinF5NHiQ",
    "signFileInfoList": [
      {
        "name": "案例图.dwg",
        "businessFileId": "1896749367729123330",
        "fileUrl": "http://106.14.254.175:18301/file/downloadFile/1896749367729123330",
        "isLeaf": "true"
      },
      {
        "name": "目录1",
        "isLeaf": "false",
        "children": [
          {
            "name": "案例图.dwg",
            "businessFileId": "1896749391347249153",
            "fileUrl": "http://106.14.254.175:18301/file/downloadFile/1896749391347249153",
            "isLeaf": "true"
          }
        ]
      }
    ]
  }
}
```

- 回调接口响应参数说明

| 参数             | 类型     | 是否允许为空 | 说明                                |
|----------------|--------|--------|-----------------------------------|
| uocsToken      | String | 否      | UOCS系统的Token，通过事先提供给业务系统的用户名和密码获取 |
| name           | String | 否      | 文件名                               |
| businessFileId | String | 否      | 业务系统id                            |
| fileUrl        | String | 否      | 文件下载地址                            |

