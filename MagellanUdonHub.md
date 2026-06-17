# 麦哲伦的Udon面馆

这里是麦麦的 Udon 面馆，内有各种随机干货，欢迎光临~

(╹ڡ╹ )

# VRChat Udon DataToken & VRCJson 食用指南

## 概述

在 VRChat World 开发中，JSON + `DataToken` 是处理动态数据的核心工具组合。它们使得以下场景成为可能：

- **自定义系统配置**：从外部加载世界设置
- **动态菜单选项**：运行时生成可配置的功能菜单
- **网络数据读取**：从服务器获取配置或内容数据
- **玩家数据保存**：存储和读取玩家偏好设置
- **可扩展内容系统**：道具、关卡、任务等数据的灵活管理

> **官方文档**
> 
> - [Data Containers 总览](https://creators.vrchat.com/worlds/udon/data-containers/)
> - [DataToken 详细文档](https://creators.vrchat.com/worlds/udon/data-containers/data-tokens/)
> - [VRCJson 官方文档](https://creators.vrchat.com/worlds/udon/data-containers/vrcjson/)

---

## 第一章：核心概念

### 1.1 DataToken — 数据的基本单元

#### 什么是 DataToken？

`DataToken` 是 VRChat Udon 中的**通用数据容器**，用于存储单一类型的值。它是整个数据容器体系的基础单元。

**关键特性**：

- 每个 DataToken **只存储一个值**
- 支持多种数据类型（见下表）
- 用于 DataDictionary 和 DataList 的数据存储

#### 支持的数据类型

<div class="table-wrapper" id="bkmrk-tokentype-c%23-%E7%B1%BB%E5%9E%8B-%E8%AF%B4%E6%98%8E-n"><table><thead><tr><th>TokenType</th><th>C# 类型</th><th>说明</th></tr></thead><tbody><tr><td>`Null`</td><td>—</td><td>空值</td></tr><tr><td>`Boolean`</td><td>`bool`</td><td>布尔值</td></tr><tr><td>`SByte`</td><td>`sbyte`</td><td>8位有符号整数</td></tr><tr><td>`Byte`</td><td>`byte`</td><td>8位无符号整数</td></tr><tr><td>`Short`</td><td>`short`</td><td>16位有符号整数</td></tr><tr><td>`UShort`</td><td>`ushort`</td><td>16位无符号整数</td></tr><tr><td>`Int`</td><td>`int`</td><td>32位有符号整数</td></tr><tr><td>`UInt`</td><td>`uint`</td><td>32位无符号整数</td></tr><tr><td>`Long`</td><td>`long`</td><td>64位有符号整数</td></tr><tr><td>`ULong`</td><td>`ulong`</td><td>64位无符号整数</td></tr><tr><td>`Float`</td><td>`float`</td><td>32位浮点数</td></tr><tr><td>`Double`</td><td>`double`</td><td>64位浮点数</td></tr><tr><td>`String`</td><td>`string`</td><td>字符串</td></tr><tr><td>`DataList`</td><td>`DataList`</td><td>列表容器</td></tr><tr><td>`DataDictionary`</td><td>`DataDictionary`</td><td>字典容器</td></tr><tr><td>`Reference`</td><td>`Object`</td><td>对象引用（无法序列化）</td></tr><tr><td>`Error`</td><td>`DataError`</td><td>错误信息</td></tr></tbody></table>

</div>#### UdonSharp 隐式创建语法

在 UdonSharp 中，DataToken 支持**隐式创建**，无需显式使用 `new DataToken()`：

```csharp
// 显式创建（可读性差）
DataToken explicitFloat = new DataToken(5.3f);
DataToken explicitInt = new DataToken(5);
DataToken explicitString = new DataToken("value");

// 隐式创建（推荐）
DataToken _float = 5.3f;
DataToken _int = 5;
DataToken _string = "value";
DataToken _bool = true;

// 在函数参数中直接使用
dictionary.SetValue("key", "value");  // "value" 自动转为 DataToken

```

> **注意**：不要使用 `nameof()` 隐式创建 DataToken，可能会导致错误。

#### 获取 DataToken 中的值

**重要**：在获取值之前，**必须进行类型校验**，否则会 `halt`（暂停）UdonBehaviour。

**方式一：使用 TokenType 检查**

```csharp
if (unknownToken.TokenType == TokenType.String)
{
    Debug.Log(unknownToken.String);  // 安全获取字符串
}

```

**方式二：使用 IsNumber 特性**

```csharp
if (unknownToken.IsNumber)
{
    double num = unknownToken.Number;  // 安全获取数值
}

```

**方式三：使用带类型检查的 TryGetValue**

```csharp
if (dictionary.TryGetValue("key", TokenType.Float, out DataToken value))
{
    float f = value.Float;  // 安全获取
}

```

**方式四：ToString（始终安全）**

```csharp
string str = unknownToken.ToString();  // 始终有效，不会 halt

```

#### String vs ToString 的区别

<div class="table-wrapper" id="bkmrk-%E6%96%B9%E6%B3%95-%E9%80%82%E7%94%A8%E5%9C%BA%E6%99%AF-%E5%A4%B1%E8%B4%A5%E8%A1%8C%E4%B8%BA-datatok"><table><thead><tr><th>方法</th><th>适用场景</th><th>失败行为</th></tr></thead><tbody><tr><td>`DataToken.String`</td><td>仅当确认类型为 String 时</td><td>抛出异常，halt UdonBehaviour</td></tr><tr><td>`DataToken.ToString()`</td><td>**任何类型**</td><td>返回类型的字符串表示</td></tr></tbody></table>

</div>---

### 1.2 DataDictionary — 键值对容器

#### 结构说明

DataDictionary 是**键值对**形式的数据容器，类似 C# 的 `Dictionary<DataToken, DataToken>`。

**与 JSON 的对应关系**：

```
DataDictionary ←→ JSON Object（使用字符串键时）

```

**重要特性**：

- 键（Key）和值（Value）都是 DataToken
- 序列化到 JSON 时，**仅支持字符串键**
- 非字符串键不会报错，但无法序列化

#### 常用方法

<div class="table-wrapper" id="bkmrk-%E6%96%B9%E6%B3%95-%E5%8F%82%E6%95%B0-%E8%BF%94%E5%9B%9E%E5%80%BC-%E8%AF%B4%E6%98%8E-add-key"><table><thead><tr><th>方法</th><th>参数</th><th>返回值</th><th>说明</th></tr></thead><tbody><tr><td>`Add`</td><td>key, value</td><td>void</td><td>添加键值对（键存在则抛异常）</td></tr><tr><td>`SetValue`</td><td>key, value</td><td>void</td><td>设置值（键不存在则创建）</td></tr><tr><td>`TryGetValue`</td><td>key</td><td>bool, token</td><td>安全获取值</td></tr><tr><td>`TryGetValue`</td><td>key, TokenType</td><td>bool, token</td><td>带类型检查的获取</td></tr><tr><td>`ContainsKey`</td><td>key</td><td>bool</td><td>检查键是否存在</td></tr><tr><td>`ContainsValue`</td><td>value</td><td>bool</td><td>检查是否包含指定值</td></tr><tr><td>`Remove`</td><td>key</td><td>bool</td><td>删除键值对</td></tr><tr><td>`Remove`</td><td>key</td><td>bool, token</td><td>删除并返回被删除的值</td></tr><tr><td>`GetKeys`</td><td>—</td><td>DataList</td><td>获取所有键</td></tr><tr><td>`GetValues`</td><td>—</td><td>DataList</td><td>获取所有值</td></tr><tr><td>`Count`</td><td>—</td><td>int</td><td>获取元素数量</td></tr><tr><td>`ShallowClone`</td><td>—</td><td>DataDictionary</td><td>浅拷贝</td></tr><tr><td>`DeepClone`</td><td>—</td><td>DataDictionary</td><td>深拷贝</td></tr><tr><td>`Clear`</td><td>—</td><td>void</td><td>清空所有键值对</td></tr></tbody></table>

</div>> ⚠️ **性能警告**：`ContainsValue`、`ShallowClone`、`GetValues` 会对未解析的 JSON 值触发全量解析操作，数据量大时开销显著。

#### 使用示例

```csharp
public DataDictionary Config;

// 初始化
public void Initialize()
{
    Config = new DataDictionary();
}

// 添加数据
public void AddData()
{
    // SetValue：键不存在则创建，存在则覆盖
    Config.SetValue("name", "MyWorld");
    Config.SetValue("maxPlayers", 20);
    Config.SetValue("enabled", true);
    
    // Add：键存在则抛异常，适合初始化
    Config.Add("version", "1.0");
}

// 安全读取
public void ReadData()
{
    // 方式一：TryGetValue（推荐）
    if (Config.TryGetValue("name", TokenType.String, out DataToken nameToken))
    {
        string name = nameToken.String;
        Debug.Log($"World name: {name}");
    }
    
    // 方式二：直接访问（仅当你 100% 确定类型时使用）
    string certainName = Config["name"].String;
    
    // 方式三：遍历所有键
    DataList keys = Config.GetKeys();
    for (int i = 0; i < keys.Count; i++)
    {
        keys.TryGetValue(i, out DataToken keyToken);
        Config.TryGetValue(keyToken, out DataToken valueToken);
        Debug.Log($"{keyToken.String}: {valueToken}");
    }
}

// 批量操作
public void BatchOperations()
{
    // 检查存在性
    if (Config.ContainsKey("maxPlayers"))
    {
        // 安全删除
        Config.Remove("maxPlayers");
    }
    
    // 清空
    Config.Clear();
}

```

#### 初始化语法

在 UdonSharp 中，可以在变量声明时初始化 DataDictionary：

```csharp
private DataDictionary users = new DataDictionary()
{
    { "John Doe", new DataDictionary()
        {
            {"email", "johndoe@example.com"},
            {"age", 35},
            {"address", new DataDictionary()
                {
                    {"city", "Anytown"},
                    {"zip", 12345}
                }
            }
        }
    },
    { "Jane Smith", new DataDictionary()
        {
            {"email", "janesmith@example.com"},
            {"age", 28}
        }
    }
};

```

> ⚠️ **限制**：
> 
> - UdonSharp 不支持在函数内使用此语法
> - Unity 不序列化 DataDictionary，不建议用于 public 序列化变量
> - 应使用 `private` 或 `[NonSerialized] public` 变量

---

### 1.3 DataList — 有序列表容器

#### 结构说明

DataList 是**有序数组**形式的数据容器，类似 C# 的 `List<DataToken>`。

**与 JSON 的对应关系**：

```
DataList ←→ JSON Array

```

**重要特性**：

- 通过整数索引（0, 1, 2...）访问
- 支持任意类型的 DataToken
- 可嵌套 DataDictionary

#### 常用方法

<div class="table-wrapper" id="bkmrk-%E6%96%B9%E6%B3%95-%E5%8F%82%E6%95%B0-%E8%BF%94%E5%9B%9E%E5%80%BC-%E8%AF%B4%E6%98%8E-add-val"><table><thead><tr><th>方法</th><th>参数</th><th>返回值</th><th>说明</th></tr></thead><tbody><tr><td>`Add`</td><td>value</td><td>void</td><td>添加到末尾</td></tr><tr><td>`AddRange`</td><td>DataList</td><td>void</td><td>合并另一个列表</td></tr><tr><td>`Insert`</td><td>index, value</td><td>bool</td><td>插入到指定位置</td></tr><tr><td>`InsertRange`</td><td>index, DataList</td><td>void</td><td>范围插入</td></tr><tr><td>`SetValue`</td><td>index, value</td><td>void</td><td>设置索引处的值</td></tr><tr><td>`TryGetValue`</td><td>index</td><td>bool, token</td><td>安全获取值</td></tr><tr><td>`TryGetValue`</td><td>index, TokenType</td><td>bool, token</td><td>带类型检查的获取</td></tr><tr><td>`Contains`</td><td>value</td><td>bool</td><td>检查是否包含</td></tr><tr><td>`IndexOf`</td><td>value</td><td>int</td><td>查找索引（首个匹配）</td></tr><tr><td>`IndexOf`</td><td>value, startIndex</td><td>int</td><td>范围查找</td></tr><tr><td>`IndexOf`</td><td>value, startIndex, count</td><td>int</td><td>指定范围查找</td></tr><tr><td>`LastIndexOf`</td><td>value</td><td>int</td><td>查找索引（最后匹配）</td></tr><tr><td>`LastIndexOf`</td><td>value, startIndex</td><td>int</td><td>范围反向查找</td></tr><tr><td>`LastIndexOf`</td><td>value, startIndex, count</td><td>int</td><td>指定范围反向查找</td></tr><tr><td>`BinarySearch`</td><td>value</td><td>int</td><td>二分查找（需先排序）</td></tr><tr><td>`BinarySearch`</td><td>value, startIndex, count</td><td>int</td><td>范围二分查找</td></tr><tr><td>`Remove`</td><td>value</td><td>bool</td><td>删除（首个匹配）</td></tr><tr><td>`RemoveAll`</td><td>value</td><td>bool</td><td>删除所有匹配</td></tr><tr><td>`RemoveAt`</td><td>index</td><td>void</td><td>删除指定索引</td></tr><tr><td>`RemoveRange`</td><td>index, count</td><td>void</td><td>范围删除</td></tr><tr><td>`GetRange`</td><td>index, count</td><td>DataList</td><td>范围拷贝</td></tr><tr><td>`Reverse`</td><td>—</td><td>void</td><td>反转全部</td></tr><tr><td>`Reverse`</td><td>index, count</td><td>void</td><td>反转范围</td></tr><tr><td>`Sort`</td><td>—</td><td>void</td><td>排序全部</td></tr><tr><td>`Sort`</td><td>index, count</td><td>void</td><td>范围排序</td></tr><tr><td>`ShallowClone`</td><td>—</td><td>DataList</td><td>浅拷贝</td></tr><tr><td>`DeepClone`</td><td>—</td><td>DataList</td><td>深拷贝</td></tr><tr><td>`ToArray`</td><td>—</td><td>DataToken\[\]</td><td>转为数组</td></tr><tr><td>`TrimExcess`</td><td>—</td><td>void</td><td>压缩容量</td></tr><tr><td>`Count`</td><td>—</td><td>int</td><td>获取元素数量</td></tr><tr><td>`Capacity`</td><td>—</td><td>int</td><td>容量（可读写）</td></tr><tr><td>`Clear`</td><td>—</td><td>void</td><td>清空所有元素</td></tr></tbody></table>

</div>> ⚠️ **性能警告**：`Contains`、`IndexOf`、`LastIndexOf` 会对未解析的 JSON 值触发全量解析操作，数据量大时开销显著。

#### 使用示例

```csharp
public DataList PlayerList;

// 初始化
public void Initialize()
{
    PlayerList = new DataList();
}

// 添加数据
public void AddPlayers()
{
    // 方式一：逐个添加
    PlayerList.Add("Alice");
    PlayerList.Add("Bob");
    PlayerList.Add("Charlie");
    
    // 方式二：批量添加（通过合并列表）
    DataList newPlayers = new DataList();
    newPlayers.Add("David");
    newPlayers.Add("Eve");
    PlayerList.AddRange(newPlayers);
}

// 安全读取
public void ReadPlayers()
{
    // 方式一：TryGetValue（推荐）
    if (PlayerList.TryGetValue(0, TokenType.String, out DataToken firstPlayer))
    {
        Debug.Log($"First player: {firstPlayer.String}");
    }
    
    // 方式二：遍历
    for (int i = 0; i < PlayerList.Count; i++)
    {
        PlayerList.TryGetValue(i, out DataToken token);
        Debug.Log($"Player {i}: {token}");
    }
    
    // 方式三：查找
    int index = PlayerList.IndexOf("Bob");
    if (index >= 0)
    {
        Debug.Log($"Found Bob at index {index}");
    }
}

// 嵌套字典示例
public void CreateComplexData()
{
    DataList items = new DataList();
    
    // 创建第一个物品
    DataDictionary item1 = new DataDictionary();
    item1.SetValue("id", 1);
    item1.SetValue("name", "Sword");
    item1.SetValue("price", 100);
    items.Add(item1);
    
    // 创建第二个物品
    DataDictionary item2 = new DataDictionary();
    item2.SetValue("id", 2);
    item2.SetValue("name", "Shield");
    item2.SetValue("price", 80);
    items.Add(item2);
    
    PlayerList.Add(items);  // 列表中嵌套列表
}

```

#### 初始化语法

在 UdonSharp 中，可以在变量声明时初始化 DataList：

```csharp
private DataList _groceries = new DataList()
{
    "Bananas",
    "Grapes",
    "Milk",
    "Soda",
    "Turkey"
};

```

> ⚠️ **限制**（同上）：
> 
> - UdonSharp 不支持在函数内使用此语法
> - Unity 不序列化 DataList，不建议用于 public 序列化变量
> - 应使用 `private` 或 `[NonSerialized] public` 变量

---

### 1.4 三者关系图

```
┌───────────────────────────────────────────────────────┐
│                    数据容器体系                         │
├───────────────────────────────────────────────────────┤
│                                                       │
│  ┌─────────────┐      存储       ┌────────────────┐    │
│  │DataDictionary│◄───────────────│   DataToken   │    │
│  │ (键值对)     │   (DataToken)  │   (单一值)      │    │
│  └──────┬──────┘               └────────┬────────┘    │
│         │                              │              │
│         │ 存储                          │ 存储         │
│         ▼                              ▼              │
│  ┌─────────────┐               ┌─────────────────┐    │
│  │ DataList    │               │  基础类型        │    │
│  │ (有序列表)   │               │  int, float,    │    │
│  └─────────────┘               │  string, bool   │    │
│                                └─────────────────┘    │
│                                                       │
│  ┌─────────────────────────────────────────────┐      │
│  │           JSON 序列化格式                     │      │
│  │  DataDictionary ←→ JSON Object              │      │
│  │  DataList ←→ JSON Array                     │      │
│  │  DataToken ←→ JSON Value (需包装)            │      │
│  └─────────────────────────────────────────────┘      │
│                                                       │
└───────────────────────────────────────────────────────┘

```

**关键关系**：

- `DataToken` 是基础单元，存储单一值
- `DataDictionary` 和 `DataList` 是容器，**存储** DataToken
- 它们是"包含"关系，**不是继承关系**
- JSON 中：Object = Dictionary（字符串键），Array = List

---

## 第二章：VRCJson API 详解

> **命名空间**：`VRC.SDK3.Data`
> 
> **Udon Graph 中**：搜索 "Json"（不带 "VRC" 前缀）

### 2.1 序列化 — DataToken → JSON

#### 基本用法

```csharp
VRCJson.TrySerializeToJson(
    DataToken input,      // 要序列化的数据
    JsonExportType type,  // 导出格式
    out DataToken result  // 输出结果
);

```

<p class="callout danger">`VRCJson.TrySerializeToJson` ：这个是非常高开销行为，请务必在有需要的时候使用，并且缓存结果。</p>

#### JsonExportType 选项

<div class="table-wrapper" id="bkmrk-%E7%B1%BB%E5%9E%8B-%E8%AF%B4%E6%98%8E-%E7%A4%BA%E4%BE%8B%E8%BE%93%E5%87%BA-beautify-"><table><thead><tr><th>类型</th><th>说明</th><th>示例输出</th></tr></thead><tbody><tr><td>`Beautify`</td><td>格式化（可读）</td><td>换行 + 缩进</td></tr><tr><td>`Minify`</td><td>压缩（传输）</td><td>单行，无多余空格</td></tr></tbody></table>

</div>```json
// Beautify 输出
{
    "name": "Test",
    "value": 123
}

// Minify 输出
{"name":"Test","value":123}

```

#### 完整示例

```csharp
public DataDictionary Config;
public string JsonOutput;

public void SerializeData()
{
    // 初始化数据
    Config = new DataDictionary();
    Config.SetValue("name", "MyWorld");
    Config.SetValue("players", 20);
    Config.SetValue("enabled", true);
    
    // 序列化为格式化 JSON
    if (VRCJson.TrySerializeToJson(Config, JsonExportType.Beautify, out DataToken result))
    {
        JsonOutput = result.String;
        Debug.Log($"Serialized: {JsonOutput}");
    }
    else
    {
        Debug.LogError($"Serialization failed: {result.ToString()}");
    }
}

public void SerializeForNetwork()
{
    // 网络传输用 Minify（更小体积）
    if (VRCJson.TrySerializeToJson(Config, JsonExportType.Minify, out DataToken result))
    {
        // 发送网络请求...
    }
}

```

---

### 2.2 反序列化 — JSON → DataToken

#### 基本用法

```csharp
VRCJson.TryDeserializeFromJson(
    string jsonString,    // JSON 字符串
    out DataToken result  // 输出结果
);

```

#### 返回值判断

<div class="table-wrapper" id="bkmrk-%E8%BF%94%E5%9B%9E%E5%80%BC-result-%E5%86%85%E5%AE%B9-true-d"><table><thead><tr><th>返回值</th><th>result 内容</th></tr></thead><tbody><tr><td>`true`</td><td>DataDictionary 或 DataList</td></tr><tr><td>`false`</td><td>DataError（包含错误信息）</td></tr></tbody></table>

</div>#### 类型检查流程

```csharp
if (VRCJson.TryDeserializeFromJson(jsonString, out DataToken result))
{
    // 成功，检查具体类型
    if (result.TokenType == TokenType.DataDictionary)
    {
        DataDictionary dict = result.DataDictionary;
        // 处理字典...
    }
    else if (result.TokenType == TokenType.DataList)
    {
        DataList list = result.DataList;
        // 处理列表...
    }
}
else
{
    // 失败，获取错误信息
    Debug.LogError($"Deserialization failed: {result.ToString()}");
}

```

#### 完整示例

```csharp
public DataList Items;
public string ErrorMessage;

public void LoadFromJson(string jsonString)
{
    if (VRCJson.TryDeserializeFromJson(jsonString, out DataToken result))
    {
        if (result.TokenType == TokenType.DataList)
        {
            Items = result.DataList;
            Debug.Log($"Loaded {Items.Count} items");
        }
        else if (result.TokenType == TokenType.DataDictionary)
        {
            // 单个对象包装成列表
            Items = new DataList();
            Items.Add(result.DataDictionary);
            Debug.Log("Wrapped single object into list");
        }
    }
    else
    {
        ErrorMessage = result.ToString();
        Debug.LogError($"Failed to parse JSON: {ErrorMessage}");
    }
}

```

> **延迟解析特性**：VRCJSON 为了性能，只解析顶层 JSON。如果嵌套结构中有无效内容，`TryDeserializeFromJson` 可能返回 `true`，但在后续访问时 `TryGetValue` 会返回 `DataError.UnableToParse`。

---

### 2.3 JSON 格式限制与注意事项

<div class="table-wrapper" id="bkmrk-%E9%99%90%E5%88%B6-%E8%AF%B4%E6%98%8E-%E9%94%99%E8%AF%AF%E7%B1%BB%E5%9E%8B-object-re"><table><thead><tr><th>限制</th><th>说明</th><th>错误类型</th></tr></thead><tbody><tr><td>Object Reference</td><td>DataToken 包含对象引用</td><td>`TypeUnsupported`</td></tr><tr><td>非字符串键</td><td>Dictionary 中有非字符串 key</td><td>`TypeUnsupported`</td></tr><tr><td>NaN/Infinity</td><td>Float/Double 为无效数值</td><td>`ValueUnsupported`</td></tr><tr><td>根节点类型</td><td>根不是 Dictionary 或 List</td><td>`TypeUnsupported`</td></tr><tr><td>数字类型丢失</td><td>反序列化后所有数字为 `Double`</td><td>—</td></tr></tbody></table>

</div>**示例：触发 ValueUnsupported 错误**

```csharp
// 危险代码
DataDictionary bad = new DataDictionary();
bad.SetValue("value", float.NaN);  // 或 float.PositiveInfinity

if (VRCJson.TrySerializeToJson(bad, JsonExportType.Minify, out DataToken result))
{
    // 成功
}
else
{
    // result.Error == DataError.ValueUnsupported
    Debug.LogError(result.ToString());
}

```

---

## 第三章：安全使用指南

### 3.1 类型校验的重要性

**问题**：直接访问错误的类型属性会导致 UdonBehaviour `halt`（暂停执行）。

```csharp
// 危险代码
DataToken unknown = /* 从某处获取 */;

string value = unknown.String;  // 如果不是 String 类型会 halt

```

**解决方案**：

```csharp
// 安全代码
if (unknown.TokenType == TokenType.String)
{
    string value = unknown.String;  // 已确认类型
}

```

---

### 3.2 TryGetValue 安全读取模式

#### DataDictionary 安全读取

```csharp
// 方式一：基础 TryGetValue + 手动类型检查
if (Config.TryGetValue("name", out DataToken token))
{
    if (token.TokenType == TokenType.String)
    {
        string name = token.String;
    }
}

// 方式二：带 TokenType 的 TryGetValue（推荐）
if (Config.TryGetValue("name", TokenType.String, out DataToken token))
{
    string name = token.String;  // 类型已验证
}
else
{
    // 获取失败（键不存在 或 类型不匹配）
    Debug.Log($"Get failed, error: {token.Error}");
}

```

#### DataList 安全读取

```csharp
// 基础用法
if (PlayerList.TryGetValue(0, out DataToken token))
{
    Debug.Log(token.String);
}

// 带类型检查（推荐）
if (PlayerList.TryGetValue(0, TokenType.DataDictionary, out DataToken token))
{
    DataDictionary player = token.DataDictionary;
    // 处理玩家数据...
}

```

#### 使用默认值

```csharp
// 如果键不存在，使用默认值
public string GetConfigValue(DataDictionary config, string key, string defaultValue)
{
    if (config.TryGetValue(key, TokenType.String, out DataToken token))
    {
        return token.String;
    }
    return defaultValue;
}

// 使用示例
string mode = GetConfigValue(Config, "gameMode", "normal");

```

---

### 3.3 错误处理 — DataError 枚举

#### 错误类型一览

<div class="table-wrapper" id="bkmrk-dataerror-%E5%90%AB%E4%B9%89-%E8%A7%A6%E5%8F%91%E5%9C%BA%E6%99%AF-no"><table><thead><tr><th>DataError</th><th>含义</th><th>触发场景</th></tr></thead><tbody><tr><td>`None`</td><td>无错误</td><td>正常获取（非错误 Token）</td></tr><tr><td>`KeyDoesNotExist`</td><td>键不存在</td><td>访问 Dictionary 中不存在的键</td></tr><tr><td>`IndexOutOfRange`</td><td>索引越界</td><td>访问 List 中超出范围的索引</td></tr><tr><td>`TypeMismatch`</td><td>类型不匹配</td><td>TryGetValue 指定类型但实际类型不符</td></tr><tr><td>`TypeUnsupported`</td><td>类型不支持</td><td>序列化包含不支持的类型</td></tr><tr><td>`ValueUnsupported`</td><td>值不支持</td><td>序列化包含 NaN/Infinity</td></tr><tr><td>`UnableToParse`</td><td>解析失败</td><td>JSON 格式错误或嵌套内容无效</td></tr></tbody></table>

</div>> **关于 DataError.None**：`Error` 属性访问**永远不会抛出异常**。即使 DataToken 不是错误类型，访问 `Error` 也只会返回 `DataError.None`，而不会导致程序 halt。

#### 错误处理示例

```csharp
public void SafeRead(DataDictionary config, string key)
{
    if (config.TryGetValue(key, TokenType.String, out DataToken result))
    {
        Debug.Log($"Success: {result.String}");
    }
    else
    {
        // 获取失败，检查错误类型
        switch (result.Error)
        {
            case DataError.KeyDoesNotExist:
                Debug.LogWarning($"Key '{key}' not found");
                break;
            case DataError.TypeMismatch:
                Debug.LogWarning($"Key '{key}' is not a string");
                break;
            default:
                Debug.LogError($"Error: {result.Error}");
                break;
        }
    }
}

```

---

### 3.4 值提取：直接访问 vs ToString

<div class="table-wrapper" id="bkmrk-%E6%96%B9%E5%BC%8F-%E5%AE%89%E5%85%A8%E6%80%A7-%E6%80%A7%E8%83%BD-%E9%80%82%E7%94%A8%E5%9C%BA%E6%99%AF-token"><table><thead><tr><th>方式</th><th>安全性</th><th>性能</th><th>适用场景</th></tr></thead><tbody><tr><td>`token.String`</td><td>需类型检查</td><td>快</td><td>100% 确定是 String</td></tr><tr><td>`token.Int/Float/Bool`</td><td>需类型检查</td><td>快</td><td>100% 确定是特定类型</td></tr><tr><td>`token.Number`</td><td>检查 IsNumber</td><td>快</td><td>任何数值类型</td></tr><tr><td>`token.ToString()`</td><td>始终安全</td><td>稍慢</td><td>调试、通用转换</td></tr></tbody></table>

</div>#### 推荐实践

```csharp
// 调试输出（始终安全）
Debug.Log(token.ToString());

// 生产代码（类型确定）
if (config.TryGetValue("id", TokenType.Int, out DataToken idToken))
{
    int id = idToken.Int;  // 类型已验证，安全
}

// 数值统一处理
if (unknown.IsNumber)
{
    double value = unknown.Number;  // 自动转换为 double
}

```

---

## 第四章：实战用法

### 4.1 从网络加载 JSON

使用 `VRCStringDownloader` 从远程服务器获取配置数据。

```csharp
using VRC.SDK3.Data;
using VRC.SDKBase;
using VRC.Udon;
using VRC.Udon.Common.Interfaces;

public class JsonLoader : UdonSharpBehaviour
{
    public string RemoteUrl = "https://example.com/config.json";
    public DataList Items;
    
    void Start()
    {
        LoadJson();
    }
    
    public void LoadJson()
    {
        VRCStringDownloader.LoadUrl(RemoteUrl, (IUdonEventReceiver)this);
    }
    
    public void OStringLoadSuccess(IVRCStringDownload result)
    {
        string json = result.Result;
        
        if (VRCJson.TryDeserializeFromJson(json, out DataToken token))
        {
            if (token.TokenType == TokenType.DataList)
            {
                Items = token.DataList;
                Debug.Log($"Loaded {Items.Count} items from server");
            }
            else if (token.TokenType == TokenType.DataDictionary)
            {
                // 单个对象包装为列表
                Items = new DataList();
                Items.Add(token.DataDictionary);
                Debug.Log("Wrapped single object");
            }
        }
        else
        {
            Debug.LogError($"Parse error: {token.ToString()}");
        }
    }
    
    public void OStringLoadError(IVRCStringDownload result)
    {
        Debug.LogError($"Download failed: {result.Error}");
        // 加载备用配置或使用默认值
    }
}

```

> **安全要求**：URL 必须是 HTTPS 不然VRChat不会受理这次Get请求——哪怕在Unity里也一样

---

### 4.2 网络同步模式

官方推荐使用 `OnPreSerialization` 和 `OnDeserialization` 进行网络同步：

```csharp
[UdonSynced]
private string _json = "";

private DataDictionary _dictionary;

public override void OnPreSerialization()
{
    if (VRCJson.TrySerializeToJson(_dictionary, JsonExportType.Minify, out DataToken result))
    {
        _json = result.String;
    }
    else
    {
        Debug.LogError(result.ToString());
    }
}

public override void OnDeserialization()
{
    if (VRCJson.TryDeserializeFromJson(_json, out DataToken result))
    {
        _dictionary = result.DataDictionary;
    }
    else
    {
        Debug.LogError(result.ToString());
    }
}

```

> 💡 **提示**：使用 `Minify` 格式可以减少网络传输的数据量。 —— 但是我也不推荐你使用他来网络同步

---

### 4.3 本地 JSON 字符串解析

```csharp
public class LocalJsonParser : UdonSharpBehaviour
{
    // 在 Inspector 中手动输入 JSON
    [TextArea(3, 10)]
    public string JsonInput;
    
    public DataList ParsedData;
    
    public void ParseInput()
    {
        if (string.IsNullOrWhiteSpace(JsonInput))
        {
            Debug.LogError("Input is empty");
            return;
        }
        
        if (VRCJson.TryDeserializeFromJson(JsonInput, out DataToken token))
        {
            if (token.TokenType == TokenType.DataList)
            {
                ParsedData = token.DataList;
            }
            else if (token.TokenType == TokenType.DataDictionary)
            {
                ParsedData = new DataList();
                ParsedData.Add(token.DataDictionary);
            }
        }
        else
        {
            Debug.LogError($"Parse failed: {token.ToString()}");
        }
    }
}

```

---

### 4.4 数据的读取与写入

#### 创建结构化数据

```csharp
public DataList CreatePlayerList()
{
    DataList players = new DataList();
    
    // 玩家 1
    DataDictionary player1 = new DataDictionary();
    player1.SetValue("id", 1);
    player1.SetValue("name", "Alice");
    player1.SetValue("score", 1000);
    player1.SetValue("isOnline", true);
    players.Add(player1);
    
    // 玩家 2
    DataDictionary player2 = new DataDictionary();
    player2.SetValue("id", 2);
    player2.SetValue("name", "Bob");
    player2.SetValue("score", 850);
    player2.SetValue("isOnline", false);
    players.Add(player2);
    
    return players;
}

```

> 这下无语了把，这玩意真的只能一个个SetValue()

#### 读取结构化数据

```csharp
public void ProcessPlayers(DataList players)
{
    for (int i = 0; i < players.Count; i++)
    {
        if (players.TryGetValue(i, TokenType.DataDictionary, out DataToken playerToken))
        {
            DataDictionary player = playerToken.DataDictionary;
            
            // 安全提取各字段（正确写法）
            int id = 0;
            if (player.TryGetValue("id", TokenType.Int, out DataToken idToken)) 
            {
                id = idToken.Int;
            }
            
            string name = "Unknown";
            if (player.TryGetValue("name", TokenType.String, out DataToken nameToken))
            {
                name = nameToken.String;
            }
            
            int score = 0;
            if (player.TryGetValue("score", TokenType.Int, out DataToken scoreToken))
            {
                score = scoreToken.Int;
            }
            
            Debug.Log($"Player {id}: {name} - {score} pts");
        }
    }
}

```

> ⚠️ **注意**：`TryGetValue` 不返回布尔值用于三元运算符，它通过 `out` 参数返回结果。

---

### 4.5 DataList + DataDictionary 组合结构

这是 VRChat 开发中最常用的数据结构，JSON 表示如下：

```json
[
    {
        "id": 1,
        "name": "Sword",
        "price": 100,
        "stats": {
            "attack": 15,
            "durability": 100
        }
    },
    {
        "id": 2,
        "name": "Shield",
        "price": 80,
        "stats": {
            "defense": 20,
            "durability": 150
        }
    }
]

```

对应代码：

```csharp
public DataList ParseInventoryJson(string json)
{
    if (!VRCJson.TryDeserializeFromJson(json, out DataToken token))
    {
        Debug.LogError(token.ToString());
        return new DataList();
    }
    
    if (token.TokenType != TokenType.DataList)
    {
        Debug.LogError("Expected array root");
        return new DataList();
    }
    
    // 验证每个物品的结构
    DataList items = token.DataList;
    for (int i = 0; i < items.Count; i++)
    {
        if (!items.TryGetValue(i, TokenType.DataDictionary, out DataToken itemToken))
        {
            Debug.LogWarning($"Item {i} is not a dictionary");
            continue;
        }
        
        DataDictionary item = itemToken.DataDictionary;
        
        // 检查必需字段
        if (!item.ContainsKey("id") || !item.ContainsKey("name"))
        {
            Debug.LogWarning($"Item {i} missing required fields");
        }
    }
    
    return items;
}

```

---

## 第五章：性能与限制

### 5.1 性能开销说明

**DataToken 系统存在显著性能开销**，主要原因：

1. **装箱/拆箱**：基础类型需要 boxing 到 DataToken
2. **类型检查**：每次访问都需要验证类型
3. **内存分配**：每次创建 DataToken 都会分配新对象
4. **反射/查找**：容器操作涉及额外的查找开销
5. **JSON 全量解析**：以下操作会触发未解析值的全量解析： 
    - DataDictionary: `ContainsValue`、`ShallowClone`、`GetValues`
    - DataList: `Contains`、`IndexOf`、`LastIndexOf`

**使用建议**：

- 用于配置加载、初始化阶段
- 用于网络通信数据处理
- 避免在每帧更新中使用
- 避免在大量数据循环中使用

### 5.2 DataList vs Array 使用建议

<div class="table-wrapper" id="bkmrk-%E5%9C%BA%E6%99%AF-%E4%BD%BF%E7%94%A8-datalist-%E4%BD%BF%E7%94%A8-ar"><table><thead><tr><th>场景</th><th>使用 DataList</th><th>使用 Array</th></tr></thead><tbody><tr><td>容器嵌套</td><td>✅ 需要嵌套容器时</td><td>❌ 数组需要严格定义深度</td></tr><tr><td>类型混合</td><td>✅ 支持混合类型</td><td>❌ 只能包含单一类型</td></tr><tr><td>动态增删</td><td>✅ 动态增删元素</td><td>❌ 长度固定</td></tr><tr><td>网络同步</td><td>⚠️ 可通过 JSON 实现</td><td>✅ 普通数组更高效</td></tr><tr><td>性能关键</td><td>❌ 每帧迭代有开销</td><td>✅ 性能更优</td></tr><tr><td>Unity 序列化</td><td>❌ 不支持</td><td>✅ 支持</td></tr></tbody></table>

</div>**何时选择 DataList**：

- 需要嵌套容器（DataList/Dictionary）
- 需要混合类型数据
- 需要动态增删元素
- 需要通过 JSON 进行网络同步

**何时选择 Array**：

- 只需要单一类型
- 需要网络同步（性能更优）
- 性能关键场景（每帧迭代）
- 需要 Unity 序列化支持

### 5.3 序列化限制总结

<div class="table-wrapper" id="bkmrk-%E9%A1%B9%E7%9B%AE-%E9%99%90%E5%88%B6-%E6%94%AF%E6%8C%81%E7%9A%84%E9%94%AE%E7%B1%BB%E5%9E%8B-%E4%BB%85%E5%AD%97%E7%AC%A6%E4%B8%B2%EF%BC%88js"><table><thead><tr><th>项目</th><th>限制</th></tr></thead><tbody><tr><td>支持的键类型</td><td>仅字符串（JSON 兼容）</td></tr><tr><td>不支持的值</td><td>Object Reference、NaN、Infinity</td></tr><tr><td>数字精度</td><td>反序列化后统一为 Double</td></tr><tr><td>根节点</td><td>必须为 Dictionary 或 List</td></tr></tbody></table>

</div>### 5.4 使用建议

1. **预验证 JSON**：在发送到网络前验证格式
2. **使用默认值**：缺失字段时提供默认处理
3. **错误隔离**：单个字段错误不应中断整个解析
4. **定期清理**：大量数据使用后适时释放

```csharp
// 推荐：预验证模式
public bool ValidateForSerialization(DataDictionary data)
{
    // 检查危险值
    foreach (DataToken key in data.GetKeys())
    {
        data.TryGetValue(key, out DataToken value);
        
        // 检查数值类型
        if (value.IsNumber)
        {
            double num = value.Number;
            if (double.IsNaN(num) || double.IsInfinity(num))
            {
                Debug.LogWarning($"Invalid number at key: {key}");
                return false;
            }
        }
    }
    return true;
}

```

---

<div class="table-wrapper" id="bkmrk--18"></div>### 附录 ：官方资源链接

- [VRChat Data Containers 总览](https://creators.vrchat.com/worlds/udon/data-containers/)
- [DataToken 官方文档](https://creators.vrchat.com/worlds/udon/data-containers/data-tokens/)
- [VRCJson 官方文档](https://creators.vrchat.com/worlds/udon/data-containers/vrcjson/)
- [DataDictionary 文档](https://creators.vrchat.com/worlds/udon/data-containers/data-dictionaries/)
- [DataList 文档](https://creators.vrchat.com/worlds/udon/data-containers/data-lists/)

# 超级Udon笔记

这里的都是我写过的项目里面摘抄出来的部分，用来提供给大家学习使用，不一定是最优解，只是能用的标准。

#### 1：根据玩家名称获取玩家PlayerAPI

```c#
public void ChangeTarget(string playername)
        {
            //需要更改所有者的物体
            Gameobject OBJ；
  
            VRCPlayerApi[] Players = new VRCPlayerApi[VRCPlayerApi.GetPlayerCount()];
            VRCPlayerApi.GetPlayers(Players);
  
            var Displayers = new string[Players.Length];
            for (int i = 0; i < Players.Length; i++)
            {
                var Name = Players[i].displayName;
                Displayers[i] = Name;
            }

            var PlayerIndex = Array.IndexOf(Displayers, playername);
            if (PlayerIndex == -1)
            {
                Debug.Log("未找到玩家");
                return;
            }
            else
            {
               var playerAPI = Players[PlayerIndex]

               Networking.SetOwner(playerAPI, OBJ);
            }
        }
```

#### 2:根据数组动态创建按钮

```c#
public void CreaterButton(string[] Displayers)
  {
        var Buttomss = new GameObject[Displayers.Length];

        //动态创建部分
        if (Displayers.Length > Buttoms.Length)
        {
            //增加
            for (int i = Buttoms.Length; i < Buttomss.Length; i++)
            {
                var a = Instantiate(_prefab, ObjParent);
                Buttomss[i] = a;
            }
            for (int i = 0; i < Buttoms.Length; i++)
            {
                Buttomss[i] = Buttoms[i];
            }
            Buttoms = Buttomss;
        }
        else if (Displayers.Length < Buttoms.Length)
        {
            //减少
            for (int i = Displayers.Length; i < Buttoms.Length; i++)
            {
                Destroy(Buttoms[i]);
            }
            for (int i = 0; i < Buttomss.Length; i++)
            {
                Buttomss[i] = Buttoms[i];
            }
            Buttoms = Buttomss;
        }

        
        //初始化按钮部分-需要按钮有对应的字段的方法，自己使用需要自己更改
        for (int i = 0; i < Displayers.Length; i++)
        {
            var cmpObj = Buttoms[i].GetComponent<UdonBehaviour>();
            cmpObj.SetProgramVariable("DefectName", $"#{i}  " + Displayers[i]);
            cmpObj.SetProgramVariable("Index", i);
            cmpObj.SendCustomEvent("Init");
            Buttoms[i] = cmpObj.gameObject;
        }
  }
```

#### 3：稳定的网络同步方法

```c#

//用来显示Debug的文字框
public Text DebugT;

//网络重传机制-更安全的网络同步
    public void SafeSync()
    {
        NetworkingOn = true;
        SendCustomNetworkEvent(VRC.Udon.Common.Interfaces.NetworkEventTarget.All, "NetworkStart1");
        DebugT.text = "Safe SYNC parameter Is Start,Your value Set Successful";
        OnDeserialization();
    }

    public void NetworkS()
    {   if(NetworkingOn == false) { return; }
        if (LocalPlayer == Networking.GetOwner(this.gameObject)) { RequestSerialization(); }
        NetworkingOn = false;
        DebugT.text = "Safe SYNC Sending data";
        
    }

    public void NetworkStart1()
    {
        if (LocalPlayer == Networking.GetOwner(this.gameObject)) { DebugT.text = "Wait Other Player Return Info"; return; }
        NetworkingStart = true;
        SendCustomEventDelayedSeconds("NetwokEnd", 10);
        SendCustomNetworkEvent(VRC.Udon.Common.Interfaces.NetworkEventTarget.Owner, "NetworkS");
        
    }

    public void NetwokEnd()
    {
        if (NetworkingStart) 
        { 
            SendCustomNetworkEvent(VRC.Udon.Common.Interfaces.NetworkEventTarget.Owner, "NetworkError");
            DebugT.text = "Oh,Your SYNC is Error,System will be ReSync";
        }
    }

    public void NetworkError()
    {
        DebugT.text = "SomeBody SYNC is Error,System will be ReSync";
        RequestSerialization();
    }

    public override void OnDeserialization() 
    {
        //接受成功
        NetworkingStart = false;
    }
```

#### 4:Json的序列化和反序列化

```c#
//序列化
public void ToJson(DataToken List)
    {
        VRCJson.TrySerializeToJson(List, JsonExportType.Beautify, out DataJson);
        JsonOutput.text = $"{DataJson}";
        Debug.text =
            $" The attempt to export has ended. Here are the results of your attempt<br> " +
            $"{DataJson}" ;
    }
    //反序列化
    public void FromJson(string Json)
    {
        VRCJson.TryDeserializeFromJson(Json, out DataJson);
      //这部分根据自己的设计来写，属于检查Json是不是你要的Json
        if (DataJson.TokenType == TokenType.DataList)
        {
            List = (DataList)DataJson;
            
        }
        else
        {
            UnityEngine.Debug.Log(DataJson);
            UnityEngine.Debug.Log("DataList");
            Debug.text = $"Reading the token failed, and it is possible that the data has been corrupted";
            return;
        }
        Debug.text = "Get Data Successful , Data initialization is complete";
    }
```

# 特性驱动架构：从拖拽引用到声明式 Udon 开发

> 本文档系统分析 VVMW 工程中的 C# 特性体系，通过源码追踪揭示复杂特性如何从简单声明解析而来，最终实现声明式、高可维护性的 Udon 工程。
> 
> 冷知识：VVMW工程也就是VizVid播放器~~~~

---

## 第1层：设计哲学层

### 1.1 VVMW 的设计目标与 Udon 约束

VVMW 是一个视频播放器系统，面临三个 Udon 核心约束：

```
┌─────────────────────────────────────────────────────────────────────┐
│                        Udon 约束三角                                 │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│         约束1：拖拽引用繁琐                                           │
│         "Core 需要被 PlayerHandler、FrontendHandler、UIHandler... 引用" │
│         → 手动拖拽 10+ 个组件？                                       │
│                                                                      │
│              ↓                                                       │
│                                                                      │
│         约束2：事件订阅重复                                           │
│         "每个组件都要监听 OnVideoPlay、OnVideoPause、OnVideoEnd..."    │
│         → 每个组件写 3 遍相同的订阅代码？                              │
│                                                                      │
│              ↓                                                       │
│                                                                      │
│         约束3：序列化逻辑分散                                         │
│         "Loop 属性改变时需要同步、UI 需要更新、AudioLink 需要同步..."   │
│         → 在 N 个地方写相同的同步逻辑？                                │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘

```

### VVMW 的解法

<div class="table-wrapper" id="bkmrk-%E7%BA%A6%E6%9D%9F-%E4%BC%A0%E7%BB%9F%E6%96%B9%E6%A1%88-vvmw-%E6%96%B9%E6%A1%88-%E6%8B%96%E6%8B%BD%E5%BC%95%E7%94%A8"><table><thead><tr><th>约束</th><th>传统方案</th><th>VVMW 方案</th></tr></thead><tbody><tr><td>拖拽引用</td><td>手动拖拽 10+ 次</td><td>`[Locatable] + [Resolve]` 自动定位</td></tr><tr><td>事件订阅</td><td>每个组件重复订阅</td><td>`[BindUdonSharpEvent]` 声明式绑定</td></tr><tr><td>序列化逻辑</td><td>散落在各处</td><td>`[FieldChangeCallback]` 属性化</td></tr></tbody></table>

</div>**核心思路**：用声明替代命令，用标记替代调用。

---

## 1.2 声明式 vs 命令式：设计范式转变

### 命令式风格（传统 Udon）

```csharp
public class MyUI : UdonSharpBehaviour {
    public Core core;  // 需要手动拖拽赋值
    
    void Start() {
        // 手动订阅事件
        core.OnVideoPlay += OnVideoPlay;
        core.OnVideoPause += OnVideoPause;
        core.OnVideoEnd += OnVideoEnd;
    }
    
    public void OnVideoPlay() { /* 更新 UI */ }
    public void OnVideoPause() { /* 更新 UI */ }
    public void OnVideoEnd() { /* 更新 UI */ }
}

```

**问题**：

- 每增加一个监听事件，需要修改 Start 方法
- 组件未初始化时事件可能为 null
- 拖拽引用容易遗漏

### 声明式风格（VVMW）

```csharp
public class MyUI : UdonSharpBehaviour {
    [SerializeField, LocalizedLabel(Key = "JLChnToZ.VRC.VVMW.Core")]
    [Resolve(nameof(handler) + "." + nameof(FrontendHandler.core), HideInInspectorIfResolvable = true)]
    [Locatable, BindUdonSharpEvent(
        nameof(OnVideoPlay),
        nameof(OnVideoPause),
        nameof(OnVideoEnd)
    )] Core core;
    
    // 事件方法声明即可，编辑器自动完成订阅
    public void OnVideoPlay() { /* 更新 UI */ }
    public void OnVideoPause() { /* 更新 UI */ }
    public void OnVideoEnd() { /* 更新 UI */ }
}

```

**优势**：

- 引用赋值由编辑器自动完成
- 事件订阅由特性声明指定
- 代码意图清晰，易于维护

---

## 1.3 特性系统的三层架构

VVMW 的特性系统分为三个层次：

```
┌─────────────────────────────────────────────────────────────────────┐
│                      特性系统三层架构                                 │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  第1层：声明层（开发者编写）                                          │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │ [Locatable]              ← "我需要被自动定位"                   │  │
│  │ [Resolve(...)]           ← "帮我解析这个引用"                   │  │
│  │ [BindUdonSharpEvent(...)]← "帮我订阅这些事件"                   │  │
│  └───────────────────────────────────────────────────────────────┘  │
│                              ↓                                       │
│  第2层：编辑器层（VRC.Foundation 实现）                             │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │ LocatableDrawer          ← 检测拖拽 → 自动解析 → 填充引用        │  │
│  │ ResolveDrawer            ← 查找 GameObject → 填充 _GO 字段       │  │
│  │ BindUdonSharpEventDrawer ← 反射方法 → 订阅事件                   │  │
│  └───────────────────────────────────────────────────────────────┘  │
│                              ↓                                       │
│  第3层：运行时层（Udon VM 执行）                                      │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │ [UdonSynced] + Property   ← 网络同步 + 业务逻辑                 │  │
│  │ FieldChangeCallback       ← 值变化 → 回调触发                   │  │
│  └───────────────────────────────────────────────────────────────┘  │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘

```

**关键洞察**：开发者的声明只是"意图"，真正的绑定工作由编辑器在第2层完成。

---

# 第2层：核心特性详解与源码追踪

## 2.1 引用解析三剑客

### 2.1.1 Locatable — 自动定位

**设计动机**：解决预制体实例化后引用丢失的问题。

**源码追踪：特性声明**

来自 `AutoPlayOnNearV2.cs` 第 16-18 行：

```csharp
[SerializeField, LocalizedLabel(Key = "JLChnToZ.VRC.VVMW.Core")]
[Resolve(nameof(handler) + "." + nameof(FrontendHandler.core), HideInInspectorIfResolvable = true)]
[Locatable] Core core;

```

**源码追踪：特性定义**

VRC.Foundation 中的 `LocatableAttribute` 定义：

```csharp
// JLChnToZ.VRC.Foundation/LocatableAttribute.cs（VRC.Foundation 源码）
namespace JLChnToZ.VRC.Foundation {
    public enum InstaniatePrefabHierachyPosition { Before, After }

    [AttributeUsage(AttributeTargets.Field, AllowMultiple = false)]
    public class LocatableAttribute : Attribute {
        /// <summary>缺失时实例化的预制体路径</summary>
        public string InstaniatePrefabPath { get; set; }
        /// <summary>预制体实例化位置</summary>
        public InstaniatePrefabHierachyPosition InstaniatePrefabPosition { get; set; }
    }
}

```

**参数解析**：

<div class="table-wrapper" id="bkmrk-%E5%8F%82%E6%95%B0-%E7%B1%BB%E5%9E%8B-%E8%AF%B4%E6%98%8E-instaniatep"><table><thead><tr><th>参数</th><th>类型</th><th>说明</th></tr></thead><tbody><tr><td>`InstaniatePrefabPath`</td><td>string</td><td>缺失时实例化的预制体路径</td></tr><tr><td>`InstaniatePrefabPosition`</td><td>enum</td><td>预制体实例化位置（Before/After）</td></tr></tbody></table>

</div>**Udon 适配**：

- UdonSharp 编译时字段自动解析
- 支持场景中定位和预制体实例化两种模式

---

### 2.1.2 Resolve — 引用解析

**设计动机**：替代手动拖拽引用，支持条件隐藏。

**源码追踪：特性声明**

来自 `VideoPlayerHandler.cs` 第 34、40-42 行：

```csharp
// 解析自身组件
[SerializeField, HideInInspector, Resolve(".")] Animator animator;
[SerializeField, HideInInspector, Resolve(".")] BaseVRCVideoPlayer videoPlayer;
[SerializeField, HideInInspector, Resolve(".")] new Renderer renderer;

// 解析其他字段引用的 GameObject
[SerializeField, HideInInspector, Resolve(nameof(primaryAudioSource))] GameObject primaryAudioSourceGO;

```

**源码追踪：复杂表达式解析**

来自 `AutoPlayOnNearV2.cs` 第 17 行：

```csharp
[Resolve(nameof(handler) + "." + nameof(FrontendHandler.core), HideInInspectorIfResolvable = true)]

```

这段代码解析了链式引用：

- `handler` → `FrontendHandler` 字段
- `.core` → `FrontendHandler` 的 `core` 属性
- 最终找到 `Core` 实例

**源码追踪：特性定义**

```csharp
// JLChnToZ.VRC.Foundation/ResolveAttribute.cs
namespace JLChnToZ.VRC.Foundation {
    [AttributeUsage(AttributeTargets.Field, AllowMultiple = false)]
    public class ResolveAttribute : Attribute {
        /// <summary>要解析的路径，如 "." 表示自身，"nameof(core)" 表示某字段</summary>
        public string Path { get; }
        /// <summary>默认为 true，仅在字段为空时解析</summary>
        public bool NullOnly { get; set; } = true;
        /// <summary>解析成功后隐藏字段</summary>
        public bool HideInInspectorIfResolvable { get; set; }

        public ResolveAttribute(string path) => Path = path;
    }
}

```

**参数解析**：

<div class="table-wrapper" id="bkmrk-%E5%8F%82%E6%95%B0-%E7%B1%BB%E5%9E%8B-%E8%AF%B4%E6%98%8E-path-string"><table><thead><tr><th>参数</th><th>类型</th><th>说明</th></tr></thead><tbody><tr><td>`Path`</td><td>string</td><td>解析路径</td></tr><tr><td>`NullOnly`</td><td>bool</td><td>默认为 true，仅在字段为空时解析</td></tr><tr><td>`HideInInspectorIfResolvable`</td><td>bool</td><td>解析成功后隐藏字段</td></tr></tbody></table>

</div>**Udon 适配**：

- Path 支持 `nameof()` 表达式和字符串拼接
- 自动查找引用的 GameObject 并填充

---

### 2.1.3 BindUdonSharpEvent — UdonSharp 事件绑定

**设计动机**：自动化 UdonSharpBehaviour 事件订阅。

**源码追踪：特性声明**

来自 `FrontendHandler.cs` 第 20-34 行：

```csharp
[LocalizedLabel(Key = "JLChnToZ.VRC.VVMW.Core"), Locatable, BindUdonSharpEvent(
    nameof(_OnRangeLoopToggled),
    nameof(_OnScreenSharedPropertiesChanged),
    nameof(_OnSpeedChange),
    nameof(_OnSyncOffsetChange),
    nameof(_OnTitleData),
    nameof(_OnVideoBeginLoad),
    nameof(_OnVideoError),
    nameof(_OnVolumeChange),
    nameof(OnVideoReady),
    nameof(OnVideoStart),
    nameof(OnVideoPlay),
    nameof(OnVideoPause),
    nameof(OnVideoEnd)
), SingletonCoreControl] public Core core;

```

这个声明绑定了 **13 个事件**！如果用传统方式，需要写 13 次手动订阅。

**源码追踪：特性定义**

```csharp
// JLChnToZ.VRC.Foundation/BindUdonSharpEventAttribute.cs
namespace JLChnToZ.VRC.Foundation {
    [AttributeUsage(AttributeTargets.Field, AllowMultiple = false)]
    public class BindUdonSharpEventAttribute : Attribute {
        public string[] EventNames { get; }
        public BindUdonSharpEventAttribute(params string[] eventNames) => EventNames = eventNames;
    }
}

```

**源码追踪：编辑器解析逻辑**

VRC.Foundation 的编辑器会在运行时通过反射找到这些方法：

```csharp
// 伪代码：VRC.Foundation 编辑器的 BindUdonSharpEvent 解析逻辑
void ProcessBindUdonSharpEvent(FieldInfo field, BindUdonSharpEventAttribute attr) {
    var target = field.GetValue(component) as UdonSharpBehaviour;
    foreach (var eventName in attr.EventNames) {
        // 通过反射找到事件方法
        var method = target.GetType().GetMethod(eventName, 
            BindingFlags.Public | BindingFlags.Instance);
        if (method != null) {
            // 自动订阅（通过 VRC.Foundation 的内部机制）
            SubscribeToEvent(target, eventName);
        }
    }
}

```

**Udon 适配**：

- 事件方法必须声明为 `public`
- 编辑器通过反射找到同名字方法并自动订阅
- 支持多个事件一次性绑定

---

## 2.2 属性化同步

### FieldChangeCallback — 字段变化回调

**设计动机**：将业务逻辑封装在属性中，而非散落在各处。

**源码追踪：特性声明**

来自 `Core.cs` 第 49-50 行：

```csharp
[UdonSynced, FieldChangeCallback(nameof(Loop))]
bool loop;

```

**源码追踪：属性实现**

来自 `Core.cs` 第 116-128 行：

```csharp
public bool Loop {
    get => loop;
    set {
        bool wasLoop = loop;
        loop = value;
        // 业务逻辑 1：同步到 Handler
        if (Utilities.IsValid(activeHandler)) activeHandler.Loop = value;
        // 业务逻辑 2：网络同步
        if (synced && wasLoop != value && Networking.IsOwner(gameObject))
            RequestSerialization();
        // 业务逻辑 3：AudioLink 同步
#if AUDIOLINK_V1
        if (IsAudioLinked()) audioLink.SetMediaLoop(value ? MediaLoop.LoopOne : MediaLoop.None);
#endif
    }
}

```

**特性定义**：

```csharp
// JLChnToZ.VRC.Foundation/FieldChangeCallbackAttribute.cs
namespace JLChnToZ.VRC.Foundation {
    [AttributeUsage(AttributeTargets.Field, AllowMultiple = false)]
    public class FieldChangeCallbackAttribute : Attribute {
        public string CallbackPropertyName { get; }
        public FieldChangeCallbackAttribute(string callbackPropertyName) => 
            CallbackPropertyName = callbackPropertyName;
    }
}

```

**原理图解**：

```
┌─────────────────────────────────────────────────────────────────────┐
│                      FieldChangeCallback 原理                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  传统方式：                                                          │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ loop = true;           ← 直接赋值，无业务逻辑                │   │
│  │ SyncToHandler();        ← 手动调用业务逻辑                   │   │
│  │ RequestSerialization(); ← 手动网络同步                       │   │
│  │ SyncAudioLink();        ← 手动 AudioLink 同步               │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                              ↓                                       │
│  FieldChangeCallback 方式：                                          │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ Loop = true;             ← 通过属性赋值                     │   │
│  │       ↓                                                           │   │
│  │ 属性 setter 被调用                                               │   │
│  │       ↓                                                           │   │
│  │ 所有业务逻辑自动执行（同步 Handler、同步网络、同步 AudioLink）    │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│  源码映射：                                                          │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ [UdonSynced, FieldChangeCallback(nameof(Loop))]              │   │
│  │ bool loop;  ← backing field，存储实际值                       │   │
│  │                                                              │   │
│  │ public bool Loop {   ← 属性，触发 [FieldChangeCallback]     │   │
│  │     get => loop;     ← 读取 backing field                   │   │
│  │     set { ... }      ← 赋值触发业务逻辑                      │   │
│  │ }                                                              │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘

```

**Udon 适配**：

- `[UdonSynced]` 字段 + `[FieldChangeCallback]` 实现网络同步与业务逻辑联动
- 赋值通过属性触发，确保业务逻辑不遗漏

---

## 2.3 BindEvent — Unity 事件绑定

**设计动机**：自动绑定 Unity 组件的事件（如 Button.onClick）到方法。

**源码追踪：特性声明**

来自 `ButtonEntry.cs` 第 16 行：

```csharp
[RequireComponent(typeof(Button))]
[BindEvent(typeof(Button), nameof(Button.onClick), nameof(_OnClick))]
public class ButtonEntry : UdonSharpBehaviour {
    public void _OnClick() { ... }
}

```

**源码追踪：完整使用实例**

来自 `ButtonEntry.cs` 第 1-109 行：

```csharp
using UnityEngine;
using UnityEngine.UI;
using TMPro;
using VRC.SDKBase;
using UdonSharp;
using JLChnToZ.VRC.Foundation;
using JLChnToZ.VRC.Foundation.I18N;

namespace JLChnToZ.VRC.VVMW {
    [UdonBehaviourSyncMode(BehaviourSyncMode.NoVariableSync)]
    [RequireComponent(typeof(Button))]
    [DisallowMultipleComponent]
    [BindEvent(typeof(Button), nameof(Button.onClick), nameof(_OnClick))]
    [AddComponentMenu("VizVid/Components/Button Entry")]
    [DefaultExecutionOrder(3)]
    public class ButtonEntry : UdonSharpBehaviour {
        LanguageManager manager;
        [SerializeField, LocalizedLabel] GameObject buttonGO;
        [SerializeField, HideInInspector, Resolve(nameof(buttonGO), NullOnly = false)] Text buttonText;
        [SerializeField, HideInInspector, Resolve(nameof(buttonGO), NullOnly = false)] TextMeshProUGUI buttonTMPro;
        
        /// <summary>回调目标</summary>
        [LocalizedLabel] public UdonSharpBehaviour callbackTarget;
        /// <summary>回调事件名</summary>
        [LocalizedLabel] public string callbackEventName;
        /// <summary>变量名</summary>
        [LocalizedLabel] public string callbackVariableName;
        /// <summary>自定义数据</summary>
        [LocalizedLabel] public object callbackUserData;

        /// <summary>本地化文本参数</summary>
        public object[] Args {
            get => args;
            set { args = value; _OnLanguageChanged(); }
        }

        /// <summary>本地化文本键</summary>
        public string Key {
            get => key;
            set { key = value; _OnLanguageChanged(); }
        }

        /// <summary>本地化文本</summary>
        public string Text {
            get {
                if (Utilities.IsValid(buttonText)) return buttonText.text;
                if (Utilities.IsValid(buttonTMPro)) return buttonTMPro.text;
                return "";
            }
        }

        /// <summary>语言管理器</summary>
        public LanguageManager LanguageManager {
            get => manager;
            set {
                manager = value;
                if (Utilities.IsValid(manager)) manager._AddListener(this);
                _OnLanguageChanged();
            }
        }

        void _OnLanguageChanged() {
            var result = manager.GetLocale(key);
            if (Utilities.IsValid(args) && args.Length > 0)
                result = string.Format(result, args);
            if (Utilities.IsValid(buttonText)) buttonText.text = result;
            if (Utilities.IsValid(buttonTMPro)) buttonTMPro.text = result;
        }

        /// <summary>按钮点击回调入口</summary>
        public void _OnClick() {
            if (!Utilities.IsValid(callbackTarget)) return;
            if (!string.IsNullOrEmpty(callbackVariableName))
                callbackTarget.SetProgramVariable(callbackVariableName, callbackUserData);
            if (!string.IsNullOrEmpty(callbackEventName))
                callbackTarget.SendCustomEvent(callbackEventName);
        }
    }
}

```

**特性定义**：

```csharp
// JLChnToZ.VRC.Foundation/BindEventAttribute.cs
namespace JLChnToZ.VRC.Foundation {
    [AttributeUsage(AttributeTargets.Class, AllowMultiple = false)]
    public class BindEventAttribute : Attribute {
        public Type ComponentType { get; }
        public string EventMemberName { get; }
        public string CallbackMethodName { get; }

        public BindEventAttribute(Type componentType, string eventMemberName, string callbackMethodName) {
            ComponentType = componentType;
            EventMemberName = eventMemberName;
            CallbackMethodName = callbackMethodName;
        }
    }
}

```

**参数解析**：

<div class="table-wrapper" id="bkmrk-%E5%8F%82%E6%95%B0-%E7%B1%BB%E5%9E%8B-%E8%AF%B4%E6%98%8E-%E7%AC%AC1%E5%8F%82%E6%95%B0-type-%E7%9B%AE"><table><thead><tr><th>参数</th><th>类型</th><th>说明</th></tr></thead><tbody><tr><td>第1参数</td><td>Type</td><td>目标组件类型（如 `typeof(Button)`）</td></tr><tr><td>第2参数</td><td>string</td><td>事件成员名称（如 `nameof(Button.onClick)`）</td></tr><tr><td>第3参数</td><td>string</td><td>回调方法名称</td></tr></tbody></table>

</div>---

## 2.4 本地化支持

### LocalizedLabel — 本地化标签

**设计动机**：支持多语言 UI 显示。

**源码追踪：简单用法**

来自 `AbstractMediaPlayerHandler.cs` 第 29-34 行：

```csharp
[LocalizedLabel]
#if COMPILER_UDONSHARP
public
#else
[SerializeField] internal
#endif
string playerName = "";

```

**源码追踪：指定 key**

来自 `FrontendHandler.cs` 第 20 行：

```csharp
[LocalizedLabel(Key = "JLChnToZ.VRC.VVMW.Core")] public Core core;

```

**编译期条件**（核心技巧）：

```csharp
[LocalizedLabel]
#if COMPILER_UDONSHARP
public                    // UdonSharp：公开访问
#else
[SerializeField] internal // 编辑器：可序列化
#endif
string playerName = "";

```

这确保了：

- UdonSharp 编译时：字段是 `public`，供其他 UdonSharpBehaviour 访问
- 编辑器编译时：字段是 `[SerializeField] internal`，可在 Inspector 中编辑

---

### LocalizedEnum — 本地化枚举

**源码实例**（FrontendHandler.cs）：

```csharp
[SerializeField, LocalizedLabel, LocalizedEnum] 
internal CoreMatchingStrategy coreControlStrategy = CoreMatchingStrategy.All;

```

---

## 2.5 编辑器增强

### SingletonCoreControl — 单例核心控制

**设计动机**：编辑器据此提供特殊交互（如一键定位 Core）。

**源码追踪：特性声明**

来自 `FrontendHandler.cs` 第 34 行：

```csharp
[SingletonCoreControl] public Core core;

```

**源码追踪：编辑器类型发现**

来自 `EditorBase.cs` 第 84-94 行：

```csharp
if (type.IsSubclassOf(typeof(UdonSharpBehaviour))) {
    var fields = type.GetFields(BindingFlags.Instance | BindingFlags.Public | BindingFlags.NonPublic);
    if (fields.Length == 0) continue;
    foreach (var field in fields) {
        // 查找：Core 类型 + SingletonCoreControl 特性
        if (field.FieldType == typeof(Core) && 
            field.GetCustomAttribute<SingletonCoreControlAttribute>() != null) {
            // 建立映射：脚本类型 → (Core字段, 编辑器类型)
            controllableTypes[type] = (field, editorType);
            break;
        }
    }
}

```

**特性定义**：

```csharp
// JLChnToZ.VRC.Foundation/SingletonCoreControlAttribute.cs
namespace JLChnToZ.VRC.Foundation {
    [AttributeUsage(AttributeTargets.Field, AllowMultiple = false, Inherited = true)]
    public class SingletonCoreControlAttribute : Attribute { }
}

```

---

### HelpURL — 帮助链接

**设计动机**：在 Inspector 中添加文档链接。

**源码实例**（Core.cs 第 28 行）：

```csharp
[HelpURL("https://xtlcdn.github.io/VizVid/docs/#vvmw-game-object")]
public partial class Core : UdonSharpBehaviour { }

```

---

# 第3层：编译器条件架构

这是 VVMW 的精髓：**同一份源码，两个身份**。

## 3.1 COMPILER\_UDONSHARP 的双模式编译

```csharp
#if COMPILER_UDONSHARP
    public           // UdonSharp：运行时公开访问
#else
    internal protected // 编辑器工具：程序集内访问
#endif
    Core core;

```

**编译目标**：

```
┌─────────────────────────────────────────────────────────────────────┐
│                        双模式编译架构                                 │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  源码文件                                                            │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │ #if COMPILER_UDONSHARP                                        │  │
│  │     public Core core;           ← 编译为 Udon Bytecode        │  │
│  │ #else                                                         │  │
│  │     internal Core core;         ← 编译为 .NET 程序集          │  │
│  │ #endif                                                        │  │
│  └───────────────────────────────────────────────────────────────┘  │
│                        ↓                         ↓                   │
│         ┌─────────────────────┐     ┌─────────────────────┐        │
│         │ Udon Bytecode       │     │ .NET 程序集          │        │
│         │ (VRChat 运行时)      │     │ (Unity 编辑器)       │        │
│         └─────────────────────┘     └─────────────────────┘        │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘

```

## 3.2 条件可见性模式

VVMW 定义了四种条件可见性模式：

```csharp
// 模式 1：简单 public/internal
#if COMPILER_UDONSHARP
    public
#else
    internal
#endif
    void MyMethod() { }

// 模式 2：带 protected
#if COMPILER_UDONSHARP
    public
#else
    internal protected
#endif
    Core core;

// 模式 3：完全相反
#if COMPILER_UDONSHARP
    internal
#else
    public
#endif
    Core core;

```

## 3.3 跨模式设计实例

### VizVidBehaviour — 行为基类

来自 `VizVidBehaviour.cs`：

```csharp
namespace JLChnToZ.VRC.VVMW {
    // 运行时：普通 UdonSharpBehaviour 基类
    public abstract class VizVidBehaviour : UdonSharpBehaviour {
        public virtual void OnVideoStart() { }
        public virtual void OnVideoPlay() { }
        public virtual void OnVideoPause() { }
        public virtual void OnVideoEnd() { }
    }
    
#if UNITY_EDITOR && !COMPILER_UDONSHARP
    // 编辑器：额外实现接口
    public abstract partial class VizVidBehaviour : IVizVidCompoonent {
        Core IVizVidCompoonent.Core => core;
    }
#endif
}

```

### AbstractMediaPlayerHandler — 媒体处理器

来自 `AbstractMediaPlayerHandler.cs`：

```csharp
// 运行时：声明属性
#if COMPILER_UDONSHARP
    public
#else
    internal protected
#endif
    override bool IsActive { get => isActive; set => isActive = value; }

// 编辑器：声明字段
#if COMPILER_UDONSHARP
    [NonSerialized] public
#else
    internal protected
#endif
    Core core;

```

---

# 第4层：编辑器工具链

## 4.1 IPreprocessor — 场景预处理器

**设计动机**：在场景加载前执行数据预处理。

**接口定义**：

```csharp
// JLChnToZ.VRC.Foundation/IPreprocessor.cs
namespace JLChnToZ.VRC.Foundation {
    public interface IPreprocessor {
        int Priority { get; }
        void OnPreprocess(Scene scene);
    }
}

```

**源码追踪：VVMW 实现**

来自 `CoreConfigPreprocessor.cs` 第 1-41 行：

```csharp
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.SceneManagement;
using UdonSharpEditor;
using JLChnToZ.VRC.Foundation.Editors;

namespace JLChnToZ.VRC.VVMW.Editors {
    internal class CoreConfigPreprocessor : IPreprocessor {
        public int Priority => 99;  // 执行优先级

        public void OnPreprocess(Scene scene) {
            // 遍历场景中所有 Core 组件
            foreach (var core in scene.IterateAllComponents<Core>()) {
                
                // === 预处理 1：音量持久化 ===
#if VRC_ENABLE_PLAYER_PERSISTENCE
                if (core.enablePersistence) {
                    var pathStack = new Stack<string>();
                    for (var transform = core.transform; transform; transform = transform.parent)
                        pathStack.Push(transform.name);
                    var path = string.Join("/", pathStack);
                    core.volumePersistenceKey = $"VVMW:{path}:Volume";
                    core.mutedPersistenceKey = $"VVMW:{path}:Muted";
                }
#endif
                
                // === 预处理 2：音频控制器收集 ===
                var audioControllers = new List<AbstractAudioController>();
                var audioSources = new HashSet<AudioSource>(core.audioSources);
                foreach (var audioSource in core.audioSources) {
                    if (audioSource == null || !audioSource.TryGetComponent(out AbstractAudioController controller))
                        continue;
                    // 自动注入 Core 引用
                    controller.core = core;
                    audioSources.Remove(audioSource);
                    audioControllers.Add(controller);
                    // 同步到 Udon
                    UdonSharpEditorUtility.CopyProxyToUdon(controller);
                }
                core.audioSources = new AudioSource[audioSources.Count];
                audioSources.CopyTo(core.audioSources);
                core.audioControllers = audioControllers.ToArray();
                
                // === 预处理 3：区域配置检测 ===
                core.hasRegion = ActiveRegionConfig.GetRegionConfigs(core).Count > 0;
                
                // === 预处理 4：默认音量处理 ===
                if (!core.muteOnOutOfRange) core.outOfRangeVolume = 1f;
                
                // === 预处理 5：同步到 Udon ===
                UdonSharpEditorUtility.CopyProxyToUdon(core);
            }
        }
    }
}

```

**预处理流程图解**：

```
┌─────────────────────────────────────────────────────────────────────┐
│                    IPreprocessor 执行流程                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  场景加载前                                                          │
│       │                                                             │
│       ↓                                                             │
│  VRCFoundation.Preprocessors.OnPreprocess(scene)                     │
│       │                                                             │
│       ├── CoreConfigPreprocessor.Priority = 99                      │
│       │       │                                                     │
│       │       ↓                                                     │
│       │   1. 音量持久化路径生成                                       │
│       │   2. 音频控制器收集 + Core 注入                              │
│       │   3. 区域配置检测                                            │
│       │   4. 默认值处理                                              │
│       │   5. CopyProxyToUdon 同步                                    │
│       │                                                             │
│       └── [其他 Preprocessor...]                                     │
│                                                                      │
│       ↓                                                             │
│  场景加载完成（数据已预处理）                                          │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘

```

**使用场景**：

- 引用填充（查找依赖组件）
- 数据校验（确保配置完整）
- 性能优化（预计算、缓存）

---

## 4.2 CustomEditor — 自定义编辑器

**设计动机**：提供品牌化的 Inspector UI。

**源码追踪：编辑器基类**

来自 `EditorBase.cs` 第 14-224 行（核心部分）：

```csharp
namespace JLChnToZ.VRC.VVMW.Editors {
    public abstract class VVMWEditorBase : Editor {
        // === 静态资源 ===
        const string bannerTextureGUID = "e8354bc2ac14e86498c0983daf484661";
        const string iconGUID = "a24ecd1d23cca9e46871bc17dfe3bd46";
        const string fontGUID = "088cf7162d0a81c46ad54028cfdcb382";
        
        // === 类型映射 ===
        protected static readonly Dictionary<Type, (FieldInfo fieldInfo, Type editorType)> controllableTypes = new Dictionary<Type, (FieldInfo, Type)>();
        protected static readonly Dictionary<Type, Type> editorTypes = new Dictionary<Type, Type>();
        
        // === 启动时类型收集 ===
        [InitializeOnLoadMethod]
        static void Init() {
            AssemblyReloadEvents.afterAssemblyReload += GatherControlledTypes;
            GatherControlledTypes();
        }
        
        // === 类型发现核心逻辑 ===
        static void GatherControlledTypes() {
            controllableTypes.Clear();
            const BindingFlags flags = BindingFlags.Instance | BindingFlags.Public | BindingFlags.NonPublic;
            
            // 遍历所有程序集中的所有类型
            foreach (var assembly in AppDomain.CurrentDomain.GetAssemblies())
                foreach (var type in assembly.GetTypes()) {
                    if (type.IsAbstract) continue;
                    
                    // === 查找 UdonSharpBehaviour ===
                    if (type.IsSubclassOf(typeof(UdonSharpBehaviour))) {
                        var fields = type.GetFields(flags);
                        foreach (var field in fields) {
                            // 查找：Core 类型 + SingletonCoreControl 特性
                            if (field.FieldType == typeof(Core) && 
                                field.GetCustomAttribute<SingletonCoreControlAttribute>() != null) {
                                editorTypes.TryGetValue(type, out var editorType);
                                controllableTypes[type] = (field, editorType);
                                break;
                            }
                        }
                    }
                }
        }
        
        // === Banner 绘制 ===
        protected static void DrawBanner() {
            // 绘制 Logo
            var bannerRect = new Rect(...);
            GUI.DrawTexture(bannerRect, bannerTexture);
            
            // 绘制版本号
            var versionStyle = new GUIStyle(EditorStyles.whiteLargeLabel) { ... };
            GUI.Label(new Rect(...), $"v{selfUpdater.CurrentVersion}", versionStyle);
        }
        
        // === Inspector GUI ===
        public override void OnInspectorGUI() {
            // 绘制 Banner
            DrawBanner();
            
            // UdonSharp 特殊处理
            if (isUdonSharp) {
                if (UdonSharpGUI.DrawDefaultUdonSharpBehaviourHeader(target, true, false))
                    return;
            }
            
            DrawInspectorGUI();
        }
    }
}

```

**源码追踪：Core 编辑器实现**

来自 `CoreEditor.cs` 第 138-150 行：

```csharp
public override void DrawEmbeddedInspectorGUI() {
    if (HandleDragDrop()) return;
    
    var autoPlayControllerEditor = GetAutoPlayControllerEditor();
    if (autoPlayControllerEditor != null)
        autoPlayControllerEditor.serializedObject.Update();
        
    DrawCommonSettings(autoPlayControllerEditor);      // 通用设置
    HorizontalLine();
    DrawDefaultBehaviourSettings(autoPlayControllerEditor);  // 默认行为
    HorizontalLine();
    DrawAdvancedSettings(autoPlayControllerEditor);   // 高级设置
    
    if (autoPlayControllerEditor != null)
        autoPlayControllerEditor.serializedObject.ApplyModifiedProperties();
}

```

---

## 4.3 启动钩子

### InitializeOnLoadMethod

**设计动机**：编辑器启动时执行初始化。

**源码追踪**

来自 `EditorBase.cs` 第 30-34 行：

```csharp
[InitializeOnLoadMethod]
static void Init() {
    // 订阅程序集重载事件
    AssemblyReloadEvents.afterAssemblyReload += GatherControlledTypes;
    // 执行类型收集
    GatherControlledTypes();
}

```

### DidReloadScripts

**设计动机**：脚本重新编译后执行刷新。

**源码追踪**（来自其他 VVMW 组件）：

```csharp
[DidReloadScripts]
public static void RefreshAll() {
    regionConfigTable.Clear();
    foreach (var config in FindObjectsOfType<ActiveRegionConfig>(true))
        config.Register();
}

```

---

# 第6层：代码流程解析

本章通过完整流程图和伪代码，详细解析 VRC.Foundation 特性系统如何从"声明"变成"功能"。

## 6.1 完整生命周期总览

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    VRC.Foundation 特性系统完整生命周期                          │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  阶段 1：编辑器启动（一次性）                                                 │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │ [InitializeOnLoadMethod]                                              │   │
│  │        ↓                                                              │   │
│  │ GatherControlledTypes()                                              │   │
│  │        ↓                                                              │   │
│  │ 遍历所有程序集 → 找到 UdonSharpBehaviour → 查找 [SingletonCoreControl] │   │
│  │        ↓                                                              │   │
│  │ 建立映射：controllableTypes[type] = (fieldInfo, editorType)           │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                              ↓                                               │
│  阶段 2：场景加载前（每个场景一次）                                           │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │ IPreprocessor.OnPreprocess(scene)                                    │   │
│  │        ↓                                                              │   │
│  │ CoreConfigPreprocessor.OnPreprocess()                                │   │
│  │        ↓                                                              │   │
│  │ 遍历场景中所有 Core → 填充 audioControllers → CopyProxyToUdon()      │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                              ↓                                               │
│  阶段 3：组件检视（持续）                                                    │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │ CustomEditor.OnInspectorGUI()                                        │   │
│  │        ↓                                                              │   │
│  │ DrawEmbeddedInspectorGUI()                                           │   │
│  │        ↓                                                              │   │
│  │ PropertyDrawer 处理特性字段                                            │   │
│  │   ├── LocatableDrawer → 拖拽检测 → 自动解析                           │   │
│  │   ├── ResolveDrawer   → 引用解析 → 填充 GameObject                    │   │
│  │   └── BindUdonSharpEventDrawer → 反射方法 → 订阅事件                  │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                              ↓                                               │
│  阶段 4：运行时执行（Udon VM）                                               │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │ Udon Bytecode 执行                                                    │   │
│  │        ↓                                                              │   │
│  │ [UdonSynced] + [FieldChangeCallback] → 属性赋值触发业务逻辑            │   │
│  │ BindUdonSharpEvent 订阅的方法 → 事件触发时自动调用                    │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘

```

---

## 6.2 GatherControlledTypes — 类型发现流程

### 6.2.1 源码追踪

来自 `EditorBase.cs` 第 30-34、55-94 行：

```csharp
// === 启动时注册 ===
[InitializeOnLoadMethod]
static void Init() {
    // 订阅程序集重载事件（脚本修改后自动重新收集）
    AssemblyReloadEvents.afterAssemblyReload += GatherControlledTypes;
    // 立即执行一次
    GatherControlledTypes();
}

// === 类型收集核心逻辑 ===
static void GatherControlledTypes() {
    controllableTypes.Clear();
    const BindingFlags flags = BindingFlags.Instance | BindingFlags.Public | BindingFlags.NonPublic;
    
    // 遍历所有已加载的程序集
    foreach (var assembly in AppDomain.CurrentDomain.GetAssemblies())
        foreach (var type in assembly.GetTypes()) {
            if (type.IsAbstract) continue;
            
            // === 步骤 1：查找 UdonSharpBehaviour ===
            if (type.IsSubclassOf(typeof(UdonSharpBehaviour))) {
                var fields = type.GetFields(flags);
                if (fields.Length == 0) continue;
                
                // === 步骤 2：遍历字段查找 [SingletonCoreControl] ===
                foreach (var field in fields) {
                    // 查找条件：Core 类型 + SingletonCoreControl 特性
                    if (field.FieldType == typeof(Core) && 
                        field.GetCustomAttribute<SingletonCoreControlAttribute>() != null) {
                        
                        // === 步骤 3：建立映射 ===
                        editorTypes.TryGetValue(type, out var editorType);
                        controllableTypes[type] = (field, editorType);
                        break;
                    }
                }
            }
            
            // === 步骤 4：收集 CustomEditor 类型 ===
            if (type.IsSubclassOf(typeof(VVMWEditorBase))) {
                var customEditorAttribute = type.GetCustomAttribute<CustomEditor>();
                if (customEditorAttribute == null) continue;
                
                var inspectedType = inspectedTypeField.GetValue(customEditorAttribute) as Type;
                
                // 更新映射
                if (controllableTypes.TryGetValue(inspectedType, out var value))
                    controllableTypes[inspectedType] = (value.fieldInfo, type);
                editorTypes[inspectedType] = type;
                
                // 处理继承关系
                if ((bool)editorForChildClassesField.GetValue(customEditorAttribute)) {
                    inheritedEditors[inspectedType] = type;
                    // 为所有子类建立映射...
                }
            }
        }
}

```

### 6.2.2 流程图解

```
┌─────────────────────────────────────────────────────────────────────┐
│                  GatherControlledTypes 执行流程                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  启动时触发                                                           │
│       │                                                             │
│       ↓                                                             │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ for each assembly in AppDomain.CurrentDomain.GetAssemblies() │   │
│  │       │                                                       │   │
│  │       ↓                                                       │   │
│  │   for each type in assembly.GetTypes()                       │   │
│  │       │                                                       │   │
│  │       ├───[否]─ type.IsSubclassOf(UdonSharpBehaviour)? ──┐   │   │
│  │       │                                                       │   │
│  │       │   [是]                                                │   │
│  │       │       │                                               │   │
│  │       │       ↓                                               │   │
│  │       │   for each field in type.GetFields()                 │   │
│  │       │       │                                               │   │
│  │       │       ├───[否]─ field 是 Core 类型? ──┐              │   │
│  │       │       │                               │              │   │
│  │       │       │   [是]                          │              │   │
│  │       │       │       │                       │              │   │
│  │       │       │       ↓                       │              │   │
│  │       │       │   [否]─ 有 SingletonCoreControl? ─→ 跳过       │   │
│  │       │       │       │                                       │   │
│  │       │       │   [是]                                        │   │
│  │       │       │       │                                       │   │
│  │       │       │       ↓                                       │   │
│  │       │       │   建立映射: controllableTypes[type] = ...    │   │
│  │       │       │                                                       │   │
│  │       └───[是]─ 是 VVMWEditorBase?                               │   │
│  │               │                                                       │   │
│  │               ↓                                                       │   │
│  │           收集 CustomEditor 类型                                     │   │
│  └─────────────────────────────────────────────────────────────┘   │
│       │                                                             │
│       ↓                                                             │
│  完成：controllableTypes 和 editorTypes 已填充                       │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘

```

---

## 6.3 Locatable + Resolve — 引用解析完整流程

### 6.3.1 从声明到解析的完整链路

以 `AutoPlayOnNearV2.cs` 第 16-21 行为例：

```csharp
// 开发者编写的声明
[SerializeField, LocalizedLabel(Key = "JLChnToZ.VRC.VVMW.Core")]
[Resolve(nameof(handler) + "." + nameof(FrontendHandler.core), HideInInspectorIfResolvable = true)]
[Locatable] Core core;

// 隐式声明（编辑器自动生成）
[SerializeField, HideInInspector, Resolve(nameof(core))] GameObject coreGO;
[SerializeField, HideInInspector, Resolve(nameof(handler))] GameObject handlerGO;
[SerializeField, HideInInspector, BindUdonSharpEvent(nameof(_OnMatchingCoresChanged))] 
ActiveRegionManager activeRegionManager;

```

### 6.3.2 ResolveDrawer 解析流程

```
┌─────────────────────────────────────────────────────────────────────┐
│                    ResolveDrawer 解析流程                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  1. 属性绘制器被调用                                                   │
│       │                                                             │
│       ↓                                                             │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ public override void OnGUI(Rect position, SerializedProperty│   │
│  │                              property, GUIContent label)     │   │
│  │       │                                                       │   │
│  │       ↓                                                       │   │
│  │   var attribute = fieldInfo.GetCustomAttribute<ResolveAttribute>();│
│  │       │                                                       │   │
│  │       ↓                                                       │   │
│  │   // 获取 Path                                                │   │
│  │   var path = attribute.Path;  // 如 "handler.core" 或 "."    │   │
│  └─────────────────────────────────────────────────────────────┘   │
│       │                                                             │
│       ↓                                                             │
│  2. Path 解析                                                        │
│       │                                                             │
│       ├─── "." ───────────────────────────→ 解析自身组件              │
│       │                                                             │
│       ├─── "nameof(core)" ────────────────→ 解析 core 字段           │
│       │                                                             │
│       └─── "handler.core" ────────────────→ 链式解析（见下方）        │
│                                                                      │
│  3. 链式解析（handler.core）                                          │
│       │                                                             │
│       ┌─────────────────────────────────────────────────────────┐   │
│       │ 步骤 1: 解析 "handler" → FrontendHandler 实例            │   │
│       │           ↓                                              │   │
│       │ 步骤 2: 访问 ".core" → FrontendHandler.core → Core 实例  │   │
│       │           ↓                                              │   │
│       │ 步骤 3: 获取 Core 的 GameObject                          │   │
│       │           ↓                                              │   │
│       │ 步骤 4: 赋值给 coreGO 字段                                │   │
│       └─────────────────────────────────────────────────────────┘   │
│       │                                                             │
│       ↓                                                             │
│  4. 条件隐藏                                                          │
│       │                                                             │
│       if (attribute.HideInInspectorIfResolvable && resolved)        │
│           editorGUI.BeginDisabledGroup(true);                       │
│       │                                                             │
│       ↓                                                             │
│  5. 绘制 UI 并返回                                                    │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘

```

### 6.3.3 LocatableDrawer 拖拽检测流程

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LocatableDrawer 拖拽检测流程                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  OnGUI() 被调用                                                      │
│       │                                                             │
│       ↓                                                             │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ var e = Event.current;                                       │   │
│  │                                                               │   │
│  │ switch (e.type) {                                            │   │
│  │   case EventType.DragUpdated:                                 │   │
│  │       // 拖拽进入                                               │   │
│  │       if (IsValidDrag(e)) {                                   │   │
│  │           DragAndDrop.visualMode = DragAndDropVisualMode.Link;│   │
│  │           e.Use();                                            │   │
│  │       }                                                        │   │
│  │       break;                                                   │   │
│  │                                                               │   │
│  │   case EventType.DragPerform:                                 │   │
│  │       // 拖拽释放                                               │   │
│  │       if (IsValidDrag(e)) {                                   │   │
│  │           var target = DragAndDrop.objectReferences[0];       │   │
│  │           ResolveAndAssign(target);  // 解析并赋值              │   │
│  │           e.Use();                                            │   │
│  │       }                                                        │   │
│  │       break;                                                   │   │
│  │ }                                                              │   │
│  └─────────────────────────────────────────────────────────────┘   │
│       │                                                             │
│       ↓                                                             │
│  ResolveAndAssign():                                                 │
│       │                                                             │
│       ┌─────────────────────────────────────────────────────────┐   │
│       │ if (NullOnly && currentValue != null) return;          │   │
│       │                                                           │   │
│       │ var resolved = Resolve(Path);  // 解析引用                │   │
│       │ if (resolved != null) {                                  │   │
│       │     serializedObject.Update();                            │   │
│       │     property.objectReferenceValue = resolved;            │   │
│       │     serializedObject.ApplyModifiedProperties();           │   │
│       │ }                                                         │   │
│       └─────────────────────────────────────────────────────────┘   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘

```

---

## 6.4 BindUdonSharpEvent — 事件订阅完整流程

### 6.4.1 源码追踪：特性处理逻辑

```csharp
// 伪代码：VRC.Foundation 编辑器的 BindUdonSharpEvent 处理逻辑
void ProcessBindUdonSharpEvent(UdonSharpBehaviour component, FieldInfo field, 
                                BindUdonSharpEventAttribute attr) {
    // === 步骤 1：获取字段值（被绑定的 Core 实例） ===
    var targetComponent = field.GetValue(component) as UdonSharpBehaviour;
    if (targetComponent == null) return;
    
    // === 步骤 2：获取事件发送者（Core 或其基类）===
    var eventSender = GetEventSender(targetComponent);
    if (eventSender == null) return;
    
    // === 步骤 3：遍历特性中声明的所有事件名 ===
    foreach (var eventName in attr.EventNames) {
        // === 步骤 4：通过反射查找回调方法 ===
        var callbackMethod = component.GetType().GetMethod(
            eventName,
            BindingFlags.Public | BindingFlags.Instance | BindingFlags.DeclaredOnly
        );
        
        if (callbackMethod == null) {
            Debug.LogWarning($"Method '{eventName}' not found on {component.GetType().Name}");
            continue;
        }
        
        // === 步骤 5：注册到事件系统 ===
        RegisterEventListener(eventSender, eventName, component, callbackMethod);
    }
}

```

### 6.4.2 流程图解

```
┌─────────────────────────────────────────────────────────────────────┐
│              BindUdonSharpEvent 事件订阅完整流程                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  场景加载 / 组件添加                                                  │
│       │                                                             │
│       ↓                                                             │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ for each UdonSharpBehaviour in scene                         │   │
│  │       │                                                       │   │
│  │       ↓                                                       │   │
│  │   for each field in behaviour.GetType().GetFields()          │   │
│  │       │                                                       │   │
│  │       ├───[否]─ 有 BindUdonSharpEvent? ──┐                  │   │
│  │       │                                   │                  │   │
│  │       │   [是]                            │                  │   │
│  │       │       │                           │                  │   │
│  │       │       ↓                           │                  │   │
│  │       │   获取 attr.EventNames[]                                 │   │
│  │       │       │                           │                  │   │
│  │       │       ↓                           │                  │   │
│  │       │   for each eventName in attr.EventNames                │   │
│  │       │       │                           │                  │   │
│  │       │       ↓                           │                  │   │
│  │       │   var method = component.GetMethod(eventName)         │   │
│  │       │       │                           │                  │   │
│  │       │       ├───[失败]─ 跳过              │                  │   │
│  │       │       │                           │                  │   │
│  │       │       [成功]                        │                  │   │
│  │       │       │                           │                  │   │
│  │       │       ↓                           │                  │   │
│  │       │   // 注册到 VRC.Foundation 事件系统                    │   │
│  │       │   EventRegistry.Register(component, eventName, method) │   │
│  └─────────────────────────────────────────────────────────────┘   │
│       │                                                             │
│       ↓                                                             │
│  事件触发时（Core.SendEvent("_OnMatchingCoresChanged")）             │
│       │                                                             │
│       ↓                                                             │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ var listeners = EventRegistry.GetListeners("_OnMatching...") │   │
│  │       │                                                       │   │
│  │       ↓                                                       │   │
│  │ for each listener in listeners                               │   │
│  │       │                                                       │   │
│  │       ↓                                                       │   │
│  │   listener.method.Invoke(listener.component, null);         │   │
│  │   // → AutoPlayOnNearV2._OnMatchingCoresChanged() 被调用        │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘

```

---

## 6.5 FieldChangeCallback — 属性化同步流程

### 6.5.1 编译时 vs 运行时

```
┌─────────────────────────────────────────────────────────────────────┐
│              FieldChangeCallback 编译时 vs 运行时                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ 编译时（UdonSharp 编译器）                                      │   │
│  │                                                              │   │
│  │ 源码:                                                         │   │
│  │ ┌─────────────────────────────────────────────────────────┐ │   │
│  │ │ [UdonSynced, FieldChangeCallback(nameof(Loop))]         │ │   │
│  │ │ bool loop;                                              │ │   │
│  │ │                                                         │ │   │
│  │ │ public bool Loop {                                      │ │   │
│  │ │     get => loop;                                        │ │   │
│  │ │     set {                                               │ │   │
│  │ │         loop = value;                                   │ │   │
│  │ │         if (Utilities.IsValid(activeHandler))            │ │   │
│  │ │             activeHandler.Loop = value;                 │ │   │
│  │ │         if (synced && ... && Networking.IsOwner(...))    │ │   │
│  │ │             RequestSerialization();                      │ │   │
│  │ │     }                                                    │ │   │
│  │ │ }                                                        │ │   │
│  │ └─────────────────────────────────────────────────────────┘ │   │
│  │       │                                                       │   │
│  │       ↓                                                       │   │
│  │ UdonSharp 编译器:                                             │   │
│  │ - 识别 [UdonSynced] → 生成同步逻辑                           │   │
│  │ - 识别 [FieldChangeCallback] → 生成 setter 包装              │   │
│  │ - 将所有对 loop 的赋值改为 Loop = value                       │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                              ↓                                       │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ 运行时（Udon VM）                                             │   │
│  │                                                              │   │
│  │ 玩家 A 调用: Loop = true;                                     │   │
│  │       │                                                       │   │
│  │       ↓                                                       │   │
│  │ Udon Bytecode:                                               │   │
│  │ ┌─────────────────────────────────────────────────────────┐ │   │
│  │ │ // setter 被调用                                         │ │   │
│  │ │ set_Loop(true);                                          │ │   │
│  │ │ // setter 内部:                                          │ │   │
│  │ │     loop = true;                                         │ │   │
│  │ │     activeHandler.Loop = true;  // 同步到视频处理器        │ │   │
│  │ │     if (owner) RequestSerialization();  // 网络同步       │ │   │
│  │ └─────────────────────────────────────────────────────────┘ │   │
│  │       │                                                       │   │
│  │       ↓                                                       │   │
│  │ RequestSerialization() → 序列化数据 → 发送给其他客户端          │   │
│  │       │                                                       │   │
│  │       ↓                                                       │   │
│  │ 玩家 B 收到同步 → OnDeserialization() → Loop 属性被赋值      │   │
│  │       │                                                       │   │
│  │       ↓                                                       │   │
│  │ B 的 activeHandler.Loop 也被设置为 true                        │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘

```

### 6.5.2 核心代码映射

来自 `Core.cs` 第 49-50、116-128 行：

```csharp
// === 声明 ===
[UdonSynced, FieldChangeCallback(nameof(Loop))]
bool loop;

// === 属性实现 ===
public bool Loop {
    get => loop;
    set {
        bool wasLoop = loop;
        loop = value;
        
        // 业务逻辑 1：同步到视频处理器
        if (Utilities.IsValid(activeHandler)) activeHandler.Loop = value;
        
        // 业务逻辑 2：网络同步
        if (synced && wasLoop != value && Networking.IsOwner(gameObject))
            RequestSerialization();
        
        // 业务逻辑 3：AudioLink 同步
#if AUDIOLINK_V1
        if (IsAudioLinked()) audioLink.SetMediaLoop(value ? MediaLoop.LoopOne : MediaLoop.None);
#endif
    }
}

```

**编译后（伪代码）**：

```csharp
// UdonSharp 生成的 setter
void set_Loop(bool value) {
    // 调用属性 setter
    this.Loop.set(value);  // 触发完整业务逻辑
}

// 当需要同步 loop 时，直接调用 setter
void SyncLoopToNetwork() {
    set_Loop(true);  // 自动触发 RequestSerialization()
}

```

---

## 6.6 完整示例：AutoPlayOnNearV2 的特性解析全过程

### 6.6.1 原始声明

来自 `AutoPlayOnNearV2.cs` 第 16-22 行：

```csharp
[SerializeField, LocalizedLabel(Key = "JLChnToZ.VRC.VVMW.Core")]
[Resolve(nameof(handler) + "." + nameof(FrontendHandler.core), HideInInspectorIfResolvable = true)]
[Locatable] Core core;

[SerializeField, HideInInspector, Resolve(nameof(core))] GameObject coreGO;
[SerializeField, HideInInspector, Resolve(nameof(handler))] GameObject handlerGO;
[SerializeField, HideInInspector, BindUdonSharpEvent(nameof(_OnMatchingCoresChanged))] 
ActiveRegionManager activeRegionManager;

```

### 6.6.2 解析时间线

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                  AutoPlayOnNearV2 特性解析完整时间线                          │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  T0: 编辑器启动                                                              │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │ [InitializeOnLoadMethod] Init()                                        │   │
│  │       ↓                                                              │   │
│  │ GatherControlledTypes()                                               │   │
│  │       ↓                                                              │   │
│  │ 发现 AutoPlayOnNearV2 是 UdonSharpBehaviour                           │   │
│  │       ↓                                                              │   │
│  │ 查找字段:                                                              │   │
│  │   - handler (FrontendHandler) + 无特性 → 跳过                         │   │
│  │   - core (Core) + [Locatable] → 建立映射                               │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                                                                              │
│  T1: 场景加载                                                                │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │ IPreprocessor.OnPreprocess(scene)                                     │   │
│  │       ↓                                                              │   │
│  │ CoreConfigPreprocessor.OnPreprocess()                                 │   │
│  │       ↓                                                              │   │
│  │ 为场景中每个 Core:                                                     │   │
│  │   - 收集 audioControllers                                             │   │
│  │   - 检测 hasRegion                                                     │   │
│  │   - CopyProxyToUdon()                                                 │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                                                                              │
│  T2: AutoPlayOnNearV2 被添加到 GameObject                                    │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │ ResolveDrawer 处理 core 字段:                                          │   │
│  │   - Path = "handler.core"                                              │   │
│  │   - 链式解析: handler → FrontendHandler.core → Core                   │   │
│  │   - 赋值: core = resolvedCore                                         │   │
│  │       ↓                                                              │   │
│  │ ResolveDrawer 处理 coreGO 字段:                                        │   │
│  │   - Path = "core"                                                     │   │
│  │   - 解析: core.gameObject                                              │   │
│  │   - 赋值: coreGO = core.gameObject                                     │   │
│  │       ↓                                                              │   │
│  │ ResolveDrawer 处理 handlerGO 字段:                                     │   │
│  │   - Path = "handler"                                                   │   │
│  │   - 解析: handler.gameObject                                           │   │
│  │   - 赋值: handlerGO = handler.gameObject                             │   │
│  │       ↓                                                              │   │
│  │ BindUdonSharpEventDrawer 处理 activeRegionManager:                    │   │
│  │   - eventName = "_OnMatchingCoresChanged"                              │   │
│  │   - 反射查找方法: AutoPlayOnNearV2._OnMatchingCoresChanged             │   │
│  │   - 注册到事件系统                                                     │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                                                                              │
│  T3: 运行时执行                                                              │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │ AutoPlayOnNearV2.OnEnable()                                           │   │
│  │       ↓                                                              │   │
│  │ isSynced = core.IsSynced;  // 使用自动解析的 core 引用                 │   │
│  │       ↓                                                              │   │
│  │ ...                                                                   │   │
│  │       ↓                                                              │   │
│  │ ActiveRegionManager._UpdateMatching()                                 │   │
│  │       ↓                                                              │   │
│  │ SendEvent("_OnMatchingCoresChanged");                                │   │
│  │       ↓                                                              │   │
│  │ VRC.Foundation 事件系统                                               │   │
│  │       ↓                                                              │   │
│  │ AutoPlayOnNearV2._OnMatchingCoresChanged() 被调用                      │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘

```

### 6.6.3 隐式字段的生成逻辑

```csharp
// 开发者只需声明带特性的字段
[Locatable] Core core;

// 编辑器自动"生成"（或理解为处理）隐式字段
[SerializeField, HideInInspector, Resolve(nameof(core))] GameObject coreGO;

// 这个"生成"是在什么时机发生的？
// 
// 答案：在 PropertyDrawer 处理时动态创建，而不是真正修改源文件。
// 
// 机制：
// 1. EditorBase.GatherControlledTypes() 扫描所有类型
// 2. 发现 AutoPlayOnNearV2 有 [Locatable] Core core
// 3. 在编辑器中为其创建对应的 _GO 字段引用
// 4. PropertyDrawer 检测到 [Resolve(nameof(core))] GameObject coreGO
// 5. 解析 core 并赋值给 coreGO

```

---

## 6.7 场景预处理完整流程

### 6.7.1 源码追踪

来自 `CoreConfigPreprocessor.cs` 第 11-39 行：

```csharp
public void OnPreprocess(Scene scene) {
    // === 遍历场景中所有 Core 组件 ===
    foreach (var core in scene.IterateAllComponents<Core>()) {
        
        // === 预处理 1：音量持久化路径 ===
#if VRC_ENABLE_PLAYER_PERSISTENCE
        if (core.enablePersistence) {
            // 生成层级路径作为持久化 key 前缀
            var pathStack = new Stack<string>();
            for (var transform = core.transform; transform; transform = transform.parent)
                pathStack.Push(transform.name);
            var path = string.Join("/", pathStack);
            core.volumePersistenceKey = $"VVMW:{path}:Volume";
            core.mutedPersistenceKey = $"VVMW:{path}:Muted";
        }
#endif
        
        // === 预处理 2：音频控制器收集 ===
        var audioControllers = new List<AbstractAudioController>();
        var audioSources = new HashSet<AudioSource>(core.audioSources);
        
        foreach (var audioSource in core.audioSources) {
            if (audioSource == null || 
                !audioSource.TryGetComponent(out AbstractAudioController controller))
                continue;
            
            // 自动注入 Core 引用
            controller.core = core;
            audioSources.Remove(audioSource);
            audioControllers.Add(controller);
            
            // 关键：将编辑器数据同步到 Udon
            UdonSharpEditorUtility.CopyProxyToUdon(controller);
        }
        
        core.audioSources = new AudioSource[audioSources.Count];
        audioSources.CopyTo(core.audioSources);
        core.audioControllers = audioControllers.ToArray();
        
        // === 预处理 3：区域配置检测 ===
        core.hasRegion = ActiveRegionConfig.GetRegionConfigs(core).Count > 0;
        
        // === 预处理 4：默认值处理 ===
        if (!core.muteOnOutOfRange) core.outOfRangeVolume = 1f;
        
        // === 预处理 5：同步到 Udon ===
        UdonSharpEditorUtility.CopyProxyToUdon(core);
    }
}

```

### 6.7.2 流程图解

```
┌─────────────────────────────────────────────────────────────────────┐
│                    场景预处理执行流程                                 │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  场景加载前（Build 或 Play）                                         │
│       │                                                             │
│       ↓                                                             │
│  VRCFoundation.Preprocessors.OnPreprocess(scene)                    │
│       │                                                             │
│       ↓                                                             │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ CoreConfigPreprocessor.OnPreprocess(scene)                   │   │
│  │       │                                                       │   │
│  │       ↓                                                       │   │
│  │   for each core in scene.IterateAllComponents<Core>()        │   │
│  │       │                                                       │   │
│  │       ┌─────────────────────────────────────────────────┐   │   │
│  │       │ 步骤 1: 持久化路径生成                           │   │   │
│  │       │   if (enablePersistence)                        │   │   │
│  │       │       path = "Parent/Child/Core"                │   │   │
│  │       │       volumePersistenceKey = "VVMW:path:Volume" │   │   │
│  │       └─────────────────────────────────────────────────┘   │   │
│  │       │                                                       │   │
│  │       ┌─────────────────────────────────────────────────┐   │   │
│  │       │ 步骤 2: 音频控制器收集                           │   │   │
│  │       │   audioControllers = []                        │   │   │
│  │       │   for each audioSource in core.audioSources     │   │   │
│  │       │       if (has AbstractAudioController) {        │   │   │
│  │       │           controller.core = core               │   │   │
│  │       │           audioControllers.Add(controller)    │   │   │
│  │       │           CopyProxyToUdon(controller)         │   │   │
│  │       │       }                                        │   │   │
│  │       └─────────────────────────────────────────────────┘   │   │
│  │       │                                                       │   │
│  │       ┌─────────────────────────────────────────────────┐   │   │
│  │       │ 步骤 3: 区域配置检测                             │   │   │
│  │       │   hasRegion = GetRegionConfigs(core).Count > 0  │   │   │
│  │       └─────────────────────────────────────────────────┘   │   │
│  │       │                                                       │   │
│  │       ┌─────────────────────────────────────────────────┐   │   │
│  │       │ 步骤 4: 同步到 Udon                              │   │   │
│  │       │   CopyProxyToUdon(core)                        │   │   │
│  │       └─────────────────────────────────────────────────┘   │   │
│  └─────────────────────────────────────────────────────────────┘   │
│       │                                                             │
│       ↓                                                             │
│  场景加载完成（所有数据已预处理）                                      │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘

```

---

## 6.8 CopyProxyToUdon — 数据同步机制

### 6.8.1 机制解释

```
┌─────────────────────────────────────────────────────────────────────┐
│                    CopyProxyToUdon 机制                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  UdonSharp 的双对象模型:                                             │
│                                                                      │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │                     Editor 中的对象                           │   │
│  │                                                               │   │
│  │  ┌──────────────────┐     ┌──────────────────┐             │   │
│  │  │ ProxyObject     │ ←──→ │ UdonBehaviour    │             │   │
│  │  │ (MonoBehaviour)  │     │ (UdonSharp)       │             │   │
│  │  │                  │     │                  │             │   │
│  │  │ core (字段)      │     │ _core (Udon变量)  │             │   │
│  │  │ audioControllers │     │ _audioControllers│             │   │
│  │  │ hasRegion        │     │ _hasRegion        │             │   │
│  │  └──────────────────┘     └──────────────────┘             │   │
│  │               ↑                   ↑                          │   │
│  │               │                   │                          │   │
│  │               └──── CopyProxyToUdon() ────┘                 │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
│  为什么需要这个？                                                      │
│  ┌─────────────────────────────────────────────────────────────┐   │
│  │ 1. UdonSharp 字段在编辑器中显示为 Proxy 对象                 │   │
│  │ 2. 预处理修改的是 Editor 中的数据                             │   │
│  │ 3. 需要同步到 UdonBehaviour 的内部变量                        │   │
│  │ 4. CopyProxyToUdon() 就是做这个同步                          │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘

```

---

# 第5层：工程实践

## 5.1 特性组合公式

### 基础组合：Core 引用（完整版）

来自 `AutoPlayOnNearV2.cs` 第 16-21 行：

```csharp
// 声明层：开发者编写的特性组合
[SerializeField, LocalizedLabel(Key = "JLChnToZ.VRC.VVMW.Core")]
[Resolve(nameof(handler) + "." + nameof(FrontendHandler.core), HideInInspectorIfResolvable = true)]
[Locatable] Core core;

// 隐藏的 GameObject 引用（自动生成）
[SerializeField, HideInInspector, Resolve(nameof(core))] GameObject coreGO;
[SerializeField, HideInInspector, Resolve(nameof(handler))] GameObject handlerGO;
[SerializeField, HideInInspector, BindUdonSharpEvent(nameof(_OnMatchingCoresChanged))] 
ActiveRegionManager activeRegionManager;

```

**解析**：这个组合实际上是 **5 个特性**的协同工作：

1. `[LocalizedLabel]` — 本地化显示名称
2. `[Resolve(...)]` — 解析链式引用 `handler.core`
3. `[HideInInspectorIfResolvable = true]` — 解析成功后隐藏
4. `[Locatable]` — 支持自动定位
5. `[BindUdonSharpEvent(...)]` — 自动订阅事件

### 简化组合：仅自动定位

```csharp
[Locatable] public Core core;

```

### 扩展组合：带事件和配置

```csharp
[SerializeField, LocalizedLabel]
[Locatable, BindUdonSharpEvent(
    nameof(OnVideoPlay),
    nameof(OnVideoPause),
    nameof(OnVideoEnd)
)]
public Core core;

public void OnVideoPlay() { /* 处理播放 */ }
public void OnVideoPause() { /* 处理暂停 */ }
public void OnVideoEnd() { /* 处理结束 */ }

```

---

## 5.2 避坑指南

### 坑1：Udon 不支持反射

**问题**：`BindUdonSharpEvent` 依赖反射，但 Udon VM 不支持。

**解决**：特性仅在编辑器编译时生效，运行时事件通过 UdonSharp 生成的代码处理。

### 坑2：特性顺序敏感

**问题**：`[Resolve]` 必须在 `[HideInInspector]` 之后才能正确隐藏。

**正确**：

```csharp
[SerializeField, HideInInspector, Resolve(nameof(core))] GameObject coreGO;

```

### 坑3：编译器符号不一致

**问题**：`COMPILER_UDONSHARP` 与 `UDONSHARP` 可能不同。

**VVMW 用法**：

```csharp
#if COMPILER_UDONSHARP
    public
#else
    internal
#endif

```

### 坑4：编辑器代码无法访问 Udon 字段

**问题**：编辑器程序集看不到 Udon 字段。

**解决**：使用双模式声明，或通过 `UdonSharpEditorUtility` 访问。

### 坑5：Locatable 预制体路径错误

**问题**：`InstaniatePrefabPath` 路径不正确导致实例化失败。

**检查**：确保路径与 VPM 包中的实际路径匹配。

---

## 5.3 最简实现模板

### 模板1：声明式 UdonSharpBehaviour

```csharp
using UnityEngine;
using UdonSharp;
using VRC.SDKBase;
using JLChnToZ.VRC.Foundation;

public class MyComponent : UdonSharpBehaviour {
    // 声明：需要自动定位的 Core
    [SerializeField, LocalizedLabel(Key = "MyProject.Core")]
    [Locatable, BindUdonSharpEvent(
        nameof(OnVideoPlay),
        nameof(OnVideoPause)
    )]
    public Core core;
    
    // 事件处理方法（声明即可，编辑器自动订阅）
    public void OnVideoPlay() {
        Debug.Log("Video playing");
    }
    
    public void OnVideoPause() {
        Debug.Log("Video paused");
    }
}

```

### 模板2：属性化同步字段

```csharp
using UnityEngine;
using VRC.SDKBase;
using UdonSharp;
using JLChnToZ.VRC.Foundation;

[UdonBehaviourSyncMode(BehaviourSyncMode.Manual)]
public class MySyncComponent : UdonSharpBehaviour {
    // 同步字段 + 回调
    [UdonSynced, FieldChangeCallback(nameof(MyValue))]
    private int myValue = 0;
    
    public int MyValue {
        get => myValue;
        set {
            if (myValue == value) return;
            myValue = value;
            OnValueChanged();
            if (Networking.IsOwner(gameObject))
                RequestSerialization();
        }
    }
    
    void OnValueChanged() {
        Debug.Log($"Value changed to: {myValue}");
    }
}

```

### 模板3：Unity 事件绑定

```csharp
using UnityEngine;
using UnityEngine.UI;
using UdonSharp;
using JLChnToZ.VRC.Foundation;

[RequireComponent(typeof(Button))]
[BindEvent(typeof(Button), nameof(Button.onClick), nameof(_OnClick))]
public class MyButtonHandler : UdonSharpBehaviour {
    public void _OnClick() {
        Debug.Log("Button clicked!");
    }
}

```

### 模板4：编辑器工具类

```csharp
#if UNITY_EDITOR
using UnityEditor;

[InitializeOnLoadMethod]
static class MyEditorInit {
    static MyEditorInit() {
        // 编辑器启动时执行
        Debug.Log("Editor initialized");
    }
}
#endif

```

### 模板5：场景预处理器

```csharp
#if UNITY_EDITOR
using UnityEngine;
using UnityEngine.SceneManagement;
using JLChnToZ.VRC.Foundation;

public class MyPreprocessor : IPreprocessor {
    public int Priority => 50; // 执行优先级，越小越早

    public void OnPreprocess(Scene scene) {
        foreach (var component in scene.IterateAllComponents<MyComponent>()) {
            // 预处理逻辑
            component.Initialize();
        }
    }
}
#endif

```

---

## 5.4 特性速查表

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           特性速查表                                     │
├──────────────────┬──────────────────────────────────────────────────────┤
│ 特性              │ 用法                                                │
├──────────────────┼──────────────────────────────────────────────────────┤
│ LocalizedLabel   │ [LocalizedLabel] / [LocalizedLabel(Key = "...")]      │
│ LocalizedEnum    │ [LocalizedEnum]                                      │
│ Locatable        │ [Locatable] / [Locatable(InstaniatePrefabPath=...)]  │
│ Resolve          │ [Resolve(".")] / [Resolve(nameof(field))]            │
│ BindUdonSharpEvent│ [BindUdonSharpEvent(nameof(Method1), nameof(Method2))]│
│ BindEvent        │ [BindEvent(typeof(Button), nameof(Button.onClick), │
│                  │   nameof(OnClick))]                                 │
│ FieldChangeCallback│ [FieldChangeCallback(nameof(Property))]            │
│ SingletonCoreControl│ [SingletonCoreControl]                            │
│ HideInInspector  │ [HideInInspector]                                     │
│ HelpURL          │ [HelpURL("https://...")]                             │
│ DefaultExecutionOrder│ [DefaultExecutionOrder(0)]                       │
├──────────────────┼──────────────────────────────────────────────────────┤
│ 编辑器特性        │ 用法                                                │
├──────────────────┼──────────────────────────────────────────────────────┤
│ InitializeOnLoadMethod│ [InitializeOnLoadMethod]                        │
│ DidReloadScripts │ [DidReloadScripts]                                   │
│ CustomEditor     │ [CustomEditor(typeof(Component))]                   │
│ IPreprocessor    │ 实现接口 + 注册                                       │
└──────────────────┴──────────────────────────────────────────────────────┘

```

---

# 附录：VVMW 特性系统全景图

```
┌─────────────────────────────────────────────────────────────────────────┐
│                     VRC.Foundation 特性系统                              │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  命名空间                                                               │
│  ├── JLChnToZ.VRC.Foundation          → 特性定义 + 核心功能              │
│  ├── JLChnToZ.VRC.Foundation.I18N      → 本地化功能                     │
│  └── JLChnToZ.VRC.Foundation.Editors   → 编辑器工具                     │
│                                                                          │
│  特性分类                                                               │
│  ├── 引用绑定                                                           │
│  │   ├── [Locatable]           自动定位与实例化                          │
│  │   ├── [Resolve]             引用解析                                 │
│  │   └── [BindUdonSharpEvent]  UdonSharp 事件绑定                        │
│  ├── 字段回调                                                           │
│  │   └── [FieldChangeCallback] 字段变化回调                             │
│  ├── 本地化                                                             │
│  │   ├── [LocalizedLabel]      本地化标签                               │
│  │   └── [LocalizedEnum]       本地化枚举                               │
│  ├── 组件标记                                                           │
│  │   └── [SingletonCoreControl] 单例核心控制                           │
│  └── 编辑器工具                                                         │
│      ├── IPreprocessor           场景预处理器                           │
│      ├── InitializeOnLoadMethod  编辑器启动钩子                         │
│      └── DidReloadScripts        脚本重载钩子                           │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘

```

---

# 附录：源码索引

本文档引用的 VVMW 源码文件：

<div class="table-wrapper" id="bkmrk-%E6%96%87%E4%BB%B6-%E8%A1%8C%E5%8F%B7-%E5%BC%95%E7%94%A8%E5%86%85%E5%AE%B9-autoplayo"><table><thead><tr><th>文件</th><th>行号</th><th>引用内容</th></tr></thead><tbody><tr><td>`AutoPlayOnNearV2.cs`</td><td>16-22</td><td>Locatable + Resolve + BindUdonSharpEvent 组合</td></tr><tr><td>`VideoPlayerHandler.cs`</td><td>34, 40-42</td><td>Resolve 解析自身组件</td></tr><tr><td>`FrontendHandler.cs`</td><td>20-34</td><td>BindUdonSharpEvent 13 事件绑定</td></tr><tr><td>`Core.cs`</td><td>49-50, 116-128</td><td>FieldChangeCallback 属性化同步</td></tr><tr><td>`ButtonEntry.cs`</td><td>1-109</td><td>BindEvent Unity 事件绑定完整示例</td></tr><tr><td>`AbstractMediaPlayerHandler.cs`</td><td>29-34</td><td>LocalizedLabel 编译期条件</td></tr><tr><td>`EditorBase.cs`</td><td>14-224</td><td>VVMWEditorBase 编辑器基类</td></tr><tr><td>`CoreEditor.cs`</td><td>1-983</td><td>Core 自定义编辑器完整实现</td></tr><tr><td>`CoreConfigPreprocessor.cs`</td><td>1-41</td><td>IPreprocessor 场景预处理</td></tr></tbody></table>

</div>---

*参考来源：VVMW 源码分析（Core.cs、FrontendHandler.cs、VideoPlayerHandler.cs、AbstractMediaPlayerHandler.cs、VizVidBehaviour.cs、ButtonEntry.cs、EditorBase.cs、CoreEditor.cs、CoreConfigPreprocessor.cs、AutoPlayOnNearV2.cs 等）*