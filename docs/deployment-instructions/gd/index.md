# 广东国地批签定制部署

## 创建测试用户

`gd-business-system-server项目(模拟业务系统后端)`配置写死的uocs用户和密码。

<figure markdown="span">
  ![](https://raw.githubusercontent.com/luguosong/images/master/blog-img/202504231750056.png){ loading=lazy }
  <figcaption>模拟业务系统后端测试</figcaption>
</figure>

如果需要使用模拟业务系统进行测试，需要创建`测试用户`。

可以使用uocs网关提供的swagger页面进行用户注册，地址为`http://网关ip:网关端口/doc.html`：

<figure markdown="span">
  ![](https://raw.githubusercontent.com/luguosong/images/master/blog-img/202504241359078.png){ loading=lazy }
  <figcaption>注册测试用户</figcaption>
</figure>

## 证书绑定

<figure markdown="span">
  ![](https://raw.githubusercontent.com/luguosong/images/master/blog-img/202504231651717.png){ loading=lazy }
  <figcaption>使用网证通提供的工具，读取签名证书数据</figcaption>
</figure>

<figure markdown="span">
  ![](https://raw.githubusercontent.com/luguosong/images/master/blog-img/202504231653726.png){ loading=lazy }
  <figcaption>将签名证书绑定到Gmcore用户名下</figcaption>
</figure>

## 印章制作绑定

使用Gmcore创建印章，并绑定到指定用户名下。

<figure markdown="span">
  ![](https://raw.githubusercontent.com/luguosong/images/master/blog-img/202504231714605.png){ loading=lazy }
  <figcaption>印章制作</figcaption>
</figure>
