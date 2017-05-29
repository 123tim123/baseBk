---
title: api markdown 文档模板
tags: [文档]
---


# 修改历史
日期|内容
---|---
2016-11-04|增加8根据群id查询群
2016-11-05|7接口增加group_desc字段

# 约束
约束|描述
---|---
?|非必要参数
1|必要参数
2|并集关系，非必要，但是必须有一个

# 数据结构
> ``` 
> {"msg":{"code":"0000","msg":"请求成功"},"data":{}}
> ```


## 1.列表账号和群组信息查询   im/imAction!findAcctount.action

#### 输入参数
参数名称 | 参数名| 类型| 所属类|约束 | 描述 |
-----|-----|-----|-----|---|----
群组id列表 | groupRows|list<String>| |2|群组id列表
联系人id列表 | memberRows|list<String>| |2|联系人id列表


#### 输出参数
参数名称 | 参数名 | 类型|所属类|约束 | 描述 |
-----|-----|-----|-----|----|---
data |||||
账号列表 | memberRows|list<ImAcctountBean>|data|?|
账号id|id|String|ImAcctountBean|?|
手机号码|mobile|String|ImAcctountBean|?|
头像|head_url|String|ImAcctountBean|?|
别名|nick|String|ImAcctountBean|?|
账号|account|String|ImAcctountBean|?|
性别|sex|String|ImAcctountBean|?|1.男2.女
年龄|age|String|ImAcctountBean|?|
身高|height|String|ImAcctountBean|?|
居住地|residence|String|ImAcctountBean|?|   
群组id列表 | groupRows|list<ImGroupBean>|data|?|
活动id | activity_id|String|ImGroupBean|?|
群id | group_id|String|ImGroupBean|?|
群创建时间 | create_time|String|ImGroupBean|?|
群名称 | group_name|String|ImGroupBean|?|
群简介 | group_desc|String|ImGroupBean|?|
群头像 | group_head|String|ImGroupBean|?|
群成员 | group_member|List<String>|ImGroupBean|?|
群主 | account|String|ImGroupBean|?|

## 2.根据活动id查询群是否存在   im/imAction!findActivityGroup.action
#### 输入参数
参数名称 | 参数名| 类型| 所属类|约束 | 描述 |
-----|-----|-----|-----|---|----
活动id | activityId|String||1|

#### 输出参数
参数名称 | 参数名 | 类型|所属类|约束 | 描述 |
-----|-----|-----|-----|----|---
data |||||
群组对象 | groupData|ImGroupBean|data|?|
活动id | activity_id|String|ImGroupBean|?|
群id | group_id|String|ImGroupBean|?|
群创建时间 | create_time|String|ImGroupBean|?|
群名称 | group_name|String|ImGroupBean|?|
群简介 | group_desc|String|ImGroupBean|?|
群头像 | group_head|String|ImGroupBean|?|
群成员 | group_member|List<String>|ImGroupBean|?|
群主 | account|String|ImGroupBean|?

## 3.根据活动id创建群组   im/imAction!createActivityGroup.action
#### 输入参数
参数名称 | 参数名| 类型| 所属类|约束 | 描述 |
-----|-----|-----|-----|---|----
群主 | account|String||1|
活动id | activityId|String||?|
群名称 | groupName|String||1|
群简介 | groupDesc|String||1|
群头像 | groupHead|String||?|
群成员账号 | groupMembers|List<String>||1|成员account

#### 输出参数
参数名称 | 参数名 | 类型|所属类|约束 | 描述 |
-----|-----|-----|-----|----|---
data |||||
群组id | groupId|String|data|?|


## 4.根据手机号码模糊查询用户   im/imAction!findImMobile.action
#### 输入参数
参数名称 | 参数名| 类型| 所属类|约束 | 描述 |
-----|-----|-----|-----|---|----
手机号码关键字 | mobile|String||1|

#### 输出参数
参数名称 | 参数名 | 类型|所属类|约束 | 描述 |
-----|-----|-----|-----|----|---
data |||||
用户组 | imAcctountBeans|List<ImAcctountBean>|data|?|
账号id|id|String|ImAcctountBean|?|
手机号码|mobile|String|ImAcctountBean|?|
头像|head_url|String|ImAcctountBean|?|
别名|nick|String|ImAcctountBean|?|
账号|account|String|ImAcctountBean|?|
性别|sex|String|ImAcctountBean|?|1.男2.女
年龄|age|String|ImAcctountBean|?|
身高|height|String|ImAcctountBean|?|
居住地|residence|String|ImAcctountBean|?|   

## 5.获取群人员   im/imAction!getGroupMember.action
#### 输入参数
参数名称 | 参数名| 类型| 所属类|约束 | 描述 |
-----|-----|-----|-----|---|----
群id | group_id|String||1|

#### 输出参数
参数名称 | 参数名 | 类型|所属类|约束 | 描述 |
-----|-----|-----|-----|----|---
data |||||
账号列表 | memberRows|list<ImAcctountBean>|data|?|
账号id|id|String|ImAcctountBean|?|
手机号码|mobile|String|ImAcctountBean|?|
头像|head_url|String|ImAcctountBean|?|
别名|nick|String|ImAcctountBean|?|
账号|account|String|ImAcctountBean|?|
性别|sex|String|ImAcctountBean|?|1.男2.女
年龄|age|String|ImAcctountBean|?|
身高|height|String|ImAcctountBean|?|
居住地|residence|String|ImAcctountBean|?|      


## 6.批量添加群人员   im/imAction!addGroupMembers.action
#### 输入参数
参数名称 | 参数名| 类型| 所属类|约束 | 描述 |
-----|-----|-----|-----|---|----
群id | group_id|String||1|
成员 | memberRows|List<String>||1|需要添加的成员列表


#### 输出参数
参数名称 | 参数名 | 类型|所属类|约束 | 描述 |
-----|-----|-----|-----|----|---
data |||||


## 7.修改群信息   im/imAction!updataGroupData.action
#### 输入参数
参数名称 | 参数名| 类型| 所属类|约束 | 描述 |
-----|-----|-----|-----|---|----
群id | group_id|String||1|
群昵称 | group_name|String||?|
群头像 | group_head|String||?|
群简介 | group_desc|String||?|


## 8.群id查询群   im/imAction!findGroupIdGroup.action
#### 输入参数
参数名称 | 参数名| 类型| 所属类|约束 | 描述 |
-----|-----|-----|-----|---|----
群组id | group_id|String||1|

#### 输出参数
参数名称 | 参数名 | 类型|所属类|约束 | 描述 |
-----|-----|-----|-----|----|---
data |||||
群组对象 | groupData|ImGroupBean|data|?|
活动id | activity_id|String|ImGroupBean|?|
群id | group_id|String|ImGroupBean|?|
群创建时间 | create_time|String|ImGroupBean|?|
群名称 | group_name|String|ImGroupBean|?|
群简介 | group_desc|String|ImGroupBean|?|
群头像 | group_head|String|ImGroupBean|?|
群成员 | group_member|List<String>|ImGroupBean|?|
群主 | account|String|ImGroupBean|?
账号列表 | memberRows|list<ImAcctountBean>|ImGroupBean|?|
账号id|id|String|ImAcctountBean|?|
手机号码|mobile|String|ImAcctountBean|?|
头像|head_url|String|ImAcctountBean|?|
别名|nick|String|ImAcctountBean|?|
账号|account|String|ImAcctountBean|?|
性别|sex|String|ImAcctountBean|?|1.男2.女
年龄|age|String|ImAcctountBean|?|
身高|height|String|ImAcctountBean|?|
居住地|residence|String|ImAcctountBean|?|   