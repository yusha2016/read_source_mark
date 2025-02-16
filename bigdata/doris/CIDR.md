CIDR（Classless Inter-Domain Routing，无类域间路由）是一种用于表示IP地址和网络前缀的方法，目的是替代旧的基于类的IP地址分配方法。CIDR使用IP地址和一个附加的斜杠后跟数字的方式来表示网络，提供了更灵活和高效的IP地址分配和路由。

CIDR网段表示法

一个CIDR网段由两部分组成：

	1.	IP地址：一个标准的IPv4或IPv6地址。
	2.	前缀长度：一个斜杠（/）后面跟着一个数字，表示网络部分的长度（以比特为单位）。

示例：

	•	IPv4：192.168.1.0/24
	•	IPv6：2001:db8::/32

解析CIDR表示法

1. IPv4 CIDR表示法

示例：192.168.1.0/24

	•	IP地址部分：192.168.1.0
	•	前缀长度：/24

解释：

	•	前缀长度为24意味着网络部分占据前24位，比特。
	•	子网掩码为255.255.255.0（即24个1跟随8个0）。

网络范围：

	•	网络地址：192.168.1.0
	•	广播地址：192.168.1.255
	•	可用主机范围：192.168.1.1 到 192.168.1.254

2. IPv6 CIDR表示法

示例：2001:db8::/32

	•	IP地址部分：2001:db8::
	•	前缀长度：/32

解释：

	•	前缀长度为32意味着网络部分占据前32位，比特。
	•	子网范围覆盖所有以2001:db8开头的地址。

网络范围：

	•	网络地址：2001:db8:0000:0000:0000:0000:0000:0000
	•	广播地址（IPv6没有传统的广播地址）：全局范围的IPv6地址从2001:db8::到2001:db8:ffff:ffff:ffff:ffff:ffff:ffff

计算CIDR网段

1. IPv4

示例：192.168.1.0/26

	•	IP地址：192.168.1.0
	•	前缀长度：26

子网掩码：

	•	转换前缀长度为子网掩码：11111111.11111111.11111111.11000000 (255.255.255.192)

网络范围：

	•	网络地址：192.168.1.0
	•	广播地址：192.168.1.63
	•	可用主机范围：192.168.1.1 到 192.168.1.62

CIDR的优点

	1.	更高效的地址分配：CIDR允许分配非标准大小的网络，减少了IP地址的浪费。
	2.	简化路由：CIDR通过聚合路由条目减少了路由表的大小，提升了路由性能。
	3.	灵活性：CIDR支持可变长度的子网掩码（VLSM），使得网络管理员可以根据需要划分子网。

总结

CIDR（Classless Inter-Domain Routing）是一种灵活、高效的IP地址分配和路由表示方法，使用斜杠后跟数字的方式表示网络前缀长度。CIDR表示法广泛应用于现代网络，支持IPv4和IPv6地址，并通过更精细的地址划分和路由聚合，提高了网络资源的利用率和路由效率。