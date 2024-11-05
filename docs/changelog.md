# 更新日志

## 24.10

### Server

- 为文件 API 添加新字段：

updatedAt: string - 文件最后更新时间（UTC）

updateBy: ShortUserDesc - 最后更新的用户

新增请求 GET files//versions/ - 通过版本号获取版本信息

改进：在 /files 请求中，新增了按文件大小排序的功能，使用的是当前版本文件的大小。

### ClientJS

- 新文件版本 API

以下方法可用于处理文件版本：

- File.uploadVersion() - 将文件的新版本上传到服务器。
- File.getVersions() - 返回版本文件列表。
- File.getVersion() - 返回版本文件。
- File.deleteVersion() - 删除版本文件。
- File.setActiveVersion() - 用选定版本替换文件的当前版本。

以下属性用于处理文件版本：

- File.versions：返回文件版本列表。
- File.activeVersion：返回文件的当前版本号。
- File.version：返回从零开始的文件版本号。原始文件的版本号为0。
- File.originalFileId：返回版本文件的原始文件ID。
- File.sizeTotal：返回存储中所有文件版本的总大小（以字节为单位）。

现有方法将基于活动文件版本操作：

- Client.getFiles() - 返回活动文件版本的列表。
- Viewer.open() - 打开文件的活动版本。
- File.getModels() - 返回文件活动版本的模型列表。
- File.getProperties() - 返回文件活动版本中对象的属性。
- File.searchProperties() - 搜索文件活动版本中的对象。
- File.getCdaTree() - 返回文件活动版本的cda.json。
- File.download() - 下载文件活动版本的源文件。
- File.downloadResource() - 下载文件活动版本的资源数据。
- File.createJob() - 为文件活动版本创建新任务。
- File.waitForDone() - 等待文件活动版本的任务完成。

```javascript title="示例"
  await file
    .uploadVersion(event.target.files[0], {waitForDone: true})
    .catch((e) => alert(`Cannot upload file version. ${e.message}`));
```

- 改进：在用户界面中新增用户字段`fullName`和`initials`。

firstName：用户的名字和姓氏。适合在表格中显示用户名。initials：用户名字和姓氏的首字母。适合用于用户头像。

- 改进：使用 Ctrl+滚轮缩放模型时，不再缩放浏览器窗口。 •
- 改进：您现在可以跟踪等待任务的进度。

为 waitForDone() 方法新增了一个 onCheckout 参数：

- File.waitForDone()
- AssemblyFile.waitForDone()
- Job.waitForDone()

```javascript
await file.validate();
await file.waitForDone("validation", true, {
    onCheckout: (file, ready) => {
        console.log(ready ? "Validation completed" : "Waiting for validation to complete...");
    },
});
```

### WebViewer示例网站

- 改进：在文件上传过程中禁用几何数据中对象树的保存，以提升性能。这将减少几何数据的大小，并缩短打开时的解析时间。

### Bug修复

- CLOUD-4661 - 修复了在特定文件渲染时几何图形被限制在边界内的错误
- CLOUD-4808 - 修复了下载文件源请求 GET /downloads?version=0 返回活动版本源而不是请求的0版本源的错误
- CLOUD-4807 - 修复了使用 ?version=XXX 查询参数指定版本时文件属性搜索无效的错误

## 24.11

### Server

视点`线`对象的新选项：

- 颜色 - ARGB格式的字符串值。
- 类型 - 线的类型（“实线”、“点线”、“虚线”）
- 宽度 - 线的宽度（范围在1到10之间）

### ClientJS

为Viewer实现了额外的注册方法：

- registerUser() - 使用指定的电子邮件和密码注册新用户。
- resendConfirmationEmail() - 重新发送确认邮件给新用户。
- confirmUserEmail() - 确认用户的电子邮件。

### WebViewer示例网站

- 现在使用开放云服务器来注册新用户，不再需要独立的注册服务器。
- 配置文件 config.json 中已移除注册服务器的 URL。
- 用户注册或登录后（如有需要），会显示电子邮件确认通知。

