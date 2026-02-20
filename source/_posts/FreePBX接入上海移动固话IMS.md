---
title: FreePBX接入上海移动固话IMS
abbrlink: b4f33810
date: 2026-02-20 12:16:07
tags:
---

# 0x00 前言

上海移动的固话和很多运营商一样，是IMS网络，这里记录一下如何用FreePBX接入上海移动固话。

# 0x01 准备

- FreePBX
- 获得的IMS鉴权密码

# 0x02 配置

将语音VLAN透传给FreePBX，使用DHCP获取语音业务IP和DNS。解析sbc.chinamobile.com获得Outbound Proxy IP地址。这里的sbc.chinamobile.com每个省份可能不同，按照自己光猫配置获得。

配置自己的互联网网口，如果有需要映射公网，可能需要把默认路由指向自己的路由器，删除语音VLAN的默认路由。

在FreePBX中配置PJSIP，设置如下参数：

Username/Auth Username: +8621XXXXXXXX@ims.sh.chinamobile.com
Secret: 提取的IMS密码
SIP Server: ims.sh.chinamobile.com
SIP Port: 5060
Outbound Proxy: sip:221.181.108.129\;lr 这里的IP地址是sbc.chinamobile.com的IP地址
From Domain: ims.sh.chinamobile.com
From User: +8621XXXXXXXX
Client URI: sip:+8621XXXXXXXX@ims.sh.chinamobile.com:5060
Server URI: sip:ims.sh.chinamobile.com
AOR: sip:+8621XXXXXXXX@ims.sh.chinamobile.com:5060
AOR Contact: sip:ims.sh.chinamobile.com
Match (Permit): 221.181.108.0/24 这里从信令获得，我这里大部分呼叫都来自221.181.108.129周围的IP，所以就这么写了。可以注册后在asterisk -rvvvvvv使用pjsip set logger on查看呼叫信令。
Direct Media: No

在Codec界面，把G.729关掉，保证G.711a和G.711u在最前面。
完成后，分机就可以正常呼入和呼出，如果有问题，可以在信令中查看问题。