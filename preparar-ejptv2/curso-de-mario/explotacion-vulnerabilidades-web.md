---
icon: '4'
---

# Explotación vulnerabilidades Web

## Qué es el Fuzzing Web – Uso de Gobuster

```bash
dirb http://<IP máquina víctima>
```

```bash
gobuster dir -u http://IP/ -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x txt,py,php,sh
```

## Enumeración de Subdominios con Wfuzz