### WebTools

使用FileConverter工具导出为GLB格式的新功能。

### Bug修复

- CLOUD-3740 — 修复了客户端显示已删除图层数据的错误。
- CLOUD-4852 — 修复了使用AWS S3存储时按参数搜索对象无效的问题。
- CLOUD-3741 — 修复了图层在客户端未隐藏的问题。
- CLOUD-4875 — 修复了独立查看器初始化时的错误。

## 24.12

### Server

- 对于所有服务器的GET方法，添加了一个强ETag，该ETag由请求路径和返回内容的哈希值组成。这样可以更好地利用缓存，并通过在内容未更改时避免服务器发送整个响应来节省带宽。
- 服务器端对象属性的缓存。属性将在缓存中存储30分钟。这使得获取对象属性的速度提高了四倍，同时减少了数据库的负载。
- 服务器端支持glTF几何图形，新增了一个名为`geometryGltf`的作业类型。
- 使用用户名和密码登录。

### ClientJS

- 在注册或创建新用户时，如果未定义`username`参数，则使用电子邮件中的用户名。
- 使用ClientJS创建glTF几何作业。现在使用Client.uploadFile()时，可以通过`geometry`参数指定几何作业类型。
- 新增了File.geometryType属性。
- 新增了Option.geometryType选项，表示上传文件的默认几何数据类型。它用于旧式调用Client.upload.file()时带有geometry=true参数的情况。

在使用以下文件方法运行作业时，为 JobRunner 指定参数：

- extractGeometry
- extractProperties
- validate

### VisualizeJS

对于 update() 函数，使用一个新的可选参数 maxRegenTime 来设置最大再生时间，并通过再生中止功能进行更新。

### Bug修复⭐

- CLOUD-4901 — 修复了 .rvt 文件中管道元素的渲染问题。
- CLOUD-4868 - 修复了导出为 3D PDF 时 FileConverter 崩溃并出现分段错误的问题。
- CLOUD-4874 - 修复了当 requestId >= 1 时，服务器在文件下载请求中丢失 "content-length" 头的问题。
- CLOUD-4876 - 修复了初始化独立查看器时出现的 Konva 错误问题。
- CLOUD-4912 - 修复了删除预览和删除头像的功能。

## 25.1

### Server

在服务器设置的 OdWebSettings 中新增了 "CosmosDbUrl" 字段。服务器要与 Azure CosmosDB 一起工作，必须使用该字段代替 "
DatabaseUrl" 字段。

只有在数据保存到数据库后，属性任务才会被标记为完成。

avePropertiesToMongoDB 服务器参数已弃用。现在，在完成属性检索任务后，数据总是会保存到数据库中。

一些文件的属性已弃用并从服务器 REST API 中移除：

- Metadata
- Properties
- GeometryStatus
- PropertiesStatus
- ValidationStatus

### ClientJS

API 方法 openVsfFile() 和 openVsfxFile() 现在会触发几何特定事件：

- geometrystart - 在文件加载前触发。
- databasechunk - 在场景描述加载时触发。
- geometryprogress - 在文件加载进度达到100%时触发。
- geometryend - 在文件成功加载后触发。
- geometryerror - 在文件打开失败时触发。

### FileConverter

为FileConverter添加了一个新参数，用于指定.rvt文件的细节级别。参数名称为"--rvt_detail_level"，它有三个可能的取值：coarse(
粗略)、medium(中等)、fine(精细)。

### VisualizeJS

为OdTvGeometryData类新增了方法：

- emscripten::val 获取捕捉点(OdTvGeometryData::SnapMode snapMode, const OdTvPoint& pickPoint, const OdTvMatrix&
  xWorldToEye, const OdTvMatrix& transform, double focalLength, bool bWire) — 返回此几何对象的适当捕捉点。
- bool getSupportSnapMode(OdTvGeometryData::SnapMode snapMode, bool bWire) const — 检查此几何对象是否支持请求的捕捉模式；

