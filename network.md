# 网路基础
- 网络传输中的三张表，MAC地址表（二层：交换机），ARP缓存表（三层到二层：路由器，PC），路由表（三层：路由器，PC）
- 路由表用于三层在发送IP数据包前，查找下一跳的IP地址，下一跳IP地址可能是Gateway（跨网段）也可能直接是目标IP（同一个网段内）。路由表有静态路由（手动设置和动态路由（根据路由发现协议学习而得）。路由表有3 种路由类型，主机路由，网路路由和默认路由。
- ARP缓存表用来查找下一跳IP（三层）对应的MAC地址（二层）的，ARP缓存表由ARP协议主动获得。
- MAC地址表用来查询MAC地址对应的交换机端口以便于交换机完成源端口到目标端口的数据交换，MAC地址表由交换机从端口接收到的二层数据帧的源MAC地址学习而得。
# A网路 - Internet - B网路之间的通信
- 客户端发出请求（A网路内）
- A网路内请求出A网路, 执行SNAT（Terget IP不变，Source IP每经过一层NAT就变一次，最后到Internet的数据Source IP应该是A网络的公网IP）
- A网路内请求在公网传输（从A网络的公网IP到B网路的公网IP）
- A网路内请求入B网络，执行DNAT（Source IP不变，起始Target IP应该是B网络的公网IP，然后Target IP每经过一层NAT就变一次）
- 请求到达最终服务器（B网络内），服务起发出相应数据
- B网路内响应出B网路，执行SNAT（Terget IP不变，Source IP每经过一层NAT就变一次，最后到Internet的数据Source IP应该是B网络的公网IP）
- B网路内响应在公网传输（从B网络的公网IP到A网路的公网IP）
- B网路内响应入A网络，执行DNAT（Source IP不变，起始Target IP应该是A网络的公网IP，然后Target IP每经过一层NAT就变一次）
- 相应数据到达客户端（A网路内）
# TCP分段与IP分片
- 最大传输单元(Maximum Transmission Unit)，即MTU，为数据链路层的最大载荷上限(即IP数据报最大长度)，每段链路的MTU可能都不相同，一条端到端路径的MTU由这条路径上MTU最小的那段链路的MTU决定。
- MTU是链路层中的网络对数据帧的一个限制，以以太网为例，MTU通常为1500字节，采用巨帧(Jumbo Frame)时可以达到9000字节。所谓的MTU，是二层协议的一个限制，对不同的二层协议可能有不同的值，只有二层协议为以太网(Ethernet)时，MTU一般才取1500字节，注意它不是物理链路介质的限制，只有工作在二层的设备才需要指定MTU的值，如网卡、转发设备端口(统称为网络接口)等，通过同一段线缆直连的通信端口或网卡，其MTU值一定相同。
- 一个IP数据报在以太网中传输，如果它的长度大于当前链路MTU值，就要进行分片传输(这里指IP层分片)，使得每片数据报的长度都不超过MTU。分片传输的IP数据报不一定按序到达，但IP首部中的信息能让这些数据报片按序组装。IP数据报的分片与重组是在网络IP层完成的。
- 最大报文段长度(Maximum Segment Size)，即MSS，为TCP传输层的最大载荷上限(即应用层数据最大长度)，TCP三次握手期间通过TCP首部选项中的MSS字段通知对端，通常一条TCP连接的MSS取通信双方较小的那一个MSS值，与MTU的换算关系为：MTU = MSS + TCP首部长度 + IP首部长度
- 故在以太网中(网络层以IPv4为例)：
- MSS = 以太网MTU - TCP首部长度 - IPv4首部长度 = 1500 - 20 - 20 = 1460字节
- 未指定MSS时默认值为536字节，这是因为在Internet中标准的MTU值为576字节，576字节MTU = TCP首部长度20字节 + IPv4首部长度20字节 + 536字节MSS。
- 一个应用程序如果要发送超过MSS大小的数据，就要进行分段传输(这里指TCP分段)，使得每个报文段长度都不超过MSS。分片传输的TCP报文段不一定按序到达，但实现可靠传输的TCP协议中有处理乱序的机制，即利用报文段序列号在接收缓冲区进行数据重排以实现重组。TCP分段的重组是在TCP传输层完成的。
# 二层网络与三层网络
- 二层网络只能通过邻接网络完成寻址通讯。
- 三层网络可以跨邻接网络完成寻址通讯。
- 一次三层跨网络通讯是基于多个二层邻接网络的通讯接力合作完成的。
# TCP keepalive and firewall killing idle sessions
## TCP keepalive
- netstat --timer -cnpt
- ss -n -o state established
## Firewall killing idle sessions
- A stateful firewall checks the packets and also confirm if the connection is alive.
## Manual kill session by tcpkill
- tcpkill -i wlan0 <express>
- ss -K dst 192.168.1.2 dport=49029
- ss --kill -o state established "( dst 192.168.1.2 and dport = 49029 )"

# How to limit idle timeout?
## Option 1 - global
- Limit the idle timeout value of an established connection, for all connections by modifying the appropriate global netfilter variable.
- Verify the current default value with:
- `cat /proc/sys/net/netfilter/nf_conntrack_tcp_timeout_established`
- Modify the default value (for example, to 600 seconds) with:
- `echo 600 > /proc/sys/net/netfilter/nf_conntrack_tcp_timeout_established`
## Option 2 - granular
- Limit the idle timeout value of an established connection, for specific connections, by setting up a timeout policy and using that policy with the iptables command.
- Setup a timeout policy with:
- `sudo nfct add timeout nam1 inet tcp established 600`
- `sudo nfct list timeout`
- `sudo nfct delete timeout nam1`
- Apply that policy to iptables rules:
- `iptables -I PREROUTING -t raw -p tcp -j CT --timeout nam1`
- `iptables -I OUTPUT -t raw -p tcp -j CT --timeout nam1`
- You might need to install the nfct tool, which on debian / ubuntu, comes from the package with the same name (nfct).
## Additional info
- man iptables-extensions shows some information on the CT target.
- man nfct shows an example which is close to the information you requested. (You may have issues using the timeout policy with a name of more than four characters in length.)
- The conntrack tool can be useful for following what's going on at different events of the connection (e.g. conntrack -E -p tcp).
