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

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

**What is the admin email contact for the tesla.com domain (also in-scope for the Tesla bug bounty program)?**

```
whois tesla.com
```

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

## DNS & Subdominions

### Digging DNS

#### Preguntas

**Which IP address maps to inlanefreight.com?**

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

**Which domain is returned when querying the PTR record for 134.209.24.248?**

Viene en la anterior captura

**What is the full domain returned when you query the mail records for facebook.com?**

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

### Subdomain Bruteforcing

```
dnsenum --enum inlanefreight.com -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -r
 dnsenum --enum inlanefreight.com -f  /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt
```

#### Preguntas

**Using the known subdomains for inlanefreight.com (www, ns1, ns2, ns3, blog, support, customer), find any missing subdomains by brute-forcing possible domain names. Provide your answer with the complete subdomain, e.g., www.inlanefreight.com.**

