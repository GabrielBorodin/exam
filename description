https://amirkhans.notion.site/1ec7c3d805674e459a7b202d2d9178de

https://www.notion.so/s-n-2222/cd184760b0e54fdf832ca2ea2364a70e

https://clck.ru/3BUBnW

# AOC
- RTR-L
    1. hostnamectl set-hostname rtr-l.au.team
    2. vim /etc/net/sysctl.conf 
        1. net.ipv4.ip_forward = 1
    3. cd /etc/net/ifaces
    4. cp -r ens19/ ens20
    5. vim ens20/options
        1. BOOTPROTO=static
    6. cp -r ens20/ enp0s21
    7. vim ens20/ipv4address
        1. 10.10.10.1/24
    8. vim ens21/ipv4address
        1. 20.20.20.1/24
    9. systemctl restart network
    10. reboot
    11. apt-get update && apt-get install nftables dhcp-server  -y
    12. vim /etc/nftables/nftables.nft
        1. в начало:
        flush ruleset;
        2. в chain input:
        ip saddr 10.10.10.100 icmp type echo-request drop;
        ip saddr 10.10.10.100 tcp dport 65000 drop;
        3. в chain output:
        ip daddr 10.10.10.100 icmp type echo-request drop;
        4. в конец:
        table ip nat {
          chain postrouting {
             type nat hook postrouting priority 0;
             oifname ens19 masquerade;
          }
        }
        5. вот так:
  ![1](https://github.com/ErmKaterina/AOC/assets/109353253/9d99c015-5567-4577-83ce-19c08c4cadf6)
          
          
    13. systemctl enable --now nftables
    14. nft -f /etc/nftables/nftables.nft
    15. vim /etc/dhcp/dhcpd.conf
        1. вписываем это:
            
            ```
            option subnet-mask 255.255.255.0;
            option domain-name "au.team";
            option domain-name-servers 10.10.10.100;
            subnet 10.10.10.0 netmask 255.255.255.0 {
              range 10.10.10.100 10.10.10.120;
              option routers 10.10.10.1;
            }
            subnet 20.20.20.0 netmask 255.255.255.0 {
              range 20.20.20.150 20.20.20.200;
              option routers 20.20.20.1;
            }
            host l-srv {
              hardware ethernet (MAC-адрес l-srv см. пункт b);
              fixed-address 10.10.10.100;
            }
            ```
            
        2. Чтобы узнать MAC-адрес l-srv, пишем на l-srv команду ip -c a, ищем строку link/ether xx:xx:xx:xx:xx:xx, вот здесь, тут написан MAC-адрес, его записываем в hardware ethernet в пункте выше **БЕЗ СКОБОК**:
            
![2](https://github.com/ErmKaterina/AOC/assets/109353253/5c801399-1968-4bc9-882e-4613fa988754)
  
        3. вот так:
            
![3](https://github.com/ErmKaterina/AOC/assets/109353253/262360b8-43b9-45d3-9bdf-8ef03e36a222)            
    16. vim /etc/sysconfig/dhcpd
        1. DHCPDARGS="ens20 ens21"
    17. systemctl enable --now dhcpd


- ADMIN-PC
    1. hostnamectl set-hostname admin-pc.au.team
    2. vim /etc/net/ifaces/ens19/options
        1. BOOTPROTO=dhcp
        2. TYPE=eth
        3. NM_CONTROLLED=no
        4. DISABLED=no
        5. **вот так и больше ничего**:
            
![4](https://github.com/ErmKaterina/AOC/assets/109353253/d478ef31-fc7d-4cde-9046-785e475a25da)
     
    3. reboot
    4. Если DNS на SRV еще не настроили, а интернет нужен, то в 
    vim /etc/resolv.conf пишем:
        1. добавить в начало:
        nameserver 94.232.137.104
        2. после того, как мы настроили свой DNS сервер в лице L-SRV, а именно после 15 пунтка в L-SRV, надо в /etc/resolv.conf указать только domain au.team и nameserver 10.10.10.100, больше ничего.

    - L-SRV
    1. hostnamectl set-hostname l-srv.au.team
    2. vim /etc/net/ifaces/ens19/options
        1. BOOTPROTO=dhcp
        2. TYPE=eth
        3. NM_CONTROLLED=no
        4. DISABLED=no
        5. **вот так и больше ничего**:

![5](https://github.com/ErmKaterina/AOC/assets/109353253/83376222-a554-41f2-adeb-817c9dcd19af)

reboot
vim /etc/resolv.conf
1 в начало:
    nameserver 94.232.137.104

    apt-get update && apt-get install task-samba-dc krb5-kdc -y

    systemctl stop smb nmb krb5kdc slapd bind dnsmasq
    systemctl disable smb nmb krb5kdc slapd bind dnsmasq
    rm -f /etc/samba/smb.conf
    rm -rf /var/lib/samba
    rm -rf /var/cache/samba
    mkdir -p /var/lib/samba/sysvol
    samba-tool domain provision

будут вылезать подсказки для настройки домена, нужно ответить на них вот так:

Realm [AU.TEAM]: //жмем Enter

Domain [AU]: //жмем Enter

Server Role (dc, member, standalone) [dc]: //жмем Enter

DNS backend (SAMBA_INTERNAL, BIND9_FLATFILE, BIND9_DLZ, NONE) [SAMBA_INTERNAL]: //жмем Enter

DNS forwarder IP address (write 'none' to disable forwarding) [94.232.137.104]: //если в квадратных скобках не указан 94.232.137.104, то пишем 94.232.137.104 и жмем Enter, если уже указан, то просто жмем Enter.

Administrator password: //Вводим пароль P@ssw0rd

Retype password: //Повторяем пароль P@ssw0rd

13. systemctl enable --now samba
14. reboot
15. cp /var/lib/samba/private/krb5.conf /etc/krb5.conf
16. vim /etc/resolv.conf
    1. должно быть указано только:
    domain au.team
    nameserver 10.10.10.100
    
![6](https://github.com/ErmKaterina/AOC/assets/109353253/a0d0cbff-720c-4310-b2b8-e53de23ba330)
    
    17. systemctl restart network
18. проверяем (на всякий случай):
    1. samba-tool domain info 10.10.10.100
![7](https://github.com/ErmKaterina/AOC/assets/109353253/c9665596-3176-489c-8310-577a529d9677)
    
    kinit administrator
    
вводим пароль P@ssw0rd

klist

![8](https://github.com/ErmKaterina/AOC/assets/109353253/b9908d85-b2a6-4c8b-9ba2-a39e04c75de6)

20. for i in {1..15}; do samba-tool user create user$i.userl P@ssw0rd; done;
21. for i in {1..5}; do samba-tool user create user$i.admin P@ssw0rd; done;
22. samba-tool group add left
23. samba-tool group add admin
24. for i in {1..15}; do samba-tool group addmembers left user$i.userl; done;
25. for i in {1..5}; do samba-tool group addmembers admin user$i.admin; done;
26. samba-tool dns zonecreate 10.10.10.100 10.10.10.in-addr.arpa -U administrator
    1. пароль P@ssw0rd
27. samba-tool dns zonecreate 10.10.10.100 20.20.20.in-addr.arpa -U administrator
    1. пароль P@ssw0rd
28. samba-tool dns add 10.10.10.100 au.team admin-pc A 20.20.20.150 -U administrator
    1. пароль P@ssw0rd
29. samba-tool dns add 10.10.10.100 au.team rtr-l A 20.20.20.1 -U administrator
    1. пароль P@ssw0rd
30. samba-tool dns add 10.10.10.100 au.team rtr-l A 10.10.10.1 -U administrator
    1. пароль P@ssw0rd
31. samba-tool dns add 10.10.10.100 10.10.10.in-addr.arpa 1 PTR rtr-l -U administrator
    1. пароль P@ssw0rd
32. samba-tool dns add 10.10.10.100 10.10.10.in-addr.arpa 100 PTR l-srv -U administrator
    1. пароль P@ssw0rd
33. samba-tool dns add 10.10.10.100 20.20.20.in-addr.arpa 150 PTR admin-pc -U administrator
    1. пароль P@ssw0rd
34. samba-tool dns add 10.10.10.100 20.20.20.in-addr.arpa 1 PTR rtr-l -U administrator
    1. пароль P@ssw0rd
