### Конфигурация на alt-srv3
```bash
#!/usr/sbin/nft -f

flush ruleset

table inet filter {
    chain input {
        type filter hook input priority 0; policy drop;
        iif lo accept
        ct state { established, related } accept
        tcp dport 22 accept
        tcp dport { 80, 443 } ip saddr 192.168.100.0/24 accept
    }
    chain forward {
        type filter hook forward priority 0; policy drop;
    }
    chain output {
        type filter hook output priority 0; policy accept;
    }
}
