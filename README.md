# 🌐 Ping Sweep Cheatsheet

> A collection of the best methods to identify live hosts on a network.
> **by RedNeutron**
>
> ⚠️ *For educational purpose only. Only use on networks you have explicit permission to test.*

---

## 🔴 Fastest & Best for Bug Bounty

### fping
Designed specifically for ping sweep — much faster than regular ping.
```bash
fping -a -g 192.168.1.0/24 2>/dev/null
```

### masscan
The fastest scanner in existence — millions of IPs per second.
```bash
masscan 192.168.1.0/24 -p0 --ping
```

---

## 🐧 Linux Terminal

### ping (classic)
```bash
for i in {1..255}; do (ping -c 1 172.17.1.$i | grep "bytes from" &); done
```

### fping (recommended)
```bash
fping -a -g 192.168.1.0/24 2>/dev/null
```

### arp-scan (most reliable on local network)
More reliable than ICMP — uses ARP protocol, harder to block.
```bash
# Local network
arp-scan --localnet

# Specific range
arp-scan 192.168.1.0/24
```

---

## 🪟 Windows Command Line
```batch
for /L %i in (1,1,255) do @ping -n 1 -w 200 172.17.1.%i > nul && echo 172.17.1.%i is up.
```

---

## 💙 PowerShell
```powershell
1..255 | ForEach-Object {
    $ip = "192.168.1.$_"
    if (Test-Connection $ip -Count 1 -Quiet -TimeoutSeconds 1) {
        "$ip is UP"
    }
}
```

---

## 🗺️ Nmap

### Basic ping sweep
```bash
nmap -sn 172.17.1.0/24
```

### Faster with aggressive timing
```bash
nmap -sn -T4 192.168.1.0/24
```

### Output live hosts only
```bash
nmap -sn -T4 192.168.1.0/24 -oG - | grep "Up" | awk '{print $2}'
```

---

## 🔍 Netdiscover
Cannot be detected by the target — slower but stealthier.
```bash
netdiscover -p -r 172.17.1.0/16
```

---

## 🐍 Python (no tools required)
Useful when no external tools are available.
```bash
python3 -c "
import subprocess, ipaddress
[print(ip,'is UP') for ip in ipaddress.IPv4Network('192.168.1.0/24').hosts()
 if subprocess.run(['ping','-c1','-W1',str(ip)],capture_output=True).returncode==0]
"
```

---

## 📊 When to Use What

| Situation | Best Tool |
|---|---|
| Local network, need speed | `arp-scan` |
| External network, need speed | `fping` or `nmap -T4` |
| Stealth, avoid detection | `netdiscover -p` |
| Massive range scan | `masscan` |
| Windows, no extra tools | PowerShell |
| No tools available at all | Python oneliner |

---

> *Bravo! Feel free to develop these things — but remember, educational purpose only.*
