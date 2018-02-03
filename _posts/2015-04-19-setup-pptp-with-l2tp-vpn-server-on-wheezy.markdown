---
layout: post
title:  "Setup pptp & l2tp/IPsec(PSK) mixed up VPN server on debian wheezy"
date:   2015-04-19 14:17:21
categories: sysadmin
comments: true
---

## Setup PPTP VPN server.
> PPTP is not secure, but it works where xl2tpd/openswan fails to work(iOS for example).

{% highlight bash %}
#be root & go on

# Install PPTP server
apt-get install -y pptpd

# toolkit to save iptables-rules
apt-get install iptables-persistent

# Using private address 10.0.4.x/24 for ppp0 device
echo 'localip 10.0.4.1' >> /etc/pptpd.conf
echo 'remoteip 10.0.4.2-200' >> /etc/pptpd.conf

# Using better authentication
cat <<EOCFG >>/etc/ppp/pptpd-options
refuse-pap
refuse-chap
refuse-mschap
require-mschap-v2
require-mppe-128
ms-dns 8.26.56.26
ms-dns 8.20.247.20
proxyarp
nodefaultroute
lock
novj
novjccomp
nobsdcomp
noipx
# debug to syslog
debug
mtu 1490
mru 1490
EOCFG

# Add test account
echo 'user pptpd password *' >> /etc/ppp/chap-secrets


# Enable packets forwarding for VPN server
echo 'net.ipv4.ip_forward=1' >>/etc/sysctl.conf
sysctl --system
# With out POSTROUTING you will able to connect VPN but not able to redirect packets
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
iptables -A POSTROUTING -t nat -o ppp+ -j MASQUERADE
# Enable IP forwarding
iptables -F FORWARD
iptables -A FORWARD -j ACCEPT
# Accept GRE packets
iptables -A INPUT -p 47 -j ACCEPT
iptables -A OUTPUT -p 47 -j ACCEPT
# Accept incoming connections to port 1723 (PPTP)
iptables -A INPUT -p tcp --dport 1723 -j ACCEPT
# Accept all packets via ppp* interfaces (for example, ppp0)
iptables -A INPUT -i ppp+ -j ACCEPT
iptables -A OUTPUT -o ppp+ -j ACCEPT

# Saving iptables rules, keep sane after reboot
iptables-save >/etc/iptables/rules.v4
echo <<EOCFG >>/etc/network/if-pre-up.d/iptables-restore-pptp
#!/bin/bash --
/sbin/iptables-restore < /etc/iptables/rules.v4

EOCFG

# Restart service & enable on next boot
service pptpd start
service pptpd stop
service pptpd restart
service pptpd status
update-rc.d pptpd enable

{% endhighlight %}
****
%%MORE%%
<br>
<br>

## Setup L2TP/IPsec VPN server    

> L2TP using UDP packets, if your ISP was known to drop them, consider using pptp instead.

{% highlight bash %}
# Be root, then go on

# Disable ICMP redirect, prevent protocol message redirect
cat <<EOCFG >> /etc/sysctl.conf
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.all.send_redirects = 0
EOCFG

# Install openswan for IPsec tunnel setup
# Install xl2tpd as L2TP server
apt-get install openswan xl2tpd

# Config IPsec tunnel
# note: Openswan using left & right describe VPN server & clients

cat <<EOCFG >>/etc/ipsec.conf
config setup
nat_traversal=yes
# Do not use 192.168.0.0/16 for it's using everywhere
virtual_private=%v4:10.0.0.0/8,%v4:172.16.0.0/12
oe=off
protostack=netkey
conn L2TP-PSK-NAT
rightsubnet=vhost:%priv
also=L2TP-PSK-noNAT
conn L2TP-PSK-noNAT
authby=secret
pfs=no
auto=add
keyingtries=3
rekey=yes
ikelifetime=8h
keylife=1h
type=transport
# Replace 8.8.8.8 with your VPN server IP
left=8.8.8.8
# If your server is within a gateway, you ought to configure leftnexthop to gateway IP
leftnexthop=%defaultroute
leftprotoport=17/1701
right=%any
rightprotoport=17/%any
EOCFG

# Add test PSK key, protect your key from the bright side of network
# Again, replace 8.8.8.8 with your VPN server IP
echo '8.8.8.8 %any : PSK “my-secure-psk”' >> /etc/ipsec.secrets

# Configure L2TP
cat <<EOCFG >/etc/xl2tpd/xl2tpd.conf
[global]
ipsec saref = yes
rand source = dev

[lns default]
ip range = 10.0.5.50-10.0.5.255
local ip = 10.0.5.1
refuse chap = yes
refuse pap = yes
require authentication = yes
name=l2tpd
ppp debug = yes
pppoptfile = /etc/ppp/options.xl2tpd
length bit = yes
EOCFG

# Now work around with ppp, to provide DNS info to L2TP tunnel
cat <<EOCFG >/etc/ppp/options.xl2tpd
require-mschap-v2
ms-dns 8.26.56.26
ms-dns 8.20.247.20
proxyarp
asyncmap 0
auth
crtscts
lock
hide-password
modem
debug
proxyarp
lcp-echo-interval 30
lcp-echo-failure 4
mtu 1400
connect-delay 5000
noccp
EOCFG

# Add test account
echo 'user l2tpd password *' >> /etc/ppp/chap-secrets

# Restart server
/etc/init.d/ipsec start
/etc/init.d/xl2tpd start
update-rc.d ipsec enable
update-rc.d xl2tpd enable
{% endhighlight %}
    
###referenced
[Archwiki PPTP_server](https://wiki.archlinux.org/index.php/PPTP_server)    
[Linux L2TP/IPsec VPN server](http://www.elastichosts.com/support/tutorials/linux-l2tpipsec-vpn-server/)
