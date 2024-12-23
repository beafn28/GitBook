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

```
