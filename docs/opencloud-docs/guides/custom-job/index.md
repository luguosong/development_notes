# 定制Job

使用自定义 jobs，您可以从源文件中提取额外数据，将用户文件和派生数据发送到第三方服务，发送通知等。

在本指南中，我们将展示如何添加一种新的任务类型，用于提取 DWG 文件中所有实体的信息，包括 `XDATA`。

首先，我们需要创建一个控制台应用程序，用于读取 DWG 文件并遍历所有实体。为此，可以使用用 C# 和 .NET 编写的 "OdReadExSwigMgd"
示例代码。

## 控制台DWG数据提取器

- 下载并安装所有适用于 Drawings.NET SDK 的二进制文件。

!!! warning

	平台应与 WebTools 保持一致。

- 在 Visual Studio 中打开 `Platforms\<platform>\Drawings.NET_<platform>_ex.sln` 解决方案。

解决方案包含所有适用于 Drawings.NET SDK 的示例。需要在项目树中的 OdReadExSwigMgd 项目中找到并打开 Program.cs 源文件。

- 请从NuGet包管理器中安装`Newtonsoft.JSON`包，用于解析JSON配置文件。

- 现在，让我们修改源代码以读取作业的参数，打开并处理DWG文件，然后将输出重定向到生成的文件中。

``` csharp
...

using System;
using System.Collections.Generic;
using System.Text;
using Teigha.Core;
using Teigha.TD;
using System.IO;
using Microsoft.Win32;
using Newtonsoft.Json; // Add JSON parser to parse job config

namespace OdReadExSwigMgd
{
    // --------------------------------------------------------------------
    // Job配置类  
    // 用于作为解析后 JSON 数据的容器  
    // File - 目标文件的路径，用于处理  
    // Output - 保存结果的目标文件夹路径
    // --------------------------------------------------------------------
    public class JobConfig
    {
        [JsonProperty("File")]
        public string File { get; set; }
        [JsonProperty("Assets")]
        public string[] Assets { get; set; }
        [JsonProperty("Output")]
        public string Output { get; set; }
        [JsonProperty("Parameters")]
        public string Parameters { get; set; }
    }

  class Program
  {
    static Program()
    {
      OdCreationNetSwigExampleLib.Helpers.OdNetSmokeTest.Use();
    }

    static void Main(string[] args)
    {
      MemoryManager mMan = MemoryManager.GetMemoryManager();
      MemoryTransaction mStartTrans = mMan.StartTransaction();
      try
      {

        /***********************************/
        /* 验证参数数量，并根据需要显示错误信息。 */
        /***********************************/
        OdExHostAppServices HostApp = new OdExHostAppServices();
        Console.WriteLine("\nOdReadExSwigMgd developed using {0} ver {1}", HostApp.product(), HostApp.versionString());
        OdDbLibraryInfo libInfo = TD_Db.oddbGetLibraryInfo();
        Console.WriteLine("\nOdReadExSwigMgd developed using minor major ver {0} ", HostApp.releaseMajorMinorString());
        Console.WriteLine("\nOdDbLibraryInfo  libVer {0} buildComments {1}", libInfo.getLibVersion(), libInfo.getBuildComments());

        // --------------------------------------------------------------------
        // JobRunner 在启动进程时总是将配置文件作为第一个参数传递。
        // --------------------------------------------------------------------
        string jsonString = File.ReadAllText(args[0]);
        JobConfig config = Newtonsoft.Json.JsonConvert.DeserializeObject<JobConfig>(jsonString);

        // --------------------------------------------------------------------
        // 将标准输出重定向到“dwgdata”文件，并将其保存到 Output 文件夹中。
        // --------------------------------------------------------------------
        var writer = new StreamWriter(config.Output + "\\dwgdata");
        Console.SetOut(writer);


...


          OdExDbAuditInfo aiAppAudit = new OdExDbAuditInfo(System.IO.Path.GetDirectoryName(args[0]) + "AuditReport.txt");
          aiAppAudit.setFixErrors(true);
          aiAppAudit.setPrintDest(OdAuditInfo.PrintDest.kBoth);

          MemoryTransaction mTr = mMan.StartTransaction();

          // --------------------------------------------------------------------
          // 请使用job配置中的文件路径作为您要处理的DWG文件的路径。
          // --------------------------------------------------------------------
          OdDbDatabase pDb = HostApp.readFile(config.File, true, false, FileShareMode.kShareDenyNo, "");
          {
            if (pDb != null)
            {
...
```

- 构建应用程序，并将所有二进制文件放置在 JobRunner 文件夹中。

## 注册新Job模板

Job模板用于指定新Job的配置。

```
- `name` - 用作ID的Job名称。  
- `description` - Job的描述。  
- `type` - 指定如何启动新Job的“命令”。  
- `body` - 需要启动的命令或应用程序名称。
```

``` shell
curl -X POST -H "Authorization: df1dceb8e2d6f6b0894363b085801e36" -H "Content-Type: application/json" -d "{\"name\": \"dwgextract\", \"description\": \"dwg data extractor\", \"type\": \"command\", \"body\": \"OdReadExSwigMgd\"}" http://127.0.0.1:8080/jobs/templates
```

## 使用新模板运行Job

在 fileId 中需要指定需要处理的 DWG 文件的 ID。

``` shell
curl -X POST -H "Authorization: df1dceb8e2d6f6b0894363b085801e36" -H "Content-Type: application/json" -d "{\"fileId\": \"60b4e650ddf3d42984e06494\", \"outputFormat\": \"command\", \"template\": \"dwgextract\"}" http://127.0.0.1:8080/jobs
```

如果任务已完成且状态为成功，则可以通过以下请求获取结果：

```shell
curl -X GET -H "Authorization: df1dceb8e2d6f6b0894363b085801e36" http://127.0.0.1:8080/files/60b4e650ddf3d42984e06494/downloads/dwgdata
```
