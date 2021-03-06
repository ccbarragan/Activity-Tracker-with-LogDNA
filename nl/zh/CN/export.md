---

copyright:
  years: 2019
lastupdated: "2019-05-25"

keywords: IBM Cloud, LogDNA, Activity Tracker, export

subcollection: logdnaat

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:download: .download}
{:important: .important}
{:note: .note}

 
# 导出事件
{: #export}

您可以将数据以 JSONL 格式从 {{site.data.keyword.at_full_notm}} 实例导出到本地文件。您可以使用 LogDNA REST API 或通过 Web UI 以编程方式导出日志。
{:shortdesc}


## 先决条件
{: #export_prereqs}

* [了解有关导出事件的更多信息](/docs/services/Activity-Tracker-with-LogDNA?topic=logdnaat-monitor_events#mon_export)。

* **您必须具有 {{site.data.keyword.at_full_notm}} 服务的付费服务套餐**。[了解更多信息](/docs/services/Activity-Tracker-with-LogDNA?topic=logdnaat-service_plan#service_plan)。 

* 检查用户标识是否有权启动 Web UI 和查看事件。下表列出了用户要能够启动 {{site.data.keyword.at_full_notm}} Web UI 以及查看、搜索和过滤事件而必须具有的最基本角色：

| 角色                      | 授予的许可权            |
|---------------------------|-------------------------------|  
| 平台角色：`查看者`     | 允许用户在“可观察性”仪表板中查看服务实例的列表。|
| 服务角色：`读取者`     | 允许用户启动 Web UI 并在 Web UI 中查看事件。|
{: caption="表 1. IAM 角色" caption-side="top"} 

有关如何为用户配置策略的更多信息，请参阅[授予用户对用户或服务标识的许可权](/docs/services/Activity-Tracker-with-LogDNA?topic=logdnaat-iam_view_events#iam_view_events)。


## 步骤 1. 转至 Web UI
{: #export_step1}

[转至 Web UI](/docs/services/Activity-Tracker-with-LogDNA?topic=logdnaat-launch#launch)。


## 步骤 2. 创建视图
{: #export_step2}

[创建视图](/docs/services/Activity-Tracker-with-LogDNA?topic=logdnaat-views)。


## 步骤 3. 导出数据
{: #export_step3}

选择以下其中一个选项以导出事件：

### 选项 1. 从 Web UI 导出事件
{: #export_ui}

要导出数据，请完成以下步骤：

1. 单击**视图**图标 ![“配置”图标](images/views.png)。
2. 选择**所有内容**或某个视图。
3. 应用时间范围、过滤器和搜索条件，直到看到要导出的条目。
4. 如果是从**所有内容**视图开始，请单击**未保存的视图**。如果在上一步中选择了视图，请单击视图名称。
5. 选择**导出行**。这将打开一个新窗口。
6. 检查时间范围。如果需要更改时间范围，请单击*更改导出时间范围*字段中的预定义时间范围。
7. 选择**首选较新的行**或**首选较旧的行**，以防导出请求超过行限制。
8. 查看您的电子邮件。您会从 **LogDNA** 收到一封电子邮件，其中包含用于下载导出行的链接。


### 选项 2. 使用 API 以编程方式导出事件
{: #export_api}

要以编程方式导出事件，请完成以下步骤：

1. 生成服务密钥。 

    **注:**您必须具有对 {{site.data.keyword.at_full_notm}} 实例或服务的**管理者**角色才能完成此步骤。

    1. [启动 {{site.data.keyword.at_full_notm}} Web UI](/docs/services/Activity-Tracker-with-LogDNA?topic=logdnaat-launch#launch_step2)。

    2. 选择**配置**图标 ![“配置”图标](images/admin.png)。然后，选择 **组织**。 

    3. 选择 **API 密钥**。

        您可以查看创建的服务密钥。 

    4. 单击**生成服务密钥**。新的密钥会添加到列表中。复制此密钥。

2. 导出事件。运行以下 cURL 命令：

    ```
    curl "ENDPOINT/v1/export?QUERY_PARAMETERS" -u SERVICE_KEY:
    ```
    {: codeblock}

    其中 

    * ENDPOINT 表示服务的入口点。每个区域都有不同的 URL。[了解更多信息](/docs/services/Activity-Tracker-with-LogDNA?topic=logdnaat-endpoints#endpoints)。
    * QUERY_PARAMETERS 是用于定义应用于导出请求的过滤条件的参数。
    * SERVICE_KEY 是您在上一步中创建的服务密钥。

下表列出了可以设置的查询参数：

| 查询参数 | 类型       | 状态     | 描述 |
|-----------|------------|------------|-------------|
| `from`      | `int32`      | 必需   | 开始时间。设置为 UNIX 时间戳记（以秒或毫秒为单位）。|
| `to`        | `int32`      | 必需   | 结束时间。设置为 UNIX 时间戳记（以秒或毫秒为单位）。|
| `size`      | `string`     | 可选   | 要包含在导出中的日志行数。| 
| `hosts`     | `string`     | 可选   | 以逗号分隔的主机列表。|
| `apps`      | `string`     | 可选   | 以逗号分隔的应用程序列表。|
| `levels`    | `string`     | 可选   | 以逗号分隔的日志级别列表。|
| `query`     | `string`     | 可选   | 搜索查询。有关更多信息，请参阅[搜索日志](/docs/services/Log-Analysis-with-LogDNA?topic=LogDNA-view_logs#view_logs_step6)。|
| `prefer`    | `string`     | 可选   | 定义要导出的日志行。有效值为 `head`（从头开始的日志行数）和 `tail`（最后的日志行数）。如果未指定，缺省值为 tail。|
| `email`     | `string`     | 可选   | 指定包含导出的可下载链接的电子邮件。缺省情况下，日志行将流式传输。|
| `emailSubject` | `string`     | 可选   | 用于设置电子邮件的主题。</br>使用 `%20` 表示空格。例如，样本值为 `Export%20logs`。|
{: caption="查询参数" caption-side="top"} 

例如，要将事件流式传输到终端，可以运行以下命令：

```
curl "https://api.us-south.logging.cloud.ibm.com/v1/export?to=$(date +%s)000&from=$(($(date +%s)-86400))000" -u e08c0c759663491880b0d61712346789:
```
{: codeblock}

要发送电子邮件，其中包含用于下载导出上所指定的事件的链接，可以运行以下命令：

```
curl "https://api.us-south.logging.cloud.ibm.com/v1/export?to=$(date +%s)000&from=$(($(date +%s)-86400))000&email=joe@ibm.com" -u e08c0c759663491880b0d61712346789:
```
{: codeblock}


要发送使用定制主题的电子邮件，可以运行以下命令：

```
curl "https://api.us-south.logging.cloud.ibm.com/v1/export?to=$(date +%s)000&from=$(($(date +%s)-86400))000&email=joe@ibm.com&emailSubject=Export%20test" -u e08c0c759663491880b0d61712346789:
```
{: codeblock}

