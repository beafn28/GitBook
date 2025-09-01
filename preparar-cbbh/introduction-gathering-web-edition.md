# Introduction Gathering - Web Edition

{% file src="../.gitbook/assets/Information_Gathering_Web_Edition_Module_Cheat_Sheet.pdf" %}

Para ver más información pulsa en lo siguiente. Me limitaré a comandos y a la resolución de las preguntas en está página.

{% content-ref url="introduction-gathering-web-edition.md" %}
[introduction-gathering-web-edition.md](introduction-gathering-web-edition.md)
{% endcontent-ref %}

## WHOIS

### Utilizing WHOIS

```
whois facebook.com
```

#### Preguntas

**Perform a WHOIS lookup against the paypal.com domain. What is the registrar Internet Assigned Numbers Authority (IANA) ID number?**

```
whois paypal.com
```

<figure><img src="../.gitbook/assets/image (15) (1).png" alt=""><figcaption></figcaption></figure>

**What is the admin email contact for the tesla.com domain (also in-scope for the Tesla bug bounty program)?**

```
whois tesla.com
```

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (2).png" alt=""><figcaption></figcaption></figure>

## DNS & Subdominions

### Digging DNS

#### Preguntas

**Which IP address maps to inlanefreight.com?**

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Which domain is returned when querying the PTR record for 134.209.24.248?**

Viene en la anterior captura

**What is the full domain returned when you query the mail records for facebook.com?**

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Subdomain Bruteforcing

```
dnsenum --enum inlanefreight.com -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -r
dnsenum --enum inlanefreight.com -f  /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt
```

#### Preguntas

**Using the known subdomains for inlanefreight.com (www, ns1, ns2, ns3, blog, support, customer), find any missing subdomains by brute-forcing possible domain names. Provide your answer with the complete subdomain, e.g., www.inlanefreight.com.**

```
ffuf -u http://inlanefreight.htb:35684 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -mc 200,403 -t 60 -H "Host: FUZZ.inlanefreight.htb" -ac
```

my.inlanefreight.com

### DNS Zone Transfers

```
dig axfr @nsztm1.digi.ninja zonetransfer.me
```

#### Preguntas

**After performing a zone transfer for the domain inlanefreight.htb on the target system, how many DNS records are retrieved from the target system's name server? Provide your answer as an integer, e.g, 123.**

```
dig @10.129.42.195 inlanefreight.htb AXFR
dig @10.129.42.195 inlanefreight.htb AXFR | grep -v '^;' | grep -v '^$' | wc -l
```

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

**Within the zone record transferred above, find the ip address for ftp.admin.inlanefreight.htb. Respond only with the IP address, eg 127.0.0.1**

```
 dig @10.129.42.195 inlanefreight.htb AXFR | grep 'ftp.admin.inlanefreight.htb' | awk '{print $5}'
```

**Within the same zone record, identify the largest IP address allocated within the 10.10.200 IP range. Respond with the full IP address, eg 10.10.200.1**

```
 dig @10.129.42.195 inlanefreight.htb AXFR \
  | grep '10\.10\.200\.' \
  | awk '{print $5}' \
  | sort -t '.' -k4,4n \
  | tail -n 1
```

### Virtual Hosts

```
gobuster vhost -u http://<target_IP_address> -w <wordlist_file> --append-domain
gobuster vhost -u http://inlanefreight.htb:81 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain
```

#### Preguntas

```
ffuf -u http://94.237.57.211:46627 \
     -H "Host: FUZZ.inlanefreight.htb" \
     -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt \
     -fs 116
```

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Brute-force vhosts on the target system. What is the full subdomain that is prefixed with "web"? Answer using the full domain, e.g. "x.inlanefreight.htb"**

web17611.inlanefreight.htb

**Brute-force vhosts on the target system. What is the full subdomain that is prefixed with "vm"? Answer using the full domain, e.g. "x.inlanefreight.htb"**

vm5.inlanefreight.htb

**Brute-force vhosts on the target system. What is the full subdomain that is prefixed with "br"? Answer using the full domain, e.g. "x.inlanefreight.htb"**

browse.inlanefreight.htb

**Brute-force vhosts on the target system. What is the full subdomain that is prefixed with "a"? Answer using the full domain, e.g. "x.inlanefreight.htb"**

admin.inlanefreight.htb

**Brute-force vhosts on the target system. What is the full subdomain that is prefixed with "su"? Answer using the full domain, e.g. "x.inlanefreight.htb"**

support.inlanefreight.htb

## Fingerprinting

```
curl -I inlanefreight.com
wafw00f inlanefreight.com
nikto -h inlanefreight.com -Tuning b
```

#### Preguntas

**Determine the Apache version running on app.inlanefreight.local on the target system. (Format: 0.0.0)**

