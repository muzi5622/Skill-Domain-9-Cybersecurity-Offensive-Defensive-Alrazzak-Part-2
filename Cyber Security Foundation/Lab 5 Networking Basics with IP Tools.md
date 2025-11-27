# My Lab 5: Networking Basics with IP Tools – What I Learned

### What I Can Do Now
- I instantly read any Linux box’s network config  
- I test connectivity like a real analyst (ping → gateway → 8.8.8.8 → DNS)  
- I trace attack paths with traceroute/mtr and spot weird hops  
- I dig DNS like a pro (subdomains, MX, TXT records)  
- I wrote a full automated recon script I use on every machine  
- I explain every line of output in reports

### My Daily One-Liners
```bash
ip a                  # all interfaces
ip -br a              # super compact view
hostname -I           # my IP only
ip route | grep default   # my gateway
ping -c 4 8.8.8.8     # internet alive?
traceroute -n 8.8.8.8 # fast path
dig +short google.com # just the IP
ss -tuln              # open ports
curl ifconfig.me      # my public IP
```

### My Ultimate Recon Script (I run this everywhere)
```bash
#!/bin/bash
clear
echo "My Network Recon – $(date)"
echo "Host: $(hostname) | IP: $(hostname -I)"
ip -br a | grep -v lo
echo "Gateway + DNS:"
ip route | grep default
cat /etc/resolv.conf
echo "Connectivity check:"
for t in 127.0.0.1 $(ip r | grep default | awk '{print $3}') 8.8.8.8 google.com; do
  ping -c 2 -W 1 $t &>/dev/null && echo "$t → OK" || echo "$t → DOWN"
done
echo "Open ports:"; ss -tuln
echo "Active connections:"; ss -tunap | grep ESTAB || echo "None"
```
→ Saved as `~/net_recon.sh` and I run `./net_recon.sh` on every new box.

### DNS Tricks I Use All the Time
```bash
dig +short target.com
dig MX target.com
dig TXT target.com
dig +trace target.com      # full resolution path
host 8.8.8.8               # reverse lookup
```

### My Troubleshooting Flow (I follow this every time)
1. `ping 127.0.0.1` → OS OK?  
2. `ping gateway` → LAN OK?  
3. `ping 8.8.8.8` → Internet OK?  
4. `ping google.com` → DNS OK?

### Quick Port & Connection Checks I Use
```bash
ss -tuln          # listening ports
ss -tunap         # who is connected + process
lsof -i -P -n      # process → port
nc -zv 10.0.0.5 1-1000   # fast local scan
```
