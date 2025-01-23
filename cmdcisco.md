config de telnet
-conf t
-line vty 04
-password
-login
-exit
-password cisco

*config ssh
en (config)#
ip domain-name ipssi.com
hostname Routeur 1
Crypto key generate Rsa
username broly password ipssi
line vty 0 4
no login
transport input ssh

*en command prompt
ssh -l broly iprouteur