# ShadowsocksR C# 版本3.7.4.1 #

最新版下载链接：[https://github.com/breakwa11/shadowsocks-csharp/releases](https://github.com/breakwa11/shadowsocks-csharp/releases)  
BitTorrent Sync：BHS55LP54SO7A434QBB5Z2O6B7A45B2BX  
发布链接：[https://github.com/breakwa11/shadowsocks-rss](https://github.com/breakwa11/shadowsocks-rss)  
服务端配置教程：[Wiki](https://github.com/breakwa11/shadowsocks-rss/wiki/Server-Setup) （含单用户和多用户）  
ShadowsocksR其它版本：[SSR python manyuser](https://github.com/breakwa11/shadowsocks/tree/manyuser), [SSR Android](https://github.com/KagayamaKaede/ShadowsocksRDroid/releases), [SSR-libev](https://github.com/breakwa11/shadowsocks-libev)

推荐使用BitTorrent Sync免翻自动更新，最及时自动获取最新版本

### 版本特点 ###
1. 全能代理，同一端口支持socks4/socks4a/socks5/http
2. 节点统计，包括延迟、连接数、当前下载速度、最高速度、出错率等等
3. 连接管理，随时断开指定节点的连接，或修改节点后自动断开
4. 协议转换，把UDP包封装于TCP里发送，或把TCP包封装于UDP里发送
5. 多重代理，通过设置前置socks5/http代理，可达到任意重代理
6. 协议插件，支持自定义协议和协议混淆，详见[ShadowsocksR插件文档](https://github.com/instmy/shadowsocks-rss/blob/master/ssr.md)

你要是有兴趣和我联系的话，特别是编程技术上的支持，那就到  
Twitter: [@breakwa11](https://twitter.com/breakwa11)  
聊天室: [https://gitter.im/breakwa11/shadowsocksr](https://gitter.im/breakwa11/shadowsocksr)  
社区: [ShadowsocksR](https://plus.google.com/communities/117390969460066916686)  
Blogger: [https://breakwa11.blogspot.com](https://breakwa11.blogspot.com)
Google Group: [ShadowsocksR](https://groups.google.com/forum/#!forum/shadowsocksr)  

### 配置术语说明： ###
1. TCP over UDP  
打钩则把TCP包以UDP隧道发送，协议还在完善中，需要和相应的服务端配合使用。目前协议的设计是优化下载和看视频（打开网页不见得比原来的TCP好），但如果在封锁UDP严重的地区，效果会比直接使用TCP更差。建议不要过度使用以免IP被盯上。如果服务器不支持，打钩此选项会导致网页无法打开。
2. UDP over TCP   
不打钩即以UDP方式发送UDP包，打钩则把UDP包在TCP里发送。以TCP方式发送UDP包在封锁UDP的网络环境下特别有用，如无此需求就不必打钩。  
如果在发送UDP数据失败，可能是服务器不支持。此选项不会影响浏览网页（TCP）
3. 重连次数  
目标服务器连接失败时选择其它服务器再次尝试连接，仅当没有发送接收任何有效数据时进行（即出现密码错误或加密方式错误时可能不能重连）
4. 超时秒数（TTL）  
TTL为Time to Live的缩写，表示连接超过多久没有再发送或接收数据时断开（服务端默认值为300），时间单位是秒，较小的值可减少浏览器出现卡顿时的时间，但同时在网络连接不佳时可能会导致部分页面元素或整个页面随机性下载失败。一般网页浏览建议设置为30~120，下载或看视频时建议直接设置为0。设置为0表示不使用TTL设置（实际TTL值由服务端及客户端较小的一方控制）。

### 快捷操作提示： ###
1. 单击任务栏图标弹出服务器配置窗口，Shift+单击弹出选项窗口，而右键图标弹出菜单
2. 中键点击任务栏图标弹出服务器连接统计窗口
3. 连接统计窗口点击服务器会切换当前服务器
4. 连接统计窗口双击服务器会打开服务器配置窗口
5. 点击连接统计窗口列标题可排序（部分列不允许排序）
6. 连接统计窗口，在连接状态的错误记录列，双击则重置错误数，双击百分比列则重置本服务器的所有信息
7. 连接统计窗口，在连接状态的“开”一列，鼠标点击切换开关状态（随机时有效），红色为关闭
8. 连接统计窗口，右键弹出清空所有的菜单
9. 连接统计窗口，双击连接数，断开该节点现有的连接（不一定立即清零，双击过就行了）

### 其它提示： ###
- 负载均衡功能，适用于网页浏览，不适用于看视频或下载等需要大流量的环境。如需下载请在连接统计窗口通过下载测速测试速度最快的服务器然后单独连接之。  

### 更新记录： ###
版本3.7.4.1 *2016-02-01*  
1.修正超时统计  
2.优化统计流程  
3.发现协议非标准不断开连接  

版本3.7.4 *2016-01-22*  
1.增加插件auth\_sha1\_v2  
2.服务器统计增加“实际下载”列  
3.log文件按月份分离保存

版本3.7.3.1 *2016-01-11*  
1.修正部分http(s)连接错误  
---- (3.7.2 beta *2016-01-09*)  
2.重写http代理实现  
3.支持自定义abp.txt以替代gfwlist  
---- (3.7.1 alpha *2016-01-05*)  
4.支持设置验证密码，设置验证密码后允许任何IP连接，带验证时不支持socks4  
5.本地代理免验证  
6.空连统计清零规则调整

版本3.7.0 *2015-12-29*  
1.支持chacha20-ietf加密  
2.兼容FIPS设置  
3.支持UDP协议定制  
4.完整支持verify\_sha1

版本3.6.9 *2015-12-21*  
1.http\_simple插件支持自定义完整header  
2.错误统计方式调整，只记录最近100次连接的情况  
3.常见协议的正确性测试，可检测出密码错误或协议错误等  
4.仅当解密错误或协议错误或网络错误时，才自动禁用节点  
5.tls1.0插件协议修正（需要同时更新服务端）

版本3.6.8 *2015-12-18*  
1.连接超时处理优化，避免长时间连接等待（重连次数大于0时启用，建议设置5）  
2.重连机制增强，客户端未发送有效数据前均能重连  
3.无数据超时仅断开远端，满足上一条的情况下能重连  
4.更新PAC时防止缓冲  
5.tls1.0插件协议修正（需要同时更新服务端）

版本3.6.7.1 *2015-12-15*  
1.修正随机数发生方式  
2.配置界面次序调整  

版本3.6.7 *2015-12-14*  
1.调整流量计算方式，以客户端到ss服务端的TCP/UDP数据包大小为准（py服务端也已调整）  
2.verify\_deflate的BUG修正  
3.增加混淆插件tls1.0\_session\_auth  
4.负载均衡的“选中优先”bug修正  
5.服务端`auth_simple`和`auth_sha1`支持配置客户端上限

版本3.6.6 *2015-12-03*  
1.增加插件auth\_sha1  
2.过滤非局域网连接请求

版本3.6.5 *2015-11-24*  
1.移除内置http代理功能  
2.支持切换前置代理类型(Socks5/http tunnel)  
其它说明：http tunnel是指支持代理https连接的http代理，可以通过UDP over TCP方式使用UDP，不能使用原生UDP。特别注意某些代理软件某些功能上有BUG，会导致代理失败（如CCProxy开启二级代理时，不开启二级代理是正常的）  
前置代理类型中的“TCP端口转发”的混淆插件还没写

版本3.6.4.1 *2015-11-19*  
1.关闭内置http代理时privoxy的转发BUG修正  
2.不重复保存配置

版本3.6.4 *2015-11-18*  
1.支持verify\_sha1（即libev的OTA）  
2.UDP over TCP 实现调整，避免与verify\_sha1冲突  
3.UDP over TCP 细节bug修正  
4.显示超时次数列  
5.内置http代理部分bug修正  
6.特殊情况下缓冲区溢出修正

版本3.6.3.4 *2015-11-16*  
1.移除部分影响速度的调试代码  
2.修正扫码时混淆参数未解码  
3.增加插件字段

版本3.6.3 *2015-11-10*  
1.实现CONNECT方法的http代理，减少访问https站的连接数  
2.实现基本的http代理支持，更少资源占用（但存在BUG，试用阶段）  
3.内置http代理启用开关（开启后privoxy不会运行）  
4.修改生成链接格式，与SSR android兼容  
5.在服务器配置的“备注”前打钩则把备注加在链接（二维码）内  
6.支持前置socks5代理地址填写域名  
7.增加一个常见域名列表（即白名单），foxyproxy可使用此列表替代，  
&nbsp;&nbsp;&nbsp;&nbsp;因foxyproxy使用绕过大陆IP列表访问facebook等站会很卡  
其它说明：对于https站点，不管启不启用内置http代理，均无影响，  
启用内置http代理会影响http站点，目前发现的问题是页面元素较多时有部分请求不正常，用chrome打开时有一定概率变成空白页。

版本3.6.2 *2015-11-02*  
1.插件分类，独立出TCP协议字段（旧配置部分节点需要修改配置）  
2.稳定性增强  
3.更详细的log输出

版本3.6.1 *2015-10-27*  
1.多开时允许分别开机自动启动（更换前要把以前的自启动去掉）  
2.检测协议错误  
3.调整自动禁用节点的计算方式  
4.修正自动重连时没有更换混淆插件  
5.编辑节点后保持当前选择的节点  
6.添加auth\_simple混淆插件  
7.DNS缓存修正  
8.FIPS设置检查  
**附python部分**：  
版本2.6.12  
1.稳定性修正（目前最为稳定版本）  
2.添加auth\_simple混淆插件  
3.TCP分包处理修正  
4.IPv6优先  
5.混淆插件客户端部分完成支持

版本3.5.9 *2015-10-16*  
1.增强XP下的兼容性  
2.gfwlist模板不本地保存，精简exe大小  
3.gfwlist以及默认pac跳过局域网地址

版本3.5.8 *2015-10-15*  
1.增加插件参数字段  
2.PAC兼容性调整  
3.privoxy服务运行前先停止  
4.缓冲区优化

版本3.5.7 *2015-10-12*  
1.修正退出时有一定机率没退出完全的问题  
2.解决两个verify插件在部分情况下会越界错误  
3.添加绕过局域网的PAC，以替代全局模式  
4.修正系统代理在部分环境下手动取消失败的问题  
5.保证在使用PAC模式下即使强行结束客户端IE也能正常使用

版本3.5.6 *2015-10-09*  
1.增加混淆协议插件`verify_deflate`  
2.修正`tls_simple`, `random_head`的BUG

版本3.5.5 *2015-10-08*  
1.配置中remarks字段改回原文

版本3.5.4 *2015-10-08*  
1.调整插件接口，减少计算量和Bug修正  
2.网络缓冲的bug修正  
3.备注采用urlsafe-base64编码  
4.链接字段增加obfs-plugin  
5.其它编码错误修正

版本3.5.3 *2015-10-07*  
1.简化操作，**单击** 或 **shift+单击** 任务栏图标弹出配置窗口  
2.增加插件接口  
3.增加混淆协议插件`verify_simple`  
4.调整“连错”归零逻辑，避免插件的混淆返回干扰  
5.细调负载均衡，避免某个节点同时连接数大多  
6.修正端口重新监听时的异常

版本3.5.2 *2015-09-30*  
1.软件更新提示方式调整，点击气泡不弹窗  
2.注册表键值更名，避免与原版冲突  
3.Privoxy监听端口随机，不再默认为8123，避免与原版冲突  
4.移除“空连”错误统计  
5.修改图标  
6.移除table和rc4加密  
7.整理和简化右键菜单，配置移入选项设置

版本3.5.1 *2015-09-27*  
1.增加混淆协议插件`tls_simple`, `random_head`  
2.`http_simple`混淆增加随机路径和随机useragent  
3.更新配置不断开现有连接  
4.不开启系统代理时，系统代理菜单不置灰（PAC有效）

版本3.5.0 *2015-09-22*  
1.混淆协议插件`http_simple`  

版本3.4.4 *2015-09-19*  
1.设置界面拆分，区分编辑服务器和全局设置  
2.TCP连接协议改用下拉列表选择，字段名调整，相应节点要重新配置  
3.切换节点开关状态后立即保存设置  
4.调整随机数发生器，避免产生相同IV

版本3.4.3 *2015-09-16*  
1.服务器统计列的下载或上传刷新状态修正  
2.保存节点禁用状态  
3.增加按时间段切换服务器规则（每10分钟切换，或遇到连续出错时切换）

版本3.4.2 *2015-09-09*  
1.节点修改或删除后无引用的连接自动断开  
2.隐藏混淆UDP协议选项  
3.更新记录加入具体日期  
4.首包发送方式调整

版本3.4.1 *2015-08-28*  
1.增加新TCP连接协议

版本3.4.0 *2015-08-24*  
1.使用新的GFWList地址  
2.增加一个实验性功能TCP over UDP（目前有BUG，轻度使用还可，需要使用相应的服务端）

版本3.3.6 *2015-08-19*  
1.修正“自动禁用出错服务器”选项保存的问题  
2.所有临时文件全部放到./temp/下  
3.连接方式不同也视为不同服务器分别统计  
4.删除升级提示检查  
5.删除实验性功能代码并开源https://github.com/breakwa11/shadowsocks-csharp

版本3.3.5 *2015-08-17*  
1.合并部分主干代码，改用privoxy替代polipo（主程序增大140Kb）  
2.增加“低错误优先”的随机方式  
3.细调“低延迟优先”的算法  
4.增加“自动禁用出错服务器”选项  
5.连接超过超时秒数后断开算作“超时”

版本3.3.4 *2015-08-13*  
1.修正打开统计列表后，程序退出不正常的错误  
2.修正UDP over UDP的连接错误  
3.修正二重代理时UDP连接错误

版本3.3.2 *2015-08-12*  
1.合并部分主干代码  
2.断开当前所有连接功能  
3.优化统计窗口资源占用

版本3.3.1 *2015-08-06*  
1.修正连接数统计和管理错误  
2.修正多重代理时UDP代理错误  
3.重新实现UDP over TCP（与之前版本不兼容，以前的实现不正确，服务端需更新）

版本3.2.2 *2015-07-31*  
1.修正UDP下chacha20加密错误以及内存泄露  
2.提升UDP下加解密速度  
3.优化统计计算速度  
4.随机选择服务器改名负载均衡  
5.统计列表简化显示效果，减少卡顿  
6.自动更新方式调整

版本3.2.0 *2015-07-26*  
1.修正部分TCP连接失败的问题  
2.增加Socks5代理设置

版本3.1.4 *2015-07-23*  
1.调整TCP发包方式，把首协议包与第一个数据包连接发送  
2.移除混淆TCP选项  
3.服务端统计三列错误统计列改用“连错”列代替  
4.不要把版本号看成圆周率（明明差一个小数点）

版本3.1.3 *2015-07-16*  
1.本地连接的TTL支持，默认TTL=0（0表示不启用）  
2.重连次数的保存修正

版本3.1.2 *2015-07-14*  
1.连接统计窗口排序修正  
2.重连次数配置（默认值3）  
3.UDP over UDP选项更换为UDP over TCP，即高级选项全不打钩为通用设置，通用于所有服务端  
4.log文件和polipo放于temp子目录  
5.统计窗口数值格式化调整  
6.解决config问题，移除config文件  
7.允许密码显示

版本3.1.1 *2015-07-13*  
1.合并主干部分代码  
2.增加混淆UDP选项  
3.支持接收混淆UDP包  
4.连接数统计修正  
5.鼠标中键点击任务栏图标弹出服务器连接统计窗口  
6.修正配置界面部分环境下按钮不显示  
7.附加config文件避免部分系统运行异常  
8.改用7zip压缩，减少一半体积

版本3.0.1 *2015-07-09*  
1.配置保存bug修正  
2.右键菜单整理  
3.配置界面调整  
4.修正UDP握手协议实现与ProxyCap兼容  
5.加入源md格式说明文档

版本3.0 *2015-07-05*  
1.软件更名  
2.修正 UDP over UDP 的加解密错误

版本2.3.2 *2015-06-29*  
1.修正ipv4连接的问题  
其它：目前已知使用pc版微信发图发文件会导致polipo崩溃，建议改用Provixy，具体使用方法可以参考网上教程

版本2.3.1.9 *2015-06-28*  
1.修正http连接会出现连接失败的问题（同时修正了自动更新和pac更新）  
2.增加连接管理，双击连接数即可断开该节点现有连接  
3.增强UDP连接兼容性  
4.更新地址可以配置窗口点击

版本2.3.1.8 *2015-06-26*  
1.支持TCP协议头混淆（需服务端更新支持）  
2.UDP包混淆（需服务端更新支持）  
3.支持UDP包通过 UDP Relay 转发（需服务端更新支持）  
4.支持Socks4/Socks4a协议  
5.局域网共享时支持同时监听IPv4/IPv6  
6.配置添加UDP包通过方式  
7.配置添加TCP协议头混淆

版本2.3.1.7 *2015-06-19*  
1.IPv6连接修正  
2.随机时“按次序”方式的bug修正  
3.IPv6地址显示方式调整  
4.空连统计包含连接重置等错误  
5.服务器连接统计列排序功能  
6.取消自动关闭无效服务器，防误判  
7.手工检查更新

版本2.3.1.6 *2015-06-11*  
1.增加随机选择服务器的方式  
2.连接数统计修正  
3.支持代理UDP（以TCP转发），需要服务端更新

版本2.3.1.5 *2015-06-04*  
1.调整配置窗口的设置体验  
2.软件自动更新支持

版本2.3.1.4R2 *2015-06-03*  
1.修正polipo某些情况下运行异常  
2.修正连接统计窗口一些错误  
3.修正部分dns错误未处理的问题  
4.三次错误后自动关闭无效服务器  
5.增加ChnIPList的pac，国内不代理，国外全走代理  
6.调整连接统计窗口列顺序  
7.节点自动重连尝试（开启随机选择服务器时，第一次重连相同节点，第二次、第三次选择其它节点）  
8.从剪贴板复制多行文本地址功能修正  
9.服务器配置显示二维码，增加列表高度

版本2.3.1.4 *2015-06-01*  
1.polipo配置修正  
2.使用当前目录运行polipo，完美支持多开（端口同时支持socks5和http协议）  
3.增加当前下载速度统计，以及最大下载速度记录  
4.连接统计窗口优化显示效率  
5.服务器配置显示ss链接，以便快速复制  
6.连接统计窗口支持手工调整列宽

版本2.3.1.3 *2015-05-18*  
1.服务器的DNS缓冲（TTL=600），让直接填写域名与写IP一样稳定  
2.服务器连接状态记录，含上传下载流量，连接延迟统计，连接错误统计  
3.服务器独立开关，在连接状态的“开”一列，鼠标点击之，红色为关闭  
4.连接记录清除，在连接状态的错误记录三列，双击则重置错误数，双击百分比列则重置本服务器的所有信息  
5.统计列表标题显示监听方式及端口（方便多开的时候对应上）  
6.从剪贴板复制地址，方便从ss://xxxx这种格式快速输入

版本2.3.1.2 *2015-05-11*  
1.允许程序多开  
2.延迟统计，自动在低延迟服务器之中选择（高延迟的也会有低概率选取到）  
3.二维码菜单位置变动  
4.偶然会程序崩溃的问题不确定解决了没有，如还有发生请联系作者

版本2.3.1.1 *2015-05-03*  
1.随机选择服务器

### License ###
GPLv3

### Contact ###
[GPG pubkey](https://github.com/breakwa11/pubkey)
