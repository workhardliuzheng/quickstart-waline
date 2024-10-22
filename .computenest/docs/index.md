# ChatGPT-on-WeChat计算巢快速部署

>**免责声明**：本服务由第三方提供，我们尽力确保其安全性、准确性和可靠性，但无法保证其完全免于故障、中断、错误或攻击。因此，本公司在此声明：对于本服务的内容、准确性、完整性、可靠性、适用性以及及时性不作任何陈述、保证或承诺，不对您使用本服务所产生的任何直接或间接的损失或损害承担任何责任；对于您通过本服务访问的第三方网站、应用程序、产品和服务，不对其内容、准确性、完整性、可靠性、适用性以及及时性承担任何责任，您应自行承担使用后果产生的风险和责任；对于因您使用本服务而产生的任何损失、损害，包括但不限于直接损失、间接损失、利润损失、商誉损失、数据损失或其他经济损失，不承担任何责任，即使本公司事先已被告知可能存在此类损失或损害的可能性；我们保留不时修改本声明的权利，因此请您在使用本服务前定期检查本声明。如果您对本声明或本服务存在任何问题或疑问，请联系我们。

## 概述
Waline 一款简洁、安全的评论系统。
此项目的GitHub地址：[https://github.com/walinejs/waline](https://github.com/walinejs/waline)


## 前提条件
<font style="color:rgb(51, 51, 51);">部署Waline社区版服务实例，需要对部分阿里云资源进行访问和创建操作。因此您的账号需要包含如下资源的权限。</font><font style="color:rgb(51, 51, 51);"> </font>**<font style="color:rgb(51, 51, 51);">说明</font>**<font style="color:rgb(51, 51, 51);">：当您的账号是RAM账号时，才需要添加此权限。</font>

| <font style="color:rgb(51, 51, 51);">权限策略名称</font> | <font style="color:rgb(51, 51, 51);">备注</font> |
| --- | --- |
| <font style="color:rgb(51, 51, 51);">AliyunECSFullAccess</font> | <font style="color:rgb(51, 51, 51);">管理云服务器服务（ECS）的权限</font> |
| <font style="color:rgb(51, 51, 51);">AliyunVPCFullAccess</font> | <font style="color:rgb(51, 51, 51);">管理专有网络（VPC）的权限</font> |
| <font style="color:rgb(51, 51, 51);">AliyunROSFullAccess</font> | <font style="color:rgb(51, 51, 51);">管理资源编排服务（ROS）的权限</font> |
| <font style="color:rgb(51, 51, 51);">AliyunComputeNestUserFullAccess</font> | <font style="color:rgb(51, 51, 51);">管理计算巢服务（ComputeNest）的用户侧权限</font> |


## 计费说明
<font style="color:rgb(51, 51, 51);">Waline在计算巢部署的费用主要涉及：</font>

+ <font style="color:rgb(51, 51, 51);">所选vCPU与内存规格</font>
+ <font style="color:rgb(51, 51, 51);">系统盘类型及容量</font>
+ <font style="color:rgb(51, 51, 51);">公网带宽</font>

## 部署架构
本服务部署在单台ECS实例上，架构如下：

![](./images/architecture_ecs_single.png)


## 参数说明
| <font style="color:rgb(51, 51, 51);">参数组</font>     | <font style="color:rgb(51, 51, 51);">参数项</font>    | <font style="color:rgb(51, 51, 51);">说明</font>                                            |
|-----------------------------------------------------|----------------------------------------------------|-------------------------------------------------------------------------------------------|
| <font style="color:rgb(51, 51, 51);">服务实例</font>    | <font style="color:rgb(51, 51, 51);">服务实例名称</font> | <font style="color:rgb(51, 51, 51);">长度不超过64个字符，必须以英文字母开头，可包含数字、英文字母、短划线（-）和下划线（_）</font> |
|                                                     | <font style="color:rgb(51, 51, 51);">地域</font>     | <font style="color:rgb(51, 51, 51);">服务实例部署的地域</font>                                     |
|                                                     | <font style="color:rgb(51, 51, 51);">付费类型</font>   | <font style="color:rgb(51, 51, 51);">资源的计费类型：按量付费和包年包月</font>                             |
| <font style="color:rgb(51, 51, 51);">ECS实例配置</font> | <font style="color:rgb(51, 51, 51);">实例类型</font>   | <font style="color:rgb(51, 51, 51);">可用区下可以使用的实例规格</font>                                 |
|                                                     | <font style="color:rgb(51, 51, 51);">实例密码</font>   | <font style="color:rgb(51, 51, 51);">长度8-30，必须包含三项（大写字母、小写字母、数字、 ()`~!@#$%^&*-+={}[]:;<>,.?/` 中的特殊符号）</font> |
|                                                     | <font style="color:rgb(51, 51, 51);">系统盘类型</font>  | <font style="color:rgb(51, 51, 51);">实例类型支持的系统盘规格</font>                                  |
| <font style="color:rgb(51, 51, 51);">可用区配置</font>   | <font style="color:rgb(51, 51, 51);">可用区</font>    | <font style="color:rgb(51, 51, 51);">ECS实例所在可用区</font>                                    |
| <font style="color:rgb(51, 51, 51);">基础资源配置</font>  | <font style="color:rgb(51, 51, 51);">VPC ID</font> | <font style="color:rgb(51, 51, 51);">资源所在VPC</font>                                       |
|                                                     | <font style="color:rgb(51, 51, 51);">交换机ID</font>  | <font style="color:rgb(51, 51, 51);">资源所在交换机</font>                                       |



1. 访问计算巢Waline[部署链接](https://computenest.console.aliyun.com/service/instance/create/default?type=user&ServiceName=Waline社区版)，按提示填写部署参数
2. 选择付费类型
   ![](./images/pay_type_config.png)
3. 填写实例参数
   ![](./images/resource_config.png)
4. 填写可用区和网络参数并点击“下一步![](./images/zone_config.png)
5. 确认部署参数并查看预估价格后，点击立即创建，等待服务实例部署完成
6. 服务实例部署完成后，点击实例ID进入到详情界面，点击![](./images/how_to_use.png)