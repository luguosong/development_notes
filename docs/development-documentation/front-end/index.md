# 前端开发文档

## 查看器初始化过程

## 字体问题

### 现象和结论

- 当只上传文件，而不上传任何附件时。中文字体无法正常显示，英文字体可以显示（采用系统默认字体）。
- 字体文件名如果包含中文字符，作为附件上传，文件初始化job会报错。
- 真字体字体文件名与实际字体名不一致（必须保留ttf文件后缀），字体可以正常显示。型字体或大字体shx字体文件名与实际字体名不一致，字体无法正常显示。

### OpenCloud默认字体解析过程

- 调用Viewer对象的open方法。
	- `Model.getReferences`获取引用文件列表，并遍历
        - `Client.downloadReferenceFile`下载引用文件
        - 调用`visualizeJs.getViewer().addEmbeddedFile`添加引用文件。addEmbeddedFile参数一为文件名，参数二为文件流。
		  	- 调用`CloudEmbededFileManager.addFile`添加文件
		  		- 判断如果引用文件是真字体，？？？
			- 根据文件名判断文件是否为字体，如果长度小于4，返回不是字体。如果以.ttf、.ttc、.shx、.otf结尾，则认为是字体。
				- 如果是字体，遍历`字体样式列表`。并调用`setFileName`和`setBigFontFileName`方法刷新样式。
	
相关代码片段：

<figure markdown="span">
  ![](https://raw.githubusercontent.com/luguosong/images/master/blog-img/202504081751177.png){ loading=lazy }
  <figcaption>demo-viewer24.10:调用open方法打开文件</figcaption>
</figure>

<figure markdown="span">
  ![](https://raw.githubusercontent.com/luguosong/images/master/blog-img/202504090924238.png){ loading=lazy }
  <figcaption>client24.10:打开文件相关代码，获取引用文件列表，下载引用文件并使用addEmbeddedFile方法添加引用文件</figcaption>
</figure>

<figure markdown="span">
  ![](https://raw.githubusercontent.com/luguosong/images/master/blog-img/202504091317337.png){ loading=lazy }
  <figcaption>VisualizeJS:addEmbeddedFile方法内部</figcaption>
</figure>

<figure markdown="span">
  ![](https://raw.githubusercontent.com/luguosong/images/master/blog-img/202504091327131.png){ loading=lazy }
  <figcaption>添加引用文件内部，会判断如果是真字体，使用真字体的英文字体名</figcaption>
</figure>

### uocs字体解析过程

- 遍历`字体样式列表`
    - 从引用文件中查找，是否已经包含指定字体
    - 从全局字体库中查找，如果存在应用字体。
    - 使用全局字体库中的默认字体。
    - 如果连默认字体都加载失败，则标注该字体加载失败。


