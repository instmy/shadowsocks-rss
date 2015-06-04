# ShadowsocksR 协议插件文档 #
----

## 概要 ##

用于方便地产生各种协议接口。实现为在原来的协议外套一层编码和解码接口，不但可以伪装成其它协议流量，还可以把原协议转换为其它协议进行兼容或完善（但目前接口功能还没有写完，目前还在测试完善中），需要服务端与客户端配置相同的协议插件。插件共分为三类，包括原始协议插件，混淆插件，协议定义插件。

## 现有插件介绍 ##

#### 1.原始协议插件 ####
`origin`或`plain`：表示不混淆，使用原协议

#### 2.混淆插件 ####
`http_simple`：并非完全按照http1.1标准实现，仅仅做了一个头部的GET请求和一个简单的回应，之后依然为原协议流。使用这个混淆后，已在部分地区观察到似乎欺骗了QOS的结果。对于这种混淆，它并非为了减少特征，相反的是提供一种强特征，试图欺骗GFW的协议检测。要注意的是应用范围变大以后因特征明显有可能会被封锁。此插件可以兼容原协议(需要在服务端配置为`http_simple_compatible`)，延迟与原协议几乎无异（在存在QOS的地区甚至可能更快），除了头部数据包外没有冗余数据包，支持自定义参数，参数为http请求的host，例如设置为`cloudfront.com`伪装为云服务器请求，可以使用逗号分割多个host如`a.com,b.net,c.org`，这时会随机使用。注意，错误设置此参数可能导致连接被断开甚至IP被封锁，如不清楚如何设置那么请留空。  

`tls_simple`（不建议使用）：模拟https/TLS1.2的握手过程，目前未完成，server hello的应答数据不完整，但亦可使用。对于防火墙针对TLS握手协议篡改会无视，但结果是会导致此连接超时断开，浏览器响应缓慢。此插件可以兼容原协议(需要在服务端配置为`tls_simple_compatible`)，比原协议多一次握手导致连接时间会长一些，除了握手过程之后没有冗余数据包，不支持自定义参数。  

`random_head`：开始通讯前发送一个几乎为随机的数据包（目前末尾4字节为CRC32，会成为特征，以后会改进），之后为原协议流。目标是让首个数据包根本不存在任何有效信息，让统计学习机制见鬼去吧。此插件可以兼容原协议(需要在服务端配置为`random_head_compatible`)，比原协议多一次握手导致连接时间会长一些，除了握手过程之后没有冗余数据包，不支持自定义参数。  

`tls1.0_session_auth`:模拟TLS1.0在客户端有session ID的情况下的握手连接。目前为完整模拟实现，因为有session ID所以没有发送证书等复杂步骤，因而防火墙无法根据证书做判断。同时自带一定的抗重放攻击的能力。如遇到重放攻击则会在服务端log里搜索到，可以通过`grep "replay attack"`搜索，可以用此插件发现你所在地区线路有没有针对TLS的干扰。防火墙对TLS比较无能为力，抗封锁能力应该会较其它插件强，但遇到的干扰也可能不少，不过协议本身会检查出任何干扰，遇到干扰便断开连接，避免长时间等待，让客户端或浏览器自行重连。在支持本插件的情况下建议不要使用`tls_simple`。此插件可以兼容原协议(需要在服务端配置为`tls1.0_session_auth_compatible`)，比原协议多一次握手导致连接时间会长一些，除了握手过程之后没有冗余数据包，不支持自定义参数，使用C#客户端开启自动重连时比其它插件表现更好。

#### 3.协议定义插件 ####
此类型的插件实质上用于定义加密前的协议，部分插件能兼容原协议。

`verify_simple`：对每一个包都进行CRC32验证和长度混淆，数据格式为：包长度(2字节)|随机数据长度+1(1字节)|随机数据|原数据包|CRC32，此混淆插件可完全替代之前版本制作的协议。此插件与原协议握手延迟相同，整个通讯过程中存在验证及混淆用的冗余数据包，下载的情况下冗余数据平均占比1%，普通浏览时占比略高一些，但平均也不会超过5%。

