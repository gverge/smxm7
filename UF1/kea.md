DHCP con Kea

https://kea.isc.org/img/common/kea_1.4.pngDurante muchos años el servidor DHCP canónico ha sido ISC DHCP Server. Un completo servidor DHCP para IPv4 e IPv6 con todas las funciones posibles, incluyendo un cliente y un relay.

Naturalmente este ha sido el software que se ha utilizado en nuestro centro educativo para implementar el servicio DHCP. Su fichero de configuración se puede consular en: https://github.com/vcarceler/cirdan-dhcp/blob/master/dhcpd.conf.

Pero con el final del 2022 llega también el final de la vida para ISC DHC Server.

    "ISC has announced the end of life for ISC DHCP as of the end of 2022. ISC will continue providing professional support services for existing subscribers, but does not intend to issue any further maintenance releases."

    Página de ISC DHCP: https://www.isc.org/dhcp/

Afortunadamente tenemos una nueva herramienta disponible para implementar el servicio DCHP en redes IPv4 e IPv6. Se trata de Kea, también desarrollado por el ISC como un servidor DHCP moderno.

Y en el nuevo gateway de nuestro centro educativo se ha migrado el servicio DHCP a Kea. Se puede consultar su configuración en: https://github.com/vcarceler/cirdan-kea/blob/main/kea-dhcp4.conf.
Características de Kea

El desarrollo de Kea obedece a que ISC DHCP server se publicó en 1999 y durante este tiempo las redes han cambiado mucho. Por eso se ha decidido reescribir el servidor siguiendo prácticas modernas.

Kea proporciona 4 servicios controlados por unidades de systemd.
Unidad de systemd 	Fichero de configuración 	Servicio
kea-dhcp4-server 	/etc/kea/kea-dhcp4.conf 	Servidor DHCP IPv4
kea-dhcp6-server 	/etc/kea/kea-dhcp6.conf 	Servidor DHCP IPv6
kea-dhcp-ddns-server 	/etc/kea/kea-dhcp-ddns.conf 	Servidor DDNS (DNS Dinámico)
kea-ctrl-agent 	/etc/kea/kea-ctrl-agent.conf 	Agente de control de Kea. Interfaz REST para configuración dinámica.

Los 4 servicios son independientes y, si por ejemplo, únicamente estamos interesados en un servidor DHCP v4 bastará con ejecutar el primer servicio dehabilitando en systemd el resto de unidades.

El agente de control de Kea proporciona una interfaz REST para gestionar de manera dinámica Kea. De esta manera se puede integrar Kea con otras herramientas que pueden configurarlo sobre la marcha.

Por lo demás se trata de un diseño modular en el que hay un demonio básico para cada servicio que puede ser extendido con una colección de módulos.

Opcionalmente es posible instalar Stork que es una aplicación web para monitorizar tanto Kea como BIND9.

La documentación de Kea es muy completa y se puede consultar en: https://kea.readthedocs.io/en/latest/
Configuración básica de Kea DHCPv4

