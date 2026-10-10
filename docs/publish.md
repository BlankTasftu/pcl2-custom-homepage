# 联网更新与发布

这份文档讲清楚「为什么改了文件玩家还是看到旧版」以及怎么正确发一次更新。

## 它是怎么工作的

PCL 的联网主页**不是每次刷新都重新下载**，而是先比版本号，能省流量也省时间。实际流程是：

1. 你在 `设置 → 个性化 → 主页` 里选 `联网更新`，填一个地址，例如 `https://example.com/Custom.xaml`
2. PCL 把地址里的 `.xaml` 换成 `.xaml.ini`，去取这份「版本标记」——也就是本仓库的 `Custom.xaml.ini`，内容只有一行 `V 0.0.4`
3. 取到的版本号和上次缓存的一致 → 直接用缓存；不一致 → 重新下载整份 `Custom.xaml`
4. 下载的文件缓存在 `%TEMP%\PCL\Cache\Custom.xaml`，所以第二次打开启动器是不联网的
5. 如果 `.xaml.ini` 取不到（404、地址规则不对等），PCL 会记一条警告然后**直接下载**：功能还能用，但每次刷新都要重新下一遍

由此得到两条硬性要求：

- **两个文件必须成对存在**：`Custom.xaml` 和 `Custom.xaml.ini`，且后者能通过「把前者地址里的 .xaml 换成 .xaml.ini」取到
- **下载地址必须以 `.xaml` 结尾**，否则第 2 步的替换不会发生，版本比对就失效了

> 版本标记别写太长、别写多行。PCL 对超长内容会直接报错，保持 `V 0.0.4` 这种短字符串就行。

## 发一次更新的完整流程

1. 改 `Custom.xaml`，本地用 `读取本地文件` 模式先看效果
2. **把 `Custom.xaml.ini` 的版本号加一**（例如 `V 0.0.4` → `V 0.0.5`）
   *这一步最容易忘。版本号不变，PCL 就认为「已是最新」，玩家的主页永远不会变。*
3. 把两个文件一起提交/上传，确认都能用直链访问到
4. 自己验证一遍：PCL 里选 `联网更新`、填地址、点刷新；想测「更新」这个动作，就先删掉 `%TEMP%\PCL\Cache\Custom.xaml` 再刷新
5. 玩家端不需要做任何操作，下次刷新主页时自动拿到新版

## 直链怎么选

地址必须是**直接返回文件内容**的链接，不能是 GitHub/Gitee 的网页地址（那种返回的是 HTML）。

| 方式 | 地址长这样 | 优点 | 注意 |
| --- | --- | --- | --- |
| GitHub raw | `https://raw.githubusercontent.com/用户/仓库/main/Custom.xaml` | 最省事，提交即生效 | 国内网络经常连不上 |
| jsDelivr CDN | `https://cdn.jsdelivr.net/gh/用户/仓库@main/Custom.xaml` | 国内可访问性好 | 有 CDN 缓存，更新可能延迟几小时 |
| GitCode raw | `https://raw.gitcode.com/用户/仓库/raw/main/Custom.xaml` | 国内速度好 | 需要 GitCode 账号 |
| gh-proxy 等加速 | `https://gh-proxy.com/https://github.com/用户/仓库/raw/main/Custom.xaml` | 应急可用 | 第三方服务，稳定性不由你控制 |
| 自建服务器 / 对象存储 | `https://你的域名/Custom.xaml` | 完全可控 | 要自己维护 |

选择建议：**服主自用优先考虑国内可访问性**，jsDelivr 或 GitCode raw 比较稳；只在自己电脑上调试用 GitHub raw 就够了。

## 图片怎么托管

主页里的两张图（LOGO、表情包）现在是 GitCode release 的直链，本仓库 `assets/` 里有备份。想换成自己的：

1. 把图片放进仓库（例如 `assets/logo.png`）并提交
2. 用它的 raw 地址填进 `Image` 的 `Source`，例如
   `https://cdn.jsdelivr.net/gh/用户/仓库@main/assets/logo.png`
3. 注意分辨率和体积：主页图片高度写的是 `Height="150"`，宽度会按比例缩放，图太大只会拖慢加载

`Image` 只认图片直链，指向网页是不显示的。

## 出问题了怎么查

- **改了内容但没生效**：九成是版本号没改。先看 `Custom.xaml.ini`
- **清缓存**：删掉 `%TEMP%\PCL\Cache\Custom.xaml`，下次刷新会重新下载
- **看日志**：PCL 的日志里搜「主页」，能看到「主页自定义数据来源：联网缓存文件 / 联网全新下载」「当前缓存的主页已为最新」这类记录，一眼就能看出走的是缓存还是新下载
- **地址检查三连**：是不是以 `.xaml` 结尾？`.xaml.ini` 能不能取到？返回的是文件内容还是网页？
- **主页整个打不开**：多半是 XAML 语法错了（属性名拼错、标签没闭合、注释里出现连续两个短横线）。删掉 `Custom.xaml` 就能恢复默认主页，然后对照 `Custom.xaml.bak` 找回改错的地方

## 顺便同步一份到 Gitee

原仓库还在 Gitee（BlankTasftu/PCL2），想两边都更新，可以加一个远程：

```
git remote add gitee https://gitee.com/BlankTasftu/PCL2.git
git push gitee main:master
```

之后每次 `git push` 推 GitHub、`git push gitee` 推 Gitee 即可。

## 投稿到 PCL 官方主页预设

PCL 的 `设置 → 个性化 → 主页` 里有一条提示，写明「如果你制作了稳定运行的联网主页，可以点击这条提示投稿，若合格即可加入预设」。投稿通过后，主页会出现在 PCL 预设下拉框里，玩家一键就能选用，不用手填地址。

投稿前建议先做到：直链长期稳定、图片不依赖临时地址、内容里没有会失效的私人链接。
