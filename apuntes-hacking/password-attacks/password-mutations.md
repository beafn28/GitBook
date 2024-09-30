# Password Mutations

Muchas personas crean contraseñas siguiendo patrones simples en lugar de optar por la seguridad. Esto compromete las medidas de seguridad en los sistemas. Sin embargo, se pueden implementar **políticas de contraseñas** para determinar la composición de una contraseña, obligando al usuario a incluir letras mayúsculas, caracteres especiales y números, además de requerir una longitud mínima de ocho caracteres.

A pesar de estas políticas, los usuarios tienden a crear contraseñas débiles, como incluir el nombre de la empresa, el año o elementos relacionados con sus intereses o hobbies. Para contrarrestar esto, se pueden usar herramientas como **Hashcat** y **CeWL** para generar listas de contraseñas más complejas y personalizadas, basadas en patrones comunes y reglas.

### **Ejemplo de lista de contraseñas básicas**

```bash
sherlock28@htb[/htb]$ cat password.list
password
```

#### Uso de Hashcat para generar mutaciones de contraseñas

Hashcat permite crear listas de contraseñas mutadas según reglas específicas. A continuación, se presenta un ejemplo de archivo de reglas para generar variaciones de una contraseña básica, como agregar caracteres especiales, números o modificar mayúsculas.

### **Archivo de reglas de Hashcat (`custom.rule`)**

```bash
sherlock28@htb[/htb]$ cat custom.rule

:
c
so0
c so0
sa@
c sa@
c sa@ so0
$!
$! c
$! so0
$! sa@
$! c so0
$! c sa@
$! so0 sa@
$! c so0 sa@
```

### **Comando para generar lista de contraseñas mutadas**

```bash
sherlock28@htb[/htb]$ hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list
sherlock28@htb[/htb]$ cat mut_password.list
```

**Salida: Lista de contraseñas mutadas**

```bash
password
Password
passw0rd
Passw0rd
p@ssword
P@ssword
P@ssw0rd
password!
Password!
passw0rd!
p@ssword!
Passw0rd!
P@ssword!
p@ssw0rd!
P@ssw0rd!
```

### Uso de reglas predefinidas en Hashcat

Hashcat ofrece múltiples reglas predefinidas que se pueden utilizar para mutar contraseñas. Una de las reglas más populares es **best64.rule**. Estas reglas permiten aumentar la efectividad al adivinar contraseñas.

**Listado de reglas predefinidas en Hashcat**

```bash
sherlock28@htb[/htb]$ ls /usr/share/hashcat/rules/

best64.rule                  specific.rule
combinator.rule              T0XlC-insert_00-99_1950-2050_toprules_0_F.rule
d3ad0ne.rule                 T0XlC-insert_space_and_special_0_F.rule
dive.rule                    T0XlC-insert_top_100_passwords_1_G.rule
generated2.rule              T0XlC.rule
generated.rule               T0XlCv1.rule
hybrid                       toggles1.rule
Incisive-leetspeak.rule      toggles2.rule
InsidePro-HashManager.rule   toggles3.rule
InsidePro-PasswordsPro.rule  toggles4.rule
leetspeak.rule               toggles5.rule
oscommerce.rule              unix-ninja-leetspeak.rule
rockyou-30000.rule
```

### Generación de listas de palabras personalizadas con CeWL

**CeWL** es una herramienta que permite recolectar palabras de una página web y usarlas para generar listas de contraseñas personalizadas. Esto puede ser útil cuando se desea obtener palabras relacionadas con la empresa o el sitio web de la víctima.

**Comando para generar lista de palabras con CeWL**

```bash
sherlock28@htb[/htb]$ cewl https://www.inlanefreight.com -d 4 -m 6 --lowercase -w inlane.wordlist
sherlock28@htb[/htb]$ wc -l inlane.wordlist
```

**Salida: Número de palabras generadas**

```bash
326
```
