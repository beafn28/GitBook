# Attacking Web Applications with FFUZ

{% file src="../.gitbook/assets/Attacking_Web_Applications_With_Ffuf_Module_Cheat_Sheet.pdf" %}

## Basic Fuzzing

### Directory Fuzzing

```
ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ
ffuf -w <SNIP> -u http://SERVER_IP:PORT/FUZZ
ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ
```

#### Preguntas

**In addition to the directory we found above, there is another directory that can be found. What is it?**

```
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-small.txt -u http://94.237.57.115:57907/FUZZ -ac
```

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Page Fuzzing

#### Extension Fuzzing

```
ffuf -w /opt/useful/seclists/Discovery/Web-Content/web-extensions.txt:FUZZ <SNIP>
ffuf -w /opt/useful/seclists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://SERVER_IP:PORT/blog/indexFUZZ
```

#### Page Fuzzing

```
ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/blog/FUZZ.php
```

#### Preguntas

**Try to use what you learned in this section to fuzz the '/blog' directory and find all pages. One of them should contain a flag. What is the flag?**

Vemos las extensiones de los archivos.

```
 ffuf -w /usr/share/seclists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://94.237.57.115:57907/blog/indexFUZZ
```

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (2) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vemos con el nombre de los archivos.

```
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://94.237.57.115:57907/blog/FUZZ.php -ac -ic
```

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (2) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (2) (1).png" alt=""><figcaption></figcaption></figure>

## Recursive Fuzzing

```
ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ -recursion -recursion-depth 1 -e .php -v
```

#### Preguntas

**Try to repeat what you learned so far to find more files/directories. One of them should give you a flag. What is the content of the flag?**

```
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ \
     -u http://94.237.57.115:57907/FUZZ \
     -recursion -recursion-depth 1 \
     -e .php \
     -ic -ac -v
```

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (2).png" alt=""><figcaption></figcaption></figure>

## Domain Fuzzing

### Sub-domain Fuzzing

```
ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u https://FUZZ.inlanefreight.com/
ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://FUZZ.academy.htb/
```

#### Preguntas

**Try running a sub-domain fuzzing test on 'inlanefreight.com' to find a customer sub-domain portal. What is the full domain of it?**

```
 ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ \
     -u http://inlanefreight.com/ \
     -H "Host: FUZZ.inlanefreight.com" -ac -ic -r | grep -i customer
```

customer.inlanefreight.com

### Vhost Fuzzing

```
ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb'
```

### Filtering Results

```
ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb' -fs 900
```

#### Preguntas

**Try running a VHost fuzzing scan on 'academy.htb', and see what other VHosts you get. What other VHosts did you get?**

```
 ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ \
     -u http://94.237.57.115:57907/ \
     -H "Host: FUZZ.academy.htb" \
     -fs 986
```

<figure><img src="../.gitbook/assets/image (6) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

test.academy.htb

## **Parameter Fuzzing**

### Parameter Fuzzing - GET

```
ffuf -w /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php?FUZZ=key -fs xxx
```

#### Preguntas

**Using what you learned in this section, run a parameter fuzzing scan on this page. What is the parameter accepted by this webpage?**

```
 ffuf -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ      -u http://94.237.57.115:35006/admin/admin.php?FUZZ=random      -H "Host: admin.academy.htb" -fs 798
```

<figure><img src="../.gitbook/assets/image (7) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Parameter Fuzzing - POST

```
ffuf -w /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'FUZZ=key' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx
curl http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'id=key' -H 'Content-Type: application/x-www-form-urlencoded'
```

## Value Fuzzing

```
for i in $(seq 1 1000); do echo $i >> ids.txt; done
```

```
ffuf -w ids.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'id=FUZZ' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx
```

#### Preguntas

**Try to create the 'ids.txt' wordlist, identify the accepted value with a fuzzing scan, and then use it in a 'POST' request with 'curl' to collect the flag. What is the content of the flag?**

```
for i in $(seq 1 1000); do echo $i >> ids.txt; done
ffuf -w ids.txt:FUZZ      -u http://94.237.57.115:35006/admin/admin.php      -X POST -d 'id=FUZZ'      -H "Host: admin.academy.htb"      -H 'Content-Type: application/x-www-form-urlencoded'      -fs 768
```