Tal y como nos explica el apartado de la documentación de Kea dedicado a su configuración, los ficheros de configuración utilizan el formato JSON. Y aunque Kea extiende el formato JSON para permitir:

    Diferentes tipos de comentarios (#, //, /* */).
    La inclusión de ficheros con <?include "file.json"?>.
    Comas finales.

Hay que entender que aunque estas extensiones del formato JSON pueden hacer más cómodo escribir manualmente los ficheros de configuración y sobre todo, mi opción favorita, utilizar comentarios en las configuraciones. Y utilizar estas extensiones hará que el fichero se aparte de la norma JSON y cualquier herramienta para verificar o dar formato de manera automática, como jq, encontrará problemas de sintaxis en nuestros archivos.

Por esta razón con frecuencia resulta aconsejable prescindir de las extensiones y ceñirse a la norma JSON para poder verificar y dar formato a nuestros ficheros de configuración de manera automática.

Una configuración de ejemplo básica para el servidor DHCPv4 puede ser:

{
  "Dhcp4": {
    "interfaces-config": {
      "interfaces": [
        "enp0s8"
      ],
      "dhcp-socket-type": "raw"
    },
    "reservations-global": false,
    "reservations-out-of-pool": true,
    "valid-lifetime": 4000,
    "renew-timer": 1000,
    "rebind-timer": 2000,
    "subnet4": [
      {
        "subnet": "192.168.100.0/24",
        "match-client-id": false,
        "option-data": [
          {
            "name": "routers",
            "data": "192.168.100.1"
          },
          {
            "name": "domain-name-servers",
            "data": "9.9.9.9"
          },
          {
            "name": "ntp-servers",
            "data": "192.168.100.1"
          },
          {
            "name": "domain-name",
            "data": "dominio-100.test"
          }
        ],
        "pools": [
          {
            "pool": "192.168.100.100-192.168.100.199"
          }
        ],
        "reservations": [
          {
            "hw-address": "08:00:27:5c:eb:99",
            "ip-address": "192.168.100.11"
          },
          {
            "hw-address": "08:00:27:2d:8d:a2",
            "ip-address": "192.168.100.12"
          }
        ]
      }
    ],
    "loggers": [
      {
        "name": "*",
        "severity": "DEBUG"
      }
    ]
  }
}

Los apartados de configuración del ejemplo son:

Dhcp4

Configuración del servicio DHCPv4. En la configuración de interfaces se deben enumerar los adaptadores de red que utilizará el servidor.

reservations-global

En Kea las reservas pueden ser globales o locales a una subred. En este caso no queremos reservas globales.

reservations-out-of-pool

Cuando esta opción está activada el servidor entenderá que para las reservas se utilizan direcciones que están fuera de los pool dedicados a los clientes sin reserva.

valid-lifetime, renew-timer, rebind-timer

Tiempos para la adquisición y renovación de concesiones.

subnet4

Array dedicado a definir subredes. En este caso se define la subred 192.168.100.0/24.

match-client-id

Esta opción permite indicar cómo debe identificar el servidor a los clientes. Originalmente se utilizaba la dirección MAC del cliente, pero después se introdujo el DUID para identificar a los equipos independientemente de la interfaz de red (wifi o cableada) que utilicen.

La configuración por defecto utiliza el DUID para identificar a los clientes. Lo que provoca que el servidor confunda las MVs clonadas que tienen el mismo DUID.

Para evitar este problema nosotros utilizamos "match-client-id": false que configura Kea para que ignore el DUID del cliente y únicamente utilice su dirección MAC.

option-data

Opciones pasada al cliente con la concesión de red. Típicamente: puerta de enlace, servidores DNS, servidores NTP y dominio.

pools

Array de bloques de direcciones para asignar a los ordenadores que soliciten una concesión de red.

reservations

Array con las reservas (ordenadores conocidos a los que se les asigna una dirección determinada). En el ejemplo únicamente se utilizan las opciones "hw-address" (para indicar la MAC del ordenador) e "ip-address" para indicar la IP asignada. Pero es posible aportar cualquier opción de configuración al realizar la reserva.

loggers

Configuración para los ficheros de registro (y journal) de Kea. En este caso se especifica que se debe registrar cualquier mensaje marcado como DEBUG o más importante. Los niveles seleccionables, de más prólijo a menos detallado, son: DEBUG, INFO, WARN, ERROR y FATAL.

Aún seleccionado el nivel de registro DEBUG Kea no nos inunda con mensajes irrelevantes.

Aquí se pueden apreciar los mensajes registrados al encender y apagar un cliente DHCPv4.

ene 06 12:47:19 kea kea-dhcp4[617]: 2023-01-06 12:47:19.054 INFO  [kea-dhcp4.dhcp4/617.140134650707584] DHCP4_STARTED Kea DHCPv4 server version 2.0.2 started
ene 06 12:48:19 kea kea-dhcp4[617]: 2023-01-06 12:48:19.228 INFO  [kea-dhcp4.leases/617.140134650707584] DHCP4_LEASE_ADVERT [hwtype=1 08:00:27:d1:b8:5c], cid=[01:08:00:27:d1:b8:5c], tid=0xa9f404c6: lease 192.168.101.10 will be advertised
ene 06 12:48:19 kea kea-dhcp4[617]: 2023-01-06 12:48:19.229 INFO  [kea-dhcp4.leases/617.140134650707584] DHCP4_LEASE_ALLOC [hwtype=1 08:00:27:d1:b8:5c], cid=[01:08:00:27:d1:b8:5c], tid=0xa9f404c6: lease 192.168.101.10 has been allocated for 4000 seconds
ene 06 12:48:45 kea kea-dhcp4[617]: 2023-01-06 12:48:45.078 INFO  [kea-dhcp4.leases/617.140134650707584] DHCP4_RELEASE [hwtype=1 08:00:27:d1:b8:5c], cid=[01:08:00:27:d1:b8:5c], tid=0xa9f404c6: address 192.168.101.10 was released properly.

Persistencia para los concesiones con memfile

Cuando un servidor DHCP asigna una concesión a un equipo, debe guardar esta información en algún sitio de manera que persista a posibles reinicios del servidor. Así, cuando el servidor arranca puede consultar esta información para saber qué concesiones siguen siendo válidas y no evitar asignar esas direcciones a otros equipos.

Kea puede utilizar tres backends para guardar esta información:

    memfile
    MySQL
    PostgreSQL

El backend memfileutilizará un fichero en formato .csv para guardar información sobre las concesiones. Típicamente este fichero es /var/lib/kea/kea-leases4.csv y tiene el siguiente aspecto:

address,hwaddr,client_id,valid_lifetime,expire,subnet_id,fqdn_fwd,fqdn_rev,hostname,state,user_context
192.168.241.75,e0:63:da:a6:ef:cf,,600,1673032745,18,0,0,unifi-p1a1,0,
192.168.241.92,70:a7:41:64:12:49,,600,1673032749,18,0,0,unifi-u6-pro-chomsky,0,
192.168.21.51,e4:e7:49:a4:2f:20,,600,1673032760,11,0,0,hp-laserjet-color-m452dn-secretaria.,0,
192.168.241.53,f0:9f:c2:f6:8e:fd,,600,1673032763,18,0,0,unifi-aula-6,0,
192.168.241.57,f0:9f:c2:20:a8:7d,,600,1673032765,18,0,0,unifi-aig-grande,0,

A este fichero se añade una línea cada vez que se produce una concesión. Durante el arranque Kea lo lee por completo para ver si existe alguna concesión vigente. El tiempo de expiración está indicado tiempo unix.

Cuando transcurre el periodo de tiempo especificado por lfc-interval, que por defecto es 1 hora, se rota el fichero kea-leases4.csv para evitar que crezca de manera indefinida.  Se puede leer más sobre el proceso de limpieza de este fichero de concesiones en: Why Is Lease File Cleanup Necessary?
Afinidad de concesiones y descarte de los paquetes DHCP4_RELEASE

Los equipos que forman parte de una red pueden tener una reserva de dirección para garantizar que siempre obtienen la misma IP. En esta categoría encontramos los equipos fijos de las aulas o los portátiles de los carros.

Pero frecuentemente también conviene, siempre que sea posible, que los equipos que se conectan de manera ocasional a una red y por lo tanto no tienen reserva —en nuestro caso máquinas virtuales— también obtengan la misma dirección IP aunque se reinicien o se vuelvan a conectar a la red después de unos días de inactividad.

Para conseguir que los equipos que no tienen una reserva, si es posible, obtengan la misma dirección IP hemos utilizado dos características de Kea:

    Afinidad de concesiones para indicar con el parámetro "hold-reclaimed-time": 1296000 que durante 15 días una concesión reclamada —porque ha transcurrido su periodo de validez— se mantendrá en la base de datos para reasignar al mismo cliente.
    Descarte de los paquetes DHCP4_RELEASE mediante la clasificación de clientes para evitar que al liberar una concesión —porque el equipo se reinicia o apaga y el cliente transmite un paquete DHCP4_RELEASE— Kea pase a considerar la concesión liberada —released— en lugar de prescrita —expired—. Debemos agradecer este hack a Peter Davies que describe cómo descartar los paquetes en este mensaje: https://www.mail-archive.com/kea-users@lists.isc.org/msg03061.html

El fragmento de configuración que necesitamos es:

    "expired-leases-processing": {
      "hold-reclaimed-time": 1296000
    },
    "client-classes": [
      {
        "name": "DROP",
        "test": "pkt4.msgtype == 7"
      }
    ]

De esta manera el servidor Kea ignorará los paquetes DHCP4_RELEASE que envíen los clientes con lo que no se liberarán las concesiones y acabarán prescribiendo. En el momento que prescriban pasarán a formar parte de la base de datos de Kea durante 15 días para asignarse al mismo cliente.

Antes de aplicar el descarte de los paquetes DHCP4_RELEASE, reiniciar o apagar un ordenador, implica que en el próximo arranque se obtendrá una IP diferente:

Feb 10 08:29:05 cirdan-2204 kea-dhcp4[3715577]: 2023-02-10 08:29:05.680 INFO  [kea-dhcp4.leases/3715577.140059399151232] DHCP4_LEASE_ADVERT [hwtype=1 08:00:27:3c:bd:5d], cid=[01:08:00:27:3c:bd:5d], tid=0x8b2a5f24: lease 192.168.17.104 will be advertised
Feb 10 08:29:05 cirdan-2204 kea-dhcp4[3715577]: 2023-02-10 08:29:05.681 INFO  [kea-dhcp4.leases/3715577.140059399151232] DHCP4_LEASE_ALLOC [hwtype=1 08:00:27:3c:bd:5d], cid=[01:08:00:27:3c:bd:5d], tid=0x8b2a5f24: lease 192.168.17.104 has been allocated for 600 seconds
Feb 10 08:31:06 cirdan-2204 kea-dhcp4[3715577]: 2023-02-10 08:31:06.660 INFO  [kea-dhcp4.leases/3715577.140059399151232] DHCP4_RELEASE [hwtype=1 08:00:27:3c:bd:5d], cid=[01:08:00:27:3c:bd:5d], tid=0x8b2a5f24: address 192.168.17.104 was released properly.
Feb 10 08:31:14 cirdan-2204 kea-dhcp4[3715577]: 2023-02-10 08:31:14.479 INFO  [kea-dhcp4.leases/3715577.140059399151232] DHCP4_LEASE_ADVERT [hwtype=1 08:00:27:3c:bd:5d], cid=[01:08:00:27:3c:bd:5d], tid=0xa0619c4d: lease 192.168.17.105 will be advertised
Feb 10 08:31:14 cirdan-2204 kea-dhcp4[3715577]: 2023-02-10 08:31:14.480 INFO  [kea-dhcp4.leases/3715577.140059399151232] DHCP4_LEASE_ALLOC [hwtype=1 08:00:27:3c:bd:5d], cid=[01:08:00:27:3c:bd:5d], tid=0xa0619c4d: lease 192.168.17.105 has been allocated for 600 seconds
Feb 10 08:31:53 cirdan-2204 kea-dhcp4[3715577]: 2023-02-10 08:31:53.237 INFO  [kea-dhcp4.leases/3715577.140059399151232] DHCP4_RELEASE [hwtype=1 08:00:27:3c:bd:5d], cid=[01:08:00:27:3c:bd:5d], tid=0xa0619c4d: address 192.168.17.105 was released properly.
Feb 10 08:32:01 cirdan-2204 kea-dhcp4[3715577]: 2023-02-10 08:32:01.126 INFO  [kea-dhcp4.leases/3715577.140059399151232] DHCP4_LEASE_ADVERT [hwtype=1 08:00:27:3c:bd:5d], cid=[01:08:00:27:3c:bd:5d], tid=0xecfc77bc: lease 192.168.17.106 will be advertised
Feb 10 08:32:01 cirdan-2204 kea-dhcp4[3715577]: 2023-02-10 08:32:01.127 INFO  [kea-dhcp4.leases/3715577.140059399151232] DHCP4_LEASE_ALLOC [hwtype=1 08:00:27:3c:bd:5d], cid=[01:08:00:27:3c:bd:5d], tid=0xecfc77bc: lease 192.168.17.106 has been allocated for 600 seconds

Pero una vez aplicadas las dos características esto ya no sucede:

Feb 10 10:55:57 cirdan-2204 kea-dhcp4[3955122]: 2023-02-10 10:55:57.379 INFO  [kea-dhcp4.leases/3955122.140390836407936] DHCP4_LEASE_ADVERT [hwtype=1 08:00:27:3c:bd:5d], cid=[01:08:00:27:3c:bd:5d], tid=0xf03b8dfe: lease 192.168.17.106 will be advertised
Feb 10 10:55:57 cirdan-2204 kea-dhcp4[3955122]: 2023-02-10 10:55:57.380 INFO  [kea-dhcp4.leases/3955122.140390836407936] DHCP4_LEASE_ALLOC [hwtype=1 08:00:27:3c:bd:5d], cid=[01:08:00:27:3c:bd:5d], tid=0xf03b8dfe: lease 192.168.17.106 has been allocated for 600 seconds
Feb 10 10:56:21 cirdan-2204 kea-dhcp4[3955122]: 2023-02-10 10:56:21.594 INFO  [kea-dhcp4.leases/3955122.140390836407936] DHCP4_LEASE_ADVERT [hwtype=1 08:00:27:3c:bd:5d], cid=[01:08:00:27:3c:bd:5d], tid=0xbad10314: lease 192.168.17.106 will be advertised
Feb 10 10:56:21 cirdan-2204 kea-dhcp4[3955122]: 2023-02-10 10:56:21.596 INFO  [kea-dhcp4.leases/3955122.140390836407936] DHCP4_LEASE_ALLOC [hwtype=1 08:00:27:3c:bd:5d], cid=[01:08:00:27:3c:bd:5d], tid=0xbad10314: lease 192.168.17.106 has been allocated for 600 seconds

Procesado automático de la configuración JSON

Si se prescinde de las mencionadas extensiones a JSON que soporta Kea y se tiene un fichero de configuración que cumple la norma JSON se pueden utilizar herramientas para revisar y dar formato automático al fichero.

Una de estas herramientas es jq, una navaja suiza para procesar ficheros JSON.

La manera más sencilla de procesar un fichero que ha sido editado manualmente para obtener una representación bonita es:

jq . kea-dhcp4.conf

De esta manera, si no se encuentra ningún error, se mostrará en la salida estándar una representación con una identación coherente del fichero.

Es posible redirigir la salida del comando anterior para guardar la salida en un fichero o bien invocar jq directamente desde el editor vim cuando se está editando el fichero.

En este último caso el comando de vim será:

:%!jq .

En este caso se actualizará el formato del fichero que se está editando y si se quiere se podrá guardar el fichero de la manera habitual.

Más información:

    Documentación de Kea: https://kea.readthedocs.io/en/latest/
    Introducción al servicio DHCP
    DHCP: Identificadores de cliente en DHCPv4
    Configuración del servidor DHCPv4 Kea utilizada en el centro
    GNU/Linux como router: Reenvío de paquetes y NAT