`verify_deflate`：对每一个包都进行deflate压缩，数据格式为：包长度(2字节)|压缩数据流|原数据流Adler-32，此格式省略了0x78,0x9C两字节的头部。另外，对于已经压缩过或加密过的数据将难以压缩（可能增加1~20字节），而对于未加密的html文本会有不错的压缩效果。因为压缩及解压缩较占CPU，不建议较多用户同时使用此混淆插件。

`verify_sha1`：对每一个包都进行SHA-1校验，具体协议描述参阅[One Time Auth](https://shadowsocks.org/en/spec/one-time-auth.html)，握手数据包增加10字节，其它数据包增加12字节。此插件能兼容原协议（需要在服务端配置为`verify_sha1_compatible`)。

`auth_simple`：首个客户端数据包会发送由客户端生成的随机客户端id(4byte)、连接id(4byte)、unix时间戳(4byte)以及CRC32，服务端通过验证后，之后的通讯与`verify_simple`相同。此插件提供了最基本的认证，能抵抗一般的重放攻击，默认同一端口最多支持16个客户端同时使用，可通过修改此值限制客户端数量，缺点是使用此插件的服务器与客户机的UTC时间差不能超过5分钟，通常只需要客户机校对本地时间并正确设置时区就可以了。此插件与原协议握手延迟相同，支持服务端自定义参数，参数为10进制整数，表示最大客户端同时使用数。

`auth_sha1`：对首个包进行SHA-1校验，同时会发送由客户端生成的随机客户端id(4byte)、连接id(4byte)、unix时间戳(4byte)，之后的通讯使用Adler-32作为效验码。此插件提供了能抵抗CCA的认证，也能抵抗一般的重放攻击，默认同一端口最多支持64个客户端同时使用，可通过修改此值限制客户端数量，使用此插件的服务器与客户机的UTC时间差不能超过1小时，通常只需要客户机校对本地时间并正确设置时区就可以了。此插件与原协议握手延迟相同，能兼容原协议（需要在服务端配置为`auth_sha1_compatible`)，支持服务端自定义参数，参数为10进制整数，表示最大客户端同时使用数。

`auth_sha1_v2`：与`auth_sha1`相似，去除时间验证，以避免部分设备由于时间导致无法连接的问题，增长客户端ID为8字节，使用更大的长度混淆。能兼容原协议（需要在服务端配置为`auth_sha1_v2_compatible`)，支持服务端自定义参数，参数为10进制整数，表示最大客户端同时使用数。

这样以来，将来只要简单的换一个混淆插件，让大家的特征各不相同，GFW就极难下手统一封锁了。推荐使用`auth_sha1`插件，安全性在以上插件里最高，同时如果要发布公开代理，以上auth插件均可严格限制使用人数（要注意的是服务端若配置为`compatible`，那么用户只要使用原协议就没有限制效果）。

#### 协议特性 ####

假设 method = "aes-256-cfb"

| name           | encode speed | bandwidth | anti CPA | anti CCA | anti replay attack | anti packet length analysis | anti packet time sequence analysis |
| -------------- | ----: | :-------: | :------: | :------: | :---------------: | :-------------------: | :-----------------: |
| origin         | 100%  |     99%   |    Yes   |    No    |        No         |            0          |          0          |
| verify_simple  |  99%  |     96%   |    Yes   |    No    |        No         |            1          |          0          |
| verify_deflate |  96%  |  97%~110% |    Yes   |    No    |        No         |            6          |          0          |
| verify_sha1    |  98%  |   94%/99% |    Yes   |    Yes   |        No         |            0          |          0          |
| auth_simple    |  99%  |     95%   |    Yes   |    No    |        Yes        |            1          |          0          |
| auth_sha1      |  99%  |     90%   |    Yes   |    Yes   |        Yes        |            4          |          0          |
| auth_sha1_v2   |  99%  |     70%   |    Yes   |    Yes   |        Yes        |           10          |          0          |

说明：  

