# Hardening-HoneyPotSS
En este scritpt presento un honeypot (tarro de miel) que combinado con un Banneo (bloqueo de ip a nivel de cortafuegos). Nos va ha permitir parar/bloquear muchos ataques incluso antes de que se produzcan.

Hardening o endurecimiento es el proceso de asegurar un sistema reduciendo sus vulnerabilidades o agujeros de seguridad.

Explicacion: en el caso de los servidores una buena tactica de defensa es anticiparse a los ataques. Bloqueando al atacante incluso antes de que se inicie dicho ataque.

Como: casi todo ataque va precedido de una deteccion/escaner. En este caso colocaremos un servicio muy goloso pero falso. (de ahi viene lo de honeypot o tarro de miel)

Este script dada su sencillez lo he licenciado bajo Licencia MIT (la mas permisiva)
Luego podras usarlo , modificarlo incluso a nivel privado.

https://es.wikipedia.org/wiki/Licencia_MIT

https://choosealicense.com/licenses/mit/


![](Estadisticas_HoneyPotSS.jpg)

Estadisticas reales de los bloqueos realizados por el script en 5VPS
(ver http://node.jejo.pw/honeymap).

En el momento de la captura el honeypot ha bloqueado **6000 ataques** 
y solo han pasado 89 ataques a la defensa del siguiente nivel.
Luego este honeypot bloquea por si solo el **98%** de los ataques.

En un futuro articulo presentare una defensa muy superior y mas elaborada que detectara ataques incluso sin abrir los puertos. esta defensa bloquea el **99,9911** de los ataque recibidos.

```
#!/usr/bin/python
# MIT License
# Copyright (c) 2019 Sergio Blazquez Lopez (Em50L)
import os,socket

puerto = 23 #23 puerto telnet (el mas atacado) otros 22 y 445

honey_socket = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
honey_socket.bind( ('', puerto) )
honey_socket.listen( 1 )
print 'Honeypot a la escucha puerto: ', puerto

while True:
    conexion_entrante,info_conexion = honey_socket.accept()
    try:
	    conexion_entrante.send('Ups... te has equivocado esto es un honeypot\r\n')
    except socket.error, e:
	    print "honeypotSS2 error en socket:", e, "=> nmap -ST", info_conexion[0]
    conexion_entrante.close()
    print "honeypot conexion desde " , info_conexion[0]
	
    # Aqui decides que quieres hacer con los datos de la ip capturada.
    # ej Registrar avisar , banear .....
    #os.system("logger -p auth.info honeypot conexion desde "+ info_conexion[0])
    #os.system("iptables -A INPUT -j DROP -s " + info_conexion[0])
    os.system("fail2ban-client set ssh banip "+info_conexion[0])
    #os.system("curl 'http://honeymap_server/ipmap?ip="+info_conexion[0]+"&m=honeypot'&")#ojo con los &
    #os.system("echo "+info_conexion[0]+" > /dev/udp/honeymap_server/1234")

honey_socket.close()
print 'Fin programa!'
```