为OdTvMaterial类新增了方法：

- void setLuminanceMode(OdTvMaterial::LuminanceMode mode) — 设置材质对象的亮度模式；
- OdTvMaterial::LuminanceMode getLuminanceMode() const — 获取材质对象当前的亮度模式；
- std::wstring getNativeDatabaseHandle() — 获取本地数据库句柄；

OdTvGsDevice::Options 枚举成员：

- kLightSourcesLimit;
- kForceOffscreenSceneGraph;
- kForceOffscreenSceneGraph;
- kLightSourcesLimit;
- kDegradationLevel;

为OdTvModel类新增了方法：

- emscripten::val hasVisualStylesAtEntities() — 检查模型中的实体是否设置了视觉样式；
- void setIgnore3DClipping(bool bIgnore) — 指定此模型的几何体是否应忽略视图的前后剪裁平面（如果有）和视口3D剪裁（如果设置了）；
- bool getIgnore3DClipping() const — 检查此模型对象是否启用了视图剪裁覆盖；

为OdTvEntity类新增了方法：

- void removeSubEntity(const OdTvGeometryDataIdJSWrapper& geomId, bool bRemoveOnlyWithSingleChild) — 移除子实体并用其子几何体替换；
- void setExcludeFromViewExtents(bool bExclude) — 指定是否将实体几何体排除在视图范围之外；
- bool getExcludeFromViewExtents() const — 检查实体几何体是否被排除在视图范围之外；
- void setIgnoreVisualStyle(bool bIgnore, bool bMarkForRegen) — 设置标志以忽略实体的视觉样式；
- bool getIgnoreVisualStyle() const — 获取当前标志值，指示是否忽略实体的视觉样式；
- void setCalculateIsolinesForShells(bool bSet, double creaseAngle, bool bImmediateCalculation) — 指定是否计算和可视化壳体的等高线；
- emscripten::val getCalculateIsolinesForShells() const — 检查是否计算和可视化壳体的等高线；

为 CloudViewerJSWrapper 类新增了方法：

- OdTvEntityIdJSWrapper findEntity(const std::wstring& originalDatabaseHandle) — 返回与原始数据库句柄匹配的
  OdTvEntityId；
- void setPreferableVisualStyle(OdTvDatabase::PreferableVisualStylesType type, const OdTvVisualStyleIdJSWrapper&
  visStyleId) — 设置一个视觉样式对象为相应类型的首选；
- OdTvVisualStyleIdJSWrapper getPreferableVisualStyle(OdTvDatabase::PreferableVisualStylesType type) const —
  获取相应首选视觉样式类型的视觉样式对象；
- emscripten::val isPreferableVisualStyle(const OdTvVisualStyleIdJSWrapper& visStyleId) const — 检查视觉样式是否为首选；
- emscripten::val findEntityOrSubEntityByHandle(const std::wstring& handle) — 使用实体句柄搜索实体或子实体；
- OdTvEntityIdJSWrapper findEntityByHandle(const std::wstring& handle) — 使用实体句柄搜索实体；

为 CloudViewJSWrapper 类新增了方法：

- void setClipRegion2d(OdUInt32 numContours, emscripten::val numVertices, emscripten::val vertices2d) — 定义视图对象的多边形裁剪区域；
- void setClipRegion(OdUInt32 numContours, emscripten::val numVertices, emscripten::val vertices) — 定义视图对象的多边形裁剪区域；
- emscripten::val getClipRegion2d() const — 获取当前视图的多边形裁剪区域；
- emscripten::val getClipRegion() const — 获取当前视图的多边形裁剪区域；
- bool hasWCSClipRegion() const — 检查视图是否具有世界坐标系中的多边形裁剪区域；
- void removeClipRegion() — 移除视图当前的多边形裁剪区域；
- void inheritClipRegionFrom(const CloudViewJSWrapper& viewId) — 从指定视图继承裁剪区域；
- CloudViewJSWrapper clipRegionOwner() const — 获取裁剪区域的所有者；
- emscripten::val clipRegionInheritantes() const — 获取裁剪区域的继承者；
- std::wstring getDatabaseHandle() — 获取与对象关联的当前数据库句柄；
- std::wstring getNativeDatabaseHandle() — 获取本地数据库句柄；
- OdTvDatabase::PreferableVisualStylesType enum;

