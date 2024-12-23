# Assessment Methodologies: Footprinting & Scanning

## Host Discovery

```bash
ping -c 5 demo.ine.local
nmap demo.ine.local
nmap -Pn demo.ine.local
nmap -Pn -p 443 demo.ine.local
nmap -Pn -sV -p 80 demo.ine.local
```

## Port Scanning

```bash
ping -c 4 demo.ine.local
nmap demo.ine.local
nmap demo.ine.local -p-
nmap demo.ine.local -p 6421,41288,55413 -sV
```

## Evasion, Scan Performance & Output

```bash
ping -c 5 demo.ine.local
nmap demo.ine.local
nmap -p445 --script smb-protocols demo.ine.local
nmap -p445 --script smb-security-mode demo.ine.local
nmap -p445 --script smb-enum-sessions demo.ine.local
nmap -p445 --script smb-enum-sessions --script-args smbusername=administrator,smbpassword=smbserver_771 demo.ine.local
nmap -p445 --script smb-enum-shares demo.ine.local
nmap -p445 --script smb-enum-shares --script-args smbusername=administrator,smbpassword=smbserver_771 demo.ine.local
nmap -p445 --script smb-enum-users --script-args smbusername=administrator,smbpassword=smbserver_771 demo.ine.local
nmap -p445 --script smb-server-stats --script-args smbusername=administrator,smbpassword=smbserver_771 demo.ine.local
nmap -p445 --script smb-enum-domains --script-args smbusername=administrator,smbpassword=smbserver_771 demo.ine.local
nmap -p445 --script smb-enum-groups --script-args smbusername=administrator,smbpassword=smbserver_771 demo.ine.local
nmap -p445 --script smb-enum-services --script-args smbusername=administrator,smbpassword=smbserver_771 demo.ine.local
nmap -p445 --script smb-enum-shares,smb-ls --script-args smbusername=administrator,smbpassword=smbserver_771 demo.ine.local
```
