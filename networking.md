# Networking从入门到入土

本书将会从基础的Udon文档中对networking的Api，限制，功能开始，用底层开始讲解如何为乌冬代码设计合格的网络同步方案

——

本文分为三个部分

1:基础部分：由基础网络相关的udon文档开始，讲解networking系统的整体底层框架。

2:进阶技巧：如何利用网络来设计出巧妙的同步系统，包含多个我个人任在使用的特殊设计方案。

3:设计风格：多种我个人任在使用的网络同步设计方案，用于帮助构思插件设计的时候的网络同步部分的设计。

# 基础信息篇

本篇章主要讲解一些NetWorking工作的相关内容。  
具体内容请看：[Networking | VRChat Creation](https://creators.vrchat.com/worlds/udon/networking/)<span> 官方文档</span>

# Networking 基础知识

## 摘要

```
——
1. 同步变量（Synced Variables）
2. 同步时机与生命周期（Sync Timing & Lifecycle）
3. 同步目标与网络事件（Sync Target & Network Events）
4. 所有权机制（Ownership）
5. 持久化机制（Persistence）
6. 网络组件（Network Components）
7. 反外挂处理规范（Anti-Cheat）
——
附录：官方文档链接

```

---

## 1. 同步变量（Synced Variables）

### 核心问题

> "我要同步什么数据？"

---

### 1.1 支持的类型

<div class="table-wrapper" id="bkmrk-%E7%B1%BB%E5%9E%8B%E5%88%86%E7%B1%BB-%E5%85%B7%E4%BD%93%E7%B1%BB%E5%9E%8B-%E8%AF%B4%E6%98%8E-%E5%B8%83%E5%B0%94-bool"><table style="width: 100%; height: 274.541px;"><thead><tr style="height: 29.4583px;"><th style="width: 9.64957%; height: 29.4583px;">类型分类</th><th style="width: 33.2416%; height: 29.4583px;">具体类型</th><th style="width: 57.0691%; height: 29.4583px;">额外说明</th></tr></thead><tbody><tr style="height: 30.7812px;"><td style="width: 9.64957%; height: 30.7812px;">布尔</td><td style="width: 33.2416%; height: 30.7812px;">`bool`</td><td style="width: 57.0691%; height: 30.7812px;"> </td></tr><tr style="height: 46.25px;"><td style="width: 9.64957%; height: 46.25px;">文字</td><td style="width: 33.2416%; height: 46.25px;">`char`、`string`、`VRCUrl`</td><td style="width: 57.0691%; height: 46.25px;">字符串为 2 字节/字符；Continuous 模式下实际可同步字符数受 200 字节限制（≈100 字符），精确上限需实测</td></tr><tr style="height: 48.8958px;"><td style="width: 9.64957%; height: 48.8958px;">整数</td><td style="width: 33.2416%; height: 48.8958px;">`byte`、`sbyte`、`short`、`ushort`、`int`、`uint`、`long`、`ulong`</td><td style="width: 57.0691%; height: 48.8958px;">优先使用短类型节省带宽</td></tr><tr style="height: 30.7812px;"><td style="width: 9.64957%; height: 30.7812px;">浮点</td><td style="width: 33.2416%; height: 30.7812px;">`float`、`double`</td><td style="width: 57.0691%; height: 30.7812px;">  
</td></tr><tr style="height: 29.4583px;"><td style="width: 9.64957%; height: 29.4583px;">数组</td><td style="width: 33.2416%; height: 29.4583px;">以上所有类型的数组 `[]`</td><td style="width: 57.0691%; height: 29.4583px;">数组元素值改变**不**触发 OnVariableChanged</td></tr><tr style="height: 29.4583px;"><td style="width: 9.64957%; height: 29.4583px;">元组</td><td style="width: 33.2416%; height: 29.4583px;">`Vector2`、`Vector3`、`Vector4`、`Quaternion`</td><td style="width: 57.0691%; height: 29.4583px;">位置、旋转、缩放常用</td></tr><tr style="height: 29.4583px;"><td style="width: 9.64957%; height: 29.4583px;">颜色</td><td style="width: 33.2416%; height: 29.4583px;">`Color`、`Color32`</td><td style="width: 57.0691%; height: 29.4583px;">  
</td></tr></tbody></table>

</div><p class="callout info">💡 **说明**：官方文档仅明确 "string 为 2 字节/字符"，未给出 Continuous 模式下的确切字符数上限。126 字符为社区经验值，实际可同步量受序列化开销影响，建议保守使用或实测确认。</p>

---

### 1.2 声明方式

基本声明

```csharp
[UdonSynced]
private int MyInt;

```

带 FieldChangeCallback

```csharp
[UdonSynced, FieldChangeCallback(nameof(SyncedToggle))]
private bool _syncedToggle;

public bool SyncedToggle
{
    set
    {
        _syncedToggle = value;
        // 值改变时的逻辑
    }
    get => _syncedToggle;
}

```

带插值模式

```csharp
[UdonSynced(UdonSyncMode.Linear)]   // 线性插值
private float _progress;

[UdonSynced(UdonSyncMode.Smooth)]   // 平滑插值
private float _position;

```

<div class="table-wrapper" id="bkmrk-%E6%8F%92%E5%80%BC%E6%A8%A1%E5%BC%8F-%E8%AF%B4%E6%98%8E-none-%E6%97%A0%E6%8F%92%E5%80%BC%EF%BC%8C%E5%80%BC%E6%94%B9%E5%8F%98"><table><thead><tr><th>插值模式</th><th>说明</th></tr></thead><tbody><tr><td>`None`</td><td>无插值，值改变时立即更新（默认）</td></tr><tr><td>`Linear`</td><td>线性插值</td></tr><tr><td>`Smooth`</td><td>平滑插值</td></tr><tr><td>`NotSynced`</td><td>不同步（默认，当不使用 `[UdonSynced]` 时）</td></tr></tbody></table>

</div><p class="callout info">💡 **用途**：插值模式适用于 Continuous 同步模式，通过平滑过渡减少视觉跳变。适用于位置、进度条等高频变化但需要视觉流畅的数据。</p>

---

### 1.3 SetProgramVariable 与 OnVariableChanged

**核心机制**：`SetProgramVariable` 会触发目标变量的 `OnVariableChanged` 事件。

<p class="callout warning">这里的事件在UdonSharp的对应就是 FieldChangeCallback() 特性。</p>

```
SetProgramVariable(variableName, value)
        ↓
  触发 OnVariableChanged(variableName)
        ↓
  输出：newValue（当前值）、oldValue（旧值）

```

**特性说明**：

<div class="table-wrapper" id="bkmrk-%E7%89%B9%E6%80%A7-%E8%AF%B4%E6%98%8E-%E8%A7%A6%E5%8F%91%E6%9D%A1%E4%BB%B6-%E8%B0%83%E7%94%A8-setpro"><table><thead><tr><th>特性</th><th>说明</th></tr></thead><tbody><tr><td>触发条件</td><td>调用 `SetProgramVariable` 或 `Set Variable`（sendChange 勾选）时</td></tr><tr><td>触发时机</td><td>**立即**在变量被写入时触发，而非所有变量同步完成后</td></tr><tr><td>适用范围</td><td>非同步变量和同步变量均可</td></tr><tr><td>接收同步时触发</td><td>当从其他玩家接收同步变量时也会触发</td></tr><tr><td>输出参数</td><td>`newValue`（设置后的值）、`oldValue`（设置前的值）</td></tr></tbody></table>

</div><p class="callout warning">**注意**：由于 OnVariableChanged 在变量写入时**立即**触发，而非所有同步变量更新完成后，因此在一个 OnVariableChanged 中读取另一个同步变量时，无法保证该变量已经是最新的同步值。如需在所有同步完成后执行逻辑，应使用 `OnDeserialization`。**来源**：[Network Components - OnVariableChanged](https://creators.vrchat.com/worlds/udon/networking/network-components/)</p>

**应用场景**：

- 监听其他脚本通过 `SetProgramVariable` 修改的变量
- 在变量变化时执行联动逻辑
- 实现跨脚本的状态同步通知

---

### 1.4 数组行为注意

<div class="table-wrapper" id="bkmrk-%E6%93%8D%E4%BD%9C-%E6%98%AF%E5%90%A6%E8%A7%A6%E5%8F%91-onvariablech"><table><thead><tr><th>操作</th><th>是否触发 OnVariableChanged</th></tr></thead><tbody><tr><td>数组大小改变</td><td>✅ 触发</td></tr><tr><td>数组元素值改变</td><td>❌ 不触发</td></tr></tbody></table>

</div>**如需监听元素变化，需要手动比较**

> 💡 **来源说明**：官方文档明确指出"changing the contents of an array does not trigger a change, because the array itself is still the same"。**来源**：[Network Components - OnVariableChanged](https://creators.vrchat.com/worlds/udon/networking/network-components/)

---

## 2. 同步时机与生命周期（Sync Timing &amp; Lifecycle）

### 核心问题

> "什么时候触发同步？同步前中后发生了什么？"

---

### 2.1 四种同步模式对比

<div class="table-wrapper" id="bkmrk-%E6%A8%A1%E5%BC%8F-%E8%A7%A6%E5%8F%91%E6%96%B9%E5%BC%8F-%E5%8F%AF%E9%9D%A0%E6%80%A7-%E9%80%9F%E5%BA%A6-%E5%B8%A6%E5%AE%BD-%E9%80%82%E7%94%A8"><table><thead><tr><th>模式</th><th>触发方式</th><th>可靠性</th><th>速度</th><th>带宽</th><th>适用场景</th></tr></thead><tbody><tr><td>**Continuous**</td><td>自动（Owner 值改变时）</td><td>不可靠（可能丢包）</td><td>快</td><td>低（全局 200 字节）</td><td>位置、旋转等高频数据</td></tr><tr><td>**Manual**</td><td>手动（调用 RequestSerialization）</td><td>可靠</td><td>较慢</td><td>高（单次可达 280,496 字节 ≈ 274KB）</td><td>配置、状态等需要确认的数据</td></tr><tr><td>**Event**</td><td>手动（调用网络事件）</td><td>可靠</td><td>慢</td><td>低</td><td>一次性事件、请求（详见第 3 节）</td></tr><tr><td>**Auto**</td><td>自动（系统级）</td><td>可靠</td><td>最快</td><td>-</td><td>Avatar Transform、VRCObjectSync</td></tr></tbody></table>

</div>---

### 2.2 Continuous 模式特性

**更新频率**：

<div class="table-wrapper" id="bkmrk-%E7%8A%B6%E6%80%81-%E6%9B%B4%E6%96%B0%E9%A2%91%E7%8E%87-%E6%AD%A3%E5%B8%B8-20-hz-%7E-5"><table><thead><tr><th>状态</th><th>更新频率</th></tr></thead><tbody><tr><td>正常</td><td>**20 Hz ~ 5 Hz**（每 50ms ~ 200ms 更新一次）</td></tr><tr><td>带宽阻塞时</td><td>可能降至 **数秒一次**（甚至更久）</td></tr></tbody></table>

</div><p class="callout warning">⚠️ **阻塞降速**：当网络或带宽受限时，VRChat 会主动降低 Continuous 同步频率以避免进一步拥塞。这意味着高频数据在网络不佳时可能严重滞后。而且这一过程无法手动干涉。</p>

**其他特性**：

- 不保证每次都发送（VRChat 可能跳过）
- 不保证到达（丢包不重发）
- **会触发 OnDeserialization**（更新后的行为，旧版本不触发）
- 需要搭配本地插值来平滑跳变

> ⚠️ **兼容性说明**：在某个版本更新后，Continuous 模式会触发 OnDeserialization。这是一个**破坏兼容性**的变更。如果代码依赖了"Continuous 不触发 OnDeserialization"的旧行为，需要注意调整。
> 
> TIPS：至于为什么没人感觉到了——因为以前的做法是在Update里面检查。

---

### 2.3 Manual 模式特性

- 需要显式调用 `RequestSerialization()`
- 触发时序：OnPreSerialization → 发送 → OnPostSerialization
- 接收端：变量更新 → OnDeserialization
- 常用模式：Owner 调用 RequestSerialization，非 Owner 通过事件通知 Owner

---

### 2.4 Event 模式概述

- 通过网络事件触发，不涉及变量同步
- 支持四种目标：All / Others / Owner / Self
- 分为基础事件和带参事件两种方式
- **详细说明见第 3 节：同步目标与网络事件**

---

### 2.5 Manual 同步完整时序

```
┌─────────────────────────────────────────────────────────────┐
│                      Manual 同步时序                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Owner 端：                                                  │
│                                                             │
│  1. 调用 RequestSerialization()                              │
│           ↓                                                 │
│  2. OnPreSerialization() 触发                                │
│           ↓                                                 │
│  3. 序列化所有 [UdonSynced] 变量                               │
│           ↓                                                 │
│  4. 发送网络包                                                │
│           ↓                                                 │
│  5. OnPostSerialization(result) 触发，返回发送结果             │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Non-Owner 端：                                              │
│                                                             │
│  1. 接收网络包                                                │
│           ↓                                                 │
│  2. 解包并更新所有 [UdonSynced] 变量                           │
│           ↓                                                 │
│  3. OnDesSerialization() 触发                                │
│                                                             │
└─────────────────────────────────────────────────────────────┘

```

---

### 2.6 序列化事件使用场景

<div class="table-wrapper" id="bkmrk-%E4%BA%8B%E4%BB%B6-%E8%A7%A6%E5%8F%91%E6%97%B6%E6%9C%BA-%E5%85%B8%E5%9E%8B%E7%94%A8%E9%80%94-onprese"><table><thead><tr><th>事件</th><th>触发时机</th><th>典型用途</th></tr></thead><tbody><tr><td>`OnPreSerialization()`</td><td>序列化前</td><td>额外数据打包、压缩</td></tr><tr><td>`OnPostSerialization(result)`</td><td>序列化后</td><td>检查发送结果、处理失败</td></tr><tr><td>`OnDeserialization()`</td><td>反序列化后</td><td>应用同步数据、通知其他脚本</td></tr><tr><td>`OnDeserialization(DeserializationResult)`</td><td>反序列化后（含元数据）</td><td>区分实时数据与存储恢复数据</td></tr></tbody></table>

</div><p class="callout info">💡 **TIP**：`OnDeserialization(DeserializationResult result)` 可通过 `result.isFromStorage` 判断数据来源——若为 `true`，则数据是从持久化存储恢复的，而非实时同步数据。可用于区分"新加入实例"与"从存储恢复"的场景。（不过一般都是依靠存储回复事件[OnPlayerRestored](https://creators.vrchat.com/worlds/udon/graph/event-nodes#onplayerrestored)来判断的）</p>

---

### 2.7 注意事项

- Manual 同步可被**任意玩家**触发，但只有 **Owner** 执行打包
- Owner 调用 RequestSerialization 后可**直接调用** OnDeserialization 保证本地一致性
- Continuous 模式**会触发** OnDeserialization（更新后的行为）
- **Late Joiner 自动同步**：新玩家加入实例时会自动接收所有同步变量的最新状态，无需手动调用 `RequestSerialization()`（适用于 Manual 和 Continuous 两种模式）

> 💡 **来源说明**：官方文档明确 "Late joiners receive the latest state of the variable, just like other users in the instance. This works regardless of sync type, you do not need to manually call RequestSerialization when a user joins." **来源**：[Network Variables](https://creators.vrchat.com/worlds/udon/networking/variables/)

---

## 3. 同步目标与网络事件（Target &amp; Events）

### 核心问题

> "同步发给谁？如何发送网络事件？"

---

### 3.1 NetworkEventTarget 四种目标

<div class="table-wrapper" id="bkmrk-%E7%9B%AE%E6%A0%87-%E8%AF%B4%E6%98%8E-%E5%85%B8%E5%9E%8B%E7%94%A8%E6%B3%95-all-%E6%89%80%E6%9C%89%E7%8E%A9%E5%AE%B6%EF%BC%88"><table><thead><tr><th>目标</th><th>说明</th><th>典型用法</th></tr></thead><tbody><tr><td>`All`</td><td>所有玩家（包括发送者）</td><td>广播状态变更</td></tr><tr><td>`Others`</td><td>除发送者外的所有玩家</td><td>本地操作后通知他人</td></tr><tr><td>`Owner`</td><td>对象的所有者</td><td>请求-响应模式</td></tr><tr><td>`Self`</td><td>仅发送者自己</td><td>绕过速率限制、本地触发</td></tr></tbody></table>

</div>> ⚠️ **Event Splitting 机制**：当参数数据 &gt; 1024 字节时，单次 `SendCustomNetworkEvent` 会被拆分为多个内部事件，每个分片独立计入限速队列。例如配置限速为 2 events/s，但发送了 2048 字节数据，则实际速率仅为 1 event/s。（反复强调）

---

### 3.2 基础网络事件

```csharp
// 触发无参网络事件
SendCustomNetworkEvent(NetworkEventTarget.All, "OnStateChanged");

```

- 方法：`SendCustomNetworkEvent(target, eventName)`
- 不支持参数传递
- 适用于简单的事件通知

<p class="callout danger">注意：所有的事件只要没有在开头使用\_，都会被认为是网络同步事件，这个在VRChat官方标记为&lt;历史遗留兼容&gt;</p>

---

### 3.3 带参网络事件

```csharp
// 触发带参网络事件
NetworkCalling.SendCustomNetworkEvent(
    (IUdonEventReceiver)this,    // 重要：必须指定目标 Behaviour 实例（UdonBehaviour替换This）
    NetworkEventTarget.Owner,    // 事件对象
    "OnPlayerRequest",           // 事件名称
    playerId,                    // 参数
    requestType
);

```

- 方法：`NetworkCalling.SendCustomNetworkEvent((IUdonEventReceiver)udonBehaviour, target, methodName, args...)`
- 支持最多 **8 个**参数
- 需要配合 `[NetworkCallable]` 使用
- 必须包含目标 UdonBehaviour 实例作为第一个参数

---

### 3.4 NetworkCallable 声明

```csharp
[NetworkCallable]
public void OnPlayerRequest(int playerId, int requestType)
{
    // 处理来自其他玩家的请求
    // 相当于"服务端函数"
}

```

**注意事项**：

- 方法需要 `[NetworkCallable]` 标记
- 调用方使用 `NetworkCalling.SendCustomNetworkEvent(target, "MethodName", args...)`
- Self 目标可绕过速率限制（热知识—UdonSharp本来就可以实现带参函数调用，你没必要这么做）

**速率限制配置**：

```csharp
[NetworkCallable(maxEventsPerSecond: 10)]
public void OnPlayerRequest(int playerId, int requestType) { }

```

<div class="table-wrapper" id="bkmrk-%E9%85%8D%E7%BD%AE%E9%A1%B9-%E5%80%BC-%E9%BB%98%E8%AE%A4%E9%80%9F%E7%8E%87-5-events%2F"><table><thead><tr><th>配置项</th><th>值</th></tr></thead><tbody><tr><td>默认速率</td><td>5 events/s</td></tr><tr><td>最大可配置速率</td><td>100 events/s</td></tr></tbody></table>

</div>> ⚠️ **重要提示**：网络事件存在全局总限速（约 100 events/s，不可配置）。参数数据 &gt; 1024 字节时会被拆分为多个内部事件（Event Splitting），每个分片独立计入限速。建议事件参数尽量精简。

---

### 3.5 请求-响应模式示例（伪中心化网络架构）

```
玩家 A                      Owner                      玩家 B
   │                           │                           │
   │──Request(id=5)───────────>│                           │
   │                           │                           │
   │                           │ 处理请求                   │
   │                           │ 更新同步变量                │
   │                           │ RequestSerialization()    │
   │                           │                           │
   │<───同步状态更新─────────────│───同步状态更新─────────────>│
   │    (OnDeserialization)    │    (OnDeserialization)    │

```

**说明**：

- 玩家 A 通过带参网络事件向 Owner 发送请求
- Owner 处理请求后更新同步变量，并调用 `RequestSerialization()`
- **所有玩家**（包括请求方 A）通过 `OnDeserialization` 收到状态更新
- 不存在"对 A 响应、对 B 广播"的区别——同步变量对全员生效

---

## 4. 所有权机制（Ownership）

### 核心问题

> "谁来主导这个过程？"

---

### 4.1 Owner 的职责

<div class="table-wrapper" id="bkmrk-%E5%90%8C%E6%AD%A5%E6%A8%A1%E5%BC%8F-owner-%E8%81%8C%E8%B4%A3-contin"><table><thead><tr><th>同步模式</th><th>Owner 职责</th></tr></thead><tbody><tr><td>Continuous</td><td>值改变时自动同步给其他玩家</td></tr><tr><td>Manual</td><td>当调用 `RequestSerialization()` 时打包并发送数据</td></tr><tr><td>Event</td><td>作为 `Owner` 目标的接收方</td></tr></tbody></table>

</div>---

### 4.2 Networking 常用属性/方法

<div class="table-wrapper" id="bkmrk-api-%E7%B1%BB%E5%9E%8B-%E8%AF%B4%E6%98%8E-networking"><table><thead><tr><th>API</th><th>类型</th><th>说明</th></tr></thead><tbody><tr><td>`Networking.IsOwner(gameObject)`</td><td>属性</td><td>判断是否为 Owner</td></tr><tr><td>`Networking.SetOwner(player, gameObject)`</td><td>方法</td><td>请求转移 Owner</td></tr><tr><td>`Networking.IsMaster`</td><td>属性</td><td>是否为 Master（全局主持人）</td></tr><tr><td>`Networking.IsInstanceOwner`</td><td>属性</td><td>是否为实例创建者（社团实例永远返回 false）</td></tr><tr><td>`Networking.InstanceOwner`</td><td>属性</td><td>实例创建者的 Player API 对象（社团/Public 实例可能为 null）</td></tr><tr><td>`Networking.LocalPlayer`</td><td>属性</td><td>本地玩家（需要缓存，高延时）</td></tr><tr><td>`Networking.IsNetworkSettled`</td><td>属性</td><td>数据已准备好发送</td></tr><tr><td>`Networking.IsClogging`</td><td>属性</td><td>是否超过带宽阈值</td></tr><tr><td>`Networking.SimulationTime`</td><td>属性</td><td>最后更新时间，用于计算延迟</td></tr><tr><td>`Networking.Master`</td><td>属性</td><td>当前 Master 的 Player API 对象（始终有效）</td></tr><tr><td>`NetworkCalling.CallingPlayer`</td><td>属性</td><td>当前网络调用的发起者（仅在网络调用上下文中有效）</td></tr><tr><td>`NetworkCalling.InNetworkCall`</td><td>属性</td><td>当前是否处于网络调用上下文中</td></tr></tbody></table>

</div>**SimulationTime 用途示例**：

```csharp
// 计算玩家网络延迟
float latency = Time.realtimeSinceStartup - Networking.LocalPlayer.SimulationTime;

```

> 💡 **InstanceOwner vs IsInstanceOwner**：两者均针对实例创建者，但 `IsInstanceOwner` 返回 bool，`InstanceOwner` 返回 Player 对象。社团实例（Group）和 Public 实例中 `IsInstanceOwner` 永远返回 false，`InstanceOwner` 始终为 null。

---

### 4.3 Owner 转移流程

```
   请求方                    当前 Owner
      │  
   ┌─ OnOwnershipRequest() ─┐
   │  返回 true → 允许转移    │
   │  返回 false → 拒绝转移   │
   └────────────────────────┘│
      │──── SetOwner() ─────────>│
      │                          │
      │              ┌─ OnOwnershipRequest() ─┐
      │              │  返回 true → 允许转移  │
      │              │  返回 false → 拒绝转移 │
      │              └───────────────────────┘
      │                              │
      │<─── OnOwnershipTransferred ──│
      │                              │

```

---

### 4.4 OnOwnershipRequest 完整流程

> 以下流程适用于**脚本定义了 OnOwnershipRequest** 的情况。未定义时，默认允许所有转移。

```
请求方                    当前 Owner
   │                          │
┌─ OnOwnershipRequest() ─┐    │
│  请求方执行             │     │
│  返回 true → 继续       │    │
│  返回 false → 转移取消  │     │
└───────────────────────┘     │
   │──── SetOwner() ─────────>│
   │                          │
   │              ┌─ OnOwnershipRequest() ─┐
   │              │  当前 Owner 执行        │
   │              │  返回 true → 接受      │
   │              │  返回 false → 拒绝     │
   │              └───────────────────────┘
   │                          │
   │                          │
   │<── OnOwnershipTransferred ─│
   │     （原 Owner + 其他玩家） │

```

**关键说明**：

- **请求方**返回 `true` 才继续进入后续流程；返回 `false` 时整个转移在此时取消，Owner 的 OnOwnershipRequest 不会被调用
- **Owner** 返回 `false` 或未返回值时，转移被拒绝，请求方收到 OnOwnershipTransferred 但 Owner 不变
- **Owner 转移给他人时**，跳过第 4 步（被转移方不能拒绝）
- **预确认特性**：请求方在 Owner 最终确认之前就会收到 OnOwnershipTransferred，此时转移可能因 Owner 拒绝而回滚
- 拒绝转移时：请求方仍会收到 `OnOwnershipTransferred`，但此时原 Owner 仍是所有者

<p class="callout warning">SetOwner事件的实际速率 = LocalPlayer网络延迟 + Owner网络延迟 + OnOwnershipRequest   
在SetOwner结束之前，整个UdonVM会进入阻塞状态。暂时不知道阻塞状态影响的是整个游戏线程还是单个Udon。</p>

---

## 5. 持久化机制（Persistence）

### 核心问题

> "如何跨会话保存数据？"

---

### 5.1 两种持久化方案对比

<div class="table-wrapper" id="bkmrk-%E6%96%B9%E6%A1%88-%E7%BB%84%E4%BB%B6-%E6%95%B0%E6%8D%AE%E4%BD%8D%E7%BD%AE-%E8%AE%BF%E9%97%AE%E6%96%B9%E5%BC%8F-%E5%AE%B9%E9%87%8F%E9%99%90%E5%88%B6"><table><thead><tr><th>方案</th><th>组件</th><th>数据位置</th><th>访问方式</th><th>容量限制</th></tr></thead><tbody><tr><td>**脚本持久化**</td><td>VRCEnablePersistence</td><td>挂载的脚本</td><td>脚本内部</td><td>100KB/玩家（PlayerObject，可压缩存储）</td></tr><tr><td>**数据库持久化**</td><td>PlayerData</td><td>全局键值对</td><td>任意脚本</td><td>100KB/玩家（PlayerData，可压缩存储）</td></tr></tbody></table>

</div>> 💡 **压缩说明**：VRChat 使用压缩格式存储数据。如果数据易于压缩，实际可存储量可能超过 100KB（被压缩至 100KB）。

---

### 5.2 脚本持久化（VRCEnablePersistence）

- 配合 `VRCPlayerObject` 使用效果最佳
- 持久化数据必须是**同步变量**
- 进入实例时自动恢复
- 需要在 **OnPlayerLeft 之前**保存

---

### 5.3 数据库持久化（PlayerData）

```csharp
// 存储
PlayerData.SetString("key", "value");

// 读取
string value = PlayerData.GetString("key");

// 事件
OnPlayerDataUpdated()    // 数据更新时触发
OnPlayerRestored()       // 数据恢复完成时触发

```

<p class="callout info">PlayerData因为其以下特性也常被当作全局数据库使用：  
1：无需引用  
2：标准化读取  
3：标准更新事件  
4：可以跨玩家读取（其他玩家可读数据）</p>

---

### 5.4 PlayerObject vs PlayerData

<div class="table-wrapper" id="bkmrk-%E9%80%89%E6%8B%A9%E4%BE%9D%E6%8D%AE-%E9%80%89-playerobject-"><table><thead><tr><th>选择依据</th><th>选 PlayerObject</th><th>选 PlayerData</th></tr></thead><tbody><tr><td>数据结构</td><td>复杂、多个字段</td><td>简单、键值对</td></tr><tr><td>访问范围</td><td>脚本内部</td><td>全局可访问</td></tr><tr><td>多实例</td><td>每个玩家独立</td><td>共享</td></tr><tr><td>持久化需求</td><td>高</td><td>一般</td></tr></tbody></table>

</div>---

## 6. 网络组件（Network Components）

### 核心问题

> "有哪些现成的网络组件可用？"

---

### 6.1 VRC Object Sync

**用途**：自动同步 Transform 和 Rigidbody，不占用 Udon 带宽。

<div class="table-wrapper" id="bkmrk-%E5%8A%9F%E8%83%BD-%E8%AF%B4%E6%98%8E-flagdiscontinu"><table><thead><tr><th>功能</th><th>说明</th></tr></thead><tbody><tr><td>FlagDiscontinuity</td><td>禁用平滑同步，运动不连续</td></tr><tr><td>Set/Get Gravity</td><td>同步重力值（非 Owner 必须从组件获取）</td></tr><tr><td>Set/Get Kinematic</td><td>同步运动学状态</td></tr><tr><td>Respawn()</td><td>重置到初始位置，清除速度</td></tr></tbody></table>

</div>**Respawn() 详细行为**：

1. 设置 `DiscontinuityHint = true`，使后续变化立即生效
2. 设置 `transform.position` → 初始位置
3. 设置 `transform.rotation` → 初始旋转
4. 如有 Rigidbody：同时设置 `velocity` 和 `angularVelocity` 为 `Vector3.zero`，并设置 `rigidbody.position` 和 `rigidbody.rotation`

> ⚠️ **注意**：
> 
> - 同一物体的 Udon 组件必须使用 **Continuous** 模式
> - **多组件降级规则**：如果同一个 GameObject 上有多个 UdonBehaviour，其中一个使用 Manual，另一个使用 Continuous，则两者都会被降级为 Manual 模式工作

---

### 6.2 VRC Object Pool

**用途**：对象池管理，自动同步启用状态。

<div class="table-wrapper" id="bkmrk-%E6%96%B9%E6%B3%95-%E8%AF%B4%E6%98%8E-trytospawn%28%29-%E5%B0%9D"><table><thead><tr><th>方法</th><th>说明</th></tr></thead><tbody><tr><td>`TryToSpawn()`</td><td>尝试启用一个对象，返回对象或 Null</td></tr><tr><td>`Return()`</td><td>禁用对象（仅 Owner 可调用）</td></tr></tbody></table>

</div>---

### 6.3 VRCPlayerObject

**用途**：为每个玩家生成独立的 GameObject 副本。

- Owner 固定为对应玩家，不可转移
- 可使用带参事件与 Owner 通信
- 使用 VRChat 保留的 NetworkID，不占用同步带宽

---

## 7. 反外挂处理规范

### 核心问题

> "如何保护网络同步不被外挂干扰？"

<p class="callout warning">本段方法未验证可行性，里面涉及的方法是根据外挂客户端的一些行为进行针对性处理的方案，不保证任何效果。其中部分来自于VRChat官方推荐方案，部分来自于UdonVM特性处理。使用时注意辨别。</p>

---

### 7.1 网络事件命名规范

**原则**：区分网络用途和本地用途的 Public 事件。

<div class="table-wrapper" id="bkmrk-%E4%BA%8B%E4%BB%B6%E7%B1%BB%E5%9E%8B-%E5%91%BD%E5%90%8D%E8%A7%84%E5%88%99-%E7%A4%BA%E4%BE%8B-%E7%BD%91%E7%BB%9C%E7%94%A8%E9%80%94-%E6%99%AE%E9%80%9A"><table><thead><tr><th>事件类型</th><th>命名规则</th><th>示例</th></tr></thead><tbody><tr><td>网络用途</td><td>普通命名</td><td>`OnPlayerJoin()`、`RequestSync()`</td></tr><tr><td>本地用途</td><td>下划线前缀 `_`</td><td>`_OnButtonClick()`、`_UpdateState()`</td></tr></tbody></table>

</div>**原因**：VRChat 外挂通过扫描 UdonCache 中的 Public 方法列表来发现可调用事件。添加 `_` 前缀可以让外挂无法通过简单的枚举找到本地事件。

**示例**：

```csharp
// ✅ 网络事件（可被外部调用）
public void OnPlayerJoin(VRCPlayerApi player) { }

// ❌ 本地事件（不应被外挂发现）
public void _OnInternalUpdate() { }

```

---

### 7.2 同步来源验证

**背景**：对于 Requst 发送的参数同步，其调用者身份可通过 `Networking.GetOwner(gameObject)` 获取。但对于网络事件（`NetworkCalling.SendCustomNetworkEvent`），应使用 `NetworkCalling.CallingPlayer` 检测真实调用者。

**两种检测场景对比**：

<div class="table-wrapper" id="bkmrk-%E5%9C%BA%E6%99%AF-%E6%8E%A8%E8%8D%90-api-%E8%AF%B4%E6%98%8E-%E6%97%A0%E5%8F%82%E7%BD%91%E7%BB%9C%E4%BA%8B%E4%BB%B6%EF%BC%88"><table><thead><tr><th>场景</th><th>推荐 API</th><th>说明</th></tr></thead><tbody><tr><td>序列化（`RequestSerialization()`）</td><td>`Networking.GetOwner(gameObject)`</td><td>发送方应为对象 Owner</td></tr><tr><td>带参网络事件（NetworkCallable）</td><td>`NetworkCalling.CallingPlayer`</td><td>获取真实调用者 Player 对象</td></tr></tbody></table>

</div>**示例**：

```csharp
[NetworkCallable]
public void OnPlayerAction(int playerId, int actionType)
{
    // 使用 NetworkCalling.CallingPlayer 获取真实调用者
    VRCPlayerApi sender = NetworkCalling.CallingPlayer;

    if (sender == null || sender != VRCPlayerApi.GetPlayerById(playerId))
    {
        // 来自伪造请求，直接忽略
        return;
    }

    // 正常处理请求...
}

```

<p class="callout warning">⚠️ **安全提示**：建议在带参网络同步操作中增加使用 `playerId` 参数 ，在远端使用 `VRCPlayerApi.GetPlayerById()` 和`NetworkCalling.CallingPlayer`做二次验证，可以验证发送者是否篡改了发送信息。</p>

---

### 7.3 使用 BehaviourSyncMode.None

**原则**：本地优先的脚本使用 `None` 同步模式。

```csharp
[UdonBehaviourSyncMode(BehaviourSyncMode.None)]
public class LocalUIController : UdonSharpBehaviour
{
    // 本地 UI 逻辑
    // 没有网络 ID，外挂无法通过网络操作此脚本
}

```

**None vs NoVariableSync 区别**：

<div class="table-wrapper" id="bkmrk-%E6%A8%A1%E5%BC%8F-%E5%90%8C%E6%AD%A5%E5%8F%98%E9%87%8F-%E7%BD%91%E7%BB%9C%E4%BA%8B%E4%BB%B6%EF%BC%88sendcus"><table><thead><tr><th>模式</th><th>同步变量</th><th>网络事件（SendCustomNetworkEvent）</th><th>适用场景</th></tr></thead><tbody><tr><td>`None`</td><td>❌ 无</td><td>❌ 不工作</td><td>纯本地逻辑</td></tr><tr><td>`NoVariableSync`</td><td>❌ 无</td><td>✅ 可用（可与 Manual/Continuous 共存）</td><td>UI 控制器、事件转发</td></tr></tbody></table>

</div>**None优势**：

- 脚本没有网络 ID，外挂客户端无法通过网络访问
- 本地只有在实际交互后才会在 UdonCache 中出现
- 适合 UI 控制器、本地状态管理

**适用场景**：

- ✅ UI 面板
- ✅ 本地设置
- ✅ 临时状态

**不适用场景**：

- ❌ 需要网络同步的功能
- ❌ 需要多玩家协作的功能

<p class="callout info">这种情况可以由另一个脚本作为中继实现</p>

---

### 7.4 SetProgramVariable 类型混淆保护

**原理**：Udon 的 `SetProgramVariable` 不进行类型检查，可以通过设置错误类型来创建"陷阱变量"。

**实现方案**：

```csharp
public class SecureController : UdonSharpBehaviour
{
    private void Start()
    {
        // 初始化：将 错误类型的值存入变量
        SetProgramVariable("_security_tag", "init_string_value");
    }

    // 所有 Public 方法的第一行进行检测,正确调用时先修复问题,再调用
    public void OnPlayerInteract()
    {
        // 检测陷阱变量是否被正确设置
        if (!(GetProgramVariable("_security_tag") is string))
        {
            return; // 安全检查失败，忽略请求
        }

        // 正常处理...
    }

    // 在所有正常流程中，定期重置标志位
    public void _InternalUpdate()
    {
        // 重置为错误的类型和值
        SetProgramVariable("_security_tag", "valid_state");
    }
}

```

**原理说明**：

<div class="table-wrapper" id="bkmrk-%E6%AD%A5%E9%AA%A4-%E8%AF%B4%E6%98%8E-1.-%E5%88%9D%E5%A7%8B%E5%8C%96-%E5%AD%98%E5%82%A8%E4%B8%80%E4%B8%AA%E7%B1%BB%E5%9E%8B%E4%B8%BA"><table><thead><tr><th>步骤</th><th>说明</th></tr></thead><tbody><tr><td>1. 初始化</td><td>存储一个类型为 A 的变量（如 string）</td></tr><tr><td>2. 正常流程</td><td>使用时先将变量设置为类型 A 的正确值</td></tr><tr><td>3. 外挂调用</td><td>外挂直接调用</td></tr><tr><td>4. 检测</td><td>Public 方法检查类型，发现不匹配则拒绝执行/立即崩溃</td></tr></tbody></table>

</div>> ⚠️ **注意**：此方法会导致正常玩家触发时 Udon 崩溃，因此仅用于保护其他玩家的体验，而非防止作弊者本身。

<p class="callout info">如果不想要大规模重写的话，也可以选择给所有脚本增加一段炸弹代码，只要作弊客户端调用，秒炸Udon。  
  
因为Udon本身是线性执行的，可以一定程度防止更大规模的问题出现。</p>

```csharp
public class SecureController : UdonSharpBehaviour
{
    private void Start()
    {
        // 初始化：将 错误类型的值存入变量
        SetProgramVariable("_security_tag", "init_String_value");
    }

    // 所有 Public 方法的第一行进行检测,正确调用时先修复问题,再调用
    public void ABoom()
    {
        // 检测陷阱变量是否被正确设置
        _security_tag = _security_tag + _security_tag;
    }
}

```

---

### 7.5 综合安全策略

<div class="table-wrapper" id="bkmrk-%E5%B1%82%E7%BA%A7-%E6%8E%AA%E6%96%BD-%E9%98%B2%E6%8A%A4%E7%9B%AE%E6%A0%87-%E5%91%BD%E5%90%8D%E5%B1%82-_-%E5%89%8D%E7%BC%80%E5%91%BD"><table><thead><tr><th>层级</th><th>措施</th><th>防护目标</th></tr></thead><tbody><tr><td>**命名层**</td><td>`_` 前缀命名本地事件</td><td>防止外挂发现事件</td></tr><tr><td>**来源层**</td><td>PlayerId 验证</td><td>防止伪造发包者身份</td></tr><tr><td>**访问层**</td><td>BehaviourSyncMode.None</td><td>防止网络访问本地脚本</td></tr><tr><td>**调用层**</td><td>类型混淆检测</td><td>防止非正常途径调用</td></tr></tbody></table>

</div>---

## 附录：官方文档链接

<div class="table-wrapper" id="bkmrk-%E4%B8%BB%E9%A2%98-%E9%93%BE%E6%8E%A5-networking-%E6%A6%82%E8%A7%88-"><table><thead><tr><th>主题</th><th>链接</th></tr></thead><tbody><tr><td>Networking 概览</td><td>[https://creators.vrchat.com/worlds/udon/networking/](https://creators.vrchat.com/worlds/udon/networking/)</td></tr><tr><td>变量同步</td><td>[https://creators.vrchat.com/worlds/udon/networking/sync-variables](https://creators.vrchat.com/worlds/udon/networking/sync-variables)</td></tr><tr><td>网络事件</td><td>[https://creators.vrchat.com/worlds/udon/networking/events](https://creators.vrchat.com/worlds/udon/networking/events)</td></tr><tr><td>对象同步</td><td>[https://creators.vrchat.com/worlds/udon/networking/vrc-object-sync](https://creators.vrchat.com/worlds/udon/networking/vrc-object-sync)</td></tr><tr><td>持久化</td><td>[https://creators.vrchat.com/worlds/udon/persistence/](https://creators.vrchat.com/worlds/udon/persistence/)</td></tr></tbody></table>

</div>

# NetWorking基础设计

# 设计篇

# 总篇

本篇将会统一讲述各个设计方案的基础理念，适用场景以及举例。后续将会结合实际例子来详细讲解的设计方案

————

前言：

网络同步是一个复杂的工程问题，一般来说根据需求，我们需要研究以下问题

1:我们需要同步什么东西

2:我们的同步是部分同步还是完全同步

3:同步更注重于连续还是注重于结果

4:我们的同步的是参数还是结果

5:同步逻辑是群发的还是单发的

6:同步逻辑是否需要双向通信

7:同步是否需要适配持久化保存

有一些东西是……原则上必须实现的：

1:迟到者同步：为新加入的玩家重新同步

2:性能考虑：你必须考虑到网络同步的性能问题

3:防呆设计：你必须保证无论什么情况下你的同步过程中都不会导致崩溃

4：主副机相等：发出同步的人必须和接受信息的人最后的结果一致。

基于上述问题和原则，根据常用的场景，我个人常用以下几种设计原型

————

单文件同步设计

理念：将业务代码和网络同步放入同一个脚本内，同步只需要将重点变量同步即可。同步方式根据需求自由选择。

适用性：这种网络同步模式是最基础的模式。也是大部分复杂度不高，同步要求也不高的插件的通用方案。

其有着设计简单，逻辑清晰的优点，而且易于debug。

其缺点也很明显，网络部分和业务代码高度耦合，后期维护性较差。

适合场景：简单插件

————

网络同步隔离设计：

将网络同步部分代码和业务代码分离，互相之间通过通过函数进行通信。常用于网络同步部分参数除了本体需要以外，还可能被参数多个插件需要的情况。例如UdonChips-Udon钱包。

同步方式主要以手动同步和自动同步为主，常见搭配PlayerObject使每一个人都有独立的对象。

而且这种同步方案对持久化设计十分友好，可以轻松实现持久化。

优点：结构相对简单，开发难度较低，后续继续开发。

缺点：性能较差，因其它脚本无法主动得知更新时间，通常需要多次无意义的重复获取数值来保证数据最新，这带来了十分严重的性能浪费。

通常会结合下面一个模式的部分特性来弥补缺点。

————

同步管理模式：

同样是业务代码和同步代码分离，但前者强调同步代码不参与管理，只提供同步。而这个模式下的网络同步组件需要负责组件管理职责。

比如业务代码A是一个控制台，他负责收集玩家的操作指令，组合成参数然后传输给同步代码。

同步代码接收到之后，触发同步，并且主动通知所有需要这些同步的脚本数据已更新。或者自己本身就是一个业务脚本，根据参数进行相关行为。

这种同步模式可以形成可接受多种输入，多种输出的模式，无论上层和下层如何改变，只要最终数据符合要求就可以正常使用。

优点：可拓展性强，根据设计可以适配多种不同的场景，非常好用的大型网络同步方案。因为有了通知机制，没有太多的性能浪费。

————

同步对象池模式：

简单来说，在这个模式下，将多个同步脚本组成对象池，业务代码根据需要将结果存入对象池中的任意/随机同步对象中。由对象池管理同步信息。

这种同步模式在没有PlayerObject的时候曾经是热门的模式。现在大部分时候用于例如点菜系统，任务分配系统等——玩家的每一个操作都需要独立记录的场景。

优点：效果好，是特殊场景的设计方案

缺点：复杂，自由度低。

——

# 基础网络同步知识——VRChatNetworking到底是如何工作的？

### 同步模式：如何同步？

---

VRChat的同步分为4种同步模式 ：”自动“，”手动变量同步“，”连续变量同步“，”事件同步“

---

#### 连续同步(Continuous Variable):

连续同步是Udon两种参数同步模式的其中一种，VRChat会尝试在每一次变量同步帧中同步变量。但是：  
1：连续同步不一定会每一次都同步，有可能会因为节省带宽等原因减少同步频率，或者不同步其中的某几次。  
2：连续同步不保证每一次都会到达，当丢包发生时，连续同步不会尝试重新发送同步包。直到主机下一次发生变化。  
3：连续同步保证所有玩家都能收到最新的同步结果（在不丢包的情况下）——包括后加入的玩家。  
4：连续同步的带宽极低，整个游戏的连续同步带宽仅200字节。  
5：连续同步仅支持最多126个字符的字符串/字符数组。  
连续同步比较常用与对数据精度要求不高，数据量不高但是对即时性要求很高的场景。即使是连续同步同步延迟也大概率大于1second，所以需要搭配手写平滑函数来减少同步频率低带来的卡顿感。

在通常情况下，当一个脚本是连续同步的。你需要通过在Update中检查变量结果或者使用变量CallBack来处理同步逻辑。因为连续同步不会触发OnDesSerialization事件

> 连续同步会在Owner的变量发生改变后的下一帧尝试进行同步，发送数据包。

<p class="callout danger">连续同步会很容易受到丢包影响，导致行为不一致。</p>

---

#### 手动同步（Manual Variable）

手动同步是Udon两种参数同步模式的其中一种，VRChat会立即序列化所有的同步变量，并且在下一次同步帧到来时将同步数据发送出去。  
手动同步是最常用的同步模式，也是我最推荐的同步模式。它有如下的特点：  
1：可控—手动同步的发送，打包，解包都有相对应的事件存在，你可以自定义同步的流程  
2：可靠—手动同步相对于连续同步，没有VRChat的介入，不会因为各种各样的原因导致包被丢弃，可以认为是可靠的。  
3：低速—虽然手动同步和连续同步共享了同一个网络同步通道，但是由于需要手动触发，等待网络帧，所以速度相对较低。  
4：高带宽—连续同步的单次最高同步为49千字节只要低于这个值都可以在一个网络帧内发送，如果高于则会分两次进行同步。

  
在后续的设计模式中，我们也会大多数以手动同步为同步方案。

> 手动同步需要任意玩家触发RequestSerialization()事件，才会开始网络同步。
> 
> 手动同步会首先触发OnPreSerialization()事件
> 
> 然后脚本会序列化数据并发送网络包，然后在OnPostSerialization()事件返回发送结果。
> 
> 其他玩家接收到网络包会立即开始解包更新变量，并且在完成所有更新后触发OnDesSerialization()

<p class="callout info">OnDesSerialization()允许玩家在代码中直接调用，设计的时候经常会在Rquest后直接触发OnDesSerialization来保证行为一致。</p>

---

#### 特殊同步：事件

事件同步是Udon的一个独立的同步模式，其允许脚本使用SendCustomNetworkingEvent（）方法来触发自身以及远端玩家（新版本可以选择4种不同目标，分别是Owner，ALL，Others，Self）的事件。在3-8-1版本，VRChat官方为事件系统提供了带参网络事件，使得事件同步已经成为了一个完整的同步方案。

  
事件同步有以下特点：  
1：可靠—事件同步通过TCP协议进行同步，保证事件一定抵达。  
2：缓慢—事件同步需要等待网络事件帧到来才会开始同步。  
3：即时—只有在发送的那一刻存在在房间内的玩家才会收到事件同步。后续到来的玩家不会收到同步。  
事件同步时独立于Udon参数同步的同步方式，可以与手动同步和连续同步一同使用。

---

#### 特殊同步：自动

自动同步是VRChat最底层的同步模式，自动同步的每一个改变都会立即被同步，同步速度仅取决于网络延迟。

  
这个是VRChat最快的同步模式，但是只有两个可以享受这种待遇  
Avatars ：包括其碰撞体，音效和 IK 动作 (译者注：就是骨骼的位置)  
VRCObjectSync：包括物体的变换组件(Transform) 和刚体组件 (Rigidbody)内的所有信息，不包括其他组件（不包括Udon）

而且VRCObjectSync内部自带平滑函数，使得同步看上去十分流畅快速，这点从带有SYNC的PickUp脚本中可以体现。在实践中，Sync脚本大概率会和PickUp，刚体这两个组件一同出现。

（当然，也有时候会使用Sync的特点。将Sync挂载的物体的Transform值作为9float的同步变量，为手动同步或者没有同步的脚本提供连续快速同步的功能。）

需要注意：VRCObjectSync要求与其在同一物体上的Udon组件必须要以Continuous（连续同步）模式运行。

---

### 所有者机制：谁来同步？

在VRChat中，每一个网络组件都需要一个玩家作为主机，来进行同步操作。在VRChat中，我们称这个人为Owner。

在连续同步中，Owner会在自身变量发生改变的时候，将变量同步给其他玩家。

在手动同步中，无论是Owner，还是其他玩家触发RequestSerialization()事件。都会由Owner执行打包数据并且发送给其他玩家。

在事件同步中，玩家只能选择给**"全部玩家“**或者给**”Owner“**发送事件。

————

对于特殊的机制，如PlayerData，PlayerObject，本质都是一个”不能被转移Owner“的脚本。每一名玩家进入游戏的时候生成一个Owner固定为这个玩家的脚本来实现的。VRChat也允许我们使用**OnOwnershipRequest()**来控制是否允许Owner转移。

————

我们可以通过SetOwner()来**请求**Owner转移。如果我们没有在代码中定义**OnOwnershipRequest()**事件，那么这SetOwner()将会被放行。

流程如下

1：B玩家为Owner，

2.A玩家SetOwner发送请求,停止帧生成等待Owner转移。

3.B玩家接收到请求，调用OnOwnershipRequest()。

4.OnOwnershipRequest()返回True，B玩家向所有玩家发送OnOwnershipTransferred()事件。

5.A玩家继续帧生成。

<p class="callout info">如果OnOwnershipRequest()没有定义，则A玩家会直接发送OnOwnershipTransferred()事件。</p>

<p class="callout warning">只有当所有玩家都受到了OnOwnershipTransferred()之后，A玩家才会继续执行下一个步骤。</p>

<p class="callout danger">如果B玩家拒绝了这次更改，则A玩家会继续执行剩下的部分并且在OnOwnershipTransferred()得知自己的请求被拒绝</p>

---

### 持久化机制：如何存储？

VRChat为我们提供了两种不同的持久化方法

1：将某个脚本定义为持久化脚本，该脚本的所有同步参数将会变成持久化参数。在每一次进入实例时会自动回复。

<div class="message-content mix-message__inner" id="bkmrk--9"></div><div class="message-content__wrapper" data-event="cp-3046-event-3054" id="bkmrk-%E5%BD%93b%E7%8E%A9%E5%AE%B6%E4%B8%BAowner%EF%BC%8Ca%E7%8E%A9%E5%AE%B6setown-1"><div bf-label-inner="true" bf-list-item="" class="msg-content-container container--self mix-message__container" data-ref="cp-3046-ref-3055" tabindex="0"><div class="message-content mix-message__inner">2：Udon提供了一个键值对数据库，存入数据库的值将会被持久化。</div></div></div><div class="message-content mix-message__inner" id="bkmrk--6"></div><div class="message-content mix-message__inner" id="bkmrk-%E6%89%80%E6%9C%89%E7%9A%84%E6%8C%81%E4%B9%85%E5%8C%96%E6%95%B0%E6%8D%AE%E9%83%BD%E8%A6%81%E6%B1%82%E6%98%AF%E7%BD%91%E7%BB%9C%E6%95%B0%E6%8D%AE%EF%BC%8C%E5%8F%AA%E6%9C%89%E5%BD%93">所有的持久化数据都要求是网络数据，只有当数据更新成功，持久化才会正常运作。</div><div class="message-content mix-message__inner" id="bkmrk--7"></div><div class="message-content mix-message__inner" id="bkmrk-%E4%B8%A4%E7%A7%8D%E6%8C%81%E4%B9%85%E5%8C%96%E6%96%B9%E6%B3%95%E7%9A%84%E4%BC%98%E7%82%B9%E5%92%8C%E7%BC%BA%E7%82%B9%E9%83%BD%E5%8D%81%E5%88%86%E6%98%8E%E6%98%BE">两种持久化方法的优点和缺点都十分明显</div><div class="message-content mix-message__inner" id="bkmrk--10">  
</div><div class="message-content mix-message__inner" id="bkmrk-1%EF%BC%9A%E4%BC%98%E7%82%B9%E5%9C%A8%E4%BA%8E%E6%9C%AC%E8%BA%AB%E5%B0%B1%E6%98%AF%E8%84%9A%E6%9C%AC%EF%BC%8C%E6%90%AD%E9%85%8Dplaye">1：优点在于本身就是脚本，搭配PlayerObject可以实现多种不同玩法。而且持久化不需要写过多的代码，丝滑接入。</div><div class="message-content mix-message__inner" id="bkmrk--11"></div><div class="message-content mix-message__inner" id="bkmrk-2%EF%BC%9A%E4%BC%98%E7%82%B9%E5%9C%A8%E4%BA%8E%E5%85%B6%E6%9C%AC%E8%BA%AB%E5%8F%AF%E4%BB%A5%E8%A2%AB%E4%BB%BB%E4%BD%95%E8%84%9A%E6%9C%AC%E5%9C%A8%E4%BB%BB%E4%BD%95%E5%9C%B0">2：优点在于其本身可以被任何脚本在任何地方访问，调用，设置，并且设置即同步，在多脚本协作时效果很好。</div>---

<div class="message-content mix-message__inner" id="bkmrk--13">  
</div>

# 最常用的网络同步架构——单文件同步

再VRChat中，大部分的网络同步场景都是简单的场景，比如：

数个同步开关

一个同步的滑条（音乐播放器的音量）

————————

单文件同步——即业务代码和同步代码在同一个代码文件内。

单文件同步是所有同步模式中性能最好的同步方式，因为Udon的架构问题，导致任何跨脚本同步的方案都需要至少一次或多次的方法调用和变量传输，

单文件同步也是最好设计的同步模式，即使在业务代码和同步代码分离的同步模式中，同步代码所在的文件的结构设计也需要遵循单文件同步的设计原则。

————————

————————

单文件同步的设计原则如下：

优先有限变量：在标记变量时采用有限长度的变量作为同步变量

解释：Udon的同步变量存在切片概念，即当同步变量长度过大时，会触发切片机制。在Udon的旧版本Bug中，切片机制有概率扰乱网络同步，导致在

最低限度原则：在

解释：

# 技巧篇

# 通过注册实现-自定义同步事件



# 持久化设计——PlayerObject/PlayerDataBase使用优缺点。



# 参数变化触发事件



# 在手动同步脚本实现部分连续同步。



# 同步对象池设计案例



# 实现1对1发送信息的N种方式



# 脚本同步设计通用方法论——如何设计网络同步