-  以上为浏览普通网页（非下载非看视频）的平均测试结果，浏览不同的网页会有不同的偏差
-  encode speed仅用于提供相对速度的参考，不同环境下代码执行速度不同
-  verify_deflate的bandwidth（有效带宽）上限110%仅为估值，若数据经过压缩或加密，那么压缩效果会很差
-  verify_sha1的bandwidth意为上传平均有效带宽96%，下载99%
-  auth\_sha1\_v2的bandwidth在浏览普通网页时较低（为了较强的长度混淆，但单个数据包尺寸会保持在1500以内），而看视频或下载时比auth_sha1要高，可达98%，所以不用担心大流量下载时的速度
-  如果同时使用了其它的混淆插件，会令bandwidth的值降低，具体由所使用的混淆插件及所浏览的网页共同决定
-  对于抗包长度分析一列，满分为100，即0为完全无效果，5以下为效果轻微，具体分析方法可参阅方叫兽等人论文
-  对于抗包时序分析一列，方某人的论文表示虽然可利用，但利用难度大（也即他们还没能达到实用级），目前对此也不做处理
-  在现阶段，功夫网的重放攻击(Replay attack)十分常见，建议使用能抗重放攻击的协议。

#### 配置方法 ####
服务端配置：使用最新[SSR的manyuser](https://github.com/breakwa11/shadowsocks/tree/manyuser)分支  
user-config.json或config.json里有一个protocol的字段，目前的可能取值为：  
`origin`  
`verify_simple`  
`verify_deflate`  
`verify_sha1`  
`verify_sha1_compatible`  
`auth_simple`  
`auth_sha1`  
`auth_sha1_compatible`  
`auth_sha1_v2`  
`auth_sha1_v2_compatible`  
config.json里有一个obfs的字段，目前的可能取值为：  
`plain`  
`http_simple`  
`http_simple_compatible`  
`tls_simple`  
`tls_simple_compatible`  
`random_head`  
`random_head_compatible`  
`tls1.0_session_auth`  
`tls1.0_session_auth_compatible`  
默认为  
`"protocol":"auth_sha1_compatible",`  
`"obfs":"tls1.0_session_auth_compatible",`  
相应的  
协议插件参数默认为`"protocol_param":""`  
混淆插件参数默认为`"obfs_param":""`  
对于protocol，必须服务端与客户端严格匹配  
服务端配置为`xxabc_compatible`时（以compatible为后缀的），即服务端**支持使用原版客户端**，或使用配置插件为`xxabc`或`plain`的ssr客户端。

客户端配置：使用本ssr版本，在编辑服务器配置里找到相应节点，最后在protocol选项和obfs选项的列表里选择需要使用的插件，然后填写相应的参数即可  

兼容性：目前ssr-libev客户端仅支持`verify_simple`, `auth_simple`, `auth_sha1`, `auth_sha1_v2`协议，及`http_simple`, `tls1.0_session_auth`混淆。其余的ssr-python及ssr-c#支持全部的协议和混淆。

## 实现接口 ##
以下以C#语言为例做说明

interface IObfs

成员函数

`InitData()`  
参数：无  
返回：一个自定义类型变量，通常用于保存此接口的全局信息，不应返回null，c语言中返回void*  
说明：第一次创建实例前调用，同一服务端配置不会重复调用，服务端在建立监听时调用，客户端在第一次连接时调用。  

`SetServerInfo(ServerInfo serverInfo)`  
参数：ServerInfo结构，包含成员变量：  

- host: 字符串类型，服务端ip，客户端需要把域名转换为ip，如有前置代理，则直接使用配置时所用的域名也可，服务端需要获取监听ip
- port: 整数类型，服务端监听端口
- param: 用户设置的参数，字符串类型
- data: 由InitData返回的结果，为object类型（c语言中使用void*）
- iv: 客户端或服务端加密时使用的iv数组（c语言中需要添加额外字段以记录其长度，下同）
- recv_iv: 客户端或服务端接收到的iv数组
- key: 加密时使用的key（不是原key，是通过BytesToKey生成的指定长度数组）
- tcp_mss: 整数类型，TCP分包大小，设置为1440

返回：无  
说明：实例构造的时候（每个连接建立时）调用，调用前iv和key必须已经初始化；而接收到数据后先初始化recv_iv再调用插件。  

`Dispose()`  
参数：无  
返回：无  
说明：实例析构时（每个连接关闭时）调用

`byte[] ClientPreEncrypt(byte[] plaindata, int datalength, out int outlength)`  
参数：需要处理的字节数组及其长度  
返回：处理后的字节数组及其长度  
说明：客户端发送到服务端数据**加密前**调用此接口

`byte[] ClientEncode(byte[] encryptdata, int datalength, out int outlength)`  
参数：需要编码的字节数组及其长度  
返回：编码后的字节数组及其长度  
说明：客户端发送到服务端数据**加密后**调用此接口

`byte[] ClientDecode(byte[] encryptdata, int datalength, out int outlength, out bool needsendback)`  
参数：需要解码的字节数组及其长度  
返回：解码后的字节数组及其长度，以及needsendback标记是否立即回发服务端数据。如needsendback为true，则会立即调用ClientEncode，调用时参数是一个长度为0的字节数组  
说明：客户端收到服务端数据在**解密前**调用此接口

`byte[] ClientPostDecrypt(byte[] plaindata, int datalength, out int outlength)`  
参数：需要处理的字节数组及其长度  
返回：处理后的字节数组及其长度  
说明：客户端收到服务端数据在**解密后**调用此接口

`byte[] ServerPreEncrypt(byte[] plaindata, int datalength, out int outlength)`  
参数：需要处理的字节数组及其长度  
返回：处理后的字节数组及其长度  
说明：服务端发送到客户端数据**加密前**调用此接口

`byte[] ServerEncode(byte[] encryptdata, int datalength, out int outlength)`  
参数：需要编码的字节数组及其长度  
返回：编码后的字节数组及其长度  
说明：服务端发送到客户端数据**加密后**调用此接口

`byte[] ServerDecode(byte[] encryptdata, int datalength, out int outlength, out bool needdecrypt, out bool needsendback)`  
参数：需要解码的字节数组及其长度  
返回：解码后的字节数组及其长度，以及needdecrypt标记数据是否需要解密（一般都应该为true），以及needsendback标记是否立即回发客户端数据。如needsendback为true，则会立即调用ServerEncode并发送其返回结果，调用时参数是一个长度为0的字节数组  
说明：服务端收到客户端数据在**解密前**调用此接口

`byte[] ServerPostDecrypt(byte[] plaindata, int datalength, out int outlength)`  
参数：需要处理的字节数组及其长度  
返回：处理后的字节数组及其长度  
说明：服务端收到客户端数据在**解密后**调用此接口

`byte[] ClientUdpPreEncrypt(byte[] plaindata, int datalength, out int outlength)`  
参数：需要处理的字节数组及其长度  
返回：处理后的字节数组及其长度  
说明：客户端发送到服务端UDP数据**加密前**调用此接口

`byte[] ClientUdpPostDecrypt(byte[] plaindata, int datalength, out int outlength)`  
参数：需要处理的字节数组及其长度  
返回：处理后的字节数组及其长度  
说明：客户端收到服务端UDP数据在**解密后**调用此接口

`byte[] ServerUdpPreEncrypt(byte[] plaindata, int datalength, out int outlength)`  
参数：需要处理的字节数组及其长度  
返回：处理后的字节数组及其长度  
说明：服务端发送到客户端UDP数据**加密前**调用此接口

`byte[] ServerUdpPostDecrypt(byte[] plaindata, int datalength, out int outlength)`  
参数：需要处理的字节数组及其长度  
返回：处理后的字节数组及其长度  
说明：服务端收到客户端UDP数据在**解密后**调用此接口

### 插件编写 ###

有两类插件，一类是协议插件，一类是混淆插件

其中接口InitData, SetServerInfo, Dispose接口必须实现，其它的接口为通讯接口

编写协议插件的话，需要重写ClientPreEncrypt, ClientPostDecrypt, ServerPreEncrypt, ServerPostDecrypt，其它的按原样返回，needdecrypt必须为true，needsendback必须为false

编写混淆插件的话，需要重写ClientEncode, ClientDecode, ServerEncode, ServerDecode，其它的按原样返回。

如果编写的部分仅含客户端部分，那么只需要编写Client为前缀的两个接口，服务端同理。

目前支持此插件接口的，有 [ShadowsocksR C#](https://github.com/breakwa11/shadowsocks-csharp/releases) 和 [ShadowsocksR Python](https://github.com/breakwa11/shadowsocks/tree/manyuser)