<figure><img src="../.gitbook/assets/image (8) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vemos el contenido.

```
curl -s -X POST      -H "Host: admin.academy.htb"      -d "id=73"      http://94.237.57.115:35006/admin/admin.php
```

<figure><img src="../.gitbook/assets/image (9) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Skills Assessment

#### Preguntas

**Run a sub-domain/vhost fuzzing scan on '\*.academy.htb' for the IP shown above. What are all the sub-domains you can identify? (Only write the sub-domain name)**

```
 ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ \
     -u http://94.237.61.242:35855/ \
     -H "Host: FUZZ.academy.htb" \
     -mc 200,301,302,403 \
     -ac -ic -r
```

<figure><img src="../.gitbook/assets/image (10) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Before you run your page fuzzing scan, you should first run an extension fuzzing scan. What are the different extensions accepted by the domains?**

```
echo "94.237.61.242 archive.academy.htb" | sudo tee -a /etc/hosts
echo "94.237.61.242 test.academy.htb"    | sudo tee -a /etc/hosts
echo "94.237.61.242 faculty.academy.htb" | sudo tee -a /etc/hosts
echo "94.237.61.242 admin.academy.htb" | sudo tee -a /etc/hosts
```

Escaneamos las extensiones de los 3 subdominios.

```
WORDLIST="/usr/share/seclists/Discovery/Web-Content/web-extensions.txt"
ffuf -w "$WORDLIST":FUZZ -u http://archive.academy.htb:35855/indexFUZZ -ic -ac -mc 200,403
ffuf -w "$WORDLIST":FUZZ -u http://test.academy.htb:35855/indexFUZZ    -ic -ac -mc 200,403
ffuf -w "$WORDLIST":FUZZ -u http://faculty.academy.htb:35855/indexFUZZ -ic -ac -mc 200,403
```

<figure><img src="../.gitbook/assets/image (11) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**One of the pages you will identify should say 'You don't have access!'. What is the full page URL?**

```
EXTS=.php,.phps,.php7
WL=/usr/share/seclists/Discovery/Web-Content/directory-list-2.3-small.txt

for sub in archive test faculty; do
  echo "== $sub ==";
  ffuf -w "$WL":FUZZ \
       -u http://$sub.academy.htb:35855/FUZZ \
       -e $EXTS -ic -ac -r -mc 200,403 \
       -recursion -recursion-depth 1 \
       -mr "You don't have access!"
done
```

<figure><img src="../.gitbook/assets/image (16) (2).png" alt=""><figcaption></figcaption></figure>

Vemos todo hasta encontrar ese mensaje.

```
curl http://faculty.academy.htb:35855/courses/linux-security.php7
```

<figure><img src="../.gitbook/assets/image (12) (1) (2).png" alt=""><figcaption></figcaption></figure>

**In the page from the previous question, you should be able to find multiple parameters that are accepted by the page. What are they?**

```
ffuf -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://94.237.61.242:35855/courses/linux-security.php7 -X POST -H "Host: faculty.academy.htb" -d 'FUZZ=key' -H 'Content-Type: application/x-www-form-urlencoded' -fs 774
```

<figure><img src="../.gitbook/assets/image (13) (1).png" alt=""><figcaption></figcaption></figure>

**Try fuzzing the parameters you identified for working values. One of them should return a flag. What is the content of the flag?**

Escaneamos para ver los usuarios.

```
ffuf -w /usr/share/seclists/Usernames/Names/names.txt:FUZZ -u http://94.237.61.242:35855/courses/linux-security.php7 -X POST -H "Host: faculty.academy.htb" -d 'username=FUZZ' -H 'Content-Type: application/x-www-form-urlencoded' -fs 781
```

<figure><img src="../.gitbook/assets/image (14) (1).png" alt=""><figcaption></figcaption></figure>

Vemos el contenido con ese usuario.

```
curl http://94.237.61.242:35855/courses/linux-security.php7 -X POST -H "Host: faculty.academy.htb" -d 'username=harry' -H 'Content-Type: application/x-www-form-urlencoded'
```

<figure><img src="../.gitbook/assets/image (15) (1).png" alt=""><figcaption></figcaption></figure>
