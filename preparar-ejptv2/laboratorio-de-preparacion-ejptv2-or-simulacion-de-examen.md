# Laboratorio de preparación eJPTv2 | Simulación de examen

{% embed url="https://youtu.be/v20IsEd5nUU?si=5v8wrsLm_pxpzXXj" %}

## Enumeración de red - Descubrimientos de hosts y escaneo de puertos

```bash
ip a
nmap -sn 10.0.2.0/24 #manda paquetes ICMP a ese segmento de red
sudo nmap -sS --min-rate 5000 -p- --open 10.0.2.43,45,46,53,55 -oN tcp_scan.txt

```
