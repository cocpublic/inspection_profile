# 静态代码检测指引(V1.0-beta3)

## 版本说明

### v1.0-beta3

在beta2的基础上移除以下：
* Android - Lint - Correctness - ChromeOS组
* Android - Lint - Correctness - Messages组(format校验除外)
* Java - Javadoc组

### 历史版本

**v1.0-beta2**
禁用不相关的规则组。

**v1.0-beta1**
初版，基于Android Studio 3.4 Build AI-183.5429导出并分级，规则1635条；
 
Inspection List文档链接：https://mubu.com/doc/l8iHAlZvr 密码：3j81

## 一、概述
为了保证提测质量，减少代码潜在的缺陷，给用户带来更优秀的产品体验，现计划在项目提测CheckList中加入 静态代码检测，保证代码的健壮性。

从原则来说，检测报告列出的条目，都需要开发人员给予关注，并根据项目状态 进行修正或忽略；​但在实际操作中，存在一个很现实的问题——工期限制。

由于历史遗留，前期检测报告产出的条目数量巨大，单一版本难以完成所有工作。

为了保证实际可操作性，我们根据几个维度（Issue对运行指标的影响程度、修复issue的工作量)，对​​​​处理优先级进行划分，在落地过程中，分阶段完成相关Issue的处理工作。
​
以下是详细说明：

## 二、规则分级说明
目前仅对Android开发相关的规则进行筛选分级，在实际落地过程中，会根据实际情况，对现有分级规则中遗漏、不合理的条目，进行增删改动。

v1.0-beta1版本说明 ：
初版，基于Android Studio 3.4 Build AI-183.5429导出分级，规则1635条；
 
Inspection List文档链接：https://mubu.com/doc/l8iHAlZvr 密码：3j81

#### 1、Error
打包编译通过，​但存在以下情况：
​1、潜在的崩溃；
2、明显的安全隐患；
3、明显的编码问题；​
4、团队制定的指标(比如Apk体积)​

Inspection List标记为 红​色
​
#### 2、Warnning​​
打包编译通过，但存在以下情况：
1、性能问题；
2、部分安全问题；​​​
3、部分编码标准；​

Inspection List标记为 黄色

#### 3、​​​Weak Warning
编译通过，存在一些问题，但重要程度不及上面

Inspection List标记为 蓝色

#### 4、​​Server problem
分为两类：
编译通过，编码标准、规范类问题​​；
编译不通过，IDE要求强制修正；​

Inspection List标记为 绿色

> **定制规则导入方式 ，参考下方附录一**

## 三、落地规划

项目落地分为以下三个阶段，详细说明如下：

### 1、第一阶段：稳定保障
* Error类问题 清零;


### 2、第二阶段：性能保障
* ​Error类 保持为零​;
* Warning 每版本 减少​一定数值20%~30%


### 3、第三阶段：编码完善

* Error类保持为零​;
* Warning类 保持在100~400以内​
* Warkey Warning 每版本 减少10%~20%​
* Server  problem 按需减少


## 四、实施流程
整个计划实施流程分为两类：审核流程、自测流程、。

### 1、审核流程
审核流程分为两步：版本指标审核、完成状况审核。

#### 版本指标核定
每个版本上线后，会对当前版本的线上代码进行静态扫描，由项目相关负责人确定 下个版本需要达成的指标：Error、Warning数是多少。

该指标商定后填充到 相关文档进行备忘，便于后续 “达成审核”的标准。

#### 完成状况审查
完成状况审核 可以分为“前置审核”、”后置审核”两种，由项目根据实际情况选择。

前置审核，会在版本上线前，对代码进行静态扫描，不达标不予上线。
后置审核，会在版本上线后，对代码进行静态扫描，根据达成状态予以奖惩。

### 2、自测流程

由于“完成状况审核” 环节，只会由 **专人** 执行 **有限次**，为了更好的达成版本指标，建议项目开发人员在开发过程中，“隔三差五”地执行静态扫描，对指标覆盖的Issue予以消除；项目管理人员在关键环节(提测前、上线前)进行静态扫描。


## 附录
###  附一、导入自定义 Inspection Profile
本段主要介绍 Android Studio 如何导入自定义自定义 Inspection Profile。

#### 1、获取最新的自定义Inspection Profile
在git.xin.com对应仓库获取最新的 Inspection profile.xml文件

#### 2、导入Android Studio
在Android Studio中 选择 Analyze--Inspect code
![导入Inspection profile.png](https://i.loli.net/2019/05/13/5cd94e37ac30198433.png)


#### 3、执行扫描

![选择Profile扫描.png](https://i.loli.net/2019/05/13/5cd94e5841f0525194.png)

用户可以根据自己的需要，选择合适的Scope检测，以下是必要Scope的简述：

* Changed File。变动的文件。
* Module File。指定模块的文件
* Project Production Files。项目素有源码文件(不包含Test、依赖库、AAR)
* Whole File。所有文件。


### 附二：Android Studio 增大Heap内存

![](https://cdn.nlark.com/yuque/0/2019/png/208918/1558521899946-dc4edab1-b3f8-491b-b89a-1db1146dba4d.png)

根据设备状态指定合适的内存值

![](https://cdn.nlark.com/yuque/0/2019/png/208918/1558521905741-a55e2b3a-bc4a-491e-8cf3-5d74177c4163.png)

```
-Xms512m
-Xmx6g
-XX:ReservedCodeCacheSize=480m
-XX:+UseCompressedOops
-Dfile.encoding=UTF-8
-XX:+UseConcMarkSweepGC
-XX:SoftRefLRUPolicyMSPerMB=50
-Dsun.io.useCanonCaches=false
-Djava.net.preferIPv4Stack=true
-Djdk.http.auth.tunneling.disabledSchemes=""
-Djna.nosys=true
-Djna.boot.library.path=

-da
-Xverify:none

-XX:ErrorFile=$USER_HOME/java_error_in_studio_%p.log
-XX:HeapDumpPath=$USER_HOME/java_error_in_studio.hprof
```