```
curl -s -I -H "Host: app.inlanefreight.local" http://10.129.193.142
```

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Which CMS is used on app.inlanefreight.local on the target system? Respond with the name only, e.g., WordPress.**

```
whatweb -H "Host: app.inlanefreight.local" http://10.129.193.142
```

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**On which operating system is the dev.inlanefreight.local webserver running in the target system? Respond with the name only, e.g., Debian.**

```
curl -s -I -H "Host: dev.inlanefreight.local" http://10.129.193.142
```

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Crawling

#### Preguntas

**After spidering inlanefreight.com, identify the location where future reports will be stored. Respond with the full domain, e.g., files.inlanefreight.com.**

```
python3 ReconSpider.py http://dev.web1337.inlanefreight.htb:<port>
```

## Web Archives

#### Preguntas

**How many Pen Testing Labs did HackTheBox have on the 8th August 2018? Answer with an integer, eg 1234.+ 0**&#x20;

74

**How many members did HackTheBox have on the 10th June 2017? Answer with an integer, eg 1234.**

3054

**Going back to March 2002, what website did the facebook.com domain redirect to? Answer with the full domain, eg http://www.facebook.com/**

[http://site.aboutface.com/](http://site.aboutface.com/)

**According to the paypal.com website in October 1999, what could you use to "beam money to anyone"? Answer with the product name, eg My Device, remove the ™ from your answer.**

Palm 0rganizer

**Going back to November 1998 on google.com, what address hosted the non-alpha "Google Search Engine Prototype" of Google? Answer with the full address, eg http://google.com**

{% embed url="http://google.stanford.edu/" %}

**Going back to March 2000 on www.iana.org, when exacty was the site last updated? Answer with the date in the footer, eg 11-March-99**

17-December-99

**According to the wikipedia.com snapshot taken in March 2001, how many pages did they have over? Answer with the number they state without any commas, eg 2000 not 2,000**

3000

## Skills Assessment

#### Preguntas

Primero añadimos en el `/etc/hosts` el VHOST.

```
sudo sh -c "echo '94.237.57.115 inlanefreight.htb' >> /etc/hosts"
```

**What is the IANA ID of the registrar of the inlanefreight.com domain?**

```
whois inlanefreight.com
```

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**What http server software is powering the inlanefreight.htb site on the target system? Respond with the name of the software, not the version, e.g., Apache.**

```
curl -I http://inlanefreight.htb:30494
```

<figure><img src="../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

**What is the API key in the hidden admin directory that you have discovered on the target system?**

Escaneamos.

```
ffuf -u http://inlanefreight.htb:30494 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -mc 200,403 -t 60 -H "Host: FUZZ.inlanefreight.htb" -ac
```

<figure><img src="../.gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

Añadimos al fichero `/etc/hosts`.

```
sudo sh -c "echo '94.237.57.115 web1337.inlanefreight.htb' >> /etc/hosts"
```

Escaneamos directorios.

```
ffuf -u http://web1337.inlanefreight.htb:30494/FUZZ -w /usr/share/seclists/Discovery/Web-Content/common.txt -t 60
```

<figure><img src="../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>

Entramos en el **robots.txt**.

```
curl http://web1337.inlanefreight.htb:30494/robots.txt
```

<figure><img src="../.gitbook/assets/image (9) (1) (1).png" alt=""><figcaption></figcaption></figure>

```
curl http://web1337.inlanefreight.htb:30494/admin_h1dd3n -I
curl  http://web1337.inlanefreight.htb:30494/admin_h1dd3n/
```

<figure><img src="../.gitbook/assets/image (10) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vemos el contenido.

<figure><img src="../.gitbook/assets/image (11) (1) (1).png" alt=""><figcaption></figcaption></figure>

**After crawling the inlanefreight.htb domain on the target system, what is the email address you have found? Respond with the full email, e.g., mail@inlanefreight.htb.**

```
python3 ReconSpider.py http://inlanefreight.htb:30494
```

Escaneamos porque no funciono lo anterior.

```
ffuf -u http://web1337.inlanefreight.htb:30494 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -mc 200,403
 -t 60 -H "Host: FUZZ.web1337.inlanefreight.htb" -ac
```

<figure><img src="../.gitbook/assets/image (12) (1).png" alt=""><figcaption></figcaption></figure>

Lo añadimos al `/etc/hosts`.

```
sudo sh -c "echo '94.237.57.115 dev.web1337.inlanefreight.htb' >> /etc/hosts"
```

Volvemos a crawlear.

```
python3 ReconSpider.py http://dev.web1337.inlanefreight.htb:30494
```

Vemos los resultados.

<figure><img src="../.gitbook/assets/image (13) (1).png" alt=""><figcaption></figcaption></figure>

**What is the API key the inlanefreight.htb developers will be changing too?**

En el mismo archivo de resultados se encuentra esa información.

<figure><img src="../.gitbook/assets/image (14) (1).png" alt=""><figcaption></figcaption></figure>
