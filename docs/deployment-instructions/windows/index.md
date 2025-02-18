# Windows环境部署

## 安装Mongo

安装最新的 MongoDB LTS 版本。Open Cloud Server 从 3.6 版本开始支持与 MongoDB 协作。

对于 Windows
系统，您可以使用此安装程序：[https://www.mongodb.com/try/download/community](https://www.mongodb.com/try/download/community)

## 安装Minio

[下载地址](https://dl.min.io/server/minio/release/windows-amd64/minio.exe)

- minio.exe是绿色安装的。
- 例如存放在: D:\Minio\minio.exe. 并新建文件夹: D:\Minio\data\
- 在D:\Minio\使用CMD启动命令行，输入: `minio.exe server D:\Minio\data --console-address ":18101"`启动minio程序。