OdTvEntity::FillingCheckResult 枚举；

OdTvMaterial::LuminanceMode 枚举；

OdTvMaterial::ReflectivityType 枚举；

为 OdTvCDATreeNodePtrJSWrapper 类新增了方法：

- OdTvGeometryDataIdJSWrapper getSubEntityId() const — 返回此节点的tv子实体ID。

OdTvVisualStyleOptions::FaceLightingModel 枚举；

OdTvVisualStyleOptions::FaceLightingQuality 枚举；

OdTvVisualStyleOptions::FaceColorMode 枚举；

OdTvVisualStyleOptions::FaceModifiers 枚举；

OdTvVisualStyleOptions::EdgeModel 枚举；

OdTvVisualStyleOptions::EdgeModel 枚举；

OdTvVisualStyleOptions::EdgeStyles 枚举；

OdTvVisualStyleOptions::EdgeModifiers 枚举；

OdTvVisualStyleOptions::EdgeJitterAmount 枚举；

OdTvVisualStyleOptions::EdgeLinePattern 枚举；

OdTvVisualStyleOptions::DisplayStyles 枚举；

为 OdTvAnimationAction 类添加了新方法：

- std::wstring getDatabaseHandle() — 获取与对象关联的当前数据库句柄；
- std::wstring getNativeDatabaseHandle() — 获取本地数据库句柄；

为OdTvAnimationContainer类新增了方法：

-std::wstring getDatabaseHandle() — 获取与对象关联的当前数据库句柄；
-std::wstring getNativeDatabaseHandle() — 获取本地数据库句柄；

为OdTvBlock类新增了方法：

- std::wstring getDatabaseHandle() — 获取与对象关联的当前数据库句柄；
- std::wstring getNativeDatabaseHandle() — 获取本地数据库句柄；

为OdTvCamera类新增了方法：

- std::wstring getDatabaseHandle() — 获取与对象关联的当前数据库句柄；
- std::wstring getNativeDatabaseHandle() — 获取本地数据库句柄；

为OdTvGsDevice类新增了方法：

- std::wstring getDatabaseHandle() — 获取与对象关联的当前数据库句柄；
- std::wstring getNativeDatabaseHandle() — 获取本地数据库句柄；

为OdTvGsViewEnvironmentBackground类添加了新方法：

- std::wstring getDatabaseHandle() — 获取与对象关联的当前数据库句柄；
- std::wstring getNativeDatabaseHandle() — 获取本地数据库句柄；

为OdTvGsViewGradientBackground类添加了新方法：

- std::wstring getDatabaseHandle() — 获取与对象关联的当前数据库句柄；
- std::wstring getNativeDatabaseHandle() — 获取本地数据库句柄；

为OdTvGsViewImageBackground类添加了新方法：

- std::wstring getDatabaseHandle() — 获取与对象关联的当前数据库句柄；
- std::wstring getNativeDatabaseHandle() — 获取本地数据库句柄；

为OdTvGsViewSolidBackground类添加了新方法：

- std::wstring getDatabaseHandle() — 获取与对象关联的当前数据库句柄；
- std::wstring getNativeDatabaseHandle() — 获取本地数据库句柄；

为OdTvHighlightStyle类添加了新方法：

- std::wstring getDatabaseHandle() — 获取与对象关联的当前数据库句柄；
- std::wstring getNativeDatabaseHandle() — 获取本地数据库句柄；

为OdTvLayer类添加了新方法：

- std::wstring getDatabaseHandle() — 获取与对象关联的当前数据库句柄；
- std::wstring getNativeDatabaseHandle() — 获取本地数据库句柄；

