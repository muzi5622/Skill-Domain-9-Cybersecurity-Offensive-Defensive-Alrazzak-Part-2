# Lab 6: Port Scanning & Service Enumeration – Full Personal Cheat Sheet (What I Actually Use Every Day)

### What I Can Do Now (Real Skills I Gained)
- I discover every open port on any target (fast or full 65k)  
- I accurately fingerprint services + exact versions with Nmap  
- I manually banner-grab with nc, curl, telnet to verify Nmap results  
- I run one-click professional recon scripts that save everything  
- I generate clean security reports automatically  
- I instantly spot misconfigurations and exploitable services  

### My Workspace Setup (I Always Do This First)
```bash
mkdir -p ~/portscan_lab && cd ~/portscan_lab
sudo apt update && sudo apt install -y nmap netcat-openbsd curl telnet whois jq ncat
```

### Task 1 – See Exactly What’s Listening on My Box
```bash
ss -tuln                          # clean, no process names
sudo ss -tulnp                    # with process names ← my favorite
sudo netstat -tulnp               # old-school, still works everywhere
sudo lsof -i -P -n | grep LISTEN  # alternative view
```

### Task 2 – Nmap Commands I Actually Use Daily
```bash
nmap -F 127.0.0.1                              # quick top 1000 ports
nmap -p- --open 127.0.0.1                      # all 65535 ports, show only open
sudo nmap -sS -sV -sC -O 127.0.0.1              # my go-to aggressive scan
sudo nmap -A 127.0.0.1                         # literally everything
sudo nmap -sU --top-ports 100 127.0.0.1         # UDP quick scan
nmap --script vuln 127.0.0.1                   # find real vulnerabilities
nmap --script "http-enum,http-title,http-methods" 127.0.0.1   # web recon
nmap -p 22,80,443,8080 -sV 127.0.0.1           # specific ports + version
sudo nmap -oA fullscan 127.0.0.1               # save .nmap, .gnmap, .xml
```

### Task 3 – Manual Banner Grabbing (I Always Verify Nmap)
```bash
# Quick connectivity check
nc -zv 127.0.0.1 1-10000

# SSH banner
echo "QUIT" | nc 127.0.0.1 22

# HTTP headers + title
curl -s -I http://127.0.0.1:8080 | head -20
curl -s http://127.0.0.1:8080 | grep -i "<title>"

# Telnet banner
telnet 127.0.0.1 22 <<EOF
QUIT
EOF

# One-liner: banner grab every open port
for p in $(nmap -p- --open 127.0.0.1 -oG - | awk '/open/ {print $2}'); do
    echo "→ Port $p"
    timeout 3 bash -c "echo '' > /dev/tcp/127.0.0.1/$p" && echo "$p open" || echo "$p closed"
done
```

### Task 4 – My One-Click Professional Recon Script (I Run This Everywhere)
```bash
#!/bin/bash
# ~/portscan_lab/recon.sh
TARGET="${1:-127.0.0.1}"
DATE=$(date +%Y-%m-%d_%H%M)
REPORT="recon_$DATE"

clear
echo "Starting Professional Recon on $TARGET"
echo "Results → $REPORT.{nmap,gnmap,xml}"
sleep 2

sudo nmap -sS -sV -sC -O -p- --open \
    --script="vuln,http-enum,http-title,http-methods,ssh2-enum-algos" \
    -T4 -oA $REPORT $TARGET

echo
echo "Done! Quick open ports:"
grep "open" $REPORT.nmap | head -20
```
```bash
chmod +x recon.sh
./recon.sh 127.0.0.1
```

### Task 5 – My Automatic Security Report Generator
```bash
#!/bin/bash
# ~/portscan_lab/report.sh
TARGET="${1:-127.0.0.1}"
FILE="SECURITY_REPORT_$(date +%Y%m%d_%H%M).txt"

{
  echo "===================================================="
  echo "    LOCALHOST SECURITY ASSESSMENT REPORT"
  echo "    Generated: $(date)"
  echo "    Analyst: $(whoami) @ $(hostname)"
  echo "===================================================="
  echo
  echo "[1] OPEN PORTS & SERVICES"
  nmap -sV --open $TARGET | grep -E "open|Service Info"
  echo
  echo "[2] POTENTIAL VULNERABILITIES"
  nmap --script vuln $TARGET | grep -i "VULNERABLE\|exploit" || echo "   None detected"
  echo
  echo "[3] QUICK RECOMMENDATIONS"
  echo "   • Disable unnecessary services"
  echo "   • Update all software"
  echo "   • Use SSH keys only"
  echo "   • Enable firewall (ufw allow 22 && ufw enable)"
  echo
  echo "Report generated at: $FILE"
} > "$FILE"

echo "Report saved → $FILE"
cat "$FILE"
```
```bash
chmod +x report.sh
./report.sh
```

### Task 6 – Quick Fake Services (For Testing My Scans)
```bash
# Start dummy services in background
sudo python3 -m http.server 80 &
nc -l -p 2222 -k < /dev/null &
echo -e "Fake SSH login:\nadmin:password123" | nc -l -p 2223 -k &
echo "Services started on 80, 2222, 2223 — scan again!"
```

### My Personal Nmap/Netcat Cheat Card
```bash
# FAST SCANS
nmap -F <IP>
nmap -p- --open <IP>

# PRO SCANS
sudo nmap -sS -sV -sC -O <IP>
sudo nmap -A <IP>
nmap --script vuln <IP>

# SAVE RESULTS
nmap -oA name <IP>

# BANNER GRAB
nc -zv <IP> 1-1000
echo "QUIT" | nc <IP> 22
curl -s -I http://<IP>

# LISTENING ON MY BOX
sudo ss -tulnp
```

I now have the exact same port-scanning and enumeration workflow used by professional pentesters, bug bounty hunters, and blue team analysts.  
