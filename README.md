# DNSPodDdns
原版的ipv6支持在RT-AC68U上并不起效

所以做了以下修改：
1. 修复了ipv6解析不起效的问题（ipv6地址实际上在br0网卡上）
2. ipv4和ipv6分别解析到两个子域名（或主域名），可以只解析其中一个
3. 解析ipv4时调用的api换为修改记录的api，以免没有公网ipv4时，本地IP和远程解析的IP始终不同，但远程解析的IP仅为WAN口IP
4. 同步成功的标准改为ipv4和ipv6有一个成功即为成功（我发现其实ipv6获取得也很快？）
5. 其他细小改动

无名萌新，第一次写脚本，仅在自己的RT-AC68U上测试能够正常，如需使用请谨慎，不足之处请见谅。

# 以下原版介绍
对荒野无灯基于[anrip](https://github.com/anrip/ArDNSPod)的脚本添加 ipv6 支持
* 2019.01.21 - 由于梅林获取ipv6有延迟，修改了下脚本，只在ddns ipv4 更新后通知梅林成功，更新ipv6后不会通知。当然实际上已经可用了

荒野无灯原帖：http://koolshare.cn/thread-37553-1-1.html

主要做了以下几点修改：
1. arNslookup （查询域名已解析的IP地址 ）修改为 dnspod api post方式
2. 梅林固件重启后不会立刻获取到公网 ipv6 地址，当脚本检测不到 ipv6 地址时会等待 5 分钟后重新获取，还是没有则继续等待 5 分钟 ，以此类推。 期间梅林控制台页面会显示叹号，但此时 ipv4 动态解析已经可用 
3. 域名，二级域名 的传入参数作为全局变量使用，相关函数的传入参数做了简化

使用此脚本，需：
1. 指定 arToken ，Token请去 https://www.dnspod.cn/console/user/security 获取
2. 指定 arDdnsCheck "域名" "二级域名(可为空)" 如：
arDdnsCheck "baidu.com" ""  会动态解析 baidu.com 域名
arDdnsCheck "baidu.com" "www"  会动态解析 www.baidu.com 域名
3. 因为 ipv4 与 ipv6 使用不同的recordID，使用脚本前先在 dnspod 官方控制台页面添加该域名（或二级域名）的 AAAA 记录，以生成 ipv6 的recordID