为OdTvLinetype类添加了新方法：

- std::wstring getDatabaseHandle() — 获取与对象关联的当前数据库句柄；
- std::wstring getNativeDatabaseHandle() — 获取本地数据库句柄；

为OdTvRasterImage类添加了新方法：

- std::wstring getDatabaseHandle() — 获取与对象关联的当前数据库句柄；
- std::wstring getNativeDatabaseHandle() — 获取本地数据库句柄；

为OdTvTextStyle类添加了新方法：

- std::wstring getNativeDatabaseHandle() — 获取本地数据库句柄；

为OdTvVisualStyle类添加了新方法：

- std::wstring getNativeDatabaseHandle() — 获取本地数据库句柄；

OdTvGeometryData::TargetDisplayMode枚举成员：

- kEveryWhereExceptIsolines;

### Bug修复

- CLOUD-4930 — 修复了启动包含 .rvt 文件的 Worker 时 wss 协议的问题。
- CLOUD-4938 — 修复了检索特定用户文件几何数据的错误。
- CLOUD-4933 — 修复了打开用户文件中特定图纸几何数据的错误。

## 25.2

### ClientJS

为缩放拖动器添加了交互模式，使缩放操作更加流畅。

新增了用于新标记对象的 API，添加了处理以下基本图形的方法：

- Cloud
- Arrow
- Rectangle
- Circle
- Image

### FileConverter

在 FileConverter 中新增了 `use_properties_group` 标志，该标志允许生成分组属性。此标志仅在导出对象属性时可用。

### Bug修复

- CLOUD-4996 - 修复了更改活动模型后VisualizeJS视口大小不正确的问题
- CLOUD-4998 - 修复了导出到gltf时设置最小访问器值的问题
- CLOUD-5027 - 修复了nwd文件缺少本机句柄的错误

## 25.3

### Server

实现了对 `Google Cloud Storage` 的支持，用于存储文件和几何数据。

新增了对连接身份提供者的LDAP协议支持。通过`LDAP认证`，用户现在可以通过LDAP服务器进行系统访问认证。连接参数可以在服务器设置中找到。

在服务器设置中新增了`ContainerOrBucketName`参数，用于在使用第三方存储（如 Azure BLOB、AWS S3、Google Cloud
Storage）存储文件时指定存储名称。

### ClientJS⭐

Visualize驱动的查看器已从Client.js中分离出来，成为一个独立的JavaScript库。您需要在HTML页面中包含它，以确保查看器正常工作。

```html title="示例"

<script src="[https://unpkg.com/@inweb/client@25.3/dist/client.js](https://unpkg.com/@inweb/client@25.3/dist/client.js)"></script>
<script src="[https://unpkg.com/@inweb/viewer-visualize@25.3/dist/viewer-visualize.js](https://unpkg.com/@inweb/viewer-visualize@25.3/dist/viewer-visualize.js)"></script>
```

全局命名空间也已更改。Client.js 的全局命名空间是 ODA.Api，Viewer 的全局命名空间是 ODA.Visualize。

```html

<script>
    const client = new ODA.Api.Client({serverUrl: "[https://cloud.opendesign.com/api](https://cloud.opendesign.com/api)"});

    const viewer = new ODA.Visualize.Viewer(client);
</script>
```

open-cloud-client npm 包已被弃用且不再维护。您应该改用两个新包 `@inweb/client` 和 `@inweb/viewer-visualize`。要通过 npm
安装它们，请使用以下终端命令：

```shell
npm install @inweb/client @inweb/viewer-visualize
```

```javascript
import {Client} from "@inweb/client";
import {Viewer} from "@inweb/viewer-visualize";

const client = new Client({serverUrl: "[https://cloud.opendesign.com/api](https://cloud.opendesign.com/api)"});
const viewer = new Viewer(client);
```

添加 TypeScript 接口和类以支持视点

### WebViewer示例网站

在网页查看器中新增了对标记工具的支持。

