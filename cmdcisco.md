
*config ssh
en (config)#
ip domain-name ipssi.com
hostname Routeur 1
Crypto key generate Rsa
username broly password ipssi
line vty 0 4
login local
transport input ssh

*en command prompt
ssh -l broly iprouteur