# JobRunner

Open Cloud Server使用`job`实体来描述将文件从一种格式转换为另一种格式或获取几何数据的过程。服务器仅允许您创建和接收任务信息，而任务的执行由
JobRunner 应用程序完成。

以下示意图展示了 JobRunner 的工作原理：

<figure markdown="span">
  ![](https://raw.githubusercontent.com/luguosong/images/master/blog-img/202503111052393.png){ loading=lazy }
  <figcaption>JobRunner 的工作原理</figcaption>
</figure>

1. 客户端通过REST API创建一个新任务。
2. JobRunner从Open Cloud Server请求任务。
3. JobRunner从Open Cloud Server加载文件进行处理。
4. 应用程序根据任务参数在本地处理文件。
5. JobRunner上传处理结果并更新服务器上的任务状态。

## 用法

要使用 Jobrunner 从归档文件中启动，请打开控制台并将当前目录切换到解压 WebTools 归档文件的文件夹。

Windows: `/WebTools/exe/vc<platformId>`

Linux: `/WebTools/bin/lnxX<platformId>`

然后运行以下命令：

```shell
JobRunner --token=df1dceb8e2d6f6b0894363b085801e36 --host=127.0.0.1 --port=8081 --name=basic_runner --waitTimeOut=10000 --rootUrl=/  

```

## 选项

| 变量            | 描述                                         |
|---------------|--------------------------------------------|
| --host        | 服务器主机名。                                    |
| --port        | 服务器端口。                                     |
| --storagePath | 服务器存储目录的完整路径。（已弃用）将在未来移除                   |
| --name        | 要在服务器上注册并在响应GET /runners请求时显示的JobRunner名称。 |
| --waitTimeOut | 向服务器请求新作业之间的延迟时间。                          |
| --rootUrl     | 接收Job的URL前缀。在服务器位于另一个代理服务器后面的情况下，这是必需的。    |
| --cachePath   | 保存本地缓存的目录路径。                               |