### Bug修复

- CLOUD-5023 - 修复了从 PDF 导入的文本在所有浏览器中不显示的错误。
- CLOUD-5057 - 修复了 data.json 中缺失的项目基点参数。

## 25.4

### Server

增加了对遵循OAuth2规范的第三方身份提供商的支持。

增加了对遵循SAML规范的第三方身份提供商的支持。

在POST注册/电子邮件确认请求中使用用户名而不是电子邮件。

### ClientJS

引入了Client.getIdentityProviders()方法，用于获取服务器身份提供者列表。

在`User类`中新增了`isAdmin属性`，用于指示用户是否具有管理员权限。

新增了Viewer事件：`initialize`和`initializeprogress`。

- `initialize`：在 Viewer 初始化后触发。
- `initializeprogress`：在 VisualizeJS 库加载进度变化时触发。

为组件添加了对 viewpoints API 的支持。

应用类的新方法：

- getViewpoints(): 返回装配视点列表。
- saveViewpoint(): 添加新的装配视点。
- deleteViewpoint(): 删除装配视点。
- getSnapshot(): 返回视点预览图像的Data URL。
- getSnapshotData(): 返回视点预览数据。

### WebViewer示例网站

通过允许使用服务器上配置的身份提供商进行身份验证，改进了登录功能。

增加了为装配体创建标记和保存视点的功能。

增加了在演示查看器中上传和查看内部 glTF 格式文件的功能。

- 实验功能：可以在config.json文件中指定上传文件的内部格式。

新增了在查看器中打开整个几何文件的功能。

网页查看器：新增启用流媒体模式开关。

Client：Viewer's 选项现在包含enableStreamingMode字段。

- 当 enableStreamingMode 为 false 时，enablePartialMode 字段将被忽略，文件/程序集将一次性加载。否则，将使用
  enablePartialMode 字段。

### VisualizeJS

为OdTvGeometryData类新增了方法：

- OdTvResult setEmission(const OdTvMaterialColorJSWrapper& emissionColor, const OdTvMaterialMapJSWrapper&
  emissionMap); - 设置此材质对象的发光颜色。
- OdTvMaterialMapJSWrapper getEmissionMap() const; — 获取此材质对象的发光贴图。

### Bug修复

- CLOUD-5142 - 修复了AWS S3存储无法工作并抛出SignatureDoesNotMatch异常的错误。
- CLOUD-5031 - 修复了上传DWG文件到服务器时抛出文件错误的问题。
- CLOUD-5085 - 修复了在上传文件时将waitForDone设置为false时几何状态不正确的问题。
- CLOUD-5086 - 修复了由于错误导致最后一个版本没有几何数据时，新版本几何任务仍在运行的问题。
- CLOUD-5087 - 修复了POST /files/{:id}/versions响应中创建/更新日期的毫秒数无效的问题。
- CLOUD-5121 - 修复了在服务器端保存视点中的图像高度、宽度和旋转信息的问题。

## 25.5

### Server

管理员仪表盘。内置于服务器的管理网页应用。

增加了通过REST API和管理面板管理服务器设置的功能。

该参数适用于.dwg文件，并允许获取.dwg文件的模型空间的geometry 。

- Flag:仅支持 .dwg 格式

### ClientJS

新增功能：为用户类添加了新属性：

- identityProvider ： 用于创建账户的身份提供商名称。可以是ldap、oauth、saml或为空。

改进：新增支持服务器设置的API：

- Client.getServerSettings()：返回当前服务器设置。
- Client.updateServerSettings()：更改服务器设置。

通过 API 增加创建多行文本的功能

### FileConverter

新增参数`--rvt_enable_levellines_and_basepoints`用于导入RVT文件。

### Bug修复

- CLOUD-5146 - 修复了在 Windows 平台上装配几何作业失败的错误
- CLOUD-5151 - 修复了绘图样式应用无法正常工作的错误

## 25.6

### Server

管理员仪表盘。

