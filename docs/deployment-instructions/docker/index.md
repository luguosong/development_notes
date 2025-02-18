# docker部署

## 文档结构

- `data`：存放容器产生的数据
- `uocs-app`：docker容器构建所需材料

!!! note

	之所及将data数据存放在`uocs-app目录`外部，是考虑到`svn`同步。

	这样做uocs-app内部所有文件都是需要同步至`svn`的，`data目录`中的数据不需要同步。这样做更加清晰。

!!! warning

	注意：容器启动所需要的配置文件，是存放在`uocs-app目录`中的，而不是放在`data目录`。

<figure markdown="span">
  ![](https://raw.githubusercontent.com/luguosong/images/master/blog-img/202502121700318.png){ loading=lazy }
  <figcaption>文档结构</figcaption>
</figure>

## 端口暴露

### 约定

- `17xxx`:前端服务
- `18xxx`:后端服务
- `19xxx`:数据库或其它

### 具体服务端口

#### 前端

- OpenCloud示例前端
	- `17001`
- 东莞模拟业务系统前端
	- `17002`
- 东莞批量签章定制
	- `17003`
- uocs前端
	- `17102`

#### 服务

- OpenCloud服务
    - `18001`:服务端口，供WebViewerDemo前端示例程序调用，如果不部署该Demo前端，则无需暴露此端口
- minio服务
    - `18100`：服务调用端口
    - `18101`：管理控制台端口
- uocs Java 服务
    - `18201:8201`:网关
    - `x:8202`：basic基础服务,前端通过网关访问服务，端口无需暴露
    - `x:8203`：openCloud服务，前端通过网关访问服务，端口无需暴露
    - `x:8204`：广东定制（包含批量签章对接相关接口），前端通过网关访问服务，端口无需暴露
- 东莞业务模拟系统
	- `18301:8301`:服务端口，供WebViewerDemo前端示例程序调用，如果不部署该Demo前端，则无需暴露此端口

#### 数据库

- mongo数据库
	- `19001:27017`：方便可视化工具连接，如果不需要连接则无需暴露此端口(建议内网环境中暴露，端口不公开)
- mysql
	- `19002:3306`:外部可视化工具方便访问

## 基础镜像制作

### OpenCloudServerBasic

- 根据Dockerfile生成镜像(此时镜像会保存在Docker环境中)

```shell
docker build -t open-cloud-server-basic:latest .
```

- 为了之后复用，将镜像导出为tar包。

```shell
docker save -o open-cloud-server-basic.tar open-cloud-server-basic
```

### JobRunner

- 创建离线镜像

```shell
docker build -t job-runner-basic:latest .
```

- 将离线镜像保存为tar包，方便后续复用。

```shell
docker save -o job-runner-basic.tar job-runner-basic
```

## 容器创建前的准备

### OpenCloudServer

docker环境下，OpenCloudServer不再读取`appsettings.json`,配置文件统一在`compose.yaml`文件中通过环境变量进行配置。

<figure markdown="span">
  ![](https://raw.githubusercontent.com/luguosong/images/master/blog-img/202502121726547.png){ loading=lazy }
  <figcaption>使用环境变量配置OpenCloudServer</figcaption>
</figure>

## 启动容器

```shell
docker compose up
```

## 容器启动后的配置
