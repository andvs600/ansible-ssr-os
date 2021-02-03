# Настройка брандмэуэра в ОС CentOS/RHEL с помощью Ansible

??? abstract
    1. [Общее описание](#общее-описание)
    2. [Параметры](#параметры)
    3. [Теги](#теги)
    4. [Примеры](#примеры)
    5. [Дополнительные материалы](#дополнительные-материалы)

## Общее описание
Роль для настройки [NetFillter](https://ru.wikipedia.org/wiki/Netfilter#:~:text=netfilter%20%E2%80%94%20%D0%BC%D0%B5%D0%B6%D1%81%D0%B5%D1%82%D0%B5%D0%B2%D0%BE%D0%B9%20%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%20(%D0%B1%D1%80%D0%B0%D0%BD%D0%B4%D0%BC%D0%B0%D1%83%D1%8D%D1%80),%D1%8F%D0%B4%D1%80%D0%BE%20Linux%20%D1%81%20%D0%B2%D0%B5%D1%80%D1%81%D0%B8%D0%B8%202.4.) с расширенными правилами под высокую нагрузку. Базовые настройки доработаны на основе статьи [DDoS Protection With IPtables](https://javapipe.com/blog/iptables-ddos-protection/). 

## Параметры
|Название переменной    | Тип переменной | Значения по умолчанию | Описание                                                                                 |
|:----------------------|:--------------:|:---------------------:|:-----------------------------------------------------------------------------------------|
|activate_firewall      | boolean        | undef (false)         | Активирует брандмэуэр.                                                                   |
|firewalld_not_disabled | boolean        | undef (false)         | Оставляет брандмэуэр по умолчанию (firewalld), дальнейшая настройка не производится.     |
|nf_ports               | array          | undef                 | Для открытия отдельного входящего порта.                                                 |
|nf_custom_ruleset      | array          | undef                 | Для добавления своих правил.                                                             |
|nf_int                 | boolean        | undef (true)          | Если не переопределен доступ возможен только из сети СПД `10.0.0.0/8`.                   |
|sper_limit_conn        | string         | 1024                  | Устанавливает ограничение на количество одновременных новых соединений.                  |
|lxc_host               | boolean        | undef                 | Добавляет правила работы docker контейнеров. Динамическое управления правилами отключено.|

!!! failure
    На хостах c установренным Docker'ом необходимо настравить правила в ручную с использованием `firewalld`.

!!! todo
    Дорабатывается поддержка расширинных правил для LXC.

## Теги
|Тег                  | Описание                                          |
|:--------------------|:--------------------------------------------------|
|iptables             | Выполняет сценарии для настройки `iptables`.      |
|nftables             | Выполняет сценарии для настройки `nftables`.      |

## Примеры

!!! example "inventory/hosts"
    ``` ini
    [example-servers]
    <host_name> ansible_ssh_host=<host_ip> ansible_ssh_user=<user_name_for_connect>

    [example-servers:vars]
    ansible_connection=ssh
    activate_firewall=true
    nf_int=true
    nf_ports=['80','443']
    ```

??? example "Правила для iptables в RHEL/CentOS 7"
    ``` cfg
    IPT=iptables
    $IPT -F
    $IPT -t nat -F
    $IPT -t mangle -F
    $IPT -P INPUT DROP
    $IPT -P FORWARD DROP
    $IPT -P OUTPUT ACCEPT
    # +++ STATE RULES +++
    # === Drop fragments in all chains ===
    $IPT -t mangle -A PREROUTING -f -j DROP
    # === Drop TCP packets that are new and are not SYN ===
    $IPT -t mangle -A PREROUTING -p tcp ! --syn -m conntrack --ctstate NEW -j DROP
    # === Drop invalid packets ===
    $IPT -A INPUT -m state --state INVALID -j DROP
    $IPT -A FORWARD -m state --state INVALID -j DROP
    $IPT -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
    $IPT -A FORWARD -m state --state ESTABLISHED,RELATED -j ACCEPT
    # === Drop SYN packets with suspicious MSS value ===
    $IPT -t mangle -A PREROUTING -p tcp -m conntrack --ctstate NEW -m tcpmss ! --mss 536:65535 -j DROP
    # === LOCALHOST ===
    $IPT -A FORWARD ! -i lo -s 127.0.0.1 -j DROP
    $IPT -A INPUT ! -i lo -s 127.0.0.1 -j DROP
    $IPT -A FORWARD ! -i lo -d 127.0.0.1 -j DROP
    $IPT -A INPUT ! -i lo -d 127.0.0.1 -j DROP
    $IPT -A INPUT -s 127.0.0.1 -j ACCEPT
    $IPT -A OUTPUT -d 127.0.0.1 -j ACCEPT
    # === Block spoofed packets ===
    $IPT -t mangle -A PREROUTING -s 127.0.0.0/8 ! -i lo -j DROP
    $IPT -t mangle -A PREROUTING -s 0.0.0.0/8 -j DROP
    $IPT -t mangle -A PREROUTING -s 169.254.0.0/16 -j DROP
    $IPT -t mangle -A PREROUTING -s 172.16.0.0/12 -j DROP
    $IPT -t mangle -A PREROUTING -s 192.0.2.0/24 -j DROP
    $IPT -t mangle -A PREROUTING -s 224.0.0.0/3 -j DROP
    $IPT -t mangle -A PREROUTING -s 240.0.0.0/5 -j DROP
    # === STOP SCAN ===
    $IPT -t mangle -A PREROUTING -p tcp --tcp-flags ALL FIN -m limit --limit 1/m --limit-burst 1  -j LOG --log-prefix "FIN-SCAN: " --log-level info
    $IPT -t mangle -A PREROUTING -p tcp --tcp-flags ALL FIN -j DROP
    $IPT -t mangle -A PREROUTING -p tcp --tcp-flags ALL SYN,FIN -m limit --limit 1/m --limit-burst 1 -j LOG --log-prefix "SYNFIN-SCAN: " --log-level info
    $IPT -t mangle -A PREROUTING -p tcp --tcp-flags ALL SYN,FIN -j DROP
    $IPT -t mangle -A PREROUTING -p tcp --tcp-flags ALL URG,PSH,FIN -m limit --limit 1/m --limit-burst 1 -j LOG --log-prefix "NMAP-XMAS-SCAN: " --log-level info
    $IPT -t mangle -A PREROUTING -p tcp --tcp-flags ALL URG,PSH,FIN -j DROP
    $IPT -t mangle -A PREROUTING -p tcp --tcp-flags ACK,FIN FIN -m limit --limit 1/m --limit-burst 1 -j LOG --log-prefix "FIN scan: " --log-level info
    $IPT -t mangle -A PREROUTING -p tcp --tcp-flags ACK,FIN FIN -j DROP
    $IPT -t mangle -A PREROUTING -p tcp --tcp-flags ACK,PSH PSH -m limit --limit 1/m --limit-burst 1 -j LOG --log-prefix "PSH scan: " --log-level info
    $IPT -t mangle -A PREROUTING -p tcp --tcp-flags ACK,PSH PSH -j DROP
    $IPT -t mangle -A PREROUTING -p tcp --tcp-flags ACK,URG URG -m limit --limit 1/m --limit-burst 1 -j LOG --log-prefix "URG scan: " --log-level info
    $IPT -t mangle -A PREROUTING -p tcp --tcp-flags ACK,URG URG -j DROP
    $IPT -t mangle -A PREROUTING -p tcp --tcp-flags ALL ALL -m limit --limit 1/m --limit-burst 1 -j LOG --log-prefix "XMAS scan: " --log-level info
    $IPT -t mangle -A PREROUTING -p tcp --tcp-flags ALL ALL -j DROP
    $IPT -t mangle -A PREROUTING -p tcp --tcp-flags ALL NONE -m limit --limit 1/m --limit-burst 1 -j LOG --log-prefix "NULL scan: " --log-level info
    $IPT -t mangle -A PREROUTING -p tcp --tcp-flags ALL NONE -j DROP
    $IPT -t mangle -A PREROUTING -p tcp --tcp-flags ALL URG,PSH,SYN,FIN -m limit --limit 1/m --limit-burst 1 -j LOG --log-prefix "NMAP-ID: " --log-level info
    $IPT -t mangle -A PREROUTING -p tcp --tcp-flags ALL URG,PSH,SYN,FIN -j DROP
    $IPT -t mangle -A PREROUTING -p tcp --tcp-flags ALL SYN,RST,ACK,FIN,URG -j DROP
    $IPT -t mangle -A PREROUTING -p tcp --tcp-flags FIN,SYN,RST,PSH,ACK,URG NONE -j DROP
    # === ICMP ENABLED ===
    $IPT -t mangle -A PREROUTING -p icmp -m limit --limit 1/s --limit-burst 1 -j ACCEPT
    $IPT -t mangle -A PREROUTING -p icmp -m limit --limit 1/s --limit-burst 100 -j LOG --log-prefix "ICMP FLOOD: " --log-level info
    $IPT -t mangle -A PREROUTING -p icmp -m recent --name icmp --update --seconds 60 --hitcount 75 -j DROP
    $IPT -A OUTPUT -p icmp -j ACCEPT
    # === SSH ENABLED ===
    $IPT -A INPUT -p tcp -m state --state NEW --dport 22 -m recent --name ssh --update --seconds 25 --hitcount 3 -j DROP
    $IPT -A INPUT -p tcp -m state --state NEW --dport 22 -j LOG --log-prefix "SSH-WARNING: " --log-level warning
    # === SSH ALLOW ===
    $IPT -A INPUT -p tcp -m state --state NEW --dport 22 {% if nf_int is undefined or nf_int is sameas true %}-s 10.0.0.0/8 {% endif %}-m recent --name ssh --set -j ACCEPT
    # === Limit connections per source IP ===
    $IPT -A INPUT -p tcp -m connlimit --connlimit-above 111 -j REJECT --reject-with tcp-reset
    # === Limit RST packets ===
    $IPT -A INPUT -p tcp --tcp-flags RST RST -m limit --limit 2/s --limit-burst 2 -j ACCEPT
    $IPT -A INPUT -p tcp --tcp-flags RST RST -j DROP
    # === RATE LIMITING ===
    #$IPT -A INPUT -p tcp --syn -m limit --limit 180/s --limit-burst 10000 -j ACCEPT
    #$IPT -A INPUT -p tcp --syn -j DROP
    #$IPT -A INPUT -p udp -m state --state NEW -m limit --limit 24/s --limit-burst 10000 -j ACCEPT
    #$IPT -A INPUT -p udp -m state --state NEW -j DROP
    # === DNS SERVER ===
    $IPT -A PREROUTING -t raw -j NOTRACK -m udp -p udp --dport 53
    $IPT -A PREROUTING -t raw -j NOTRACK -m tcp -p tcp --dport 53
    $IPT -A INPUT -p udp --dport 53 -j ACCEPT
    $IPT -A INPUT -p tcp --dport 53 -j ACCEPT
    # === FTP SERVER ===
    modprobe ip_conntrack_ftp
    $IPT -A INPUT -p tcp -m multiport --dports 20,21 -j ACCEPT
    # === WEB CONTROL ===
    $IPT -A INPUT -p tcp --dport 3389 {% if nf_int is undefined or nf_int is sameas true %}-s 10.0.0.0/8 {% endif %}-j ACCEPT
    $IPT -A INPUT -p tcp --dport 9090 {% if nf_int is undefined or nf_int is sameas true %}-s 10.0.0.0/8 {% endif %}-j ACCEPT
    $IPT -A INPUT -p tcp --dport 10000 {% if nf_int is undefined or nf_int is sameas true %}-s 10.0.0.0/8 {% endif %}-j ACCEPT
    # === KATELLO AGENT ===
    $IPT -A INPUT -p udp -m multiport --dports 67,69 -j ACCEPT
    $IPT -A INPUT -p tcp -m multiport --dports 5000,5647,8000,8140,8443,9090 -j ACCEPT
    # === ITM AGENT ===
    $IPT -A INPUT -p udp --dport 1918 -j ACCEPT
    $IPT -A INPUT -p tcp -m multiport --dports 1918,1919,1920,3660,6014,10110,14206,15001 -j ACCEPT
    # === NETBACKUP AGENT ===
    $IPT -A INPUT -p tcp -m multiport --dports 443,1556,2821,10082,10102,13720,13724,13782 -j ACCEPT
    # === Cocpit ===
    $IPT -A INPUT -p tcp -m multiport --dport 9090 -j ACCEPT
    ```

??? example "Правила для nftables в RHEL/CentOS 8"
    ``` cfg
    # More documentations: https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_and_managing_networking/getting-started-with-nftables_configuring-and-managing-networking
    # Flush the rule set
    flush ruleset
    # Create a table RAW (This table’s purpose is mainly to exclude certain packets from connection tracking using the NOTRACK target.)
    table ip raw {
            chain prerouting {
                    type filter hook prerouting priority -300; policy accept;
                    udp dport { domain, bootps, tftp, ntp, nfs} notrack
                    tcp dport { domain, bootps, tftp, ntp, nfs} notrack
            }
    }
    # Create a table MANGLE (The mangle table is used to modify or mark packets and their header information.)
    table ip mangle {
            chain prerouting {
                    type filter hook prerouting priority -150; policy accept;
                    iifname != "lo" ip saddr 127.0.0.0/8 counter drop comment "These rules assume that your loopback interface"
                    ip saddr { 0.0.0.0/8,169.254.0.0/16,172.16.0.0/12,192.0.2.0/24,224.0.0.0/3,240.0.0.0/5} counter drop comment "Block Packets From Private Subnets (Spoofing)"
                    ip frag-off & 0x1fff != 0 counter drop
                    tcp flags & (fin|syn|rst|ack) != syn ct state new counter drop
                    tcp flags & (fin|syn|rst|psh|ack|urg) == fin limit rate 1/minute burst 1 packets counter log prefix "FIN-SCAN: " level warn drop
                    tcp flags & (fin|syn|rst|psh|ack|urg) == (fin|syn) limit rate 1/minute burst 1 packets counter log prefix "SYNFIN-SCAN: " level warn drop
                    tcp flags & (fin|syn|rst|psh|ack|urg) == (fin|psh|urg) limit rate 1/minute burst 1 packets counter log prefix "NMAP-XMAS-SCAN: " level warn drop
                    tcp flags & (fin|ack) == fin limit rate 1/minute burst 1 packets counter log prefix "FIN scan: " level warn drop
                    tcp flags & (psh|ack) == psh limit rate 1/minute burst 1 packets counter log prefix "PSH scan: " level warn drop
                    tcp flags & (fin|syn|rst|psh|ack|urg) == (fin|syn|rst|psh|ack|urg) limit rate 1/minute burst 1 packets counter log prefix "XMAS scan: " level warn drop
                    tcp flags & (fin|syn|rst|psh|ack|urg) == 0x0 limit rate 1/minute burst 1 packets counter log prefix "NULL scan: " level warn drop
                    tcp flags & (fin|syn|rst|psh|ack|urg) == (fin|syn|psh|urg) limit rate 1/minute burst 1 packets counter log prefix "NMAP-ID: " level warn drop
                    ip protocol icmp limit rate 1/second burst 1 packets counter accept
                    ip protocol icmp limit rate 1/second burst 100 packets counter log prefix "ICMP FLOOD: " level warn drop
                    ct state new tcp option maxseg size != 536-65535 counter drop comment "Block Uncommon MSS Values. SACK Panic: CVE-2019-11477, CVE-2019-11478, CVE-2019-11479"
            }
    }
    # Create a table NAT (This table is used for Network Address Translation (NAT). If a packet creates a new connection, the nat table gets checked for rules.)
    table ip nat {
            chain prerouting {
                    type nat hook prerouting priority -100; policy accept;
            }
            chain postrouting {
                    type nat hook postrouting priority -100; policy accept;
            }
    }
    # Create a table FILTER (The filter table is the default and most commonly used table that rules.)
    table inet filter {
            set blackhole {
                    type ipv4_addr
                    flags timeout
                    timeout 1d
            }
            set custom_accept {
                    type inet_service
                    flags interval
    
            chain prerouting {
                    type filter hook prerouting priority 0; policy accept;
                    ip saddr @blackhole drop
            }
            
            chain input {
                    type filter hook input priority 0; policy drop;
                    iifname != "lo" ip saddr 127.0.0.1 counter drop
                    iifname != "lo" ip daddr 127.0.0.1 counter drop
                    iifname lo accept comment "Accept any localhost traffic"
                    ct state invalid counter drop comment "Drop invalid connections"
                    ct state related,established counter accept comment "Accept traffic connections"
                    ip protocol icmp icmp type { destination-unreachable, router-solicitation, router-advertisement, time-exceeded, parameter-problem } accept comment "Accept ICMP"
                    ip protocol igmp accept comment "Accept IGMP"
                    meta l4proto { tcp, udp } @th,16,16 53 counter accept comment "Accept DNS service"
                    meta l4proto { tcp, udp } @th,16,16 69 counter accept comment "Accept TFTP service"
                    meta l4proto udp @th,15,16 123 counter accept comment "Accept NTP service"
                    meta l4proto { tcp, udp } @th,16,32 2049 counter accept comment "Accept NFS service"
                    ip protocol udp udp dport 67 counter accept comment "Accept DHCP service"
                    ip saddr 10.0.0.0/8 tcp dport ssh ct state new accept comment "Accept SSHD on port 22"
                    tcp dport @custom_accept counter accept comment "Accept for custom reles"
                    ip saddr 10.0.0.0/8 tcp dport { 3389,9090,10000} accept comment "Accept administrators WebUI"
                    ip saddr 10.0.0.0/8 tcp dport { 5000,5647,8000,8140,8443,9090} accept comment "Accept Katello Agent"
                    meta l4proto { tcp, udp } @th,16,16 1918 accept comment "Accept ITM service"
                    tcp dport { 1919,1920,3660,6014,10110,14206,15001} accept comment "Accept ITM Agent"
                    tcp dport { 443,1556,2821,5432,9000,9001,10082,10102,13720,13724,13782} accept comment "Accept NETBACKUP Agent"
    
            chain forward {
                    type filter hook forward priority 0; policy drop;
                    oifname != "lo" ip saddr 127.0.0.1 counter drop
                    oifname != "lo" ip daddr 127.0.0.1 counter drop
                    oifname lo accept comment "Accept any localhost traffic"
            }
            chain output {
                    type filter hook output priority 0; policy accept;
            }
    }
    ```

## Дополнительные материалы

- [iptables](https://ru.wikibooks.org/wiki/Iptables)
- [nftables](https://wiki.archlinux.org/index.php/Nftables_(%D0%A0%D1%83%D1%81%D1%81%D0%BA%D0%B8%D0%B9))