- 默认情况下，管理员面板可通过以下链接访问：“http://127.0.0.1:8080/adminboard”。
- 只有管理员组的用户才能访问此页面。
- 请确保在服务器设置中指定了正确的服务器API“ExternalUrl”。这是管理员面板正常运行所必需的。
- 允许配置服务器。仅可更改不需要重启服务器的设置。
- 可以管理用户设置。

新增了管理员更改任意用户密码的功能

新增参数 PasswordSecurityIterations。该参数用于根据 PBKDF2 标准配置哈希算法，以减少暴力破解攻击的风险。推荐值为 500000。

删除管理员用户提升10%

### ClientJS

添加自动模式，在显示低精度几何图形时调用 viewer.regenAbort() 方法。

标记椭圆：增加编辑时使半径相同的功能。按下“Ctrl”或“Shift”键时，编辑标记椭圆的两个半径相同。

新增删除预览图像的方法：

- File.deletePreview() - 移除文件预览图像
- Project.deletePreview() - 移除项目预览图像
- User.deleteAvatar() - 移除用户头像图片

### Bug修复

- CLOUD-5148 - 导出为 glTF 格式时，材质数据存在错误
- CLOUD-5159 - 从 GET/users 和 GET/user 请求结果中移除密码和盐字段
- CLOUD-5160 - 修复 Google Cloud Storage 对 etag 功能的支持

## 25.7

### Server

新功能：删除原始文件，同时保留元数据和衍生数据

- 原文件删除：服务器现在支持删除原始（源）文件，同时保留相关的元数据和派生数据。
- /files 端点新增字段：在 /files 端点中新增了一个字段 isFileDeleted，用于指示原文件是否已被删除。
- 新端点：引入了一个新的 DELETE 端点 /files//source，用于删除原文件。
- 第三方存储支持：该功能支持删除所有第三方存储中的文件，包括 Azure BLOB、AWS S3 和 Google Storage。

VSFX 文件导入：系统现在支持导入包含几何数据的 VSFX 文件。

- 文件转换：在导入VSFX文件时，FileConverter将以处理原始文件的方式进行处理。这包括生成元数据文件和几何文件。
- CDA对象树：如果VSFX文件包含CDA对象树，它也将被保存为cda.json。

JobRunner最大上传文件大小已增加。JobRunner的最大上传文件大小已提升至4GB。此更新增强了JobRunner处理大文件的能力，提高了其在需要处理大量数据文件的项目中的实用性。

自定义端口号和电子邮件/LDAP服务器配置。管理员现在可以在服务器管理网页上手动输入任何电子邮件和LDAP服务器的端口号，从而提升灵活性和可配置性。

### ClientJS

文件类的新字段和功能：

- 在 File 类中新增了一个字段 isFileDeleted。
- 在 File 类中新增了一个函数 deleteSource()。

为 Markup 类新增方法 enableEditMode(mode: MarkupMode)：

- 此方法手动启用或禁用标记编辑模式。
- 当用户在查看器中激活标记拖动工具时会自动调用。

### VisualizeJS

为OdTvEntityId类新增了方法：

- OdTvModelJSWrapper getOwnerModel() const — 返回该实体所在的模型；
- OdTvBlockIdJSWrapper getOwnerBlockId() const — 返回该实体所在的块的ID；
- OdTvBlockPtrJSWrapper getOwnerBlock() const — 返回该实体所在的块。

新增 DeviceInteractivityOptionsJSWrapper 类。

为OdTvGsDevice类新增了方法：

- void switchBitmapState() — 允许将“非设置”设备切换到位图模式或从位图模式切换；
- void setInteractivityOptions(const DeviceInteractivityOptionsJSWrapper& options) — 设置交互模式的选项；
- DeviceInteractivityOptionsJSWrapper getInteractivityOptions() const — 获取交互模式的选项。

### Bug修复

- CLOUD-5213 - 修复漏洞：处理服务器设置中 ExternalUrl 配置参数为空的情况。
