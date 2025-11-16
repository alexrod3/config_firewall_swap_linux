
✅ Requisitos: Criar SWAP (obrigatório para FreePBX)

O Debian 12 em VPSs mais baratas não vem com swap → FreePBX usa muita RAM ao instalar.

fallocate -l 4G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
echo "/swapfile none swap sw 0 0" >> /etc/fstab

# 2 Habilitando portas no Nftables

sudo nft -f /etc/nftables.conf
sudo systemctl enable nftables
sudo systemctl restart nftables
sudo systemctl status nftables


#!/usr/sbin/nft -f

flush ruleset

table inet filter {

    chain input {
        type filter hook input priority 0;

        # Aceitar loopback
        iif lo accept

        # ICMP / IPV6 ICMP
        ip protocol icmp accept
        ip6 nexthdr icmpv6 accept

        # Conexões estabelecidas
        ct state established,related accept

        # SSH porta 2222
        tcp dport 2222 accept

        # SIP PJSIP / CHAN_SIP
        udp dport 5060 accept
        tcp dport 5060 accept
        tcp dport 5061 accept

        # RTP (áudio FreePBX)
        udp dport 10000-20000 accept

        # HTTP/HTTPS (FreePBX + iRedMail)
        tcp dport 80 accept
        tcp dport 443 accept

        # Email (iRedMail)
        tcp dport { 25, 465, 587, 110, 995, 143, 993 } accept

        # SNMP
        udp dport 161 accept

        # Zabbix
        tcp dport 10050 accept       # Zabbix Agent
        tcp dport 10051 accept       # Zabbix Server

        # Bloqueia todo o resto
        reject
    }

    chain forward {
        type filter hook forward priority 0;
        reject
    }

    chain output {
        type filter hook output priority 0;
        accept
    }
}
