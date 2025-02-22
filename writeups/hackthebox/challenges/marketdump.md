---
description: '🔍 Dificultad: Fácil'
---

# MarketDump

Nos descargamos el archivo y lo ejecutamos en el Wireshark.

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-22 a las 17.56.21.png" alt=""><figcaption></figcaption></figure>

Aplicamos el filtro de `http`. Encontramos un paquete curioso.

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-22 a las 17.57.28.png" alt=""><figcaption></figcaption></figure>

Miramos a ver cadenas de caracteres a través de terminal.&#x20;

```bash
strings MarketDump.pcapng | less
```

Encontramos una que tiene una longitud más larga que las anteriores.

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-22 a las 18.00.03.png" alt=""><figcaption></figcaption></figure>

Lo decodificamos a través de esta página.

{% embed url="https://gchq.github.io/CyberChef/" %}

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-22 a las 18.01.32.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/Captura de pantalla 2025-02-22 a las 18.02.51.png" alt=""><figcaption></figcaption></figure>
