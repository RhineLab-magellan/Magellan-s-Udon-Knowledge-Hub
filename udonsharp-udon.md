# UdonSharp邪修优化指南——Udon实战案例库

本指南会收录一些个人比较喜欢的用得上的代码块，说其是邪修的原因其实是因为实现的方式和效果大概率不会是最佳实践，甚至看起来有点逆天。希望您看了之后会有所收获，如果您有一些独门邪修技法，欢迎联系我写进去

# 一、用 Toggle 替代 Button 来实现按钮传参

还在傻乎乎给每一个按钮配一个Udon脚本来实现传参？邪修教你用Toggle替代Button来实现按钮传参

众所周知，在 VRChat 的 Udon 开发中，按钮默认只能通过 `SendCustomEvent` 方法向 `UdonBehaviour` 发送无参事件。但实际开发中，我们常需按钮携带参数 —— 最典型的场景就是 “批量相同功能的按钮”（如道具选择、场景切换按钮），需要通过参数（如按钮 ID）确定具体是哪个按钮触发了事件。

我个人选择的方案是，结合 Toggle 组件与 Udon 代码：在 Toggle 被触发时，遍历所有 Toggle 的 `IsOn` 属性，此时**该 Toggle 在数组中的位置**就是其携带的基础参数。若需更复杂的参数，我们可额外创建一个与 Toggle 数组索引对应的参数数组，以 Toggle 在数组中的位置为索引获取参数；也可通过解析 `Toggle.name`（如在名称中嵌入 ID 或标识），实现按钮携带参数的需求。

使用这个方案会有以下的优点：

### 1、降低管理难度

常规做法需要为每一个按钮挂载独立的 Udon 脚本转发参数，或在主脚本中添加多个独立的 Public 事件与对应方法，才能实现按钮传参。这个方法能在可接受的性能消耗下解决该问题，后期若需增加按钮数量，只需扩展数组即可，无需复杂操作，对需要批量复制按钮的场景格外友好。

### 2、降低Udon虚拟机数量

本方案无需为每个按钮单独配备 Udon 脚本，能显著减少 Udon 脚本的总数量，进而降低 Udon 虚拟机的占用量。

<div class="auto-hide-last-sibling-br paragraph-JOTKXA paragraph-element br-paragraph-space" id="bkmrk-%E4%BD%BF%E7%94%A8%E8%BF%99%E4%B8%AA%E6%96%B9%E6%B3%95%E9%9C%80%E8%A6%81%E6%B3%A8%E6%84%8F%E4%BB%A5%E4%B8%8B%E5%87%A0%E7%82%B9%EF%BC%9A">使用这个方法需要注意以下几点：</div><div class="container-utlnW2 md-box-line-break wrapper-d0Cc1k undefined" id="bkmrk-">  
</div>1. <div class="auto-hide-last-sibling-br paragraph-JOTKXA paragraph-element br-paragraph-space">若场景中 Toggle 数量过多，仍会出现管理困难的问题，可通过按功能分区（如将按钮分为 “场景切换组”“道具选择组”）的方式解决。</div>
2. <div class="auto-hide-last-sibling-br paragraph-JOTKXA paragraph-element br-paragraph-space">性能损耗会随 Toggle 数量增加而上升，实际使用时需关注性能损耗，可通过额外脚本按功能分区，实现性能与便捷性的平衡。</div>

以下为示例代码：

```c#
//第0事件——初始化
public Toggle[] Toggles;
private start()
  {
  for (int i = 0; i < Toggles.Length; i++)
            {
                Toggles[i].SetIsOnWithoutNotify(false);
            }
  }

  //计算是哪个按钮被按下
private int CheackToggleIndex()
  {
  int ToggleIndex = 0;
  for (int i = 0; i < DishesToggle.Length; i++)
            {
                if (DishesToggle[i].isOn)
                {
                    ToggleIndex = i;
                    Toggles[i].SetIsOnWithoutNotify(false);
                  break;
                }
            }
  }

  //按钮触发
public void OnToggleUse()
  {
  int ToggleIndex = CheackToggleIndex();
  
  }
```

# 二、请在变量改变时通知其他的需要这个变量的脚本，而不是让他们在Update中不断检查

还在滥用Update来获取其他脚本变量？邪修助你在实时和性能之间取得平衡。

这个问题的核心需求其实很明确 —— 我们需要尽可能实时地获取其他脚本的变量信息。比如在 UdonChips 钱包系统中，若要为其开发全新插件，就必须实时更新钱包余额，才能确保消费、查询等关联模块正常运行。对此，UdonChips 的官方解决方案是：在Update方法中，通过每一帧检查钱包脚本的指定变量与本地备份是否存在差异，以此实现变量的实时同步。

这种方案虽能满足基础需求，但高频的帧级轮询会带来不必要的性能开销。为优化性能表现，我对该逻辑做了针对性改造，核心是将 “其他模块依赖Update轮询” 的模式，替换为 “主脚本主动推送的静态事件”，具体解决方案如下：

