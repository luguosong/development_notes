# docker部署

## 文档结构

- `data`：存放容器产生的数据
- `uocs-app`：docker容器构建所需材料

!!! note

	之所及将data数据存放在`uocs-app目录`外部，是考虑到`svn`同步。

	这样做uocs-app内部所有文件都是需要同步至`svn`的，`data目录`中的数据不需要同步。这样做更加清晰。

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
	- `17001:80`
- 东莞模拟业务系统前端
	- `17002:80`
- 东莞批量签章定制
	- `17003:80`
- GMCore前端
  - `17004:80`
- uocs前端
	- `17102:80`

#### 服务

- OpenCloud服务
	- `18001:8001`:服务端口，供WebViewerDemo前端示例程序调用，如果不部署该Demo前端，则无需暴露此端口
- GMCore服务
	- `18002:8002`
- minio服务
	- `18100:8100`：服务调用端口
	- `18101:8101`：管理控制台端口
- nacos服务
	- `18848:8848`
	- `19848:9848`
- uocs Java 微服务
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
- redis
	- `19003:6379`

## 基础镜像制作

### OpenCloudServerBasic

- 根据Dockerfile生成镜像(此时镜像会保存在Docker环境中)

```shell
docker build -t aspnet-basic:latest .
```

- 为了之后复用，将镜像导出为tar包。

```shell
docker save -o aspnet-basic.tar aspnet-basic
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

## 容器首次启动前初始化

### OpenCloudServer配置说明

docker环境下，OpenCloudServer不再读取`appsettings.json`,配置文件统一在`compose.yaml`文件中通过环境变量进行配置。

<figure markdown="span">
  ![](https://raw.githubusercontent.com/luguosong/images/master/blog-img/202502121726547.png){ loading=lazy }
  <figcaption>使用环境变量配置OpenCloudServer</figcaption>
</figure>

### 配置文件预处理

#### 设计思路

管理配置文件有两种方案，各有利弊。

`方式一`是在镜像创建阶段，在Dockerfile文件中使用`COPY指令`将配置文件拷贝至镜像中。这样配置文件会被打包到镜像中，镜像本身是完整的，运行容器时不需要额外的配置。镜像可以在任何支持
Docker 的环境中运行，无需依赖外部文件。但如果需要修改 config.json，必须重新构建镜像并重新部署容器，不够灵活。

方式一还存在一个问题，`每次svn同步，都会覆盖修改后的配置文件`。这会使每次svn同步后都需要重新手动配置config.json配置文件。

<figure markdown="span">
  ![](https://raw.githubusercontent.com/luguosong/images/master/blog-img/202502181727335.png){ loading=lazy }
  <figcaption>每次svn同步，都会覆盖修改后的配置文件</figcaption>
</figure>

`方式二`是采用docker compose配置文件中的volumes属性进行文件挂载。

<figure markdown="span">
  ![](https://raw.githubusercontent.com/luguosong/images/master/blog-img/202502181731138.png){ loading=lazy }
  <figcaption>使用docker compose挂载配置文件</figcaption>
</figure>

这样做可以随时修改宿主机上的 config.json 文件，而无需重新构建镜像或重启容器。可以直接在宿主机上编辑配置文件，快速验证效果。

但是这里有一个坑，在首次启动docker
compose时，如果宿主机中不存在对应的配置文件，docker并不会从容器中拷贝config.json配置文件到宿主机。而是会直接在宿主机中创建名为config.json的文件夹，这显然是错误的。

<figure markdown="span">
  ![](https://raw.githubusercontent.com/luguosong/images/master/blog-img/202502181738381.png){ loading=lazy }
  <figcaption>如果宿主机中不存在对应配置文件</figcaption>
</figure>

所有我们在第一次启动容器前，需要先手动创建对应配置文件，具体操作如下。

#### 手动操作

!!! warning

	这些操作都需要在`首次`执行`docker compose up`前执行，否则首次容器启动会发生异常。

- 拷贝WebViewerDemo前端项目配置文件到data目录，并修改其中的ip和端口

<figure markdown="span">
  ![](https://raw.githubusercontent.com/luguosong/images/master/blog-img/202502191426821.png){ loading=lazy }
  <figcaption>拷贝WebViewerDemo前端配置文件</figcaption>
</figure>

- 拷贝UOCSClient前端项目配置文件到data目录，并修改其中的ip和端口

<figure markdown="span">
  ![](https://raw.githubusercontent.com/luguosong/images/master/blog-img/202502191446854.png){ loading=lazy }
  <figcaption>拷贝UOCSClient前端配置文件</figcaption>
</figure>

- 拷贝BusinessClient前端项目配置文件到data目录，并修改其中的ip和端口

<figure markdown="span">
  ![](https://raw.githubusercontent.com/luguosong/images/master/blog-img/202502191533717.png){ loading=lazy }
  <figcaption>拷贝BusinessClient前端配置文件</figcaption>
</figure>

- 拷贝BatchSigningClient前端项目配置文件到data目录，并修改其中的ip和端口

<figure markdown="span">
  ![](https://raw.githubusercontent.com/luguosong/images/master/blog-img/202502191543941.png){ loading=lazy }
  <figcaption>拷贝BatchSigningClient前端配置文件</figcaption>
</figure>

#### 简化操作

考虑到手动拷贝的繁琐，在安装包中准备了`初始data`包,可以将`InitData目录`中准备好的被指文件直接拷贝到data目录。

<figure markdown="span">
  ![](https://raw.githubusercontent.com/luguosong/images/master/blog-img/202502191557357.png){ loading=lazy }
  <figcaption>将InitData目录中的数据拷贝到data中</figcaption>
</figure>

## 容器首次启动

```shell
# -d 表示后台启动（可选）
docker compose up -d
```

## 容器启动后的配置

