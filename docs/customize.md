# 自定义指南

面向想把这份主页改成自己服务器样式的人。全文的写法都对着 PCL 内置帮助和 [PCL Wiki](https://github.com/Meloong-Git/PCL/wiki) 核对过。

## 目录

- [先记住三件事](#先记住三件事)
- [12 处可改的地方](#12-处可改的地方)
- [懒人面板的路径怎么写](#懒人面板的路径怎么写)
- [常见改法示例](#常见改法示例)
- [自定义事件速查](#自定义事件速查)
- [替换标记速查](#替换标记速查)
- [控件速查](#控件速查)
- [外观、主题色与颜色](#外观主题色与颜色)
- [可选优化（还没做）](#可选优化还没做)

## 先记住三件事

1. **文件放哪**：`<PCL 所在文件夹>\PCL\Custom.xaml`。不是 `.minecraft` 文件夹。找不到就在 PCL 里点 `设置 → 个性化 → 主页 → 打开主页文件夹`。
2. **怎么生效**：改完保存，点 `刷新主页` 或按 F5。想省事也可以把 `Custom.xaml` 直接拖进 PCL 窗口。
3. **改坏了怎么办**：删掉 `Custom.xaml`，PCL 立刻恢复默认主页。它不会影响 PCL 本体和游戏文件。

> 备份习惯：改之前先把文件复制一份叫 `Custom.xaml.bak`，出问题直接换回来。

另外两个容易踩的点：

- 文件保存为 **UTF-8** 编码（不要存成 GBK，中文会变乱码）
- 语法是 WPF 的 XAML，**属性名区分大小写**，写错属性名这一页可能直接报错打不开

## 12 处可改的地方

打开 `Custom.xaml`，里面用 `【改我 1】` 到 `【改我 12】` 标注了位置，对应关系如下。

| 编号 | 位置 | 具体改什么 |
| --- | --- | --- |
| 1 | 区块 1 顶部提示条 | `📰欢迎游玩XXX` 里的 `XXX`。后面的 `{pcl_version}` 是 PCL 版本号，别删，删了就不显示版本了 |
| 2 | 区块 2 `MyCard` 的 `Title` | 卡片大标题，现在是「欢迎使用Blank制作的自定义主页」 |
| 3 | 区块 2 抬头文案 | 现在是「服务器模组整合包，丢掉大脑，拯救狗服主，你值得拥有」 |
| 4 | 区块 2 声明 | `此版本专供XXX服务器使用` 里的 `XXX` |
| 5 | 区块 2 第 1 个 `MyListItem` | Bilibili 链接。账号还没注册时用的是 `弹出窗口` 占位；注册好以后把 `EventType` 改成 `打开网页`、`EventData` 填账号主页地址 |
| 6 | 区块 2 第 2 个 `MyListItem` | TS 服务器地址和密码。`EventData="标题\|内容"`，内容里的换行写 `\n`，例如 `EventData="TS3服务器\|地址:ts.example.com\n密码:1234"` |
| 7 | 区块 2 第 3 个 `MyListItem` | 服规文档地址，换 `EventData` 里的网址 |
| 8 | 区块 3 公告 | 整合包版本号（现在是 2.1.0 测试版） |
| 9 | 区块 3 公告 | 更新内容那几行文字，直接改 `Text` |
| 10 | 区块 3 公告 | 署名「——空白ark」 |
| 11 | 区块 4 懒人面板 | 6 个文件夹按钮指向的文件夹。已经改用 `{version_indie}` 替换标记，一般不用改，见下一节 |
| 12 | 区块 2 / 区块 3 的 `Image` | 两张图的 `Source`。可以换成自己的图床直链，也可以换成 `assets/` 里图片的 raw 地址 |

## 懒人面板的路径怎么写

按钮用的是 `执行命令` 事件，格式固定为 **`命令|参数`**。本项目的 6 个按钮统一写成这样：

```
EventType="执行命令" EventData="explorer|&quot;{version_indie}mods&quot;"
```

- 竖线左边是程序：`explorer` 就是 Windows 资源管理器
- 竖线右边是参数：要打开的文件夹路径
- **竖线不能省**。省掉以后 PCL 会把整串当成程序路径去找，点击就会提示执行失败
- **路径两头用 `&quot;` 包起来**：路径里可能有空格（版本文件夹叫 `SCB 1.20.1 Fabric`），加引号能避免打开失败，PCL 官方帮助里也是这么建议的

`{version_indie}` 会替换成「当前选中版本的文件夹」（开启版本隔离时）或「.minecraft 文件夹」（未开启时），末尾自带反斜杠，所以后面直接接文件夹名：

```
{version_indie}mods                → MOD
{version_indie}resourcepacks       → 材质包
{version_indie}shaderpacks         → 光影
{version_indie}replay_recordings   → 回放录像
{version_indie}schematics          → 蓝图
{version_indie}saves               → 存档
```

> 注意：这个标记读的是「PCL 当前选中的版本」。玩家如果同时装了别的整合包，点按钮打开的会是当前选中那个版本的文件夹，这通常正是想要的效果。

## 常见改法示例

### 加一个文本段落

```
<TextBlock Margin="0,0,0,4" HorizontalAlignment="Left" Text="这里写你的文字" />
```

放在某个 `StackPanel` 里面即可。`Margin` 是外边距，`HorizontalAlignment` 控制左中右。

### 加一个打开网页的按钮

```
<local:MyButton Margin="0,0,10,0" Width="140" Height="35" Padding="13,0,13,0" ColorType="Highlight"
       Text="打开服务器官网" EventType="打开网页" EventData="https://example.com" ToolTip="鼠标悬停时显示这句话" />
```

### 加一个列表项（带图标）

```
<local:MyListItem Margin="-5,2,-5,8"
    Logo="https://example.com/favicon.ico" Title="标题" Info="副标题说明"
    EventType="打开网页" EventData="https://example.com" Type="Clickable" ToolTip="提示文字" />
```

### 加一份服规、一个群号

```
<local:MyListItem Margin="-5,2,-5,8" Logo="https://example.com/icon.png"
    Title="玩家群：123456789" Info="点击复制群号"
    EventType="复制文本" EventData="123456789" Type="Clickable" ToolTip="点一下就把群号复制到剪贴板" />
```

### 直接下载一个文件

```
<local:MyButton Text="下载整合包" EventType="下载文件"
       EventData="https://example.com/pack.zip|整合包.zip" ToolTip="不写文件名的话 PCL 会自己从网址里猜" />
```

## 自定义事件速查

写在控件的 `EventType` / `EventData` 两个属性上。表格里的 `|` 表示参数分隔符。

| 事件 | EventData 怎么写 | 说明 |
| --- | --- | --- |
| 打开网页 | `https://...` | 支持 http、https、minecraft、minecraft-preview 协议 |
| 执行命令 / 打开文件 | `程序\|参数` | 参数可省。首次执行会弹安全确认框。路径里有空格时用 `&quot;` 包起来 |
| 启动游戏 | `版本名\|服务器地址` | 版本名写 `\current` 表示当前版本；第二段可省 |
| 复制文本 | `要复制的文字` | 内容里可以有 `\|` |
| 下载文件 | `网址\|文件名\|目标文件夹` | 后两段可省，省了会弹窗让玩家选目录 |
| 弹出窗口 | `标题\|内容\|按钮文字` | 按钮文字可省，默认「确定」 |
| 弹出提示 | `内容\|Blue` | 颜色可选 Blue / Green / Red，默认 Blue |
| 刷新主页 | 留空 | 刷新启动页右侧的主页 |
| 刷新页面 | 留空 | 等于按 F5 |
| 修改设置 / 写入设置 | `设置名\|值` | 改 PCL 的设置项 |
| 修改变量 / 写入变量 | `变量名\|值` | 存进注册表，配合 `{variable:名字}` 读取 |
| 打开帮助 | `个性化/XAML 格式.json` | 打开 PCL 帮助库里的条目 |
| 导入整合包 | 留空 | 等于点「导入整合包」 |
| 切换页面 | `Setup\|SetupUI` | 强行切页面，官方标注「切到部分页面会导致 PCL 出错」，慎用 |
| 今日人品 / 内存优化 / 清理垃圾 | 留空 | 等于百宝箱里的对应按钮 |
| 加入房间 | `邀请码` | 需要 PCL 2.11.1（版本序号 374）或更高 |
| 检查更新 | 留空 | 需要 PCL 2.12.2（版本序号 380）或更高 |

一个控件也可以依次触发多个事件：

```
<local:MyButton Text="改完变量顺手刷新">
    <local:CustomEventService.Events>
        <local:CustomEventCollection>
            <local:CustomEvent Type="修改变量" Data="test|1|-" />
            <local:CustomEvent Type="刷新主页" Data="-" />
        </local:CustomEventCollection>
    </local:CustomEventService.Events>
</local:MyButton>
```

注意这里参数名是 `Type` 和 `Data`，不是 `EventType` / `EventData`。

## 替换标记速查

PCL 会把主页文件里的这些标记替换成实际值，可以写在文字、网址、路径里。

| 标记 | 会被替换成 | 例子 |
| --- | --- | --- |
| `{pcl_version}` | PCL 版本号 | `2.12.6` |
| `{pcl_version_code}` | PCL 版本序号（纯数字） | `380` |
| `{pcl_version_branch}` | 版本类型（中文） | `正式版` |
| `{path}` | PCL 所在文件夹，末尾带反斜杠 | `D:\Minecraft\` |
| `{path_with_name}` | PCL 程序完整路径 | `D:\Minecraft\PCL.exe` |
| `{date}` / `{time}` | 当前日期 / 时间 | `2026/10/11` |
| `{minecraft}` | 当前 .minecraft 文件夹 | `D:\Minecraft\.minecraft\` |
| `{version_path}`（或 `{verpath}`） | 当前版本的文件夹 | `...\versions\SCB 1.20.1 Fabric\` |
| `{version_indie}`（或 `{verindie}`） | 版本隔离文件夹，做文件夹按钮首选 | 见上一节 |
| `{name}` | 版本文件夹名 | `SCB 1.20.1 Fabric` |
| `{version}` | MC 版本 | `1.20.1` |
| `{user}` | 当前玩家名 | `Steve` |
| `{hint}` / `{cave}` | 随机一条「你知道吗」/ 回声洞 | 每次刷新都不一样 |
| `{setup:设置名}` | 读取 PCL 的设置值 | `{setup:UiLauncherTransparent}` |
| `{variable:变量名:默认值}` | 读取自定义变量，没设置过就用默认值 | `{variable:storage:空}` |

> 这些标记是**一次性替换**的：想让它们变成最新值，点「刷新主页」。`{date}`、`{time}` 只在游戏窗口标题里会动态变化。

## 控件速查

PCL 主页里能用的控件（`local:` 开头）：

| 控件 | 用途 |
| --- | --- |
| `MyCard` | 卡片容器，一张卡片一个区块。`Title` 是标题，`CanSwap="True"` 可折叠，`IsSwapped="True"` 默认折叠（**两个 p**） |
| `MyHint` | 提示条。`IsWarn="False"` 变成普通信息条，默认是黄色警告样式 |
| `MyButton` | 按钮。`ColorType` 可选 `Highlight`（主题色）、`Red` 等 |
| `MyTextButton` | 纯文字按钮 |
| `MyIconButton` / `MyIconTextButton` | 图标按钮 / 带图标文字的按钮，图标用 `Logo` 填 SVG Path |
| `MyListItem` | 列表项，`Type="Clickable"` 可点击 |
| `MyCheckBox` / `MyRadioBox` / `MyComboBox` | 勾选框、单选框、下拉框 |
| `MySlider` / `MyTextBox` / `MySearchBox` | 滑块、输入框、搜索框 |

排版用 WPF 原生控件即可：`StackPanel`（堆叠，`Orientation="Horizontal"` 横排）、`Grid`（网格）、`TextBlock`（文字）、`Image`（图片）、`Border`（边框）。

## 外观、主题色与颜色

- 想跟随 PCL 主题（浅色/深色自动切换），用 `{DynamicResource ColorBrush1}` 这类资源，数字越小越靠前景、越大越靠背景
- 写死的颜色（比如 `#8C7721`）不会随主题变化；想让它跟着主题走，就用 `{DynamicResource ColorBrushN}`。数字越小颜色越深（默认浅色主题下），顶部提示条用的就是 `ColorBrush1`
- `Opacity="0.7"` 可以让提示条半透明
- `Margin` 的四个数字顺序是「左,上,右,下」

## 可选优化（还没做）

下面这些是整理时发现、留给你自己决定的地方：

1. **图片可以搬进本仓库**
   现在两张图指向 GitCode release。仓库 `assets/` 里已有备份，想彻底自给自足就把 `Source` 换成自己仓库图片的 raw 地址。
2. **B 站账号还没注册**
   现在是点击弹「还没注册」的占位弹窗。注册好之后按第 5 项改。

> v1.0.4 已经处理掉原来列在这里的两项：顶部提示条改成了主题画刷，6 个文件夹按钮改成了 `{version_indie}` 写法。
