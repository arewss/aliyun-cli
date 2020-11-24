# 使用Aliyun CLI来管理云资源

## Alibaba Cloud CLI 介绍 
阿里云命令行工具是在阿里云GO SDK之上构建的开源工具。   
您可以通过阿里云CLI，管理您的阿里云资源。  

在 Cloud Shell 中，您可以直接输入 aliyun 来使用：
```bash
aliyun
```
## 快速体验 

在Cloud Shell中，您可以直接使用 aliyun 来管理您的云资源  
Cloud Shell为您内置了授权，无需进行额外配置

  
比如您想查看杭州region下的Ecs实例信息，可以直接输入以下命令：

```bash
aliyun ecs DescribeInstances --RegionId cn-hangzhou
```

## RPC API 和 RESTful API

阿里云云产品的API分为RPC和RESTful两种类型，大部分产品使用的是RPC风格。不同风格的API的调用方法也不同。

您可以通过以下特点判断API类型：

+ API参数中包含Action字段的是RPC API，需要PathPattern参数的是Restful API。
+ 一般情况下，每个产品内，所有API的调用风格是统一的。
+ 每个API仅支持特定的一种风格，传入错误的标识，可能会调用到其他API，或收到ApiNotFound的错误信息。

## 调用RPC API

使用阿里云CLI调用RPC API的基本结构如下：  
```
aliyun <product> <operation> [--parameter1 value1 --parameter2 value2 ...]
```

如：
查看数据库实例：
```bash
aliyun rds DescribeDBInstances --PageSize 50
```

还有您刚刚体验过的查看Ecs实例：
```bash
aliyun ecs DescribeInstances --RegionId cn-hangzhou
```

## 调用RESTful API   
部分阿里云产品如容器服务是RESTful API。RESTful API与RPC API的调用方式不同。

使用阿里云CLI调用RESTful API的基本结构如下：
```
aliyun <product> <method> /path 
```

如[创建容器集群](https://help.aliyun.com/document_detail/71197.html)：
```
aliyun cs POST /clusters --header "Content-Type=application/json" --body "$(cat create.json)"
``` 

## 输出格式化  

#### JSON格式化 
通过上面的命令，对应API的返回JSON数据，不过这看起来不够清晰，您可以配合jq命令来格式化这个JSON返回数据

如：
```bash
aliyun ecs DescribeRegions | jq 
```

## 输出格式化  
#### 列表模式   
您可能希望以列表的形式来展示您的Region信息   
可以使用--output参数提取结果中感兴趣的字段，并进行表格化输出。例如：
```bash
aliyun ecs DescribeRegions --output cols=RegionId,RegionEndpoint,LocalName rows=Regions.Region
```
这样获得的返回信息更加清晰了  
#### 小技巧： 
生成ECS实例信息列表（ID, Name, IP)： 
```bash
aliyun ecs DescribeInstances --output cols=InstanceId,InstanceName,PublicIpAddress.IpAddress,EipAddress.IpAddress rows=Instances.Instance --RegionId=cn-shanghai --PageNumber=1
```

### 注意事项
在使用--output参数时，必须指定以下子参数：
+ cols: 表格的列名，需要与json数据中的字段相对应。如ECS DescribeInstances 接口返回结果中的字段InstanceId 以及 Status。

可选子参数：

+ rows: 通过[jmespath](http://jmespath.org/?spm=a2c4g.11186623.2.17.579bb992aZEqP0)查询语句来指定表格行在json结果中的数据来源。当查询语句具有Instances.Instance[]的形式时，可以省略该参数

## waiter 参数  
该参数用于轮询实例信息直到出现特定状态。

例如使用ECS创建实例后，实例会有启动的过程。我们会不断的查询实例的运行状态，直到状态变为”Running”。  
```
aliyun ecs DescribeInstances --InstanceIds '["i-12345678912345678123"]' --waiter expr='Instances.Instance[0].Status' to=Running
```

执行以上命令后,命令行程序将以一定时间间隔进行实例状态轮询，并在实例状态变为Running时停止轮询。

在使用--waiter参数时，必须指定以下两个子参数：

+ expr: 通过[jmespath](http://jmespath.org/?spm=a2c4g.11186623.2.18.29ddb992tZYXnZ)查询语句来指定json结果中的被轮询字段。
+ to: 被轮询字段的目标值。  

可选子参数：

+ timeout: 轮询的超时时间(秒)。
+ interval: 轮询的间隔时间(秒)。

## 自动补全  
Cloud Shell 中预置了 aliyun cli 工具的自动补全

当您输入某个字段，但是忘记全程怎么写的时候，可以尝试敲击键盘上的tab键，
如
```bash
aliyun ecs DescribeReg
```
敲击tab，命令行会自动补全成  
aliyun ecs DescribeRegions

## 获取帮助信息  

阿里云CLI集成了部分产品的API和参数列表信息, 您可以使用如下命令获取帮助：

+ 获取支持的产品列表   

    ```bash
    aliyun help
    ```

+ 获取ECS的API信息  

    ```bash
    aliyun ecs help
    ```

+ 获取API调用信息  

    ```bash
    aliyun ecs CreateInstance help
    ```
## 恭喜完成教程

恭喜您完成了学习如何使用Aliyun CLI的教程。
