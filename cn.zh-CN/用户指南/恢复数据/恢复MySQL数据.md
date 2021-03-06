# 恢复MySQL数据 {#concept_vrh_qp4_ydb .concept}

您可以通过以下方式恢复RDS for MySQL的数据：

-   方式一：直接恢复到原实例。具体请参见[直接恢复到原实例](cn.zh-CN/用户指南/恢复数据/直接恢复到主实例.md)。
-   方式二：恢复到一个新实例，经过验证后，再将数据迁移到原实例。本文介绍这种方式。此功能原名为克隆实例。

恢复的内容包括数据库信息、账号信息和实例设置，例如，白名单设置、备份设置、参数设置、阈值报警设置等。您可以选择按备份集恢复或者按时间点恢复。

## 计费方式 {#section_uh5_4bq_w2b .section}

与新购实例相同，详情请参见[详细价格信息](https://www.aliyun.com/price/product?spm=5176.doc26117.2.2.yFbzvO#/rds/detail)。

## 前提条件 {#section_o5q_1p4_ydb .section}

原实例需要满足如下条件：

-   运行中且没有被锁定。

-   当前没有迁移任务。

-   如果要按时间点进行恢复，需要确保日志备份已开启。

-   若要按备份集恢复，则原实例必须至少有一个备份集。


## 恢复数据到新实例 {#section_est_dp4_ydb .section}

1.  登录[RDS管理控制台](https://rds.console.aliyun.com/)。
2.  选择实例所在地域。
3.  单击实例的ID。
4.  在左侧导航栏中，选择备份恢复。
5.  在页面右上角，单击**数据库恢复（原克隆实例）**。
6.  在弹出的页面中，选择新实例的计费方式：

    -   **按量付费**：属于后付费，即按小时扣费。适合短期需求，用完可立即释放实例，节省费用。
    -   **包年包月**：属于预付费，即在新建实例时需要支付费用。适合长期需求，价格比按量付费更实惠，且购买时长越长，折扣越多。
    **说明：** 按量付费实例可以转为包年包月实例。包年包月实例无法转为按量付费实例。

7.  设置新实例的参数：

    |参数名称|说明|
    |----|--|
    |**还原方式**|     -   **按时间点**：可以设置为日志备份保留时间内的任意时间点。如要查看或修改日志备份保留时间，请参见[备份RDS数据](cn.zh-CN/用户指南/备份数据/备份 RDS 数据.md)。
    -   **按备份集**
 **说明：** 只有开启了日志备份，才会显示**按时间点**。

 |
    |**系列**|    -   基础版：单节点，计算与存储分离，性价比高，但不建议用于生产环境。
    -   高可用版：一个主节点和一个备节点，经典高可用架构。
    -   金融版：一个主节点和两个备节点，位于同一地域的三个不同的可用区，提供金融级可靠性。仅4个地域提供金融版实例：华东1、华东2、华南1、华北2。
关于各个系列的详细介绍，请参见[产品系列概述](../../../../cn.zh-CN/产品简介/产品系列/产品系列概述.md)。不同数据库版本支持的系列不同，请以实际界面为准。

|
    |**可用区**|可用区是地域中的一个独立物理区域，不同可用区之间没有实质性区别。

 您可以选择将RDS实例与ECS实例创建在同一可用区或不同的可用区。**说明：** 新实例的地域与原实例相同，不支持修改。

|
    |**规格**|建议您选择大于主实例的规格和存储空间，否则因性能限制，数据恢复所需时间可能较长。

每种规格都有对应的CPU核数、内存、最大连接数和最大IOPS。具体请参见[实例规格表](../../../../cn.zh-CN/产品简介/实例规格/实例规格表.md)。

RDS实例有以下规格族：

    -   通用型：独享被分配的内存和I/O资源，与同一服务器上的其他通用型实例共享CPU和存储资源。
    -   独享型：独享被分配的CPU、内存、存储和I/O资源。
    -   独占物理机型：是独享型的顶配，独占整台服务器的CPU、内存、存储和I/O资源。
 例如，**8核32GB**是通用型实例规格，**8核32GB（独享套餐）**是独享型实例规格，**30核220GB（独占主机）**是独占物理机型实例规格。|
    |**存储空间**|该存储空间包括数据空间、系统文件空间、Binlog文件空间和事务文件空间。|
    |**网络类型**|     -   **经典网络**：传统的网络类型。
    -   **专有网络**（推荐）：也称为VPC（Virtual Private Cloud）。VPC是一种隔离的网络环境，安全性和性能均高于传统的经典网络。
 |

8.  设置购买时长（仅针对包年包月实例）和实例数量。
9.  单击**立即购买**。
10. 确认订单信息，勾选**《关系型数据库RDS服务条款》**，并完成支付。

## 登录到新实例并验证数据 {#section_k2y_np4_ydb .section}

关于登录实例的操作，请参见[连接实例](../../../../cn.zh-CN/快速入门MySQL版/连接实例.md)。

## 迁移数据到原实例 {#section_jtd_v11_ydb .section}

确认新实例的数据之后，您可以将需要的数据从新实例迁移回原实例。

数据迁移是指将一个实例（称为源实例）的数据复制到另一个实例（称为目标实例），迁移操作不会对源实例造成影响。

**注意事项**

数据迁移过程中不能执行DDL操作，否则可能导致迁移失败。

**操作步骤**

1.  进入[数据传输（DTS）控制台](http://dts.console.aliyun.com/)。
2.  在左侧导航栏中，选择**数据迁移**。
3.  单击**创建迁移任务**。
4.  输入任务名称、源数据库信息以及目标数据库信息。

    参数说明：

    -   任务名称：默认情况下，DTS为每个任务自动生成一个任务名称，您可以修改这个名称，为任务配置一个具备业务意义的名称，便于后续任务识别。

    -   源库信息：

        -   **实例类型**：选择**RDS实例**。
        -   **源实例地区**：选择新实例所在的地域。
        -   **RDS实例ID**：选择新实例的ID。
        -   **数据库账号**：填写新实例的账号。
        -   **数据库密码**：以上账号的密码。
    -   目标库信息

        -   **实例类型**：选择**RDS实例**。
        -   **实例地区**：选择原实例所在地域。
        -   **RDS实例ID**：选择原实例的ID。
        -   **数据库账号**：填写原实例的账号。
        -   **数据库密码**：以上账号的密码。
5.  单击**授权白名单并进入下一步**。
6.  选择**结构迁移**和**全量数据迁移**。
7.  在左侧的**迁移对象**框中，选择要迁移的对象，单击**\>**将这些对象移入已选择对象框中。

    **说明：** DTS会做**同名对象存在性检查**，如果目标RDS实例中已经存在跟待迁移的对象同名的对象，会导致迁移失败。

    修复方式为：

    -   在已选择对象框中，将鼠标放在需要修改的对象上面，单击**编辑**，修改迁移后的对象名称。
    -   使用数据传输服务提供的库表映射功能，将待迁移对象迁移到目标库中的另外一个对象名的对象中。
    -   重命名目标库中跟迁移对象同名的对象。
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7959/15381923823949_zh-CN.png)

8.  单击**预检查并启动**。

    关于具体的预检查内容，请参见[预检查简介](https://help.aliyun.com/document_detail/52099.html)。

    -   以下以预检查不通过为例。如果预检查通过，请直接跳转至步骤10。

9.  若系统显示预检查失败结果，单击检测结果为失败的检测项后的**!**，查看失败详细信息，如下图所示。根据失败原因修复后，可在数据迁移任务列表中选择该任务，并重新进行预检查。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7959/15381923823951_zh-CN.png)

10. 错误排查完毕后，在迁移任务列表页面，选择新创建的迁移任务，单击**启动**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/7959/15381923823952_zh-CN.png)

11. 若系统显示预检查通过信息，单击**确定**。
12. 在购买配置确认页面，确认配置信息并勾选**《数据传输（按量付费）服务条款》**，单击**立即购买并启动**。

