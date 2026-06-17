# Udon Graph新手入门指南（暂停更新）

Udon Graph是VRchat内置的一种可视化编程器，旨在通过降低编程门槛，让创作者可以在不具备太多的代码知识的情况下快速实现简单的功能。对比与U Shape编辑器，它拥有可视化，模块化，易上手的特点。同时他有Bug抽象，可塑性低，解决方案较少的问题。现在，本文将从头开始，一步步教您认识Udon Graph。

（答疑区域：VRCD玩家社区：ARK-Magellan，个人答疑群（球球进来吧）：902222352）

有那部分不会可以来群里接受群主一对一教导~~~~

# Udon Graph新手入门指南——基础篇

本文将介绍Udon Graph的基础操作知识

注意：教程不分前后，除了基础教程为第一个以外，其他全都是选修——请等到有需求的时候再开封~~~~

# 新页面



# Udon Graph ：基础操作与信息

## 欢迎来到Udon Graph

UdonGraph是Vrchat制作的，Udon Behaviour可以加载的两种编程格式的一种，全名Udon Node Graph（Udon节点图），正如名称所说。UdonGraph是通过节点图来实现编程的，就像是下面这样……

[![Hallo World.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/5ZcFfhuyNetPmTDX-hallo-world.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/5ZcFfhuyNetPmTDX-hallo-world.png)

如图，这段代码的意思是：当玩家交互时（Event Interact事件）向控制台输出Message（Hallo World）

很简单，对吧，UdonGraph就是这样的东西，让我们一起走入UdonGraph的世界，探寻他的一切……

---

### 接口

您可以在菜单栏点击 **VRChatSDK &gt; Udon Graph** 或直接点击 UdonBehaviour 组件上的 **Open Udon Graph** 按钮来打开 Udon 节点图窗口。

如果您是通过直接点击UdonBehaviour来打开窗口的，那么您会直接跳到节点图并且可以立即开始编程工作，当然，您也可以直接在Assets里面打开Udon Graph程序——这两个本质上是一样的。

如果您是通过菜单栏打开的 Udon 窗口，您会看到欢迎页面，其会显示一些更新日志和其他设置。

您可以同时打开多个节点图，并且通过在节点图窗口顶部的标签页来在不同的节点图之间切换。

您也可以通过点击标签一角的 X 来关闭标签页。请注意，节点图标签页并不是“真正的”的标签页，当您点击不同的标签页时，它只会重新加载节点图。

#### 创建一个新UdonGraph脚本

您可以通过两种方式新建一个UdonGraph脚本

- 1：在Assets的任意位置右键选择“创建”-“VRchat”-“UdonGraph”-“Udon Graph program Asset”即可在您的文件夹中新建一个UdonGraph脚本
- 2：通过UdonBehaviour-NewProgram来创建新的UdonGraph脚本（不建议）

---

### 流（Flow）

下面让我们来学习一下UDONGraph决定执行顺序的重要组件——流（Flow）

[![index-f9c508c-simple-branching.rADTzpNs.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/Nha9Lvn2fUsY1KjQ-index-f9c508c-simple-branching-radtzpns.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/Nha9Lvn2fUsY1KjQ-index-f9c508c-simple-branching-radtzpns.png)

上图中间的三角形就是 *流* 的接口，其按从左到右连接的“粗白线”（官方喜欢称为面条）的顺序来触发节点。 要了解 Udon 节点图中做了什么事或者制作自己的节点图请 *跟着流走*。

顶栏上有一个“高亮显示流（Highlight flow）”开关，启用后将高亮显示通过流边缘连接的节点，让您可以快速查看程序是如何到达特定节点的。

[![index-2139dee-simple-flow-highlight.4Xnb66sE.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/Cacm3yiMc0ueZCbe-index-2139dee-simple-flow-highlight-4xnb66se.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/Cacm3yiMc0ueZCbe-index-2139dee-simple-flow-highlight-4xnb66se.png)

如果一个节点没有连接任何流，那么什么事都不会发生。（就连汇编生成器都会无视这个节点的存在——无论这个“节点链接了多少东西”）

在上面节点图中：

1. `Start` 事件会在世界加载的时候触发
2. 在下一步触发的 “分支（Branch）” 节点中。 会通过复选框的值决定是触发 *True* 还是 *False* 路径。
3. 如果“分支（Branch）”节点中的值为 True，则会触发上面的节点发送一个名为“Hello”的自定义事件。
4. 如果不是，则会发送一个名为 “Goodbye” 的自定义事件。

如果您不知道 **发送自定义事件（Sending a Custom Event）** 是什么意思也是正常的。 先学习节点图中的流是您理解节点图的工作方式的第一步。跟紧脚步——您会在不久之后理解所有东西。

---

### 创建节点

在 Udon 节点图中，每一个节点都是一个包含了特定功能的方块。 您可以将多个节点以一定的方式互相连接，构成一个“节点图”，而它就是您所制作出来的程序。

有以下几种办法可以创建节点：

- 快捷键
- 拖放操作
- 搜索菜单

#### 快捷键[​](https://docs.vrczh.org/creators.vrchat.com/worlds/udon/graph/#%E5%BF%AB%E6%8D%B7%E9%94%AE)

按住以下任意一个快捷键再在节点图内点击您想创建节点的地方来创建对应的节点。

- `1`：浮
- `2`： 矢量 2
- `3`： 矢量 3
- `4`： 矢量 4
- `+`： float 加法（加法）
- `-`： float 减法（subtraction）
- `=`- float 比较是否相等（相等比较）
- `b`: 分支（Branch）
- `shift+b` : 分组（Block）

#### 其他快捷键[​](https://docs.vrczh.org/creators.vrchat.com/worlds/udon/graph/#%E5%85%B6%E4%BB%96%E5%BF%AB%E6%8D%B7%E9%94%AE)

- 按住 然后点击一个常量来把它转换成变量。`C`
- `Shift+A` 居中选择的节点。
- 按住 快速创建分组。`Ctrl+G`
- `L+鼠标左键` 记录所选节点的值。
- 按住 然后点击输出数组类型的节点来自动生成一个 foreach 循环。`Shift+F`

[![index-87b33a4-for-loop.K-6pkrFS.gif](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/kjuTF4obPdcFfXbB-index-87b33a4-for-loop-k-6pkrfs.gif)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/kjuTF4obPdcFfXbB-index-87b33a4-for-loop-k-6pkrfs.gif)

---

### 拖放游戏对象（GameObject）和组件[​](https://docs.vrczh.org/creators.vrchat.com/worlds/udon/graph/#%E6%8B%96%E6%94%BE%E6%B8%B8%E6%88%8F%E5%AF%B9%E8%B1%A1-gameobject-%E5%92%8C%E7%BB%84%E4%BB%B6)

如果要向游戏对象（GameObject）或组件添加逻辑，可以将它们从层级（Hierarchy）窗口中拖放到节点图中。 例如，您可以通过将“Light”标题拖动到节点图上来使用 Light 组件。

[![index-6238d1e-light-component.D15lZZ_Z.jpg](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/HWUDU3zf7D9pVt9M-index-6238d1e-light-component-d15lzz-z.jpg)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/HWUDU3zf7D9pVt9M-index-6238d1e-light-component-d15lzz-z.jpg)

通过拖放操作创建的节点会创建一个绑定到游戏对象或组件的变量，因此您会看到一个新变量和一个节点出现在变量（Variables）窗口中，该节点是一个“Get Variable”节点，其会自动设置为获取刚刚新添加的组件。

#### 拖放变量[​](https://docs.vrczh.org/creators.vrchat.com/worlds/udon/graph/#%E6%8B%96%E6%94%BE%E5%8F%98%E9%87%8F)

您可以通过点击 Graph Sidebar 下 Variables 面板中的 + 按钮来创建任何类型的变量。之后您可以将相应变量名称拖放到面板上以创建“Get Variable”节点，在拖动时按住 Ctrl 键以创建“Set Variable”节点，或按住 Alt 键以创建“On Variable Changed”节点。

#### 搜索节点[​](https://docs.vrczh.org/creators.vrchat.com/worlds/udon/graph/#%E6%90%9C%E7%B4%A2%E8%8A%82%E7%82%B9)

按下**空格**打开快捷搜索，输入您想要搜索的类的前几个词。

[![index-08df7d3-gameobject-search.CqVC4jBU.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/5Ew49rq8pCF2w5nZ-index-08df7d3-gameobject-search-cqvc4jbu.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/5Ew49rq8pCF2w5nZ-index-08df7d3-gameobject-search-cqvc4jbu.png)

除了这个搜索方式以外，您还可以通过按住Shift+Tap来打开全局搜索，他会在全局搜索您输入的组件——比较卡，但是有效

[![searching-for-nodes-0f8fb2b-fullsearch.3sJVGO34.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/lUoOJoD28BtD8wFm-searching-for-nodes-0f8fb2b-fullsearch-3sjvgo34.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/lUoOJoD28BtD8wFm-searching-for-nodes-0f8fb2b-fullsearch-3sjvgo34.png)

其他的搜索方法，可以通过访问[https://docs.vrczh.org/creators.vrchat.com/worlds/udon/graph/searching-for-nodes](https://docs.vrczh.org/creators.vrchat.com/worlds/udon/graph/searching-for-nodes "搜索节点")来查看，不过平时使用上述的两种就已经足够了。

---

### 编译节点图

节点图会在后台定时自动编译。编译时您会看到节点图右上角在闪动，当编译完成时会显示绿色或在编译失败时显示红色。您点击状态盒（Status Box）查看生成的“汇编”代码或者编译失败时出现的错误。

[![index-fc0a2c0-assembly.Dg_J-dwY.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/bMWtlvONYxRu4cTf-index-fc0a2c0-assembly-dg-j-dwy.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/bMWtlvONYxRu4cTf-index-fc0a2c0-assembly-dg-j-dwy.png)

（该编译并没有想象中的靠谱，实际上UdonGraph一直有着一些不易复现的BUG，这种问题在编辑大型脚本的时候尤为严重——您可以能需要花大量的时间来修复这些错误。所以我不建议您使用节点图来制作大型项目。）

在您点击OK，并且没有报错时，您就已经完成了一个脚本的构建了，剩下的事情就是将这个脚本挂载到UdonBehaviour上，然后播放，测试一下效果。

# Udon Graph 基础教程-脚本数据互相交换以及互相触发以及自我延时循环

大家好，欢迎来到Udon Graph基础教程，本期我们来讲讲多个Udon脚本之间的数据交换问题在Graph的解决方式。

---

### 数据交换：

UdonGraph提供了一个名为ProgramVariable的功能，该功能一共有三个模块，分别是Set ProgramVariable，Get ProgramVariable ，Get ProgramVariableType

[![669564a37d5337b8e722fadc7e2ca6b8.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/VfZTR8Z7WRkBOezP-669564a37d5337b8e722fadc7e2ca6b8.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/VfZTR8Z7WRkBOezP-669564a37d5337b8e722fadc7e2ca6b8.png)

这两个的作用分别是：获取指定Udon脚本的指定参数，设置指定Udon脚本的指定参数，获取指定Udon脚本的指定参数的参数类型

#### Set ProgramVariable

这个模块可以将接入Value

这个模块可以输入两个参数，分别是Instance和SymbolName

##### Instance（UdonBehaviour）

用于接入UdonBehaviour型的数据，表示“将数据输出到哪里”，在不介入数据时，默认对象为Self（自己）

##### SymbolName（string）

用于接入String型的数据，表示“将数据存放在哪个参数”

##### Value（gameobject）

用于接入需要发送的数据表示“需要发送的内容”

> 即使数据并没有成功写入，无论出于什么原因，脚本都不会报错——在发现跨脚本参数没有传递成功的时候，请检查是不是
> 
> SymbolName输入错误的原因

#### Get ProgramVariable

与名字相同，这个模块的作用是主动从指定的脚本获取指定参数参数的数值

这个模块可以输入两个参数，分别是Instance和SymbolName

##### Instance（UdonBehaviour）

用于接入UdonBehaviour型的数据，表示“从何处获取数据”

##### SymbolName（string）

用于接入String型的数据，表示“获取的参数的名称为什么”

##### 数据输出

该模块可以输出一个Object型的数据，在实际使用中，该输出后会继承获取的数据的类型

##### Get ProgramVariableType

如果您仅需要知道参数的类型，而并不希望获取参数的数值，可以使用这个模块来获取指定参数的数据类型，该接口会输出指定的参数的数据类型，以Type的形式输出。使用方法与Get ProgramVariable相似。

使用这三个组件，即可实现UdonGraph中与其他脚本交换参数数据。

---

### 相互触发：

Udon脚本之间相互触发，是通过Custom Event与Send CustomEvent来实现的，脚本通过调用Send CustomEvent来触发指定脚本的自定义事件。

> 注意：同时SendCustomEvent也可以触发自身所在的UdonBehviour，只需要Instance留空，目标就会默认为自己
> 
> <p class="callout danger">请不要使用SendCustomEvent直接构筑循环，因为会导致无限循环导致爆栈。请使用sendCustomEventDelayedFrames和sendCustomEventDelayedSeconds这来将循环延时避免爆栈。</p>

sendCustomEvent一共有四个组件，分别是sendCustomEvent，sendCustomEventDelayedFrames,sendCustomEventDelayedSeconds,SendcustomNetworkEvent

[![f1cdaa27ba85e03e74e38c466a99d27b.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/l4VgneavTrvzf1YR-f1cdaa27ba85e03e74e38c466a99d27b.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/l4VgneavTrvzf1YR-f1cdaa27ba85e03e74e38c466a99d27b.png)

#### SendCustomEvent

该模块用于触发自定义事件，在instance没有接入参数之前，它默认的触发对象为自身，他会在接收到触发之后，尝试触发指定脚本的名为”EventName“的事件。

> 主意：即使触发失败，该模块也不会回报错误。

本组件一共可以接入两个参数，分别是Instance 和 EventName 即目标和事件名称

##### Instance（UdonBehaviour）

用于接入UdonBehaviour型参数，接入参数时，SendCuston的目标会转移为设置的UdonBehaviour脚本，而当未接入时，模块的目标会被设置为Self

##### EventName（string）

触发的自定义事件的名称，只有当接入的名称与需要触发的事件的名称一样时，事件才能被触发。接口可以接入string参数，可以通过string类中的“custom string”来指定事件名称。如果instance没有接入，后面的选择框会自动出现所有已经至少接入了一个模块的本地自定义事件的名称。

后续的三个都包含上面的两个参数，后续不再写出。

#### sendCustomEventDelayedFrames

与sendCustomEvent相比，这个组件增加了一个名为DelayFrames的参数，数据类型为（Int）。该模块会在延迟指定帧之后执行事件。

##### DelayedFrames（int）

设定延迟多少帧后运行，注意，这里的帧指的时UDON帧而非实际帧率，也就是所谓的CPU帧。

##### EventTiming（EventTiming）

这个参数用于设定发送事件是在指定帧的开始时插入，还是结束时插入。

#### SendCustomEventDelayedSeconds

与sendCustomEventFrames相比，这个组件将DelayFrames替换为DelaySeconds的参数，数据类型为（Float）。该模块会在延迟指定秒数之后执行事件，一般用于延迟触发或者计时器来使用。

##### DelayedSeconds（Float）

设定延迟多少秒后运行

##### EventTiming（EventTiming）

这个参数用于设定发送事件是在指定帧的开始时插入，还是结束时插入。

#### SendcustomNetworkEvent

这个是一个网络同步版本的SendcustomEvent，与CustomEvent相比，这个组件会将触发所有Target所指定的人的对应自定义事件。与sendCustomEvent相比，这个组件多了一个Target的接口，用于设定发送（同步）的目标

> 当事件名称前面带有“\_”时，事件会被认为是本地事件，不会发送至其他人手中

##### Target（NetworkEventTarget）

这个参数用于设定事件会发送到哪个人的脚本，一共有两个选项—ALL和Owner

ALL：发送到当前场景所有的玩家

Owner：将数据发送至对象所有者

关于网络同步问题，请移步[https://creators.vrchat.com/worlds/udon/networking/](https://creators.vrchat.com/worlds/udon/networking/ "VRC网络同步组件")了解，这里不详细概述。

感谢您的阅读，我们下期再见。

# UdonGraph基础教程—int与Float使用速查表

## int与Float使用指南

欢迎来到本期UdonGraph基础教学，本期将会为各位介绍Int与Float的所有可以使用的Graph组件接口

您一定很好奇为什么要将这两个一起讲？实际上这两个作为纯数字的数据类型，其在接口方面也有极高的相似性，如下图：

[![dd9048f09894cce7d4979456293a15af.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/bvfBRm4bPFiLfMow-dd9048f09894cce7d4979456293a15af.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/bvfBRm4bPFiLfMow-dd9048f09894cce7d4979456293a15af.png)

根据统计，在int的27个组件，Float的30个组件中，总共有20个组件的功能与用法是相同的，这也是我为什么要将他们放置在一起来讲，本文将会分为三部分，第一部分介绍两者共有的组件，一共20个，第二部分讲述int独有的组件，一共七个，第三部分讲述Float独有的部分，一共10个

---

## 第一部分：共有部分

本篇一共20个组件分别是以下

<table border="1" id="bkmrk-const-addition-compa" style="border-collapse: collapse; width: 100.714%; height: 75.7083px;"><colgroup><col style="width: 6.55282%;"></col><col style="width: 9.76966%;"></col><col style="width: 8.81671%;"></col><col style="width: 9.41204%;"></col><col style="width: 11.3185%;"></col><col style="width: 17.7522%;"></col><col style="width: 6.43368%;"></col><col style="width: 12.2716%;"></col><col style="width: 10.8419%;"></col><col style="width: 6.91025%;"></col></colgroup><tbody><tr style="height: 46.25px;"><td style="height: 46.25px;">Const

</td><td style="height: 46.25px;">Addition</td><td style="height: 46.25px;">Compare to</td><td style="height: 46.25px;">Division</td><td style="height: 46.25px;">GreaterThen</td><td style="height: 46.25px;">GreaterThanOREqual</td><td style="height: 46.25px;">Parse</td><td style="height: 46.25px;">Multiplication</td><td style="height: 46.25px;">Subtraction</td><td style="height: 46.25px;">UnaryMinus</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">Get Type</td><td style="height: 29.4583px;">Get HashCode</td><td style="height: 29.4583px;">Equality</td><td style="height: 29.4583px;">Inequality</td><td style="height: 29.4583px;">LessThen</td><td style="height: 29.4583px;">LessThanOREqual</td><td style="height: 29.4583px;">To

string

</td><td style="height: 29.4583px;">Remainder</td><td style="height: 29.4583px;">TryParse</td><td style="height: 29.4583px;">Equals</td></tr></tbody></table>

下面将会对这20个组件进行分类并讲解其常规用法

---

### 第一节：常规运算(7组件)

常规运算指的是数据的常规运算所使用的符号，分别对应

常量：Const int/float ——设置一个常数

加：Addition ——加法运算

减：Subtraction ——减法运算

乘：Multiplication ——乘法运算

除：Division ——除法运算

> 注意当INT除法无法除尽的时候，算法会自动四舍五入为整数，如果希望获得小数，转换为Float再运行除法

除了上述的四个以外，还有两个比较特殊的运算符：

Remainder：余数运算

详细请见：[https://learn.microsoft.com/zh-cn/cpp/c-runtime-library/reference/remainder-remainderf-remainderl?view=msvc-170](https://learn.microsoft.com/zh-cn/cpp/c-runtime-library/reference/remainder-remainderf-remainderl?view=msvc-170 "余数运算符")

UnaryMinus：对输入取反值

### 第二节：对比（8组件）

本节包含了共有模块中表示对比的模块，分别是……

Compare To：进行比较，并返回其相对值的指示。（1，0，-1-大于等于小于）

Equality&amp;Equals：比较值是否等于Value所指定的值（Equality可以输入int/float值，而Equals还可以输入GameObject值）

Inequality：不等于

GreaterThan：大于

GreaterThanOREqual：大于等于

LessThan：小于

LessThanOREqual：小于等于

### 第三节：转换（3组件）

Tostring：将值转换为String值

Parse： 将指定区域性特定格式的数字的字符串表示形式转换为（int/float）值

TryParse：尝试将指定区域性特定格式的数字的字符串表示形式转换为（int/float）值，返回值表示转换是否成功

### 第四节：获取特殊数据（2组件）

Get HashCode：返回此实例的哈希代码。

Get Type：获取数据类型的TypeCode

---

## 第二部分：Int独占部分（7组件）

1：GetMaxValue

获取该值可以取得最大值（常值）

2：GetMinValue

获取该值可以取得最小值（常值）

3：LeftShift

理论：（逻辑移动）二进制下向左移位并在空缺处补上0

实际：对参数乘以N个2

4：RightShift

理论：（逻辑移动）二进制下向右移位并在空缺处补0

实际：对参数除以N个2，除不尽则舍去小数部分

5：LogicalAnd

逻辑运算符——和——

将上下两位转换成二进制后，对每一位进行按位与运算。

只有两位同时为1时，结果才为1，否则结果为0

最后转换成十进制输出

6：LogicalOr

逻辑运算符——或——

将上下两位转换成二进制后，对每一位进行按位与运算。

只要有一个为1，其值为1。

最后转换成十进制输出

7：LogicalXor

逻辑运算符——异或——

将上下两位转换成二进制后，对每一位进行按位与运算。

相应位相同为0，相异为1。

最后转换成十进制输出

---

## 第三部分：Float(10组件)

1：GET Epsilon：表示大于零的最小正 Single 值。此值是常量。

2：GET MAXValue ：可以取得的最大可能值，此值是常量。

3：GET MinValue ： 可以取得的最小可能值，此值是常量。

4：GET Nan ：不可表示数字 （）。此字段是常量

5：GET NegativeInfinity：负无穷大，此值是常量。

6：GET PositiveInfinity：正无穷大，此值是常量。

7: Is Infinity:是否是无限

8：Is NaN:是否为无效值（NaN）

9：Is NegativeInfinity：是否是负无穷大

10：Is PositiveInfinity：是否是正无穷大

速查表到此结束，感谢您的观看。

# UdonGraph 基础教程：Event事件速查表

本期来盘点UdonGraph中所预设的所有事件的触发条件以及对应名称，本文将会按照官方文档中记载的顺序进行讲解，如果需要阅读原文请点击[https://docs.vrczh.org/udonsharp.docs.vrchat.com/events#udon-update-events](https://docs.vrczh.org/udonsharp.docs.vrchat.com/events#udon-update-events)跳转至官方文档。

> 请善用Ctrl+F的网页搜索功能

---

## UdonGraphEvent事件速查


### Udon Events：Udon独有事件

本类下的事件由来自UdonSharpBehaviour，是Vrchat独有的事件类型。

#### Udon Update Events：Udon更新事件（1Event）

<table border="1" id="bkmrk-postlateupdate%28%29-%C2%A0-%C2%A0" style="border-collapse: collapse; width: 100%; height: 102.281px;"><colgroup><col style="width: 20.1408%;"></col><col style="width: 79.8195%;"></col></colgroup><tbody><tr style="height: 102.281px;"><td style="height: 102.281px;">[![833Gq13PALR3L7CU-bb9da30abef068a005cf0b0a276478f9.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/PpGjjvX7ZL8dEuLp-833gq13palr3l7cu-bb9da30abef068a005cf0b0a276478f9.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/PpGjjvX7ZL8dEuLp-833gq13palr3l7cu-bb9da30abef068a005cf0b0a276478f9.png)

PostLateUpdate()</td><td style="height: 102.281px;">在计算 IK 后，在帧的末尾附近触发。此时获取骨骼位置将为您提供最新的位置，以便它们不会落后一帧。

</td></tr></tbody></table>

#### Udon Input Events：Udon 输入事件（9Event）

此事件将会在所有平台以统一的方式读取玩家控制器的输入，即使玩家重新映射（更改绑定）控制器，这些事件也将会正常工作。

> 以下所有说到的对应案件仅指的是在玩家没有更改自己的默认设置的情况下的可能位置，实际上只要对应的条件达到，无论是什么东西触发的事件都会正常触发。

##### Event：Interact：触发事件

<table border="1" id="bkmrk-interact%EF%BC%88%EF%BC%89-%E5%BD%93-vrchat-" style="border-collapse: collapse; width: 100%;"><colgroup><col style="width: 22.5397%;"></col><col style="width: 77.5397%;"></col></colgroup><tbody><tr><td>[![ed579bf0259f7722ead50c064c9e39aa.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/4sYF0u1KP7dtuuTd-ed579bf0259f7722ead50c064c9e39aa.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/4sYF0u1KP7dtuuTd-ed579bf0259f7722ead50c064c9e39aa.png)

Interact（）

</td><td>当 VRChat 玩家与此对象交互时触发。</td></tr></tbody></table>


##### Button Events：按钮事件

按钮事件包括一个 *bool* 值，按下按钮时为 **true**，释放按钮时**为 false**

**除了按钮事件之外，组件还会带有一个UdonInputEventArgs的对象，对象定义请进入下方链接查看：**

**[https://creators.vrchat.com/worlds/udon/input-events/#udoninputeventargs](https://creators.vrchat.com/worlds/udon/input-events/#udoninputeventargs)**

<table border="1" id="bkmrk-inputjump%28%29-%C2%A0%E8%A7%A6%E5%8F%91%E6%9D%A1%E4%BB%B6%EF%BC%9A%E8%B7%B3%E8%B7%83" style="border-collapse: collapse; width: 100%; height: 510.115px;"><colgroup><col style="width: 23.4921%;"></col><col style="width: 76.5873%;"></col></colgroup><tbody><tr style="height: 133.24px;"><td style="height: 133.24px;">[![7af004c2d790c885ddc46d7130ed8939.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/rwT5JA8jMnOuBGzD-7af004c2d790c885ddc46d7130ed8939.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/rwT5JA8jMnOuBGzD-7af004c2d790c885ddc46d7130ed8939.png)

InputJump()

</td><td style="height: 133.24px;"> 触发条件：跳跃键：PC上的空格，控制器上的Face按钮

</td></tr><tr style="height: 172.875px;"><td style="height: 172.875px;">[![f78b17c328f3578a4fda9fd8b1231075.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/LkLCCaWuG8c1VVvf-f78b17c328f3578a4fda9fd8b1231075.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/LkLCCaWuG8c1VVvf-f78b17c328f3578a4fda9fd8b1231075.png)

InputUse（）

</td><td style="height: 172.875px;">触发条件：触发键：在 PC 上单击鼠标左键，控制器上的触发（前扳机）按钮

</td></tr><tr style="height: 174.542px;"><td style="height: 174.542px;">[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/D6AdiuGdg7wTmDKN-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/D6AdiuGdg7wTmDKN-image.png)

InputGrab（）

</td><td style="height: 174.542px;">触发条件：抓握：在桌面上单击鼠标左键，通常是 VR 控制器上的抓握按钮

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![6728b0644ccfea30906034ae6eb83565.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/p3768Xm63UVf3E6X-6728b0644ccfea30906034ae6eb83565.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/p3768Xm63UVf3E6X-6728b0644ccfea30906034ae6eb83565.png)

InputDrop（）

</td><td style="height: 29.4583px;">触发条件：结束抓握：在桌面上单击鼠标左键，松开 VR 控制器上的抓握按钮

</td></tr></tbody></table>


##### Axis Events：方向轴事件

事件的 **float** 值通常介于 -1 和 1 之间。当使用带有模拟摇杆的控制器时，每次值变化都会触发一个新事件，从 0 到 0.1，然后到 0.2，依此类推。桌面用户将输出整数：-1、0、1 等。

<table border="1" id="bkmrk-inputmovehorizontal%EF%BC%88" style="border-collapse: collapse; width: 100%; height: 371.709px;"><colgroup><col style="width: 30.0397%;"></col><col style="width: 70.0397%;"></col></colgroup><tbody><tr style="height: 157.396px;"><td style="height: 157.396px;">[![824b304323907a4b4ab5e540376b339c.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/BtVeND0sx8YOjyNq-824b304323907a4b4ab5e540376b339c.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/BtVeND0sx8YOjyNq-824b304323907a4b4ab5e540376b339c.png)

InputMoveHorizontal（）

</td><td style="height: 157.396px;">输入水平移动

桌面上的 A 和 D，通常是控制器上左右移动的左摇杆/垫

</td></tr><tr style="height: 155.396px;"><td style="height: 155.396px;">[![f37e350e7ae95f039d897a250872c182.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/fDkxE1RDXvpXvP3H-f37e350e7ae95f039d897a250872c182.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/fDkxE1RDXvpXvP3H-f37e350e7ae95f039d897a250872c182.png)

InputMoveVertical（）

</td><td style="height: 155.396px;">输入垂直移动（前后移动）

桌面上的 W 和 S，通常是控制器上下移动的左摇杆/垫

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/OqpNs2SKA4G0Gz9I-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/OqpNs2SKA4G0Gz9I-image.png)

InputLookVertical（）

</td><td style="height: 29.4583px;">输入垂直视角（上下看）

在桌面上上下移动鼠标，通常是在游戏手柄和 VR 控制器上上下移动右摇杆。

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/1YQmtr5bG2iSX1UA-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/1YQmtr5bG2iSX1UA-image.png)

InputLookHorizontal()

</td><td style="height: 29.4583px;">输入水平视角（左右看）

在桌面上左右移动鼠标，使用右摇杆/手柄向左和向右转动，无需舒适打开 VR 控制器，通常是在游戏手柄控制器上左右右摇杆。

</td></tr></tbody></table>

#### Udon Pickup Events：Udon拾取事件（4Event）

本类下的四个事件都需要保证物体已经挂载了VrcPickup脚本，您可以在以下链接找到关于这个脚本的信息

[https://docs.vrczh.org/creators.vrchat.com/worlds/components/vrc\_pickup](https://docs.vrczh.org/creators.vrchat.com/worlds/components/vrc_pickup)

<table border="1" id="bkmrk-ondrop%EF%BC%88%EF%BC%89-%E5%BD%93-vrchat-%E7%8E%A9%E5%AE%B6" style="border-collapse: collapse; width: 100%; height: 117.833px;"><colgroup><col style="width: 23.7302%;"></col><col style="width: 76.3492%;"></col></colgroup><tbody><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/GgcV2Cn8JzL10Dsy-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/GgcV2Cn8JzL10Dsy-image.png)

OnDrop（）

</td><td style="height: 29.4583px;">当 VRChat 玩家在抓住后放置此对象时触发。</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/JvEnD3z4Fx12e861-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/JvEnD3z4Fx12e861-image.png)

OnPickup()

</td><td style="height: 29.4583px;">当 VRChat 播放器拾取此对象时触发</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/I0pjCknl5hGlChCE-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/I0pjCknl5hGlChCE-image.png)

OnPickupUseup()

</td><td style="height: 29.4583px;">按住此对象并按下 Use 按钮时触发。按钮向上时触发。在桌面上需要 'Auto Hold'。</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/kcZfHnfvoRhvbCK4-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/kcZfHnfvoRhvbCK4-image.png)

OnpickupUseDown()

</td><td style="height: 29.4583px;">按住此对象并按下 Use 按钮时触发。按下按钮时触发。在桌面上需要 'Auto Hold'。</td></tr></tbody></table>

#### Udon Networking Events：Udon网络事件（5Event）

<table border="1" id="bkmrk-onownershiprequest%28%29" style="border-collapse: collapse; width: 100%;"><colgroup><col style="width: 27.7778%;"></col><col style="width: 72.3016%;"></col></colgroup><tbody><tr><td>[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/uqej8gCapyZrj9XH-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/uqej8gCapyZrj9XH-image.png)

OnOwnershipRequest()

</td><td>当 UdonBehaviour 将要更改所有者时触发。

将会返回UdonBehaviour是否接受所有权变更

</td></tr><tr><td>[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/fFU8wxVW7YQ2Bd8b-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/fFU8wxVW7YQ2Bd8b-image.png)

OnOwnershipTransferred()

</td><td>每次 UdonBehaviour 更改所有者时触发</td></tr><tr><td>[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/2YAY5ijdfb51F9At-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/2YAY5ijdfb51F9At-image.png)

OnpreSerialization()

</td><td>在发送网络数据之前触发</td></tr><tr><td>[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/N7CzwrmYvPLJxKQo-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/N7CzwrmYvPLJxKQo-image.png)

OnpostSerialization()

</td><td>在发送网络数据后触发，并提供有关序列化尝试是否成功以及序列化了多少字节的数据。</td></tr><tr><td>[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/sepgF5tj9OUfDHm3-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/sepgF5tj9OUfDHm3-image.png)

OnDeserialization()

</td><td>收到网络数据时触发</td></tr></tbody></table>


#### Udon Player Events：Udon玩家活动（11Event）

该类下的事件还会附带触发事件的玩家的数据，以供处理

<table border="1" id="bkmrk--2" style="border-collapse: collapse; width: 100%; height: 878.209px; border-width: 1px; border-spacing: 1px;"><colgroup><col style="width: 27.8969%;"></col><col style="width: 72.1826%;"></col></colgroup><tbody><tr style="height: 119.396px;"><td style="height: 119.396px;">[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/X3fLd4cwH4yrU96N-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/X3fLd4cwH4yrU96N-image.png)

</td><td style="height: 119.396px;">当玩家加入时触发

</td></tr><tr style="height: 117.396px;"><td style="height: 117.396px;">[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/1sIqjf6K8HatxdMJ-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/1sIqjf6K8HatxdMJ-image.png)

</td><td style="height: 117.396px;">当玩家离开时触发</td></tr><tr style="height: 120.396px;"><td style="height: 120.396px;">[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/AM1RjgiIhzwGPMic-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/AM1RjgiIhzwGPMic-image.png)

</td><td style="height: 120.396px;">当玩家重生时触发</td></tr><tr style="height: 117.396px;"><td style="height: 117.396px;">[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/ugsgBGwwt3RDBUoc-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/ugsgBGwwt3RDBUoc-image.png)

</td><td style="height: 117.396px;">当玩家进入触发器区域时触发</td></tr><tr style="height: 124.396px;"><td style="height: 124.396px;">[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/cE58u4s3AkbyRNgR-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/cE58u4s3AkbyRNgR-image.png)

</td><td style="height: 124.396px;">当玩家停留在触发器区域时触发</td></tr><tr style="height: 161.396px;"><td style="height: 161.396px;">[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/Oc3cPRJPMnoLOxti-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/Oc3cPRJPMnoLOxti-image.png)

</td><td style="height: 161.396px;">当玩家离开触发器区域时触发</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/EcLnpbR3PC9UrKgw-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/EcLnpbR3PC9UrKgw-image.png)

</td><td style="height: 29.4583px;">当玩家和碰撞体碰撞时触发</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![167fbe634a89f05f0759613f98a0b940.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/b9JXMq4gW7YrOm8j-167fbe634a89f05f0759613f98a0b940.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/b9JXMq4gW7YrOm8j-167fbe634a89f05f0759613f98a0b940.png)

</td><td style="height: 29.4583px;">当玩家停留在碰撞体上时触发</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/zlSBGqg4aA5hKNrP-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/zlSBGqg4aA5hKNrP-image.png)

</td><td style="height: 29.4583px;">当玩家离开碰撞体时触发</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/00IHQsSZoSNCcfju-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/00IHQsSZoSNCcfju-image.png)

</td><td style="height: 29.4583px;">当碰撞粒子击中玩家时触发</td></tr><tr><td>[![509c96bab750d250b683c2b418b18cac.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/UWskwwGTWVoGfjJr-509c96bab750d250b683c2b418b18cac.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/UWskwwGTWVoGfjJr-509c96bab750d250b683c2b418b18cac.png)

</td><td>当碰撞体与玩家触碰时触发</td></tr></tbody></table>

#### Udon Station Events：Udon站点事件（2Event）

具体用法请看官方文档，这里只介绍触发事件

[https://docs.vrczh.org/creators.vrchat.com/worlds/components/vrc\_station](https://docs.vrczh.org/creators.vrchat.com/worlds/components/vrc_station)

<table border="1" id="bkmrk-onstationentered-%E5%BD%93%E7%8E%A9%E5%AE%B6" style="border-collapse: collapse; width: 100%;"><colgroup><col style="width: 50%;"></col><col style="width: 50%;"></col></colgroup><tbody><tr><td>[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/1lPZmhp2ebVRdN1K-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/1lPZmhp2ebVRdN1K-image.png)

OnstationEntered

</td><td>当玩家进入时触发</td></tr><tr><td>[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/pmfsomaX3BINZ9FN-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/pmfsomaX3BINZ9FN-image.png)

OnStationExited

</td><td>当玩家离开时触发</td></tr></tbody></table>

#### Udon Video Events:Udon视频播放器事件（5Event）

UdonGraph不存在Pause和Start两个事件，请注意

<table border="1" id="bkmrk-onvideoend%28%29-%E5%BD%93%E8%A7%86%E9%A2%91%E6%92%AD%E6%94%BE%E5%99%A8%E7%BB%93" style="border-collapse: collapse; width: 100%; height: 488.042px;"><colgroup><col style="width: 24.8016%;"></col><col style="width: 75.2778%;"></col></colgroup><tbody><tr style="height: 110.396px;"><td style="height: 110.396px;">[![28af213c51fedfff2175bc519e4d17fd.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/5eAIpMs2hxohIEpA-28af213c51fedfff2175bc519e4d17fd.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/5eAIpMs2hxohIEpA-28af213c51fedfff2175bc519e4d17fd.png)

OnVideoEnd()

</td><td style="height: 110.396px;">当视频播放器结束播放时</td></tr><tr style="height: 131.396px;"><td style="height: 131.396px;">[![6fe62c4bf64c3afdd4a9cc9bc7c8e9b0.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/iKRW6t6bhBuGEkrf-6fe62c4bf64c3afdd4a9cc9bc7c8e9b0.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/iKRW6t6bhBuGEkrf-6fe62c4bf64c3afdd4a9cc9bc7c8e9b0.png)

OnvideoError

</td><td style="height: 131.396px;">当播放器发生错误时</td></tr><tr style="height: 108.396px;"><td style="height: 108.396px;">[![b5aa54abbdc8929208fd8ba16f824b7f.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/Ubfg1uywrMnEeSKZ-b5aa54abbdc8929208fd8ba16f824b7f.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/Ubfg1uywrMnEeSKZ-b5aa54abbdc8929208fd8ba16f824b7f.png)

OnVideoLoop

</td><td style="height: 108.396px;">如果启用了循环，这将在结束时触发</td></tr><tr style="height: 108.396px;"><td style="height: 108.396px;">[![24428cc0837c4732bc1c8640312937dc.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/xEwZGj9EzpWtLFgJ-24428cc0837c4732bc1c8640312937dc.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/xEwZGj9EzpWtLFgJ-24428cc0837c4732bc1c8640312937dc.png)

OnvideoStart

</td><td style="height: 108.396px;">首次播放视频时(未确认)</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![12404df475ab24ab7e1db5e3a64af997.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/bVsLVS8cNjH9C6pD-12404df475ab24ab7e1db5e3a64af997.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/bVsLVS8cNjH9C6pD-12404df475ab24ab7e1db5e3a64af997.png)

OnvideoRedy

</td><td style="height: 29.4583px;">当视频播放器加载了 url 时</td></tr></tbody></table>

#### Udon MIDI Events：UdonMIDI事件（3Event）

请移步[https://docs.vrczh.org/creators.vrchat.com/worlds/udon/midi/](https://docs.vrczh.org/creators.vrchat.com/worlds/udon/midi/)查看关于Midi的相关信息以及参数定义

<table border="1" id="bkmrk-midinoteon-%E6%8E%A5%E6%94%B6%E5%88%B0-%22note" style="border-collapse: collapse; width: 100%;"><colgroup><col style="width: 23.7302%;"></col><col style="width: 76.3492%;"></col></colgroup><tbody><tr><td>[![dd3fbfcb2e7de950c61d4a65381ac5c1.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/SfFAUDD2EbPLUTzU-dd3fbfcb2e7de950c61d4a65381ac5c1.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/SfFAUDD2EbPLUTzU-dd3fbfcb2e7de950c61d4a65381ac5c1.png)

MidiNoteON

</td><td>接收到 "Note On" 时触发。无论是 MIDI 回放还是按下您乐器上的按键都会触发该事件。</td></tr><tr><td>[![cd314bbee859fe2950d87dd72e883b4d.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/lHtfH3f7Gzv2ARIt-cd314bbee859fe2950d87dd72e883b4d.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/lHtfH3f7Gzv2ARIt-cd314bbee859fe2950d87dd72e883b4d.png)

MidiNoteOFF

</td><td>接收到 "Note On" 时触发。一般会在松开您乐器上的按键时触发的。</td></tr><tr><td>[![d3c49921fcb8f63c19e51bc6cade323c.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/x836PfQ6702E2dxr-d3c49921fcb8f63c19e51bc6cade323c.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/x836PfQ6702E2dxr-d3c49921fcb8f63c19e51bc6cade323c.png)

MidiControlChange

</td><td>接收到控制变化时触发。这些变化通常由 Midi 设备上的旋钮和滑块发出。</td></tr></tbody></table>

#### Udon String/Image Loading Events:Udon 字符串/图像加载事件（4Event）

具体使用方式请阅读：

[https://docs.vrczh.org/creators.vrchat.com/worlds/udon/string-loading](https://docs.vrczh.org/creators.vrchat.com/worlds/udon/string-loading)：字符串加载

[https://docs.vrczh.org/creators.vrchat.com/worlds/udon/image-loading](https://docs.vrczh.org/creators.vrchat.com/worlds/udon/image-loading)：图像加载

<table border="1" id="bkmrk-onimageloadsuccess-%E5%BD%93" style="border-collapse: collapse; width: 100%; height: 211.771px;"><colgroup><col style="width: 30.2778%;"></col><col style="width: 69.8016%;"></col></colgroup><tbody><tr style="height: 123.396px;"><td style="height: 123.396px;">[![4e20ceac1836a250a6eecb222fe4bc04.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/IPfPpDtuU9ifRPKn-4e20ceac1836a250a6eecb222fe4bc04.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/IPfPpDtuU9ifRPKn-4e20ceac1836a250a6eecb222fe4bc04.png)

OnImageLoadSuccess

</td><td style="height: 123.396px;">当图像下载成功时触发。</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![760644e41199e4f8df2e942fa6e26966.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/xB4elMVMEM09n9rg-760644e41199e4f8df2e942fa6e26966.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/xB4elMVMEM09n9rg-760644e41199e4f8df2e942fa6e26966.png)

OnImageLoadError

</td><td style="height: 29.4583px;">镜像下载失败时触发。</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![29c14646999ea1ee2a5b05bd9636273b.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/Ccylj21YR2nHmr1y-29c14646999ea1ee2a5b05bd9636273b.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/Ccylj21YR2nHmr1y-29c14646999ea1ee2a5b05bd9636273b.png)

OnstringLoadSuccess

</td><td style="height: 29.4583px;">字符串下载成功时触发。</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![2ca163c5c840119d79d5b9fde23df13f.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/GIB5XYzAECLLOleS-2ca163c5c840119d79d5b9fde23df13f.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/GIB5XYzAECLLOleS-2ca163c5c840119d79d5b9fde23df13f.png)

OnstringLoadError

</td><td style="height: 29.4583px;">字符串下载失败时触发。</td></tr></tbody></table>

---

### Unity Events：Unity事件

并非Udon特有的事件，而是Unity原生自带的事件，事件一共有28个事件，覆盖了大部分的使用场景

具体效果请看unity原文[https://docs.unity3d.com/ScriptReference/MonoBehaviour.html](https://docs.unity3d.com/ScriptReference/MonoBehaviour.html)

<table border="1" id="bkmrk--4" style="border-collapse: collapse; width: 103.571%; height: 1545.08px;"><colgroup><col style="width: 27.5223%;"></col><col style="width: 72.438%;"></col></colgroup><tbody><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/8pMVbikwNq4dElwX-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/8pMVbikwNq4dElwX-image.png)

</td><td style="height: 29.4583px;">FixedUpdate:固定更新（与帧速率无关的Event消息，用于物理计算）</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![3314b7a2d232446859c71ac4edbdd565.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/40gEthx6zNn0mkQm-3314b7a2d232446859c71ac4edbdd565.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/40gEthx6zNn0mkQm-3314b7a2d232446859c71ac4edbdd565.png)

</td><td style="height: 29.4583px;">LateUpdata:延迟更新（如果启用了 Behaviour，则每帧都会调用 ）</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![38a8fe4dea987c5c45b5caa2f9e5118a.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/wmWKXjPtaTzRtLv4-38a8fe4dea987c5c45b5caa2f9e5118a.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/wmWKXjPtaTzRtLv4-38a8fe4dea987c5c45b5caa2f9e5118a.png)

</td><td style="height: 29.4583px;">OnAnimatorIK（用于设置动画 IK （反向运动学） 的回调。）

Int LayerIndex:

调用 IK 解算器的层的索引

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/jgeFCnSKUm5R0Fwn-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/jgeFCnSKUm5R0Fwn-image.png)

</td><td style="height: 29.4583px;">OnAnimatorMove（用于处理动画移动的回调，用于修改根运动）</td></tr><tr style="height: 34.25px;"><td style="height: 34.25px;">（Graph中未找到）</td><td style="height: 34.25px;">OnAudioFilterRead（Unity 会将自定义滤波器插入音频 DSP 链中。）

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">（Graph中未找到）</td><td style="height: 29.4583px;">OnBecameVisible（在渲染器被任何照相机可见时调用）

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">（Graph中未找到）</td><td style="height: 29.4583px;">OnBecameInvisible（当渲染器不再被任何摄像机看到时调用）</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![3363f189e2d212dd4d2f9237343d1958.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/qCB5IecHfVCkfAvF-3363f189e2d212dd4d2f9237343d1958.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/qCB5IecHfVCkfAvF-3363f189e2d212dd4d2f9237343d1958.png)

</td><td style="height: 29.4583px;">OnCollisionEnter:碰撞进入（当此碰撞体/刚体开始接触另一个刚体/碰撞体时，将调用）

Collision other：

与此碰撞关联的 [Collision](https://docs.unity3d.com/ScriptReference/Collision.html) 数据

</td></tr><tr><td>[![e338c0b2b773f5f3d182c25940004dab.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/cG37Kqq93vFGoJdX-e338c0b2b773f5f3d182c25940004dab.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/cG37Kqq93vFGoJdX-e338c0b2b773f5f3d182c25940004dab.png)

</td><td>OnCollisionEnter2D:碰撞输入2D（当传入碰撞体与此对象的碰撞体接触时发送（仅限 2D 物理特性））

CollisionEnter2D Other:

与此碰撞关联的 Collision2D 数据

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![81e5a66387d04a190462b76e661fd8e1.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/PVITxjAmxDKZI5dV-81e5a66387d04a190462b76e661fd8e1.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/PVITxjAmxDKZI5dV-81e5a66387d04a190462b76e661fd8e1.png)

</td><td style="height: 29.4583px;">OnCollisionExit：碰撞离开（当此碰撞体/刚体停止接触另一个刚体/碰撞体时，将调用）

Collision other：

与此碰撞关联的 [Collision](https://docs.unity3d.com/ScriptReference/Collision.html) 数据

</td></tr><tr style="height: 46.25px;"><td style="height: 46.25px;">[![da5a85ffb2dd281ae944467aae1f0e49.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/1rD2PYAqebiSBNbW-da5a85ffb2dd281ae944467aae1f0e49.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/1rD2PYAqebiSBNbW-da5a85ffb2dd281ae944467aae1f0e49.png)

</td><td style="height: 46.25px;">OnCollisionExit2D：碰撞离开2D（当另一个对象上的碰撞体停止接触此对象的碰撞体时发送

（仅限 2D 物理特性）

CollisionEnter2D Other:

与此碰撞关联的 Collision2D 数据

</td></tr><tr style="height: 46.25px;"><td style="height: 46.25px;">[![13c484833b19a69d609efabda8a4c0ce.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/etsV0tEENE6Zxegm-13c484833b19a69d609efabda8a4c0ce.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/etsV0tEENE6Zxegm-13c484833b19a69d609efabda8a4c0ce.png)

</td><td style="height: 46.25px;">OnCollisionStay：持续碰撞（对于接触另一个碰撞体或刚体的每个碰撞体或刚体，每帧调用一次）

Collision other：

与此碰撞关联的 [Collision](https://docs.unity3d.com/ScriptReference/Collision.html) 数据

</td></tr><tr style="height: 46.25px;"><td style="height: 46.25px;">[![370826c38e8d2e6beb1c6145ce4f7f7e.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/BeBB8ImzDikh4KNM-370826c38e8d2e6beb1c6145ce4f7f7e.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/BeBB8ImzDikh4KNM-370826c38e8d2e6beb1c6145ce4f7f7e.png)

</td><td style="height: 46.25px;">OnCollisionStay2D：持续碰撞2D（在另一个对象上的碰撞体接触此对象的碰撞体时发送每一帧（仅限 2D 物理特性））

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![e76c031ba476fa3a742dae3be4c7f958.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/yiDACEIEbkTk0MWd-e76c031ba476fa3a742dae3be4c7f958.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/yiDACEIEbkTk0MWd-e76c031ba476fa3a742dae3be4c7f958.png)

</td><td style="height: 29.4583px;">OnControllerColliderHit：当控制器在执行 Move 时碰到碰撞体时，将调用

ControllerColliderHit Hit：

与此碰撞关联的所有数据（控制器，对象等）数据

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![3b28b482e0b5f7bce5e327b5a9f20d58.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/SwyzSCB33MH1TEzT-3b28b482e0b5f7bce5e327b5a9f20d58.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/SwyzSCB33MH1TEzT-3b28b482e0b5f7bce5e327b5a9f20d58.png)

</td><td style="height: 29.4583px;">OnDestroy：（销毁附加的行为将导致游戏或场景接收 OnDestroy。）

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">（Graph中未找到）</td><td style="height: 29.4583px;">OnDrawGizmo：如果要绘制同样可拾取且始终绘制的小工具，请实现这个

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">（Graph中未找到）</td><td style="height: 29.4583px;">OnDrawGizmosSelected：如果选择了对象，则实现 OnDrawGizmosSelected 以绘制 Gizmo。</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![db730393aff697ff30f5e1e78a9c302e.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/G3YgSWrZGSL4Czim-db730393aff697ff30f5e1e78a9c302e.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/G3YgSWrZGSL4Czim-db730393aff697ff30f5e1e78a9c302e.png)

</td><td style="height: 29.4583px;">OnDisable：当对象行为被禁用时，将调用此函数</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![bab14cbacd4b75fcce5bd7dfbe5e1525.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/OHENfdSCn7FJTmIF-bab14cbacd4b75fcce5bd7dfbe5e1525.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/OHENfdSCn7FJTmIF-bab14cbacd4b75fcce5bd7dfbe5e1525.png)

</td><td style="height: 29.4583px;">OnEnable：当对象变为启用和活动状态时，将调用此函数。

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![ed4bd7a90e78a9f57aa89c3c0e1765fa.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/nwoYIfqa3Pfv9Bdv-ed4bd7a90e78a9f57aa89c3c0e1765fa.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/nwoYIfqa3Pfv9Bdv-ed4bd7a90e78a9f57aa89c3c0e1765fa.png)

</td><td style="height: 29.4583px;">OnJointBreak：当连接到同一游戏对象的关节损坏时调用。

float breakForce：

应用于关节的断开力

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![285d53c47da362044ccdf6d4d7c0f144.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/2Pm6hZqpDlmHETYr-285d53c47da362044ccdf6d4d7c0f144.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/2Pm6hZqpDlmHETYr-285d53c47da362044ccdf6d4d7c0f144.png)

</td><td style="height: 29.4583px;">OnJointBreak2D：当附加到同一游戏对象的 Joint2D 中断时调用

Joint2D breakForce：

应用于关节的断开力

</td></tr><tr style="height: 32.4375px;"><td style="height: 32.4375px;">[![6e8cd20aed8d2fdf32fc9a9533072d49.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/TP9Uk4pU4RVscNTX-6e8cd20aed8d2fdf32fc9a9533072d49.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/TP9Uk4pU4RVscNTX-6e8cd20aed8d2fdf32fc9a9533072d49.png)

</td><td style="height: 32.4375px;">OnMouseDown：当用户在 Collider 上按下鼠标左键时调用。</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![fece9a6b2e2496c37d2aa2138ad1c603.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/LqCHjha8eBiupI6U-fece9a6b2e2496c37d2aa2138ad1c603.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/LqCHjha8eBiupI6U-fece9a6b2e2496c37d2aa2138ad1c603.png)

</td><td style="height: 29.4583px;">OnMouseDrag：在用户单击碰撞体并持续按住鼠标时调用。

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![ccedcb69d004455a3fc66df6b5e43563.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/nPELUqhBrKsr7SmA-ccedcb69d004455a3fc66df6b5e43563.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/nPELUqhBrKsr7SmA-ccedcb69d004455a3fc66df6b5e43563.png)

</td><td style="height: 29.4583px;">OnMouseEnter：当鼠标进入 Collider 时调用。</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![9452d4c4d03f666f8411418359d9c1f8.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/1CEa9q6xpbxV4Pus-9452d4c4d03f666f8411418359d9c1f8.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/1CEa9q6xpbxV4Pus-9452d4c4d03f666f8411418359d9c1f8.png)

</td><td style="height: 29.4583px;">OnMouseExit:当鼠标不再位于 Collider 上时调用

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![ea564d6eee9a0b94b5a6093d9ac80fa4.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/9yoKHPRLWQ9E2CE8-ea564d6eee9a0b94b5a6093d9ac80fa4.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/9yoKHPRLWQ9E2CE8-ea564d6eee9a0b94b5a6093d9ac80fa4.png)

</td><td style="height: 29.4583px;">OnMouseOver:当鼠标悬停在 Collider （碰撞体） 上时，每帧调用一次。</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![12fc59bb4fc9013c09d6f34e1d6d6dfc.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/kMFxhkf0HWeG4d1f-12fc59bb4fc9013c09d6f34e1d6d6dfc.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/kMFxhkf0HWeG4d1f-12fc59bb4fc9013c09d6f34e1d6d6dfc.png)

</td><td style="height: 29.4583px;">OnMouseUp:在用户释放鼠标按钮时调用。</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![0b9fe8ecd115eef4627e04721664ce16.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/xxOurYqU4RdY9AHJ-0b9fe8ecd115eef4627e04721664ce16.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/xxOurYqU4RdY9AHJ-0b9fe8ecd115eef4627e04721664ce16.png)

</td><td style="height: 29.4583px;">OnMouseUpAsButton:仅当鼠标在按下鼠标的同一碰撞体上释放时，才会调用

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![c9e6944689b1834eab9ee0a3b1728643.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/bmaCsK411zj6z3XW-c9e6944689b1834eab9ee0a3b1728643.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/bmaCsK411zj6z3XW-c9e6944689b1834eab9ee0a3b1728643.png)

</td><td style="height: 29.4583px;">OnParticleCollision:在粒子撞击碰撞体时调用

GameObject other:

从附加到具有[碰撞体](https://docs.unity3d.com/ScriptReference/Collider.html)[的游戏对象的](https://docs.unity3d.com/ScriptReference/GameObject.html)脚本调用 [OnParticleCollision](https://docs.unity3d.com/ScriptReference/MonoBehaviour.OnParticleCollision.html) 时，[GameObject](https://docs.unity3d.com/ScriptReference/GameObject.html) 参数表示 [ParticleSystem](https://docs.unity3d.com/ScriptReference/ParticleSystem.html)。[碰撞体 （Collider](https://docs.unity3d.com/ScriptReference/Collider.html)） 在每个粒子系统中最多接收一条消息，该粒子系统在任何给定帧中与其发生碰撞，即使碰撞体 （Collider） 当 Particle System 在当前帧中用多个粒子撞击 [Collider](https://docs.unity3d.com/ScriptReference/Collider.html) 时。 要检索有关 [ParticleSystem](https://docs.unity3d.com/ScriptReference/ParticleSystem.html) 引起的所有碰撞的详细信息，必须使用 [ParticlePhysicsExtensions.GetCollisionEvents](https://docs.unity3d.com/ScriptReference/ParticlePhysicsExtensions.GetCollisionEvents.html) 来检索 ParticleSystem.CollisionEvent 的数组。

</td></tr><tr style="height: 30.4375px;"><td style="height: 30.4375px;">[![591e5b4d032a421ac9cfe013e6abe1ea.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/NssssuWxUBKgGIvA-591e5b4d032a421ac9cfe013e6abe1ea.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/NssssuWxUBKgGIvA-591e5b4d032a421ac9cfe013e6abe1ea.png)

</td><td style="height: 30.4375px;">OnParticleTrigger:当粒子系统中的任何粒子满足触发器模块中的条件时，将调用

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![44ea4df98d38f1a6e32d100791afcf39.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/1UmslYQAORW5mySk-44ea4df98d38f1a6e32d100791afcf39.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/1UmslYQAORW5mySk-44ea4df98d38f1a6e32d100791afcf39.png)

</td><td style="height: 29.4583px;">OnPostRender:Unity 在 Camera 渲染场景后调用的事件函数。

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![67e145694dfca3bb23e7e6241df74190.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/7KLstwIooSaClmEq-67e145694dfca3bb23e7e6241df74190.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/7KLstwIooSaClmEq-67e145694dfca3bb23e7e6241df74190.png)

</td><td style="height: 29.4583px;">OnPreCull：用于在渲染循环中的此时执行您自己的代码

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![f5b2ebbf39a03f83d90fd54956d9a071.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/5zMnQg1Q4nE2gJWu-f5b2ebbf39a03f83d90fd54956d9a071.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/5zMnQg1Q4nE2gJWu-f5b2ebbf39a03f83d90fd54956d9a071.png)

</td><td style="height: 29.4583px;">OnPreRender:用于在渲染循环中的此时执行您自己的代码;例如

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![e2a7c5210da9193565eac8279f3c2048.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/6SjXWzQTFzcZGmbf-e2a7c5210da9193565eac8279f3c2048.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/6SjXWzQTFzcZGmbf-e2a7c5210da9193565eac8279f3c2048.png)

</td><td style="height: 29.4583px;">OnRenderImage:该函数在 Unity 完成渲染后调用，用于修改摄像机的最终图像。

RenderTextur sre:

包含源图像的 [RenderTexture](https://docs.unity3d.com/ScriptReference/RenderTexture.html)。

RenderTextur dest:

要使用修改后的图像更新的 [RenderTexture](https://docs.unity3d.com/ScriptReference/RenderTexture.html)

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![6f7788dd94068e9931eee1da9f587084.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/gvHXZ9nZrJs2A2g2-6f7788dd94068e9931eee1da9f587084.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/gvHXZ9nZrJs2A2g2-6f7788dd94068e9931eee1da9f587084.png)

</td><td style="height: 29.4583px;">OnRenderObject：在摄像机渲染场景后调用。

</td></tr><tr style="height: 34.25px;"><td style="height: 34.25px;">[![af782e9f1b92608e3d1924ea372e9d0c.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/onsVDDCvZravRSR5-af782e9f1b92608e3d1924ea372e9d0c.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/onsVDDCvZravRSR5-af782e9f1b92608e3d1924ea372e9d0c.png)

</td><td style="height: 34.25px;">OnTransformChildrenChanged：当 GameObject 的转换的子项列表发生更改时，将调用此函数

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![f4d4546b9b44437ed0ba8b3c581cc1ee.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/RmlwLMeoKU8J7DNP-f4d4546b9b44437ed0ba8b3c581cc1ee.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/RmlwLMeoKU8J7DNP-f4d4546b9b44437ed0ba8b3c581cc1ee.png)

</td><td style="height: 29.4583px;">OnTransformParentChanged：当游戏对象转换的直接或间接父级发生更改时，将调用此函数。

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![d27d8d7d71a964a54cac0c9cb6081e7b.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/v0uicIHXsj9s9nh4-d27d8d7d71a964a54cac0c9cb6081e7b.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/v0uicIHXsj9s9nh4-d27d8d7d71a964a54cac0c9cb6081e7b.png)

</td><td style="height: 29.4583px;">OnTriggerEnter：当一个游戏对象与另一个游戏对象发生碰撞时调用

Collision other：

与此碰撞关联的 [Collision](https://docs.unity3d.com/ScriptReference/Collision.html) 数据

</td></tr><tr style="height: 46.25px;"><td style="height: 46.25px;">[![a873f69d260a3e0a1bf6c564866df684.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/4r6OPkE9B9SIJ5Ue-a873f69d260a3e0a1bf6c564866df684.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/4r6OPkE9B9SIJ5Ue-a873f69d260a3e0a1bf6c564866df684.png)

</td><td style="height: 46.25px;">OnTriggerEnter2D：当另一个对象进入附加到此对象的触发器碰撞器时发送

（仅限 2D 物理特性）

Collision2D Other:

与此碰撞关联的 Collision2D 数据

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![bfb560d9d06e2f0b0ac1ec8627f00273.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/wSbgTCaYz4ZJXzl4-bfb560d9d06e2f0b0ac1ec8627f00273.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/wSbgTCaYz4ZJXzl4-bfb560d9d06e2f0b0ac1ec8627f00273.png)

</td><td style="height: 29.4583px;">OnTriggerExit：当 Collider other 停止接触触发器时调用。

Collision2D Other:

与此碰撞关联的 Collision2D 数据

</td></tr><tr style="height: 46.25px;"><td style="height: 46.25px;">[![f14fe42f2d537742c82d1bc6662c8057.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/xu7CFOyAQcL0A7Ee-f14fe42f2d537742c82d1bc6662c8057.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/xu7CFOyAQcL0A7Ee-f14fe42f2d537742c82d1bc6662c8057.png)

</td><td style="height: 46.25px;">OnTriggerExit2D：当另一个对象离开附加到此对象的触发器碰撞器时发送

（仅限 2D 物理特性）。

Collision2D Other:

与此碰撞关联的 Collision2D 数据

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![bd3542b714c48f3677a65fd2d2287952.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/PNAp2BRUqyT9csWW-bd3542b714c48f3677a65fd2d2287952.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/PNAp2BRUqyT9csWW-bd3542b714c48f3677a65fd2d2287952.png)

</td><td style="height: 29.4583px;">OnTriggerStay：对于每个物理更新，对于每个接触触发器的碰撞体调用一次。

Collision2D Other:

与此碰撞关联的 Collision2D 数据

</td></tr><tr style="height: 46.25px;"><td style="height: 46.25px;">[![d3fea7c4c8dcfd15fa31c9dd963d4031.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/31f2PQ8rddk7LphX-d3fea7c4c8dcfd15fa31c9dd963d4031.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/31f2PQ8rddk7LphX-d3fea7c4c8dcfd15fa31c9dd963d4031.png)

</td><td style="height: 46.25px;">OnTriggerStay2D：当另一个对象位于附加到此对象的触发碰撞器内时，每次物理更新发送一次

（仅限 2D 物理）。

Collision2D Other:

与此碰撞关联的 Collision2D 数据

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">（Graph中未找到）</td><td style="height: 29.4583px;">OnValidate:Unity 在加载脚本或 Inspector 中的值更改时调用的仅限编辑器的函数。

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![9db4838e624d90e89dffc119da84ae89.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/WyMlsvLxxk6Vwz6Q-9db4838e624d90e89dffc119da84ae89.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/WyMlsvLxxk6Vwz6Q-9db4838e624d90e89dffc119da84ae89.png)

</td><td style="height: 29.4583px;">OnWillRenderObject:如果对象可见且不是 UI 元素，则为每个摄像机调用函数

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">（Graph中未找到）</td><td style="height: 29.4583px;">Reset：重置为默认值。

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![d4bfa3e0162db7334f85da22a3ff98a8.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/sAIrIeQcpWxRoDOt-d4bfa3e0162db7334f85da22a3ff98a8.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/sAIrIeQcpWxRoDOt-d4bfa3e0162db7334f85da22a3ff98a8.png)

</td><td style="height: 29.4583px;">Start：Start 在首次调用任何 Update 方法之前启用脚本时，将在帧上调用

</td></tr><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![5c249725e252fc6e75cbf7cb575b8f46.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/1fwHsICoBeJANkXG-5c249725e252fc6e75cbf7cb575b8f46.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/1fwHsICoBeJANkXG-5c249725e252fc6e75cbf7cb575b8f46.png)

</td><td style="height: 29.4583px;">Update：如果启用了 MonoBehaviour，则每帧都会调用 Update。

</td></tr></tbody></table>

# Udon Graph 基础教程-VRCJson使用指南

本期是VRCJson在UdonGraph的使用方法，我们可以通过DownloadString来下载JSON文件，再通过下文的方法来转化成DataToken，并使用DataToken来输出参数，本文将会详细解读如何使用Graph来完成这个过程。

---

### UdonGraph：VRC JSON

[https://creators.vrchat.com/worlds/udon/data-containers/vrcjson/](https://creators.vrchat.com/worlds/udon/data-containers/vrcjson/)

### 如何序列化/反序列化一个JSON文件

首先我们需要明白，VRC的Json文件以及后续的DataToken，Datalist，Dictionaries都不可以预设，本篇我们只讨论从JSON导入到VRC中，不详细讨论Token，List，Dictionary方面的详细问题。

另外我强烈建议写Json时无论你的数据时什么格式，都一律以String格式来储存，等到获取到参数之后再转换至对应格式——VRCJson写的十分构式，这样可以降低这部分出现bug的概率。

##### 反序列化：

如果我们已经拥有了一个JSON，我们可以使用VRCJson.TryDeserializeFromJson来反序列化出Data containers文件

<table border="1" id="bkmrk-vrcjson.trydeseriali" style="border-collapse: collapse; width: 100%; height: 29.4583px;"><colgroup><col style="width: 27.0453%;"></col><col style="width: 10.7325%;"></col><col style="width: 37.2818%;"></col><col style="width: 25.0199%;"></col></colgroup><tbody><tr style="height: 29.4583px;"><td style="height: 29.4583px;">[![364ad0d3ac9ec562c5bbae23f651cd9d.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/phWB9oXmOIdbBd59-364ad0d3ac9ec562c5bbae23f651cd9d.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/phWB9oXmOIdbBd59-364ad0d3ac9ec562c5bbae23f651cd9d.png)

VRCJson.TryDeserializeFromJson

</td><td style="height: 29.4583px;">string输入</td><td style="height: 29.4583px;">bool，DataToken</td><td style="height: 29.4583px;">从 JSON 字符串输入创建 DataList 或 DataDictionary。如果成功，则返回 true，结果令牌将为 DataDictionary 或 DataList。如果不成功，这将返回 false 并放置一个错误，解释结果令牌中的问题是什么。</td></tr></tbody></table>

生成的DataToken并不可以直接用于获取参数，我们需要先将其转化为字典或者列表才可以进行下一步操作，你可以不必在此提前做好转化，只需要在您准备接入方法是调用Get XX即可，示例如下：

> VRCJson会根据输入的Json来生成字典和列表中的一个。

[![0cb33795e2927bb61bfc7702a9c06360.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/ETBtLZtLSiDMALHA-0cb33795e2927bb61bfc7702a9c06360.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/ETBtLZtLSiDMALHA-0cb33795e2927bb61bfc7702a9c06360.png)


##### 序列化：

如果我们拥有一个Data containers，我们需要将其以JSON的方式输出出去，我们可以使用VRCJson.TrySerializeToJson来序列化JSON

<table border="1" id="bkmrk-vrcjson.tryserialize" style="border-collapse: collapse; width: 100%;"><colgroup><col style="width: 25%;"></col><col style="width: 25%;"></col><col style="width: 25%;"></col><col style="width: 25%;"></col></colgroup><tbody><tr><td>[![5797524ed71b6feb06125cdfd7689adb.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/fU4Q3R37lnduX8wu-5797524ed71b6feb06125cdfd7689adb.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/fU4Q3R37lnduX8wu-5797524ed71b6feb06125cdfd7689adb.png)

VRCJson.TrySerializeToJson

</td><td>DataToken 输入，JsonExportType</td><td> bool，DataToken 结果</td><td>尝试将 DataDictionary 或 DataList 转换为 JSON 字符串输出。如果成功，则返回 true，结果令牌将是包含最终 Json 的字符串。如果不成功，这将返回 false 并放置一个错误，解释结果令牌中的问题是什么。</td></tr></tbody></table>

序列化为 Json 时，您可以选择所需的 JsonExportType。如果您想要人类可读的内容，Beautify 会更好。如果您想要通过网络发送的紧凑内容，Minify 会更好。

- 美化：将每个元素展开为新行，并为每个深度添加一个选项卡。
- 缩小：将所有内容保持在一行中，并最大限度地减少空格。

---

### 如何获取DataToken中的数据

在从 DataToken 中获取值之前，你需要确定它包含什么类型，因为如果你尝试拉取一个不兼容的类型，它会让UdonBehaviour崩溃。有几种方法可以确保包含的类型与要提取的类型兼容。

- 您可以检查属性以获取确切的类型`DataToken.TokenType`
- 从 Data List 或 Data Dictionary 中检索值时，您可以使用并指定 TokenType。如果 TokenType 不正确，则该函数将返回 false。`TryGetValue`
- 您可以检查属性以获取它是否为数字。如果是这样，那么你可以安全地拉取 property ，这将为你提供一个 double upcasted 无论它实际上是哪种类型。如果类型为 或 ，则可能会丢失精度。`DataToken.IsNumber``Number``long``ulong`
- 无论令牌的类型如何，它始终是一个有效的选项，并且永远不会引发错误。`ToString`

[![2f0f08ad069091eb2730fcef1453600b.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/sqFDQMfaSwgXO5Gc-2f0f08ad069091eb2730fcef1453600b.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/sqFDQMfaSwgXO5Gc-2f0f08ad069091eb2730fcef1453600b.png)

你可以直接使用Get来获取数据

---

### 当Json嵌套时该如何解嵌套

我们这里拿台球俱乐部的玩家分数当作示例Json，他的结构如下：

```json
{
    "scores": {
        "A1": "1882.1652568364993",
        "A2": "1863.5989323493907",
        "A3": "1686.7993635948164",
        "A4": "1392.851076060002",
        "A5": "1378.2606184626945",
        "A6": "1368.4536773345992"
    }
}
```

我们可以看出来，这个是一个二层嵌套结构的字典类型的JSON，其中第一层为KEY为：scores，第二层为数据

[![27e1b9645dc0a9b594fa262c4f32a1df.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/EAKF571gQA8F0T7Y-27e1b9645dc0a9b594fa262c4f32a1df.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/EAKF571gQA8F0T7Y-27e1b9645dc0a9b594fa262c4f32a1df.png)

我们将字符串装入参数：A中，先进行反序列化：

反序列化的结果会通过DataToken Result输出，如果您不确定输出的类型是什么，请使用GetType来确定它究竟被转换成了什么类型。

[![9d59ed3bec3019961d54d6dda650a612.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/TLQ8RfQKqzNVeXxI-9d59ed3bec3019961d54d6dda650a612.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/TLQ8RfQKqzNVeXxI-9d59ed3bec3019961d54d6dda650a612.png)

第二部我们用Get DataDictionary将数据类型从DataToken转化为DataDictionary，然后接入到TryGetValue，将我们的Key：scores输入进去。然后就完成了解开第一层嵌套的过程，现在输出的datatoken就是第二层的数据层。

> 我强烈建议您为每一个TryGetValue的输出的布尔接口都接上Debug.Log，根据奇怪的实践经验，不接的话有可能会产生不明Bug

[![fc407208c83da30b3867f31d7b398127.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/J9NyzGm5DL4vK17I-fc407208c83da30b3867f31d7b398127.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/J9NyzGm5DL4vK17I-fc407208c83da30b3867f31d7b398127.png)

然后我们在建立一个一模一样的结构，并且在Key处输入我们需要提取的数据的KEY，就可以从Json中拿取数据了

[![7365d23ad34ad04a96804a4e3166272c.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/5OAHGzAyoKoKIA6a-7365d23ad34ad04a96804a4e3166272c.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/5OAHGzAyoKoKIA6a-7365d23ad34ad04a96804a4e3166272c.png)

---

### 如何从Datalist中获取到参数

#### 直接从按照索引获取对应层级再读取数据

反序列化

[![4d0d99690b3168d80e7567b5dd45499d.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/WFkePifinCl50KUj-4d0d99690b3168d80e7567b5dd45499d.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/WFkePifinCl50KUj-4d0d99690b3168d80e7567b5dd45499d.png)

读取指定层级

[![546525324e1c7f1c0ab3b16051e462e9.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/zexixR9XLjkIAHE7-546525324e1c7f1c0ab3b16051e462e9.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/zexixR9XLjkIAHE7-546525324e1c7f1c0ab3b16051e462e9.png)

读取字典参数

[![f0eedea87f4f16c754c17049e16e25fa.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/z6ohQjNchYB9XqKn-f0eedea87f4f16c754c17049e16e25fa.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/z6ohQjNchYB9XqKn-f0eedea87f4f16c754c17049e16e25fa.png)

参数读取完毕

感谢您的阅读。

# Udon Graph 基础教程-简单的网络同步

本文所讲的网络同步部分只是基础中的基础！如果要深入研究请观看官方文档[https://creators.vrchat.com/worlds/udon/networking/](https://creators.vrchat.com/worlds/udon/networking/)

本期依然会以官方文档为基础进行讲解，并且在容易产生疑问的地方安插讲解——如果你还是不懂，请私信，这就是绝对是写的人的问题而不是你的问题！

---

## VRC Graph：简单的网络同步

大家好，欢迎来到本期UDON Graph网络同步篇章，本篇将会以简单的方式来讲述一些关于网络同步的故事……

### 概述：网络原理

Udon 中用于联网的三个主要概念是 **变量** 、 **事件** 和 **所有权** 。

**变量**是值的容器 - 如数字、一组颜色或 3D 位置。

**事件**是在某个时刻发生的事情，也就是Event

**所有权**是决定基于哪个用户的本地数据为准，在同步时会将拥有所有权的玩家的数据同步给其他人

#### 所有权（Owner）

默认情况下，世界中的对象是<span style="background-color: rgb(241, 196, 15); color: rgb(0, 0, 0);">**本地**</span>的。这意味着您捡起的物体**只会为您移动**，其他人不会看到它在移动。要同步对象，您需要告诉 VRChat 您希望它成为”网络同步对象（Networked对象）“

要使对象**联网**，您可以向其添加 UdonBehaviour 和/或 VRC 对象同步组件（Vrc Object Sync）

第一个打开世界的玩家将拥有所有权——是的，一般来说房主=所有者（Owner），如果这个地图没有发生所有者转让（后面我们会提到），一般来说这个所有者会一直由房主继承，直到房主退出房间，所有者会随着房主的转移而转移。

拥有所有权的玩家会成为所有Networked Objects以及UdonBehaviour脚本的所有者——当这些中的某些发生更改的时候，VRC将会以它为基准，更新数据。而其他人只需要接受来自所有者的数据即可。

当我们通过（set Owner）来更改所有者的时候，所有权将会转交给新的人——房主不会转交，只有所有权会转交。而当新的数据需要同步时，VRC将会以新设置的所有者为基准来同步数据。当新的所有者退出房间时，他会优先转移给房主。

##### 示例：最简单的网络同步

大家一定在很多教程中都见过一个组合——Pickup+VRC Object Sync，就像是这样

[![922418cdf7e65eaf41f330298acca8a1.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/bUqmkgR1OYmLk63j-922418cdf7e65eaf41f330298acca8a1.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/bUqmkgR1OYmLk63j-922418cdf7e65eaf41f330298acca8a1.png)

这个组合，就是VRC中最简单的网络同步

其中的VRCObjectSync的作用就是标记这个物体为”网络同步对象“，并且自动同步对象 - 将其位置、旋转、缩放和一些物理属性发送给其他玩家

注意：VRCObjectSYNC脚本有个特殊之处——他会自动将所有权转移到拿着这个物体的人手中，并且将以拿着它的人为基准为其他人发送数据。后面将会为您讲述这——为什么特殊

##### Master（房主/实例主）

Master是拥有从未手动设置或转移其所有权的任何对象的玩家。我们可以通过VRCplayerAPI中的”Is master“来确定玩家是否是房主。

[![8b45482d96fbbf7876307c647d2fce5d.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/Bzf2BRJI6BKv5zId-8b45482d96fbbf7876307c647d2fce5d.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/Bzf2BRJI6BKv5zId-8b45482d96fbbf7876307c647d2fce5d.png)

> 玩家是否是 instance master 不应用作对世界某些特征的访问的门控。为此，请考虑改用 “instance owner”（VRC原文）

[![1919c77e70845af5e3f1f68cdd4bbab4.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/xR2XkPh1aDUkoKsO-1919c77e70845af5e3f1f68cdd4bbab4.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/xR2XkPh1aDUkoKsO-1919c77e70845af5e3f1f68cdd4bbab4.png)

Master玩家选择遵循以下规则：

- 实例中将始终存在有效的Master
- 第一个进入先前为空实例（没有玩家）的玩家将成为初始主控。
- 当Master Player离开实例时，Master会发生变化。 
    - 如果Master Player在 Android 上并将 VRChat 置于后台太长时间，他们也可能会发生变化。
- 当当前 master 离开时，将从 instance 中的其他 player 中选择一个新的 master，然后再被调用。`OnPlayerLeft`
- 你不能依赖任何特定的玩家成为Master。将根据服务器端的各种标准（平台、网络条件等）选择新的主玩家。

这些是 VRChat 目前对网络主控行为所做的*唯一*保证。观察到的任何其他行为都可能发生变化。

> <p class="callout warning">在构筑网络同步的时候，请不要依赖于Master机制</p>
> 
> 请使用Get Owner来获取某个物体/脚本是由谁来更新的，而不是一味的默认这个物体会由Master来进行更新
> 
> 在某些情况下，Master Player可能会在一段时间内无响应，并且在此期间的事件可能不会执行。
> 
> 为了解决这个问题，我们提议在任何需要同步的重要参数时，使用SET Owner先转移所有者至确实正在正常运行的Player——他们一般也会是网络同步事件的申请者。

#### 变量

变量是值的容器。UdonBehaviours 运行 Udon 程序，你可以向这些程序添加变量。

[![a297a81c48dd5443f8f2e7378115b63d.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/i2avXLXvkVt1wZCQ-a297a81c48dd5443f8f2e7378115b63d.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/i2avXLXvkVt1wZCQ-a297a81c48dd5443f8f2e7378115b63d.png)

在UdonGraph中，任何可以进行同步的参数都会有Synced的选项，当您选择它为需要同步的参数时，他前面的勾会亮起且后面会出现名为”SMOOTH的选项“

> Smooth选项为参数平滑——选择None以外的任何选项都会导致您的参数的值不会以绝对正确的形式同步到其他人身上。

#### 示例：同步滑块

本文将把这个部分改造为使用UDONGraph来进行同步，如果需要阅读原版，请访问

[https://creators.vrchat.com/worlds/udon/networking/](https://creators.vrchat.com/worlds/udon/networking/)

[![fae4662319c6204ea5611fe2db1e0df7.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/jZMrVXe9QCkyHys4-fae4662319c6204ea5611fe2db1e0df7.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/jZMrVXe9QCkyHys4-fae4662319c6204ea5611fe2db1e0df7.png)

在此示例中，Slider 的 Owner 将其值同步到其他所有人。

要同步 Slider，我们只需要获取它的值。这将是一个小数点在 0 到 1 之间的数字，我们称之为浮点值，或简称浮点数。因此，我们创建一个名为 *sliderValue* 的变量，其类型为 **float**。

[![070289df8138f3bb5faef97215d1332a.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/VBh7pvQWigdy8W6w-070289df8138f3bb5faef97215d1332a.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/VBh7pvQWigdy8W6w-070289df8138f3bb5faef97215d1332a.png)

将滑块设置为在移动滑块时更新此值，当滑块的值更新时，会向UdonGraph脚本发送一个名为：”Valueupdate“的CustonEvent事件。UdonGraph会从Slider中获取值并且存入到已经标记为SYNC的值SliderValue中。

[![8630497e1c0d3eb6a6346bc0833215c4.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/nHrBNlCIxH74qLY6-8630497e1c0d3eb6a6346bc0833215c4.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/nHrBNlCIxH74qLY6-8630497e1c0d3eb6a6346bc0833215c4.png)

[![b2a09a7f1d66188af98ab399fb0f97de.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/sdJpfZ4fFotGTxPa-b2a09a7f1d66188af98ab399fb0f97de.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/sdJpfZ4fFotGTxPa-b2a09a7f1d66188af98ab399fb0f97de.png)

如果此时您的脚本设置为：Continuous（自动同步），那么太好了——您的参数会在更改时自动同步给所有人，接下来您只需要让其他玩家在收到同步时，更新以下slider值就行，恰巧，VRCEvent中的**OnDeserialization**可以完美完成我们的要求。

[![44226743ded00cdf9a9555be4eccd9a3.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/ttS6h5JhmJdYdP9b-44226743ded00cdf9a9555be4eccd9a3.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/ttS6h5JhmJdYdP9b-44226743ded00cdf9a9555be4eccd9a3.png)

只要在参数更新时，将更新的参数同步到Slider上，我们就完成了一次同步。恭喜你，完成了第一个网络同步脚本。

> 从Owner上传同步，这称为 Serialization。玩家接受并同步参数，这称为 Deserialization（来自官方）

#### 事件

事件发生，然后执行，之后它们就消失了。与只能由对象的 Owner 更新的变量不同，任何人都可以对 Object 调用事件——在本地。

但是事件的同步不需要通过Owner来进行——只需要使用SendCustomNetworkEvent来发送事件，任何的人都可以从发送同步事件

> <p class="callout info">注意这里的区别</p>
> 
> 参数同步：基于Owner，并且只会同步Owner的参数
> 
> 事件同步：由发起者发出，发起者即为调用SendCustomNetworkEvent的人。他并不要求发起人时Owner或者master

在发起事件同步的时候，我们可以选择这个事件时发出给所有人，还是只发出给Owner，这个可以在 target选项中选择

[![de91d804a0e880fc60d7d177bc6639b8.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/yEHTjVoIh0tdI2IK-de91d804a0e880fc60d7d177bc6639b8.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/yEHTjVoIh0tdI2IK-de91d804a0e880fc60d7d177bc6639b8.png)

#### 示例：泡泡枪

（以下为官方示例——官方终于记得他还有个UdonGraph了）

在此示例中，我们有一个具有粒子系统的对象和一个动画师，该动画师旋转其气泡棒并生成气泡粒子。我们希望当用户握住魔杖按下扳机时，世界上的每个人都能做到这一点。

在我们的 Udon Graph 中，我们有一个称为 “Trigger” 的自定义事件，它播放 'Spin' 动画并触发 22 个粒子来发射 - 这只是我们图表中的一个局部事件。

为了实现这一点，我们绑定了 **OnPickupUseDown** 事件，该事件在有人拿着泡泡枪按下 Use 时触发，我们使用 **SendCustomNetworkEvent**，目标为 *All* 来触发所有人的“Trigger”事件，包括对象的所有者。

[![udon-networking-e21b3b0-bubble-gun-graph-1e56fe00c5fc0c771b773b149cbef4b9.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/zYtQ3NSEX5U7Kwtg-udon-networking-e21b3b0-bubble-gun-graph-1e56fe00c5fc0c771b773b149cbef4b9.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/zYtQ3NSEX5U7Kwtg-udon-networking-e21b3b0-bubble-gun-graph-1e56fe00c5fc0c771b773b149cbef4b9.png)

> 网络同步概念：迟到者
> 
> 在发生一些同步后，加入你的世界的人会发生什么情况？很简单：变量将被更新，事件不会。当有人加入您的世界时，OnDeserialization 事件将针对世界上具有最新数据的每个 Networked Object 触发，并且他们将运行您现有的任何逻辑来根据该数据更新内容。但是——事件不会：因为事件的概念的即时的，在事件完成之后，就消失了——就像是没有理由在有人按下扳机一小时后发射气泡粒子一样

#### 概述 回顾

同步是通过 Variables 和 Events 完成的。对于变量，Owner会更新变量，并将该数据发送给所有其他 Deserialize 该变量的玩家。任何进入世界的人都会获得最新的数据来 Deserialize。对于事件，任何人都可以发送 NetworkEvent。届时，所有者或世界上的每个人都会收到它。

#### 官方网络同步示例包

[官方网络同步示例包.package](https://assets.vrchat.com/sdk/UdonNetworkingConcepts.unitypackage)

官方上面的三个示例包含在一个简单的包中，您可以导入到任何具有 Udon SDK 的项目中，以便查看它们的工作情况并自己探索

---

### 同步方式

VRC有四种方法可以同步 World 中的数据和事件

##### 1. 连续变量（Continuous）

当你有一个想要频繁更新的变量时，请使用此选项，如果它有时不更新以节省其他事情的带宽，那也没关系。这将为较晚加入的人同步。

[![6d3cb6efff2df9c1a90974793002124a.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/U1zQy6TKRacPvjBi-6d3cb6efff2df9c1a90974793002124a.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/U1zQy6TKRacPvjBi-6d3cb6efff2df9c1a90974793002124a.png)

##### 2. 手动变量(Manual)

当你有一个更新频率较低的变量时，请使用此选项，并且其值始终是最新的，这一点非常重要。这将为较晚加入的人同步。此选项与 Object Sync 不兼容。

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/7ZG52TO3jruyeZv2-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/7ZG52TO3jruyeZv2-image.png)

需要配合 RequestSerialization（同步申请）使用

[![2fbcdcb6a5d1b3e2be8aed53ca9c6696.png](https://docs.vrcd.org.cn/uploads/images/gallery/2025-01/scaled-1680-/G8FZyb8e16ixd6f3-2fbcdcb6a5d1b3e2be8aed53ca9c6696.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2025-01/G8FZyb8e16ixd6f3-2fbcdcb6a5d1b3e2be8aed53ca9c6696.png)

##### 3.自定义网络事件(SendCustomNetworkEvent)

[![de91d804a0e880fc60d7d177bc6639b8.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/yEHTjVoIh0tdI2IK-de91d804a0e880fc60d7d177bc6639b8.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/yEHTjVoIh0tdI2IK-de91d804a0e880fc60d7d177bc6639b8.png)

使用此选项可为实例中当前的每个玩家或对象的所有者触发事件。它肯定会到达，但会有相当大的延迟和开销。发送事件之后加入的任何人都不会收到此事件。

##### 4. 自动

某些特定于 VRChat 的对象会自动同步。这包括：

- 玩家（Players）：包括他们的位置、声音和 IK 运动。
- VRCObjectSync：包括对象的 Transform 和 Rigidbody

---

### 对象所有权Owner

在前文中我们已经十分详细的叙述了所有权的概念，让我们看看VRC官方是如何定义的：

> 在 VRChat 中，每个联网的游戏对象一次都由一个[玩家](https://creators.vrchat.com/worlds/udon/players/)“拥有”。只有对象的所有者才能更改其同步的 Udon 变量或影响其[对象同步](https://creators.vrchat.com/worlds/components/vrc_objectsync/)。然后，可以将这些更改发送给实例中的其他所有人。如果您希望玩家能够更改对象上的变量，请务必先检查或请求所有权！
> 
> 第一个加入实例的玩家将成为实例主服务器。默认情况下，他们拥有所有联网对象。如果该玩家离开，则其他玩家将成为 instance master。它们也将成为前一个实例 master 对象的所有者。
> 
> 当玩家使用 [Object Sync](https://creators.vrchat.com/worlds/components/vrc_objectsync/) 组件拾取[拾取](https://creators.vrchat.com/worlds/components/vrc_pickup/)物时，他们将自动成为该对象的所有者。所有者会不断将拾取物的位置发送给其他玩家。
> 
> 可以通过调用 Udon 来更改对象的所有权。这将导致实例中的每个玩家调用 ，其中 是对对象的新所有者的引用。新所有者可以立即更改同步的变量。（如果您的脚本使用手动同步，请不要忘记在更改 vairables 后调用 call。`Networking.SetOwner(VRCPlayerApi player, GameObject obj)``OnOwnershipTransferred(VRCPlayerApi player)``player``RequestSerialization()`
> 
> 当玩家拥有一个对象时，该对象可能会不断将同步的数据发送给其他玩家，例如该对象的位置或同步的 Udon 变量。

#### 请求所有权 （Advanced）

如果你需要手动将所有权赋予给其他玩家，您可以使用set owner来做到这一点

[![0451233c89249d755a7c2d3b430dd123.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/AWN9jwjQUkCWtAV7-0451233c89249d755a7c2d3b430dd123.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/AWN9jwjQUkCWtAV7-0451233c89249d755a7c2d3b430dd123.png)

当这个模块被Flow激活时，绑定在OBJ上的任何网络同步物体/脚本的所有权将会转交至Player接口上的玩家——您可以使用Get LocalPlayer来指定这个对象为本地玩家。

下面这部分十分晦涩难懂——一般来说你也不会用到，要不跳过把~~~

---

我警告过了哟~那么我们继续看吧

如果您希望对象的所有者能够接受或拒绝所有权转移，请将该事件添加到您的脚本中。`OnOwnershipRequest(VRCPlayerApi requester, VRCPlayerApi newOwner)`

通过添加到脚本，将在所有权转移期间执行其他步骤

1. 与以前一样，**请求玩家**必须调用才能开始所有权转移。`Networking.SetOwner(VRCPlayerApi player, GameObject obj)`
    - “请求”玩家可以是任何玩家，也可以是所有者。如果（前）所有者发出了请求，他们将跳过第 4 步和第 5 步。
    - 所有者可以将所有权授予任何人，但非所有者只能为自己请求所有权。（没有此限制的脚本没有此限制。`OnOwnershipRequest()`
2. `OnOwnershipRequest(VRCPlayerApi requester, VRCPlayerApi newOwner)`由**请求玩家**调用。 
    - 请求玩家必须返回到请求。否则，请求将提前取消。`true`
3. `OnOwnershipTransferred(VRCPlayerApi player)`由**请求玩家**调用。 
    - 这种情况发生在所有者确认所有权*之前*。如果转让被拒绝，所有权可能会更改回来。
4. `OnOwnershipRequest(VRCPlayerApi requester, VRCPlayerApi newOwner)`由**所有者**调用。 
    - 如果所有者返回 ，则接受所有权转移。（在 Udon Graph 中，用于返回 。`true``SetReturnValue``true`
    - 如果所有者返回或根本不返回值，则所有权转移将被拒绝。 呼叫请求玩家，通知他们初始所有者仍然拥有该对象。`false``OnOwnershipTransferred()`
    - 如果所有者将所有权转让给某人，则会跳过此步骤。新玩家无法拒绝所有权。
5. 如果请求被接受，则由**前所有者**和**所有其他玩家**调用。`OnOwnershipTransferred(VRCPlayerApi player)`[![e3ebd62b6281060b788e9fba080ee132.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/iGZGN70caekktNIg-e3ebd62b6281060b788e9fba080ee132.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/iGZGN70caekktNIg-e3ebd62b6281060b788e9fba080ee132.png)

没听懂是吗？那就让我来在解释一次~~~~

我们来假设一个场景——一个

1.本地玩家申请成为Owner，后称为申请玩家，他需要触发SetOwner，如下：

[![ba835bff901e95054cd8669461fdd6ec.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/RSh1BWMZo93w5W6D-ba835bff901e95054cd8669461fdd6ec.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/RSh1BWMZo93w5W6D-ba835bff901e95054cd8669461fdd6ec.png)

我们将OBJ留空——这将会默认为我们的脚本

2.申请玩家接收到OnOwnershipRequest，并且同意转移

[![7855d2ab0f1872d22a74d01d7e9ae6da.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/OKWRGKCqsxg51Ebs-7855d2ab0f1872d22a74d01d7e9ae6da.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/OKWRGKCqsxg51Ebs-7855d2ab0f1872d22a74d01d7e9ae6da.png)

> 如果申请玩家返回False或者不返回任何值，这一次申请将会直接结束——其他人不会知道发生了什么

3.申请玩家接收到OnOwnershipTransferred

[![588051b07f5a8f1b1d1db74e62d3fcfa.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/JJjAluPWK5v5zSKU-588051b07f5a8f1b1d1db74e62d3fcfa.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/JJjAluPWK5v5zSKU-588051b07f5a8f1b1d1db74e62d3fcfa.png)

4.原Owner收到申请并触发OnOwnershipRequest，原Owner需要返回True来同意这次转移。

[![7855d2ab0f1872d22a74d01d7e9ae6da.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/OKWRGKCqsxg51Ebs-7855d2ab0f1872d22a74d01d7e9ae6da.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/OKWRGKCqsxg51Ebs-7855d2ab0f1872d22a74d01d7e9ae6da.png)

> 如果原Owner返回False或者不返回任何值，那么这次所有权转移将会失败，申请人的OnOwnershipTransferred会触发并且返回原Owner的名字（可以认为是告知申请失败）

5.当原Owner同意了这一次转移，那么原Owner的OnOwnershipTransferred将会被触发，并且输出新的Owner的信息（可以认为是告知自己不再是Owner），随即其他玩家将会收到OnOwnershipTransferred，并且接收到新的Owner的信息。

到这里Owner转移结束。

> 在Owner转移结束时，转移结果并不会以任何形式告知新Owner——所以我建议您将该脚本的同步方式改为Continuous(自动同步)并且让其尽量独立——我们并不确定后续的事件和Owner权转交完成哪一个会先执行

---

### 使用变量

<div class="admonitionHeading_Gvgb" id="bkmrk-%E4%BD%BF%E7%94%A8%E5%8F%98%E9%87%8F%E5%90%8C%E6%AD%A5%E6%95%B0%E6%8D%AE%E5%88%86%E4%B8%BA%E4%B8%89%E4%B8%AA%E6%AD%A5%E9%AA%A4%EF%BC%9A">使用变量同步数据分为三个步骤：</div><div class="admonitionContent_BuS1" id="bkmrk-%E5%88%9B%E5%BB%BA%E5%8F%98%E9%87%8F%E3%80%82-%E6%9B%B4%E6%96%B0-owner-%E4%B8%8A%E7%9A%84%E5%80%BC%E3%80%82-">1. 创建变量。
2. 更新 Owner 上的值。
3. 对从 Owner 收到的值变化做出反应。

</div>#### 创建变量

1. 单击 Variables 窗口中的 + 按钮
2. 选择变量类型
3. 重命名您的变量（可选，但请执行此操作）
4. 点击变量名称旁边的箭头以显示更多选项，开启 'synced'。

#### 更新 Owner （所有者） 上的值

1. 在按住 'Ctrl' 的同时将变量拖放到图表上，以创建 'Set Variable' 节点。
2. 将事件或流连接到此节点上的流端口，并将新值连接到值端口。
3. 如果此 UdonBehaviour 正在使用自动同步（在 Inspector 中的 UdonBehaviour 本身上选择），则该值会自动更新。如果您使用的是手动同步，则需要添加一个“UdonBehaviour.RequestSerialization”节点，并将 Set Variable Flow Port 的输出连接到此节点上的 Flow Input 端口。您可以将此节点上的 'instance' Value Port 留空，它将默认为当前的 UdonBehaviour，这就是我们想要的。

[![6eadd7e1f274f2da1d1f957dd462d361.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/qVazFxpg5RTTMK29-6eadd7e1f274f2da1d1f957dd462d361.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/qVazFxpg5RTTMK29-6eadd7e1f274f2da1d1f957dd462d361.png)

##### 网络同步变化做出反应

1. 将 “OnDeserialization” 节点添加到同一图表中。
2. 将变量拖放到图表上，无需按住 Ctrl 键即可创建“Get Variable”节点。
3. 使用来自 OnDeserialization 节点的流和 Get Variable 节点中的值，使用此新值更新另一个节点。

[![44226743ded00cdf9a9555be4eccd9a3.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/ttS6h5JhmJdYdP9b-44226743ded00cdf9a9555be4eccd9a3.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/ttS6h5JhmJdYdP9b-44226743ded00cdf9a9555be4eccd9a3.png)

---

### 请求序列化

此节点在 手动同步 模式下用于标记目标 UdonBehaviour 上的变量，以便在下一个 Network Tick 期间进行序列化，这不会每帧发生。此节点与 OnPreSerialization Event 节点配合极好。触发 “RequestSerialization”，然后 OnPreSerialization 事件将在下一个 Network Tick 期间触发。此时，您可以将任何变量更新为要同步的值。

您可以同步以下类型的变量和变量数组：

- 整数类型 
    - `byte`和`sbyte`
    - `double`
    - `float`
    - `int`和`uint`
    - `long`和`ulong`
    - `short`和`ushort`
- 复合类型 
    - `Color`和`Color32`
    - `Quaternion`
    - `Vector2`和`Vector3``Vector4`
- 文本数据类型 
    - `string`和`char`
    - `VRCUrl`
- 逻辑数据类型 
    - `bool`

> <p class="callout warning">官方警告！</p>
> 
> 当同步带有同步数组变量的行为时 - 确保始终将这些数组初始化为某个值，例如空数组。如果任何同步的数组未初始化 - 行为将不会同步！您可以通过 [OnPostSerialization](https://creators.vrchat.com/worlds/udon/networking/network-components#onpostserialization) 节点检查序列化是否成功

---

### 使用自定义事件

SencustonNetworkEvent

Udon 脚本可以为实例中的其他玩家触发自定义事件

1. 确保您的 UdonBehaviour 的同步模式设置为“连续”或“手动”，而不是“无”。
2. 创建 “Event Custom” 节点。
3. 使用该节点的输入框为其指定唯一名称。
4. 添加 “Send Custom Network Event Node”（发送自定义网络事件节点）。
5. 在输入中输入相同的事件名称。`eventName`
6. 将默认值保留为目标，以便在 Room 中的每个 Player 上触发此事件，或将其更改为仅在 Owner 上触发此事件。`All``Owner`
7. 您可以将输入留空以定位当前 UdonBehaviour，或将引用连接到另一个 UdonBehaviour 以在该 UdonBehaviour 上触发自定义事件。`instance`

[![c16c21b92033c16bb19f65e89384e416.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/5U7xS0RsmyAd4rz9-c16c21b92033c16bb19f65e89384e416.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/5U7xS0RsmyAd4rz9-c16c21b92033c16bb19f65e89384e416.png)

#### 仅限本地的活动

如果事件名称以下划线开头，则无法通过网络调用它们。Udon 这样做是为了保护 VRChat 的内部方法（如 \_start、\_update 和 \_interact）免受恶意网络调用的侵害。VRChat 计划向事件添加一个属性，以将其标记为“仅限本地”，而无需下划线。如果要同时阻止远程执行事件，可以使用唯一的下划线前缀（如“\_u\_eventName”）来确保它与任何现有或将来的 VRC 方法不匹配。

#### 事件参数

Udon 目前不支持发送带参数的事件。无法将事件发送到特定Player（Owner除外）。

##### 解决方法 1

为每个玩家提供对游戏对象的所有权，这样每个玩家现在都有一个与他们关联的 UdonBehaviour。使用 Networking.GetOwner（GameObject） 找出要将事件发送到哪个 UdonBehaviour 以定位指定玩家。此方法的设置非常复杂。

##### 解决方法 2

使用同步变量（displayName 的 string 或 playerID 的 int）告诉每个人指的是哪个玩家。但是，由于下面将进一步描述同步变量/网络事件交互的问题，此方法有点棘手。

---

### 调试

如果您在 VRChat 中按调试菜单 8 启动并进入[调试菜单 8](https://creators.vrchat.com/worlds/udon/world-debug-views#debug-menu-8)，则可以在客户端中查看有关联网对象的一些信息。 这些叠加层向您展示`--enable-debug-gui``Right Shift``8`

- 游戏对象的网络 ID
- 游戏对象的显示名称
- `P`： Ping 时间，
- `Q`：数据质量（100% 表示没有丢弃的数据包）和
- `O`：游戏对象的所有者。

---

### 网络相关Event速查：

以下事件作为 Networking 系统的一部分提供，用于控制数据的同步方式。

#### OnPreSerialization（发送数据准备）

此事件在发送序列化数据之前触发，这是设置要为其他玩家更新的同步变量的好地方。

#### OnDeserialization（接受数据完成）


当同步数据已从字节转换回可用变量时，将触发此事件。它不会告诉您*哪些*数据已更新，但可以作为更新监视同步变量的所有内容的起点，或者是检查新数据与旧数据并进行特定更新的地方。

#### OnDeserialization&lt;DeserializationResult&gt;（接收数据·详细版）

与 OnDeserialization 相同，但包含有关发送和接收请求的时间的其他信息。

#### OnPostSerialization（发送完成触发）

此事件在尝试发送序列化数据后立即触发。

#### OnOwnershipRequest (请求所有权-申请流程触发)

当有人请求获得所有权时，将触发此事件。它包括 Requester 和 Requested Owner 的 PlayerObjects。要批准或拒绝更改，请在 “Set Return Value” 节点中设置布尔值。此逻辑在请求者和所有者上本地运行，因此请注意，两者之间的逻辑不一致将导致不同步。这很可能表现为所有权转让被所有者意外拒绝。

#### OnOwnershipTransferred（请求所有权-所有权转移触发）

当对象所有权发生更改时，将对实例中的每个人触发此事件，并包括新所有者的 PlayerObject。

#### OnMasterTransferred（房主已离去触发）

当实例 master 因前一个实例 master 已离开实例而发生更改时，会为实例中的每个人触发此事件。 它包括一个参数，即已成为主控的玩家的VRC-Player-API对象。此参数始终有效。 对于第一个加入新实例的用户，此事件将在之后触发，以指示 master 状态是从 “nobody” 转移的

#### OnVariableChanged&lt;变量&gt;（指定变量发送变化后触发）

这是您可以为任何变量创建的特殊类型的事件。在 Udon Graph 中，您可以通过按住 alt 将变量拖放到图表中来创建它。此事件检测变量何时更改，其中可能包括您何时收到来自其他玩家的同步变量。

- 更改数组的内容不会触发更改，因为数组本身仍然相同。
- OnVariableChanged 在写入变量本身时立即触发，这与 OnDeserialization 不同，后者在写入完所有同步变量后触发。这意味着，如果您从一个同步变量中使用 OnVariableChanged 并尝试获取其他同步变量的内容，则无法保证它已使用最新的同步数据进行更新。

本期教程到这里结束

# Udon Graph 基础教程：同类型参数的储存与转移利器——Array[]数组

本章节将会教最基础的Array使用方式，更多使用方式请搜索unity-数组教程

## Udon Graph：Array

---

### 基础认知

#### 什么是数组：

数组是一个固定长度的存储相同数据类型的数据结构，数组中的元素被存储在一段连续的内存空间中。它是最简单的数据结构之一，大多数现代编程语言都内置数组支持。

#### 为何使用数组存储一堆变量

与单独为每一个元素声明一个变量名相比，我们可以使用数组的索引值访问数组中的每一个元素。

例如：

你现在制作了一个游戏地图，你需要为这个地图制作了一个分数系统

我们需要实现一个系统来存储所有玩家的分数，传统的存储方式如下：

我们为每一个可能的玩家都设置一个变量。假设我们设定这个游戏的上限为四人，那我们需要为他们生成三个变量，如下：

[![84db9cef3962c869ead907b9edbb0b34.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/0q1UHDKww4PaSLZL-84db9cef3962c869ead907b9edbb0b34.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/0q1UHDKww4PaSLZL-84db9cef3962c869ead907b9edbb0b34.png)

当游戏上限增加的时候，我们需要创建更多的变量来存储玩家的分数。制作这样的系统非常麻烦，当地图的上限超过5个玩家时.逐一创建变量，并为每一个变量编写相同的程序，是十分繁琐且没有意义的事情。

数组数据结构的出现尝试解决这种问题。数组的特性之一就是，以一个名称保存多个相同数据类型的数据。

在这个例子中，我们可以添加一个INT数组来保存所有玩家的分数。

在游戏开始前，我们先为每一个玩家获取一个ID，这个ID代表了玩家的数据被储存在了数组的哪一层中。

> 在我们需要某一个玩家的数据时，我们只需要先获取玩家的ID，然后用ID作为索引，直接从数组里面获取对应的参数，我们只需要改变ID值，即可实现读取所有玩家的参数。

另外，当我们需要对计算类似于平均数/最大值一类的，需要调用所有同类型参数的计算中，数组的遍历优势才会真正显现。

如果我们需要计算平均值，那么我们就需要将所有的参数进行累加，然后除去参数的总数，我们需要2N+2个节点才能完成。

但是如果我们使用数组来制作，我们需要使用10个节点——没错这个是一个常数。

[![6e7f6c462495bac7dbf803c06fbaea83.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/15lpvu0YE1qG281k-6e7f6c462495bac7dbf803c06fbaea83.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/15lpvu0YE1qG281k-6e7f6c462495bac7dbf803c06fbaea83.png)

数组通过For循环进行遍历，将每一个参数都拿出来执行一遍Body挂载的程序，在遍历完成之后，For循环会终止并且开始执行Exit处的指令。

参数的数量越多，数组的作用就越明显数组的优点

---

#### 数组的优点

相比其它类型的数据结构，数组有以下优点：

- 数组允许随机访问数组元素，每个存储在数组中的元素可以通过直接访问其索引来使用
- 数组对存储友好。这意味着在某些情况下，由于数组的线性存储方式，代码的执行顺序会大大提高

#### 数组的缺点

- 声明数组时，需要指定数组的长度。初始声明数组的长度过长或过短，在移动数组元素时都会有导致效率变低
- 插入和删除元素之后保持数组的连续性代价是昂贵的，因为有可能需要重新排列所有数组元素。

---

### 数组使用方法

#### 1：创建数组

在UdonGraph中，数组的创建十分的简单，点击左上角的添加变量，搜索对应的数组名，然后添加即可：

例如：string对应数组名为：string\[\] int对应数组名为:int\[\],以此类推

数组的类型多种多样，从普通的int，float，string基础的参数，到GameObject，VRCPlayerapi\[\],UdonBehviour\[\]——基本上每一个类都有属于自己的数组。

#### 2：初始化数组

在UdonGraph中，Udon会自动完成初始化，但是你依旧需要提前给他设定数组长度以及初始值，部分的数组（如int\[\]，float\[\]等）他们的初始值为0，而string\[\]则会默认为（NULL）

声明数组长度，我们可以通过设置Default Value来设置，点击Edit之后手动输入Size（即为数组长度）即可完成数组长度的设置。

[![未命名的设计.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/nuDVfiJ8COrOWv8N-DjMpUpG2zs.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/nuDVfiJ8COrOWv8N-DjMpUpG2zs.png)

> 数组长度决定了这个数组能够装多少数据，所以在编译前请确定并且设置好数组的长度

#### 3.数组的遍历

数组中的每一个元素都可以通过索引访问到。数组的索引通常从0开始，即数组中第一个元素的索引为0.数组的最后一个元素位于（n-1)个索引处。我们将其称之为基于0的索引。数组也可以是基于其它数的，我们将其称之为基于n的索引。

使用for循环简单的遍历数组中的所有索引，可以访问数组中的所有元素。

我们可以通过一个简单的快捷键操作来完成FOR循环的设置

- ![制作 for 循环是简单的方法！](https://creators.vrchat.com/assets/images/index-87b33a4-for-loop-ffc65fea26c260cfd7e060bdf9554aec.gif)

> 按住 “Shift+F”，然后单击输出数组类型的节点，以自动生成 foreach 循环 其中许多功能也可以在其各自节点的右键单击菜单中使用。

在对数组元素进行插入或者删除时，为了保持数组的顺序。需要处理数组中已经存在的其它数组元素。对于较大的数组，这项工作对<span class="edu-hl hl hl-1" data-pretit="性能" data-report-click="{"spm":"1001.2101.3001.7020","extra":"{\"word\":\"性能\"}"}" data-tit="性能">性能</span>的消耗是昂贵的。

#### 4.写入元素到数组中

我们可以使用SetValue或者Set来向数组写入数据

[![1735098151567.jpg](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/bbb5nMgssezR2WrU-1735098151567.jpg)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/bbb5nMgssezR2WrU-1735098151567.jpg)

instance用于指定需要写入的数组，第二个则是数据本身，用于指定数据所在的位置（索引）

#### 5.从数组中读取元素

我们可以使用GetValue或者Get来向数组读取数据

[![1735098348064.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/uewz683U6VuaE5G6-1735098348064.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/uewz683U6VuaE5G6-1735098348064.png)

> 在读取元素时：一般常用Get来获取参数
> 
> 在写入元素时：一般常用SetValue来写入数据

#### 6.在数组中搜索指定的参数

我们可以使用IndexOF 或者BinarySearch来搜索指定的参数，如果搜索到了会返回参数所在的位置，否则返回-1

[![a09d01ba2eb5e7ace4ad48113c13f200.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/iaJkPVi0xLb1jll8-a09d01ba2eb5e7ace4ad48113c13f200.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/iaJkPVi0xLb1jll8-a09d01ba2eb5e7ace4ad48113c13f200.png)

---

### 其他方法（全）

> 数组操作一般会分布在xx\[\]和Array两个方法中，建议如果时两边都有的方法，优先使用XX\[\]中储存的方法，因为它对比Array中少了确认数组类型的一步，性能有所提升。

xx\[\]中储存的方法模块

待写

Array中储存的方法模块

待写

# Udon Graph新手入门指南——应用篇

本篇将会通过应用的方式讲述UdonGraph

# 制作一个用于开关物体/组件的按钮

本期咱们来制作一个可以开关物体的按钮

---

### PART1:程序目标设计

这个程序十分简单，我们的目标就是——

开关一个物体

---

### PART2：设计参数

#### <span data-v-2505e99a="">EVENT：事件选择</span>

<span data-v-2505e99a="">既然是一个按钮，那么我们的选择一定是当玩家按下扳机，或者按下鼠标左键后触发，VRchat为我们准备了一个Event事件——</span>

<span style="background-color: rgb(241, 196, 15); color: rgb(0, 0, 0);">**<span data-v-2505e99a="">Event Interact </span>**</span>

[![3afad599fc38af87f0e901e7724b5d81.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/EobYVaKMDLFL4qxO-3afad599fc38af87f0e901e7724b5d81.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/EobYVaKMDLFL4qxO-3afad599fc38af87f0e901e7724b5d81.png)

这个事件会在玩家交互后相应，并且开始执行后续的流

#### Variables：参数选择

在参数上，我们需要一个Pulic的接口来链接我们需要开关的东西，比如GameObject，或者是GameObject的组件，如Light，Behaviour等等。当然我们比较推荐您使用GameObject来开关您的物体。

在这里我们选择一个GameObject和一个Light组件来讲解如何制作一个开关

[![9597d187cf8e9c58da4a15aadbd32d1a.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/bEKueUsRMR1ZYmYH-9597d187cf8e9c58da4a15aadbd32d1a.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/bEKueUsRMR1ZYmYH-9597d187cf8e9c58da4a15aadbd32d1a.png)

---

### **PART3：设计程序**

#### **第一步：**

如果我们要开关一个物体，我们需要一个模块来设置物体的激活状态，在Unity中，一个GameObject的开关取决于Active属性，通过搜索GameObject-SetActive，我们可以找到我们需要的这个组件

[![1828594d9ebc6ae0c7bffca2c4d35781.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/gQb6gaJpQKaFe5ME-1828594d9ebc6ae0c7bffca2c4d35781.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/gQb6gaJpQKaFe5ME-1828594d9ebc6ae0c7bffca2c4d35781.png)

在instance中链接我们需要的控制的对象——GameObject,即可完成这个组件的基础设置。

#### 第二步：

既然我们是一个按钮，那么我们不能设置他每一次按下都只会关闭，我们需要让他在关闭的时候按下去就是开启，开启的时候按下去就是关闭，也就是说——我们需要获取物体目前的开关状态（Active），并且将其反转。

还是一样的，打开搜索界面，搜索Get activeSelf，并且把在instance中链接我们需要的控制的对象，这个组件就会读取这个物体的状态，并且以Bool值的方式输出出来。

[![11e6a3a77c94ba9d02b98c40aaa6de15.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/Kve75qXGmjEntspi-11e6a3a77c94ba9d02b98c40aaa6de15.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/Kve75qXGmjEntspi-11e6a3a77c94ba9d02b98c40aaa6de15.png)

> 关于activeSelf与activelnHierarchy的区别：activeSelf只会获取这个GameObject的状态，即使父级已经关闭，只要他本身还是开启状态，他依然会输出开启，而activelnHierarchy只要这个物体在场景中是关闭状态，无论是因为父级关闭连带的关闭，还是自身关闭，他都会输出“关闭”。

接下来我们需要反转Bool值，这将会用到“Boolean”-“UnaryNegation”组件，这个组件会将布尔值反转并输出

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/i8vxTHKI4uz9FP1q-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/i8vxTHKI4uz9FP1q-image.png)

接下来只需要将输出的已经反转的布尔值连接到SetActive-Value处，既可完成这个脚本的编辑

如果您需要在开启触发脚本的同时，希望某个物体关闭，很简单，在后面再加上一个SetActive并连接到你希望关闭的东西，将参数设置为（false）即可，如下

[![d801a2de098d80e85e5d7b6d842d157e.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/4W2AbaNbGOmI996L-d801a2de098d80e85e5d7b6d842d157e.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/4W2AbaNbGOmI996L-d801a2de098d80e85e5d7b6d842d157e.png)

基础教学到此结束，下面是进阶部分~~~

---

### 进阶1：关闭GameObject上的组件

与GameObject不同，组件使用的是“Set enabled”来控制组件的开启状态，即“激活状态”

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/upYrt5FTSulvxetM-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/upYrt5FTSulvxetM-image.png)

我们同样可以用“Get enabled”来获取到组件的开启与关闭，再通过“Boolean”-“UnaryNegation”组件反转布尔值，来控制组件的激活状态

---

### 进阶2：顺序开启

如果我们需要用一个按钮同时控制三个物体，让他们顺序启动与关闭，那么我们应该如何去做呢？很简单，让我们在上述组合中引入一个int值来保存我们开启到了哪里。

#### 第一步：

我们需要当int值等于某一个值的时候，执行某一个流，我们假定这个系统中有三个物体需要顺序激活，且激活一个时另外两个需要是关闭状态，所以我们需要先制作一个根据int来决定执行哪一个的流的组合——我们可以通过INT参数+Equals（等于）+Branch（分支）来制作这个组合。

节点图如下

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/2dvREJrg5yG0nsJI-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/2dvREJrg5yG0nsJI-image.png)

这个组合的意思是，当触发了之后，如果int值=0，1，2，则从第1，2，3的true出口执行后续流，如果int不等于0，1，2则执行最下端的False链接的流。

#### 第二部：

由于我们已经知道了Bool值，所以我们不再需要获取布尔值再反转，而是直接设置开关状态即可。

除了1.2.3条之外，我们设定第四个出口为全部关闭。节点图如下

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/IwAxX25J2vL9b2kI-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/IwAxX25J2vL9b2kI-image.png)

#### 第三步：

随后我们让当1，2，3触发后，int值+1。而如果触发了最下端的全关，则将int值清零，从头开始.

我们需要使用加法器（Addition）来做到这一点，节点图如下

[![fc32a62e396e07432e456016516209fd.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/hnoIrKchBsukoDuQ-fc32a62e396e07432e456016516209fd.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/hnoIrKchBsukoDuQ-fc32a62e396e07432e456016516209fd.png)

到这里，你已经掌握了开关的全部制作方法，快去试试吧~

# 制作一个用于白名单/黑名单系统

欢迎来到麦哲伦的udon graph实战教程，本期专栏将带大家手把手设计一个本地白名单系统。那么事不宜迟，让我们马上开始！

---

### <span data-v-2505e99a="">PART1:程序目标设计</span>

<span data-v-2505e99a="">黑白名单系统的目标很简单，分为以下几点：</span>

1. <span data-v-2505e99a="">名单</span>
2. <span data-v-2505e99a="">确认本地玩家是否是名单中的人</span>
3. <span data-v-2505e99a="">如果是-执行操作，如果不是-略过</span>

<span data-v-2505e99a="">至于具体对名单人执行什么操作，我们可以通过将customEVENT发送到下一个udon，让其他的udon来执行具体操作，而黑白名单系统只需要做好自己的事情——确认玩家是否存在于黑白名单中</span>

### <span data-v-2505e99a="">PART2：设计参数</span>

#### <span data-v-2505e99a=""> EVENT：事件选择</span>

<span data-v-2505e99a=""> 首先我们肯定是加入地图就决定玩家是不是白名单了所以我们要脚本从加入地图开始执行。而且仅需要执行一次</span>

<span data-v-2505e99a=""> 所以事件的触发我们选择——Events：Start 从玩家加入开始执行</span>

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/vyV5G4mRg9AoDUM5-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/vyV5G4mRg9AoDUM5-image.png)

#### <span data-v-2505e99a="">Variables：参数选择</span>

<span data-v-2505e99a="">在参数上我们需要</span>

<span data-v-2505e99a="">1:udonBehavious</span>

<span data-v-2505e99a="">用于把customEvent发送出去</span>

<span data-v-2505e99a="">2：string\[\] </span>

<span data-v-2505e99a="">用于存放白名单/黑名单</span>

<span data-v-2505e99a="">3：string</span>

<span data-v-2505e99a="">用于存放customEvent名称</span>

<span data-v-2505e99a="">具体命名如下：</span>

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/G2aLStUOkHWVLw2M-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/G2aLStUOkHWVLw2M-image.png)

### **PART3：设计程序**

####  **第一步：获取本地玩家名称**

<span data-v-2505e99a="">首先我们需要获取本地玩家名称，这将会用到两个模块。</span>

<span data-v-2505e99a="">1.Get Localplayer ：获取本地玩家信息</span>

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/u37VaXfvTBYS9MIO-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/u37VaXfvTBYS9MIO-image.png)

<span data-v-2505e99a="">该组件会输出包含玩家所有信息的VRCPlayerAPI属性</span>

<span data-v-2505e99a="">2.Get displayName:获取本地玩家名称</span>

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/UouZGrmtYOYZXnaI-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/UouZGrmtYOYZXnaI-image.png)

<span data-v-2505e99a="">该组件会从PlayerApi当中输出玩家的名字，以string字符串的方式</span>

<span data-v-2505e99a="">到这一步，我们完成了获取本地玩家名称的任务，储存方式为string</span>

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/G0MencLYSUVdFj6U-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/G0MencLYSUVdFj6U-image.png)

####  **第二步：判断玩家是否处于名单内**

<span data-v-2505e99a=""> 我们需要用到两个组件来完成判断：IndexOF与Equals</span>

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/sCjd0oYOBaRLInqY-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/sCjd0oYOBaRLInqY-image.png)

<span data-v-2505e99a="">IndexOF提供了一个接口，允许在事件接入时检查Value是否与Array内的元素相同，如果相同则返回相同元素所在的层级，如果不相同则返回-1</span>

<span data-v-2505e99a="">Equals则会确认int值是否等于obj，这里将值设为常数-1</span>

<span data-v-2505e99a="">在左侧的Array一栏输入参数-string\[\]-数组，下方Value输入上文获取的LocalPlay Displayname。IndexOF会比对玩家名称与string\[\]中存放的玩家名称名单是否有相同的。并输出到int值。已知如果不存在则会输出-1，那么仅需要检测值是否不等于-1，即可得知玩家是否属于名单内。</span>

<span data-v-2505e99a="">所以Udon Graph里面应该这么写——</span>

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/IDMkbnf3gaVkJeIb-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/IDMkbnf3gaVkJeIb-image.png)

#### 第三步：发送结果

至此我们已经完成了从获取玩家名称到判断是否属于白名单的整个过程了，剩下的只需要加入我们喜闻乐见的发送事件（结果）到其他Udon脚本即可

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/LGjjYJW5h623ztbt-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/LGjjYJW5h623ztbt-image.png)

<span data-v-2505e99a="">恭喜你，到这里你已经完成了一个基于UDON Graph的本地白/黑名单系统的构筑。</span>

<span data-v-2505e99a="">下面是UDON Graph全貌：</span>

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/UtHkHiiSOjFgVTx8-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/UtHkHiiSOjFgVTx8-image.png)

#### Q:如何添加白名单/黑名单？

只需要打开PlayerList的stringArray，然后为他写入default 值即可

---

## 进阶篇：黑白名单系统联网

本篇将会将前述的黑白名单系统的黑白名单的获取方式，从手动写入改成从互联网上拉取。

---

### **PART1:程序目标设计**

1. **<span data-v-2505e99a="">通过网络下载黑名单文件（通常为TXT或JSON，这期只讨论TXT类型）</span>**
2. **<span data-v-2505e99a="">本地获取文件后转化为需要的string数组</span>**
3. **<span data-v-2505e99a="">将数组发送给上一期的”白名单系统“</span>**

#### <span data-v-2505e99a="">在开始新脚本的设计之前，我们需要对老脚本进行一些更改</span>

<span data-v-2505e99a="">1：将事件触发方式（Events）从Start改为CustomEvent</span>

<span data-v-2505e99a=""> CustomEvent可以允许脚本被其他脚本触发，我们必须保证黑名单完成了装载再执行脚本。</span>

[![e0e9e326c61b6bf699717a10be728d22.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/i6nTw6Mfck7rB6YV-e0e9e326c61b6bf699717a10be728d22.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/i6nTw6Mfck7rB6YV-e0e9e326c61b6bf699717a10be728d22.png)

<span data-v-2505e99a="">在完成这部分的更改之后，我们如愿进入核心部分，节点构筑：</span>

---

#### **Part2：设计参数**

##### <span data-v-2505e99a="">Event：事件选择</span>

<span data-v-2505e99a=""> 本次我们需要使用到两个Event事件</span>

<span data-v-2505e99a=""> 1：start：从游戏开始执行</span>

<span data-v-2505e99a=""> 2：OnstringLoadSuccess:当文件下载成功时，执行后续</span>

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/M4MXKsZIiW2UrRKj-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/M4MXKsZIiW2UrRKj-image.png)

<span data-v-2505e99a="">Variables：参数选择</span>

<span data-v-2505e99a=""> 本次我们需要两个参数</span>

<span data-v-2505e99a=""> 1:udonBehaviour:用于指定需要Event激活和数组的UDON脚本</span>

<span data-v-2505e99a=""> 2:VRCUrl:用于存放下载黑名单文件所必须的URL文件。</span>

<span data-v-2505e99a=""> 具体命名如下：</span>

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/71Ao7niReSvcpRMD-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/71Ao7niReSvcpRMD-image.png)

#### **Part:3：设计程序**

##### <span data-v-2505e99a=""> 第一步：设计数据包</span>

<span data-v-2505e99a=""> 已知我们所需要的是一个数组，但是TXT文件中只包含了一个string字符串，我们不能直接使用TXT来传递数组。但是我们可以通过规定分隔符，下载到脚本后通过Split来将String转换为String\[\]数组。</span>

<span data-v-2505e99a=""> 在这里我选择使用\[#\]作为分隔符。 </span>

<span data-v-2505e99a=""> 假设我需要设计一个包含1-9的黑名单，则在TXT里面应该是</span>

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/EQCP3k4V6lGhhCoQ-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/EQCP3k4V6lGhhCoQ-image.png)

##### <span data-v-2505e99a=""> 第二步：从网络上拉取文件</span>

<span data-v-2505e99a=""> UDON为我们提供了一个组件——LoadUrl，让我们可以访问互联网发出一个GET指令到指定 URL，获取数据包。</span>

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/XVEw9FXc7CteifGs-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/XVEw9FXc7CteifGs-image.png)

#### <span data-v-2505e99a="">第三步：提取数据</span>

<span data-v-2505e99a="">OnstringLoadSuccess除了提供了下载成功后的事件执行以外，还提供了一个数据接口，让我们通过这个数据接口来获取刚刚下载的信息。</span>

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/qdrMNO9b6XBuhLgy-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/qdrMNO9b6XBuhLgy-image.png)

不过这个数据还不可以直接使用，我们需要使用Get Result来将其转化为string，才能进行后续操作

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/rvh56lTMjbHDzwRW-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/rvh56lTMjbHDzwRW-image.png)

将二者连接之后，我们成功的将文件以string的形式导入进了UDON里面。

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/1dmtwIvBfptLXPi6-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/1dmtwIvBfptLXPi6-image.png)

#### <span data-v-2505e99a="">第四步：将string以转换为string\[\]</span>

<span data-v-2505e99a=""> 为了将string转换为数组，我们需要使用Split来进行转化，Split会按照一定的规则来将输入的string切割，在选择Regex分类的Split会将转化后的切片以String\[\]的方式储存，刚刚好符合我们的要求。</span>

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/ITQuCd228uiYFIpK-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/ITQuCd228uiYFIpK-image.png)

##### <span data-v-2505e99a=""> 第五步：将数组发送到下一个脚本，并且激活下一级脚本。</span>

<span data-v-2505e99a=""> 我们需要使用SetProgramVariable来传递我们处理好的数据，这个模块会直接覆写指定的脚本的指定参数，来达到传递数值的效果。</span>

<span data-v-2505e99a="">再然后，我们使用SendCustomEvent向下一个脚本发出事件。</span>

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/C98tOFunMikJ60wT-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/C98tOFunMikJ60wT-image.png)

<span data-v-2505e99a=""> 至此完成了数据下载-数据处理-数据传输-链接执行脚本的全过程。</span>

<span data-v-2505e99a="">下面是UDON Graph全貌</span>

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/fb1rVqraExzl61rV-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/fb1rVqraExzl61rV-image.png)

恭喜您完成了进阶学习~

<span data-v-2505e99a="">感谢您的观看，我是麦哲伦，咱们下期再见~</span>

<span data-v-2505e99a="">鸣谢：</span>

<span data-v-2505e99a="">VRCD中文创作者协会</span>

<span data-v-2505e99a="">代理猫猫</span>

# 制作一个用于收集物状态统计系统

<span data-v-2505e99a=""> 本期我们来制作一个收集物统计系统</span>

<span data-v-2505e99a=""> 相信制作过游戏地图的朋友们都知道，收集物是游戏的一个很重要的元素。本期我们来制作一个完整的收集物系统。</span>

---

#### 第零步：

<span data-v-2505e99a="">首先需要为可收集物制作一个脚本，通过“开关”自己来实现可收集的感觉。</span>

<span data-v-2505e99a=""> 比较简单，直接放图。</span>

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/amnadwbttHBsU8e2-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/amnadwbttHBsU8e2-image.png)

这个脚本的效果就是将自身关闭。后面我们也是通过Active状态来确认收集物是否被收集。

---

## 收集物统计系统

### PART1:程序目标设计

<span data-v-2505e99a="">这个我们需要这个系统输出以下信息</span>

<span data-v-2505e99a="">1：收集物名单（gameobject\[\]）</span>

<span data-v-2505e99a="">2：已收集数量（int）</span>

<span data-v-2505e99a="">3：未收集数量（int）</span>

<span data-v-2505e99a="">4：未收集名单（int\[\]）(未收集的部分在数组中对应的位置)</span>

<span data-v-2505e99a="">我们需要向系统输入以下信息</span>

<span data-v-2505e99a="">1：收集物名单（gameobject\[\]）</span>

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/gNrTWIAV4VQTjWTp-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/gNrTWIAV4VQTjWTp-image.png)

我们通过一个Public的gameobject\[\]来完成输入，并且这个也是需要输出的信息之一

#### Event 事件选择

这个选择可以十分的多元，既可以选择常规的interact触发，也可以选择例如OnPlayerTriggerEnter让玩家进入某一个区域后触发。

选择十分宽泛，这里不做指定。

#### Variables 参数选择

<span data-v-2505e99a="">在参数上我们需要以下参数：</span>

<span data-v-2505e99a="">1：收集物名单（gameobject\[\]）</span>

<span data-v-2505e99a="">2：已收集数量（int）</span>

<span data-v-2505e99a="">3：未收集数量（int）</span>

<span data-v-2505e99a="">4：未收集名单（int\[\]）(未收集的部分在数组中对应的位置)</span>

<span data-v-2505e99a="">命名如下：</span>

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/LRtpcY1DRELolsU1-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/LRtpcY1DRELolsU1-image.png)

### **Part：2 设计程序**

<span data-v-2505e99a=""> 由于这个脚本理论上需要被重复触发，大部分的参数又都是本地参数，在开始之前我们需要对本地参数进行归零处理。</span>

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/sjvnn0EbL3nlRvdE-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/sjvnn0EbL3nlRvdE-image.png)

<span data-v-2505e99a="">归零后正式进入数据处理，首先我们需要制作一个For循环，每一次循环从名单中获取一个gameobject来进入后续的处理。</span>

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/4mvgFkOBrZMcn2aF-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/4mvgFkOBrZMcn2aF-image.png)

<span data-v-2505e99a=""> 使用官方的快捷键可以快速生成一个FOR循环，请在GET后增加一个NULL检测，以防止脚本崩溃。</span>

<span data-v-2505e99a=""> 获取了收集物之后，我们需要对他的状态进行检测，并且且将结果值输出到判定器来判定是否已被收集</span>

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/pwFNRpiYI8ktJOJt-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/pwFNRpiYI8ktJOJt-image.png)

<span data-v-2505e99a=""> 在获得该收集物的收集状态后，已收集的物品会执行True流，而未收集则会执行False流。</span>

##### <span data-v-2505e99a=""> False流——物品已收集</span>

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/xKrevSdLc5wTCL7r-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/xKrevSdLc5wTCL7r-image.png)

<span data-v-2505e99a=""> 倘若物品已经被收集，已收集int值+1，结束，回到循环</span>

##### <span data-v-2505e99a=""> Ture流——物品未收集</span>

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/F3pjkaWyZfBQFJ15-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/F3pjkaWyZfBQFJ15-image.png)

<span data-v-2505e99a=""> 倘若物品没有被收集，从FOR中获取该物品在数组中的位置，保存在int\[\]数组中，数组位置等于【未收集数int+1值】，然后为未收集数int+1值，结束，回到循环。</span>

<span data-v-2505e99a=""> 在循环结束之后，会执行EXIT处的流，我们将这个流接入输出，将值输出到其他脚本</span>

[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/H12THl5lCiiDSYsg-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/H12THl5lCiiDSYsg-image.png)

<span data-v-2505e99a="">以上就是收集物统计系统的全部，下面是udon Graph全代码</span>

<div class="opus-para-pic center" id="bkmrk--11"><div class="b-img"><picture class="b-img__inner"><source srcset="//i1.hdslb.com/bfs/new_dyn/0411a2cda0e6e8a6164de92dc8d2b99634693506.png@1192w_616h.avif" type="image/avif"><source srcset="//i1.hdslb.com/bfs/new_dyn/0411a2cda0e6e8a6164de92dc8d2b99634693506.png@1192w_616h.webp" type="image/webp"></source></source></picture></div></div>[![image.png](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/scaled-1680-/h6DSGBNGrWxwWWxv-image.png)](https://docs.vrcd.org.cn/uploads/images/gallery/2024-12/h6DSGBNGrWxwWWxv-image.png)

<span data-v-2505e99a="">感谢您的观看，我是麦哲伦，咱们下期再见~</span>

<span data-v-2505e99a="">鸣谢：</span>

<span data-v-2505e99a="">VRCD中文创作者协会</span>

<span data-v-2505e99a="">代理猫猫</span>

<span data-v-2505e99a="">PS：谢谢猫猫老师帮我修bug呜呜呜呜呜呜呜</span>

<div class="opus-para-pic center" id="bkmrk--13"><div class="b-img"><picture class="b-img__inner"><source srcset="//i1.hdslb.com/bfs/new_dyn/7a577e4c240d7c2521839e4da6e79da134693506.png@1192w.avif" type="image/avif"><source srcset="//i1.hdslb.com/bfs/new_dyn/7a577e4c240d7c2521839e4da6e79da134693506.png@1192w.webp" type="image/webp"></source></source></picture></div></div><div class="opus-para-pic center" id="bkmrk--14"><div class="b-img"><picture class="b-img__inner"><source srcset="//i1.hdslb.com/bfs/new_dyn/dfb83a444b4953820b1239bbf025c0a334693506.png@1192w.avif" type="image/avif"><source srcset="//i1.hdslb.com/bfs/new_dyn/dfb83a444b4953820b1239bbf025c0a334693506.png@1192w.webp" type="image/webp"></source></source></picture></div></div><div class="opus-para-pic center" id="bkmrk--15"><div class="b-img"><picture class="b-img__inner"><source srcset="//i1.hdslb.com/bfs/new_dyn/c3f7888b6bda17fc29f84c1b8acd4ba534693506.png@1192w.avif" type="image/avif"><source srcset="//i1.hdslb.com/bfs/new_dyn/c3f7888b6bda17fc29f84c1b8acd4ba534693506.png@1192w.webp" type="image/webp"></source></source></picture></div></div>