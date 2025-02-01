---
layout: default
---
# Indice
WIP :)
# Escáner de puertos sencillo
[Link al repositorio de github.](https://github.com/Minispeedyt/simplescanner/tree/main)

1/31/2025
Me propuse crear un escáner de puertos sencillo usando python, para ello opté por usar scapy. La primera versión/prototipo funciona bien pero hay que modificar el código si quieres cambiar los puertos o la IP a escanear. Aquí está la primera versión del escáner:
```
from scapy.all import *
res,unans = sr(IP(dst="172.18.0.2")/TCP(flags="S", dport=(1,100)), timeout=1 )
for s,r in res:
    if r[TCP].flags == 0x12:
        print("Port %d is open" % s[TCP].dport)
    elif r[TCP].flags == 0x14:
        print("Port %d is closed" % s[TCP].dport)
    else:
        print("Port %d is closed or filtered.")
```
Vamos a analizarlo paso a paso para ver lo que hice y cómo funciona:
## Enviar paquetes
Primero, importamos scapy y usamos `sr(IP(dst=«172.18.0.2»)/TCP(flags=«S», dport=(1,100)), timeout=1 )` para enviar los paquetes, donde `IP(dst="172.18.0. 2«)` le dice a scapy a qué IP enviar los paquetes, `TCP(flags=»S"` especifica que queremos enviar paquetes SYN (esos son los paquetes usados en el primer paso de un handshake TCP), `dport=(1,100)` significa que queremos escanear todos los puertos entre 1 y 100 y por último `timeout=1` le dice a scapy cuánto tiempo debe esperar por una respuesta. Almacenamos los paquetes que han recibido respuesta en 'res' (pares enviados y recibidos) y los paquetes que no han recibido respuesta en 'unans'.
## Encontrando puertos abiertos o cerrados
Creamos un bucle for donde miramos cada paquete enviado y recibido (`for s,r in res:`), entonces por cada paquete recibido comprobamos si la flag de respuesta del paquete es SYN-ACK (0x12) o RST-ACK (0x14). Esto funciona porque 0x12 convertido a binario y luego mapeado a las posiciones del flag de la respuesta se vería así: 
*    NS = 0
*    CWR = 0
*    ECE = 0
*    URG = 0
*    ACK = 1
*    PSH = 0
*    RST = 0
*    SYN = 1
*    FIN = 0

Si haces lo mismo con 0x14, verás que obtendrás RST-ACK.
Por cada paquete enviado vamos a usar `s[TCP].dport` a la hora de decirle al usuario que número de puerto está abierto y cerrado.
Eso es todo por hoy, mañana haré que sea más fácil cambiar los puertos y la IP a escanear y añadiré otras mejoras para que sea mas facil de usar.
