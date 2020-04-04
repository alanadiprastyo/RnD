# TUTORIAL Instal PPTP VPN
PPTP VPN adalah point to point protocol untuk vpn

## 0. Disable Firewall
```
> systemctl disable firewalld
> systemctl stop firewalld
> setenforce 0
```

## 1. Install pptp
```
> yum -y install epel-release
> yum -y install ppp pptpd net-tools iptables-services
```

## 2. Setting pptp server
```
> mv /etc/pptpd.conf /etc/pptpd.conf.bkp
> mv -f /etc/ppp/options.pptpd /etc/ppp/options.pptpd.bkp

> vi /etc/ppp/options.pptpd
----
name pptpd
refuse-pap
refuse-chap
refuse-mschap
require-mschap-v2
require-mppe-128
proxyarp
lock
nobsdcomp
novj
novjccomp
nologfd
ms-dns 192.168.1.30
ms-dns 8.8.8.8
----

> vi /etc/pptpd.conf
----
option /etc/ppp/options.pptpd
logwtmp
localip 10.0.10.1
remoteip 10.0.10.2-254
----

#enable port forward
> echo net.ipv4.ip_forward = 1 >> /etc/sysctl.conf
> sysctl -p

#firewall routing
> service iptables save
> service iptables stop
> chkconfig iptables off
> iptables -F
> chmod +x /etc/rc.d/rc.local
> echo "iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE" >> /etc/rc.d/rc.local
> source /etc/rc.d/rc.local
> sudo iptables-save
```

## 3. Setting pptp user
```
> vi /etc/ppp/chap-secrets
----
pajak pptpd redhat123 *
----

#Restart service pptpd
> systemctl restart pptpd
```