<div class="auto-hide-last-sibling-br paragraph-JOTKXA paragraph-element br-paragraph-space" id="bkmrk-%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88%EF%BC%9A">解决方案：</div><div class="container-utlnW2 md-box-line-break wrapper-d0Cc1k undefined" id="bkmrk-">  
</div>1. **注册阶段**：子脚本（如钱包插件的消费模块、余额显示模块）在初始化时，主动向主脚本（如 UdonChips 钱包核心脚本）注册自身的 “变量更新回调事件”，完成 “订阅” 关联；
2. **触发阶段**：主脚本仅在自身指定变量（如钱包余额）发生实际更改时，才触发预设的**静态事件**，并向所有已注册的子脚本主动推送变量更新信号；
3. **接收阶段**：子脚本无需再通过`Update`轮询，只需预先按照规范定义好 “接收更新信号的处理逻辑”，即可在收到主脚本推送时，实时响应并获取最新变量信息。

主脚本（核心脚本）：

```c#

//注册
public void RegisterOnMoneyChanged(GameObject target)
		{
			// 检查目标是否为UdonBehaviour
			UdonBehaviour udonBehaviour = target.GetComponent<UdonBehaviour>();
			if (udonBehaviour == null)
			{
				Debug.LogError("目标 GameObject 没有 UdonBehaviour 组件");
				return;
			}
			if (Target == null)
			{
				Target = new UdonBehaviour[0];
			}
			UdonBehaviour[] newTarget = new UdonBehaviour[Target.Length + 1];
			for (int i = 0; i < Target.Length; i++)
			{
				newTarget[i] = Target[i];
			}
			newTarget[Target.Length] = udonBehaviour;
			Target = newTarget;
			//注册Debug
			if (NewCodeDebugMode)
			{ Debug.Log("注册成功：" + target.name); }
		}

//添加-减少参数
public void ChangeMoneyAndUpdate(float Value)
		{
			money += Value;
			NotifyMoneyChanged();
		}

//发送事件
private void NotifyMoneyChanged()
		{
			if (NewCodeDebugMode)
			{ Debug.Log("金额发生更改"); }
			for (int i = 0; i < Target.Length; i++)
			{
				Target[i].SendCustomEvent("UdonChipsMoneyChanged");
			}
		}
```

子脚本（功能脚本）

```c#
public void start()
      {
        //获取UdonChips组件
        udonChips = GameObject.Find("UdonChips").GetComponent<UdonChips>();
        //注册为需要更新的组件
        udonChips.RegisterOnMoneyChanged(this.gameObject);
        }  

public void UdonChipsMoneyChanged()
        {
          currentMoney = udonChips.money;
        }
```

# 三、使用简易对象池优化生成组件时的性能

停下你每次都重新生成的代码，加入一个简易的对象池来优化生成组件时的性能。

在开始这篇 “邪修” 风格的方法教程前，我们先补充一组关键数据：

- Unity 中生成一个Prefab 实例，通常需要消耗约 1ms 时间；
- 而在此基础上，若该 Prefab 挂载了 Udon 脚本，Udon 为其生成对应的Udon 虚拟机时，还需额外消耗约 1ms 时间。

这组数据大家可自行测试验证。不难看出，生成 Udon 虚拟机的性能开销相当显著 —— 但实际开发中，很多场景（比如少量、高频复用的简单对象，如临时弹窗、交互道具）并不需要一套功能完备、逻辑复杂的标准对象池。

这种情况下，我更推荐在脚本中直接实现一个简易对象池，以此针对性优化 Udon 虚拟机的 “重复创建 - 销毁” 开销，避免频繁生成新虚拟机带来的性能波动。

这个对象池的写法也很简单，如下：

