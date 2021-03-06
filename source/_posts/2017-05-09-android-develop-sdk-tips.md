---
layout: post
title:  AndroidSDK开发注意事项
date:   2017-05-09
categories: Android
tag: android
---

 

#### SDK的实现目标  ####
 
- 简洁易用
- 稳定
- 高效

不应该对宿主应用有过多的代码侵入<br>
不应该有复杂频繁的接入工作[一行代码接入]<br>
</br>
在不影响宿主稳定的前提下，足够高效；<br>
更少的内存占用；<br>
更少的电量消耗；<br>
更少的流量消耗；<br>
 

#### 架构设计 ####

- 模块化开发
	>根据单一职责将系统拆分为不同的小模块<br>模块间通过协议或接口进行通信，以减少耦合
- 组件化开发
	>将整个项目划分为多个模块，几个模块或单个模块作为一个组件，开发过程中可分别开发，发布时通过依赖，将组件合并成完整的SDK
- 插件化开发
	>SDK 分为宿主和插件。宿主向开发者提供必要的服务接口，并提供自定义的插件加载器，可以热更新，解决方法数64k问题。
- SDK初始化
- 云更新控制
	>可以通过服务端的云控服务，关闭某些SDK功能。
- 安全
	- appId + appSecret
	- 核心逻辑采用C/C++
	- 通讯加密
		- https
		- 对称加密
		- 非对称加密
	- 设备安全[一些开发者会用虚拟机来刷广告]
- 减少传输数据的大小
	- 如果对传输的数据大小有要求，建议对数据压缩
	- 可采用json/xml/protobuf等协议，也可自定义二进制协议
- 选择支持最低系统版本
- 权限管理
	- 最小权限原则
	- 动态权限申请
- 日志服务
	- 日志要在关键位置上打点，记录有效信息
	- 上传信息要保证最大的可靠性，不能发生上传失败，抛弃日志的情况发生
	- 要考虑日志IO带来的开销，不能对正常的流程有过多的性能的影响

#### API的设计 ####
	1.  方法名能够表明其用途
	2.  参数的合法性检验
		- 对于公开方法，通过显式检查，抛出异常的方式，并且使用javadoc的@throw来说明抛出异常的原因
		- 对于私有方法，通过断言的方式来检查参数的合法
		- 检查构造参数的合法性，以使对象处在统一状态
		- 如果检查的代价太大，需要综合考量
	3. 方法要明确其单一的功能-单一职责原则
	4. 方法异常问题
		>对于需要暴露给开发者的方法，要及时地抛出可查异常，来帮助开发者在编译阶段发现的问题，对于运行时异常，必须保证不能导致宿主程序出问题
	5. 方法权限控制
		>需从安全和业务的角度考虑，方法是公开、非公开、静态
	6. 避免过长参数
		- Builder模式
		- 通过静态内部类
		- 将参数封装成对象
		- 将参数拆解为多个方法
	7. 谨慎使用方法重载
	8. 谨慎使用变长参数
	9. 避免方法直接返回null，对于返回集合或list，不要返回null，返回长度为0的容器。
	10. 必要时进行保护性拷贝

#### SDK版本管理策略 ####

- 版本号命名：V + 主版本号 + 子版本号 + 阶段版本号 + 日期版本号 + 希腊字母版本号
	> V1_0_0_108_170515_alpha
	
	希腊字母版本号说明：
	- alpha：内部测试版，表示该阶段主要是以实现功能为主，通常只在内部流通而不对外开放
	- beat：外部测试版，相对alpha版已有很大改进，需要进一步测试和检查以消除bug
	- rc：该版本已相当成熟，与正式版相差无几
	- release：最终交付或公开发布版本，该版本发布时，以符号R来代替release


- 版本号修改规则
	- 主版本号：功能模块有较大变化或整体架构发生变化
	- 子版本号：功能有一定变化
	- 阶段版本号：bug修复或较小变动
	- 日期版本号：用于记录修改项目的当前日期
	- 希腊字母版本号：标注软件所处的开发阶段
- API 版本管理
	1. 原则上SDK API一旦发布不可变
	2. 特殊情况下变更，要遵守开闭原则即对扩展开放对修改关闭
	3. 优先选择添加新方法并尽量兼容原始方法
	4. 需要废弃某些方法是，使用@deprecated标识，并给出替代方案和废弃时间(通常是版本号)

- API 文档
	- 分为内部版和公开版
	- 与SDK的改变要同步更新



 
 
 