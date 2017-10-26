

## 概述 ##

Azure SQL 数据仓库是基于 SQL 的完全托管 PB 级云数据仓库，它可以随业务需求的增长和变化而自由缩放，用户可在几分钟内完成设置并在数秒内缩放容量。可单独缩放计算和存储，针对复杂的分析工作负荷进行突发计算，或者为归档场景减少仓库，并根据使用的存储付费并且只需在需要时为所需计算付费，而不拘泥于预定义的群集配置 - 与传统数据仓库解决方案相比，可获得更高的成本效率。
本实验将基于某零售场景的样例数据，使用Azure SQL 数据仓库技术进行深入洞察处理，同时可以使用Power BI对分析结果进行可视化展现。

## 编写者 ##

- Nevin Dong - Principle Technical Evangelist, Microsoft


## 必备条件 ##

为保证成功完成本实验，以下组件需要提前安装：
- [Microsoft Visual Studio Community 2017](https://www.visualstudio.com/vs/community/)或更高版本。
- [Microsoft Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer/)或其他可管理Azure Storage的工具（要求可支持由世纪互联运营的Microsoft Azure中国区服务网站） 。
- [Microsoft Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azurermps-4.3.1) (1.0 或更高版本) ，或者可以使用 [Azure CLI](https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-install/) 跨平台工具。
- [Microsoft Power BI 桌面版](https://www.microsoft.com/en-us/download/details.aspx?id=45331)，可根据需要选择32位或64位版本。
 
需要更多了解Microsoft Azure，获得 Azure订阅，请访问：
- 由世纪互联运营的[Microsoft Azure中国区官网](https://www.azure.cn/pricing/1rmb-trial-full/)
- 由世纪互联运营的[Microsoft Azure中国区管理门户](https://portal.azure.cn/)
- 获得[Azure测试账号](https://www.azure.cn/pricing/1rmb-trial-full/)
- 参加[Azure学校](https://school.azure.cn/)
- [Azure开发人员指南](https://azure.microsoft.com/zh-cn/campaigns/developer-guide/)
- [Azure常用操作指南](https://docs.azure.cn/zh-cn/articles/)
- [Azure常见问题](https://www.azure.cn/support/faq/)


## 安装部署 ##

为完成本实验，需要创建Azure存储账户作为数据源，将原始交易数据上载到或传入存储账户，数据将被传入Azure SQL 数据仓库进行后续深入分析。

### 创建数据源 ###

1、创建Azure存储账户。可在[Microsoft Azure中国区管理门户](https://portal.azure.cn/)上选择 “存储” ，进入 “创建存储账户” 页面。其中， “部署模型 “选择 “Resource Manager” ，性能可选择 “标准” （如果是作业系统，可以选择性能更优的 “高级” ）。另外， “复制” 可选择 “本地冗余存储（LRS）” ，如需要更高的可用性，可选择 “异地冗余存储（GRS）” 或者 “读取访问权限异地冗余存储（RA-GRS）” 。

![Figure 1](https://github.com/NevinDong/FraudAnalysis-Demo-AzureChina/tree/master/images/nevdatasource02.png)

2、获取原始交易数据样例，可访问：wasbs://nyctaxidata@sqldwdatasets.blob.core.windows.net。

3、获取存储账户名称，及访问密钥。
![Figure 2](https://github.com/NevinDong/FraudAnalysis-Demo-AzureChina/tree/master/images/nevdatasource06.png)

4、通过Microsoft Azure Storage Explorer，通过上步所取得的存储账户名称，及访问密钥访问作为数据源的存储账户。注意：需设置选择 “Azure China” ，以便连接到由世纪互联运营的 Microsoft Azure中国区的账户资源。
![Figure 3](https://github.com/NevinDong/FraudAnalysis-Demo-AzureChina/tree/master/images/nevdatasource13.png)

5、创建Blob容器，以便将数据上载到存储账户中。
![Figure 4](https://github.com/NevinDong/FraudAnalysis-Demo-AzureChina/tree/master/images/nevdatasource14.png)

6、将数据传入Blob容器，交易数据存入相关文件夹。
![Figure 5](https://github.com/NevinDong/FraudAnalysis-Demo-AzureChina/tree/master/images/nevdatasource17.png)



### 创建Azure SQL 数据仓库 ###

1、使用Microsoft Azure PowerShell，直接执行实验所提供的 “OneClickDeploy.ps1” 文件，一键创建Azure SQL 数据仓库。其中，操作模式选择 “deploy” ，然后选择账户下所列的可用Azure订阅，以便创建Azure SQL 数据仓库。
![Figure 6](https://github.com/NevinDong/FraudAnalysis-Demo-AzureChina/tree/master/images/nevdatasource41.png)


2、可在Azure管理门户上看到刚才成功完成创建的Azure SQL 数据仓库，其中包括了一个SQL Server和一个SQL数据仓库。
![Figure 7](https://github.com/NevinDong/FraudAnalysis-Demo-AzureChina/tree/master/images/nevdatasource27.png)

3、可将本地客户端IP地址加入Azure SQL 数据仓库的防火墙安全设置，以便获得所有数据库的访问权限。
![Figure 8](https://github.com/NevinDong/FraudAnalysis-Demo-AzureChina/tree/master/images/nevdatasource23.png)

4、打开Microsoft Visual Studio IDE，在SQL Server Object Explorer中连接Azure SQL 数据仓库。其中：Server Name填入新建的Azure SQL 数据仓库的URL地址，User Name填入 “mylogin “，Password填入” pass@word1 “。
![Figure 9](https://github.com/NevinDong/FraudAnalysis-Demo-AzureChina/tree/master/images/nevdatasource50.png)

5、执行scripts目录中的FraudAnalysisData.dsql文件，将存储账户Blob容器中的数据加载到Azure SQL 数据仓库中。
![Figure 10](https://github.com/NevinDong/FraudAnalysis-Demo-AzureChina/tree/master/images/nevdatasource52.png)

### 使用Power BI 进行分析 ###

1、打开Microsoft Power BI 桌面IDE，在 “Get Data “对话框中选择” Azure SQL Data Warehouse “。
![Figure 11](https://github.com/NevinDong/FraudAnalysis-Demo-AzureChina/tree/master/images/nevdatasource60.png)

2、设置” SQL Server Database “中相关链接信息。其中：Server 项中填入新建的Azure SQL 数据仓库的URL地址。
![Figure 12](https://github.com/NevinDong/FraudAnalysis-Demo-AzureChina/tree/master/images/nevdatasource61.png)

3、将Azure SQL 数据仓库中相关表及视图加载到Power BI工具中。
![Figure 13](https://github.com/NevinDong/FraudAnalysis-Demo-AzureChina/tree/master/images/nevdatasource62.png)

4、将相关表及视图中相关字段拖入Power BI面板中，选择相应的展示图，进行相关的分析，例如，按不同地理区域、分析还款总额、交易总额等情况，以便分析是否存在欺诈行为。
![Figure 14](https://github.com/NevinDong/FraudAnalysis-Demo-AzureChina/tree/master/images/nevdatasource68.png)

5、可将设计完成的报告发布到Power BI网站，以便提供分享。
![Figure 15](https://github.com/NevinDong/FraudAnalysis-Demo-AzureChina/tree/master/images/nevdatasource6a.png)

6、访问Power BI网站，在线查看分析报告。
![Figure 16](https://github.com/NevinDong/FraudAnalysis-Demo-AzureChina/tree/master/images/nevdatasource6b.png)