```c#
//以下代码是从实践中复制的，命名啥的就先不管吧，你们看看就好.jpg

//
private int DishQuantity;

public void UpdateShoppingTrolley()
  {
  GeneratedShoppingTrolley(DishQuantity);
  InitShoppingTrolley(DishQuantity);
  }

//生成需要的模块
private void GeneratedShoppingTrolley(int DishQuantity)
        {
            //1:检查需要的按钮数量
            if (DishQuantity > ShoppingTrolleyButtons.Length)
            {
                //创建新数组
                GameObject[] NewShoppingTrolleyButtons = new GameObject[DishQuantity];
                //复制旧数组
                for (int i = 0; i < ShoppingTrolleyButtons.Length; i++)
                {
                    NewShoppingTrolleyButtons[i] = ShoppingTrolleyButtons[i];
                }
                //创建新按钮
                for (int i = ShoppingTrolleyButtons.Length; i < DishQuantity; i++)
                {
                    NewShoppingTrolleyButtons[i] = Instantiate(ShoppingTrolleyButtonPrefab, ShoppingTrolleyButtonParent);
                }
                //替换旧数组
                ShoppingTrolleyButtons = NewShoppingTrolleyButtons;
                //更新购物车按钮
            }
            else if (DishQuantity < ShoppingTrolleyButtons.Length)
            {
                //创建新数组
                GameObject[] NewShoppingTrolleyButtons = new GameObject[DishQuantity];
                //复制旧数组
                for (int i = 0; i < DishQuantity; i++)
                {
                    NewShoppingTrolleyButtons[i] = ShoppingTrolleyButtons[i];
                }
                //删除多余按钮
                for (int i = DishQuantity; i < ShoppingTrolleyButtons.Length; i++)
                {
                    Destroy(ShoppingTrolleyButtons[i].gameObject);
                }
                //更新数组
                ShoppingTrolleyButtons = NewShoppingTrolleyButtons;
            }

  private void InitShoppingTrolley()
    {
    for (int i = 0; i < DishQuantity; i++)
            {
                int ChooseDish = ChooseDishes[i];
                //获取按钮组件
                UdonBehaviour ButtonComponent = ShoppingTrolleyButtons[i].GetComponent<UdonBehaviour>();
                if (ButtonComponent == null)
                {
                    DebugTextUpdate($"购物车按钮组件获取失败：请联系管理员并重新进入世界");
                    continue;
                }
                //设置按钮索引
                ButtonComponent.SetProgramVariable("Index", i);
                ButtonComponent.SetProgramVariable("PlayerUseMenu", this);
                ButtonComponent.SetProgramVariable("DishIndex", ChooseDish);
                //设置按钮名称
                ButtonComponent.SetProgramVariable("DishName", DishName[ChooseDish]);
                //设置按钮数量
                ButtonComponent.SetProgramVariable("DishQuantity", DishPrice[ChooseDish]);
                ButtonComponent.SendCustomEvent("InitButton");
            }

    }
```

子脚本则通过 `InitButton` 事件进行初始化，即可完成复用。

# 四.将复杂数学运算卸载到Animator中来实现多线程计算

已知Udon是一款单线程低效率的编程语言，这种低效率同时也体现在Udon对于复杂数学运算上。如果仅仅只是加减乘除的话可以忽略不计，但是若是计算傅利叶变换，贝塞尔曲线等复杂数学的时候，我们必须要进行优化。

好在，Udon允许我们与animator进行交互，那么我们就可以将数学运算卸载到animator中并且在等待animator运行完毕后获取到运行结果。

其优点在于：

1:多线程运算，量大管饱

2:运算速度更快

缺点在于：

1:需要等待一段时间后才可以拿到运算结果

2:十分复杂，在运用和设计上都是

如果您想知道具体的原理是什么，请访问这个链接

[https://vrc.school/docs/Other/Advan](https://vrc.school/docs/Other/Advanced-BlendTrees/)[ced-BlendTrees/](https://vrc.school/docs/Other/Advanced-BlendTrees/)

本邪修方案由猫咪老师赞助推出

# 五：如何优雅的传递VRCPlayerAPI

已知VRChatPlayerAPI是指向某一名玩家的指针，在程序设计中，我们也通过这个来代表某位玩家。  
在网络同步的时候，我们经常遇到需要传输比如玩家名称等情况，因为我们无法直接传输VRCPlayerAPI，所以更多的人倾向直接在本地获取DisplayName后转成String发送——虽然这无伤大雅，但是你应该指导这对VRC孱弱的网络带宽来说也是不小的冲击。

解决方案很简单——使用PlayerID

VRChat 可以直接通过

```c#
VRCPlayerApi.GetPlayerId();
```

来获得一个数字，这个数字通常代表指定玩家是第几个进入游戏的。可以认为是VRChat当前实例的唯一认证ID。

你可以通过

```c#
VRCPlayerApi.GetPlayerById();
```

方法将ID重新转换成VRCPlayerAPI。

这样就可以衡量传输玩家所带来的带宽压力了。

# 六：就像Avatar一样，我推荐你在使用Udon做网络同步的时候也能考虑到参数压缩

特别是在使用类似PlayerData这种所有脚本，玩家都能收发信息的VRChat中转站的时候，使用参数压缩大法可以让你在限制之下拥有更多的功能以及更好的性能表现，比如：

当你有16个Bool，但是PlayerData只支持单个数据？难道要写16个键值对吗？那也太垃圾了！

使用一个UShort，将16个布尔按照Bit的形式排列在UShort上，即可在一个UShort数据上容纳16个布尔。

如果是一个数字呢？

先规定好每一个数字的最大值，并且根据最大值找位数：

如果一个数字最大值只有0-1？

那一个数字就只用占1bit即可

如果一个数字最大值只有0-15?

那一个数字占用4Bit即可

以此类推

你问如果有负数怎么办？计算机怎么学的，第一位当符号位不就好了。

然后根据你所需要的数据的数量，从以下位数中选取一个当作载体。

1：Ushort ：16bit

2：Uint : 32bit

3: Ulong : 64bit

然后写好相互转换的代码即可: