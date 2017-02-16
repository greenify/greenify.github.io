---
title: 绿色守护社区特性
---
中文|[English](/README.md)

# 关于“处方”

绿色守护中的“处方”，是一种针对任何Android应用中不受欢迎行为的精确定向屏蔽。它被社区成员以完全开放的规则形式发布在GitHub上，之后即可由任何用户导入自己设备上安装的绿色守护中。欢迎所有拥有相关经验的高级用户（尤其是开发者）自己编写、发布和传播处方，为Android应用生态带来带来更好的风貌，形成一股可以影响和鼓励应用开发者提升应用设备体验的力量。这也是绿色守护的初衷。

“处方”特性需要绿色守护工作在ROOT模式，或Android 4.4~5.x下工作在非ROOT模式。

# 处方能做什么

处方可以屏蔽Android应用中各种不受欢迎的行为，并且仍在持续进化，在未来短期内还将获得更多的能力。

* 由Android广播机制触发的行为
* 服务的启动和绑定（调用）行为
* 界面的启动行为

具体到实际场景中的用途，常见可屏蔽的不受欢迎行为包括：

* 浪费电量和内存的不必要的持续运行后台服务或周期性静默运行的后台功能，应用本身未提供设置选项。
* 一次启动大量应用后台进程的行为，造成严重的设备性能下降和电量消耗。（在某些被广泛使用的开发质量低下的SDK中尤为常见）
* 不受欢迎的推广信息推送，应用本身未提供合理的设置。
* 设备内置的某些不受欢迎或存在缺陷的功能。（如Google底层服务在中国大陆地区造成的严重耗电）
* ……

# 如何写处方

处方以XML格式书写，以格式版本（由`xmlns`定义，见下例）的方式保证兼容性。较新的内容格式版本通常需要较新版本的绿色守护支持。

目前，绿色守护支持以下两种类型的处方：

## 处方单 —— 一条具有具体规则内容的处方

编写一条处方单需要对Android开发的初步知识和对应用行为底层的深入分析，因为处方单是基于Android中负责应用间（及应用内）行为和通信的基础协议 —— “意图”（`intent`）机制屏蔽特定的行为。

语法：

```xml
<prescription xmlns="http://greenify.github.io/schemas/prescription/v2"
  type="service|broadcast|activity"
  sender="other-app|other|any"
  [package="<app package name>"]
  [class="<component class name>"]>
  <intent-filter>
    ...
  </intent-filter>
</prescription>
```

或（从绿色守护 3.2.0 版本开始)
  
```xml
<prescriptions xmlns="http://greenify.github.io/schemas/prescription/v2">
  <prescription ... >
    ...
  </prescription>
  <prescription ... >
    ...
  </prescription>
  ...
</prescriptions>
```

一条处方单可以包含单项或多项“意图筛选器”（`<intent-filter>`，其具体语法参见[Android开发者文档](https://developer.android.com/guide/topics/manifest/intent-filter-element.html)）。如果其中的任何一项匹配，行为就会被屏蔽。复杂的处方单还可以将“意图”匹配的范围限定于特定的应用或应用中的特定组件（通过`<prescription>`标签的`package`和`class`属性）。（例如：<https://github.com/greenify/rx-baidu-sso/>） 如果这两个属性同时使用，则允许完全略去“意图筛选器”，从而对一个组件进行无条件屏蔽。

从绿色守护3.2.0版本（处方格式版本2）开始，一条处方单还可以包含多个子处方单（上述`<prescriptions>`语法）。以及新的`sender`属性用以限定行为的触发来源，目前支持三种范围的触发来源：

* `other-app`：其它应用触发的行为（默认）
* `other`：非应用自身触发的行为（在前一类型基础上，还涵盖了系统触发的行为）
* `any`：任何来源触发的行为（在前一类型基础上，还涵盖了应用内部的行为）

简单示例：<https://github.com/greenify/rx-mipush>

```xml
<prescription xmlns="http://greenify.github.io/schemas/prescription/v1" type="service">
  <intent-filter>
    <action name="com.xiaomi.mipush.sdk.WAKEUP" />
  </intent-filter>
</prescription>
```

这是一条简单的处方单，包含仅仅一个用于识别所屏蔽行为的“意图筛选器”（`intent-filter`）。类型属性`type="service"`表明它处理的是“服务”行为。

## 处方集 —— 对一组处方单的汇集引用

处方集可以包含处方单及其它处方集，均以GitHub上的代码库相对路径来引用。

例如：<https://github.com/oasisfeng/rxs-push-services>

```xml
<prescriptions xmlns="http://greenify.github.io/schemas/prescription/v1">
  <prescription link="/greenify/rx-mipush" />
  <prescription link="/greenify/rx-baidu-push" />
</prescriptions>
```

# 如何发布和分发编写的处方

所有的处方都是以GitHub代码库的形式发布，处方单的名字须以“`rx-`”开始，而处方集的名字须以“`rxs-`”开始。

说明文件（`README`）虽非必须，但是强烈建议提供，以补充简短描述无法阐述清楚的关于该处方的详细说明，特别是它具体屏蔽的行为以及对用户体验的影响。

发布处方需要使用`https://greenify.github.io/<用户名>/<代码库名>`这样的网址格式（而非GitHub代码库的网址），这样绿色守护才能识别和导入你的处方。例如，一条位于<https://github.com/oasisfeng/rxs-push-services>的处方，其发布网址应为**<https://greenify.github.io/oasisfeng/rxs-push-services>**（仅域名部分不同）。当安装了绿色守护的用户在浏览器中打开后一个链接时，绿色守护将会启动并展示这条可导入其中的处方。

# 与社区一起探索和尝试

GitHub面向社区开发和协同的优秀设计，使得你不仅可以在数分钟内创建和编写一条处方，完全在网站上完成，还能随时随地使用移动设备的浏览器对其作出修改和调整。

“衍生（Fork）”是GitHub的灵魂，我们也积极拥抱同样的思想。欢迎大家在其他人编写的处方之上进行“衍生”，进行额外的修改调整。如果你愿意，也鼓励通过发起“同步请求（Pull Request）”来支持社区里彼此的努力。在未来版本的绿色守护中，我们将加入针对“衍生（Fork）”的显性标识和相应交互（比如在多个衍生之间切换，尝试不同的衍生版本）。

如果你有任何疑问想要提出，或是有想法希望分享，欢迎在我们的[G+社区](https://plus.google.com/communities/103850238949791125024)、[XDA论坛（英文）](https://forum.xda-developers.com/apps/greenify)或第三方活跃社区（如[酷安网](http://www.coolapk.com/apk/com.oasisfeng.greenify)）中友好讨论。
