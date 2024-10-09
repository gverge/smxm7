# DHCP amb Kea

Durant molts anys el servidor DHCP canònic ha estat ISC DHCP Server . Un complet servidor DHCP per a IPv4 i IPv6 amb totes les funcions possibles, incloent un client i un relay.

Però amb el final del 2022 també arriba el final de la vida per a ISC DHC Server.

Afortunadament tenim una nova eina disponible per implementar el servei DCHP a xarxes IPv4 i IPv6. Es tracta de Kea , també desenvolupat per l'ISC com un servidor DHCP modern.

Característiques de Kea

El desenvolupament de Kea obeeix que ISC DHCP server es va publicar el 1999 i durant aquest temps les xarxes han canviat molt. Per això, s'ha decidit reescriure el servidor seguint pràctiques modernes.

Kea proporciona 4 serveis controlats per unitats de systemd.

<table border="1" style="border-collapse: collapse; width: 100%; height: 105px;">
            <tbody>
             <tr style="height: 21px;">
              <td style="width: 17.4418%; height: 21px;"><strong><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Unitat de systemd</font></font></strong></td>
              <td style="width: 26.8412%; height: 21px;"><strong><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Fitxer de configuració</font></font></strong></td>
              <td style="width: 55.717%; height: 21px;"><strong><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Servei</font></font></strong></td>
             </tr>
             <tr style="height: 21px;">
              <td style="width: 17.4418%; height: 21px;"><code>kea-dhcp4-server</code></td>
              <td style="width: 26.8412%; height: 21px;"><code>/etc/kea/kea-dhcp4.conf</code></td>
              <td style="width: 55.717%; height: 21px;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Servidor DHCP IPv4</font></font></td>
             </tr>
             <tr style="height: 21px;">
              <td style="width: 17.4418%; height: 21px;"><code>kea-dhcp6-server</code></td>
              <td style="width: 26.8412%; height: 21px;"><code>/etc/kea/kea-dhcp6.conf</code></td>
              <td style="width: 55.717%; height: 21px;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Servidor DHCP IPv6</font></font></td>
             </tr>
             <tr style="height: 21px;">
              <td style="width: 17.4418%; height: 21px;"><code>kea-dhcp-ddns-server</code></td>
              <td style="width: 26.8412%; height: 21px;"><code>/etc/kea/kea-dhcp-ddns.conf</code></td>
              <td style="width: 55.717%; height: 21px;"><a href="https://translate.google.com/website?sl=es&amp;tl=ca&amp;hl=ca&amp;client=webapp&amp;u=https://en.wikipedia.org/wiki/Dynamic_DNS"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Servidor DDNS (DNS Dinàmic)</font></font></a></td>
             </tr>
             <tr style="height: 21px;">
              <td style="width: 17.4418%; height: 21px;"><code>kea-ctrl-agent</code></td>
              <td style="width: 26.8412%; height: 21px;"><code>/etc/kea/kea-ctrl-agent.conf</code></td>
              <td style="width: 55.717%; height: 21px;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Agent de control de Kea. Interfície REST per a configuració dinàmica.</font></font></td>
             </tr>
            </tbody>
           </table>

Els 4 serveis són independents i, si per exemple, únicament estem interessats en un servidor DHCP v4 només cal executar el primer servei dehabilitant a systemdla resta d'unitats.

L' agent de control de Kea proporciona una interfície REST per gestionar de manera dinàmica Kea. D'aquesta manera es pot integrar Kea amb altres eines que el poden configurar sobre la marxa.

D'altra banda, es tracta d'un disseny modular en què hi ha un dimoni bàsic per a cada servei que pot ser estès amb una col·lecció de mòduls .

Opcionalment és possible instal·lar Stork que és una aplicació web per monitoritzar tant Kea com BIND9.

La documentació de Kea és molt completa i es pot consultar als [Docs de KEA](https://kea.readthedocs.io/en/latest/).
Configuració bàsica de Kea DHCPv4

Tal com ens explica l' apartat de la documentació de Kea dedicat a la seva configuració , els fitxers de configuració utilitzen el format JSON . I encara que Kea estén el format JSON per permetre:

- Diferents tipus de comentaris ( #, //, /* */).
- La inclusió de fitxers amb <?include "file.json"?>.
- Comes finals.

Cal entendre que encara que aquestes extensions del format JSON poden fer més còmode escriure manualment els fitxers de configuració i sobretot, la meva opció preferida, utilitzar comentaris a les configuracions. I utilitzar aquestes extensions farà que el fitxer s'aparti de la norma JSON i qualsevol eina per verificar o donar format de manera automàtica, com ara jq, trobareu problemes de sintaxi als nostres arxius.

Per aquesta raó sovint és aconsellable prescindir de les extensions i cenyir-se a la norma JSON per poder verificar i donar format als nostres fitxers de configuració de manera automàtica.

Una configuració d'exemple bàsica per al servidor DHCPv4 pot ser:

~~~
{
  "Dhcp4": {
    "interfícies-config": {
      "interfícies": [
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
            "name": "encaminadors",
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
            "data": "domini-100.test"
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
~~~
Els apartats de configuració de l'exemple són:

Dhcp4

Configuració del servei DHCPv4. A la configuració d'interfícies cal enumerar els adaptadors de xarxa que utilitzarà el servidor.

reservations-global

A Kea les reserves poden ser globals o locals a una subxarxa. En aquest cas, no volem reserves globals.

reservations-out-of-pool

Quan aquesta opció està activada, el servidor entendrà que per a les reserves s'utilitzen adreces que estan fora dels pooldedicats als clients sense reserva.

valid-lifetime, renew-timer,rebind-timer

Temps per a l'adquisició i la renovació de concessions.

subnet4

Array dedicat a definir subxarxes. En aquest cas es defineix la subxarxa 192.168.100.0/24.

match-client-id

Aquesta opció permet indicar com ha didentificar el servidor als clients. Originalment es feia servir l' adreça MAC del client, però després es va introduir el DUID per identificar els equips independentment de la interfície de xarxa (wifi o cablejada) que utilitzin.

La configuració per defecte utilitza el DUID per identificar els clients. Això fa que el servidor confongui les MVs clonades que tenen el mateix DUID.

Per evitar aquest problema nosaltres utilitzem "match-client-id": falseque configura Kea perquè ignori el DUID del client i només utilitzi la seva adreça MAC.

option-data

Opcions passades al client amb la concessió de xarxa. Típicament: porta denllaç, servidors DNS, servidors NTP i domini.

pools

Array de blocs d'adreces per assignar als ordinadors que sol·licitin una concessió de xarxa.

reservations

Arralla amb les reserves (ordinadors coneguts als quals se'ls assigna una adreça determinada). A l'exemple només s'utilitzen les opcions "hw-address"(per indicar la MAC de l'ordinador) i "ip-address"per indicar la IP assignada. Però és possible aportar qualsevol opció de configuració en fer la reserva .

loggers

Configuració per als fitxers de registre (i journal) de Kea. En aquest cas s'especifica que cal registrar qualsevol missatge marcat com DEBUGa o més important. Els nivells seleccionables , de més pròlix a menys detallat, són: DEBUG, INFO, WARN, ERRORi FATAL.

Encara seleccionat el nivell de registre DEBUGKea no ens inunda amb missatges irrellevants.

Aquí es poden apreciar els missatges registrats en engegar i apagar un client DHCPv4.

~~~
gen 06 12:47:19 kea kea-dhcp4[617]: 2023-01-06 12:47:19.054 INFO [kea-dhcp4.dhcp4/617.140134650707584] DHCP2_START
gen 06 12:48:19 kea kea-dhcp4[617]: 2023-01-06 12:48:19.228 INFO [kea-dhcp4.leases/617.140134650707584] DHCP4_LE : 5c], cid=[01:08:00:27:d1:b8:5c], tid=0xa9f404c6: lease 192.168.101.10 will be advertised
ene 06 12:48:19 kea kea-dhcp4[617]: 2023-01-06 12:48:19.229 INFO [kea-dhcp4.leases/617.140134650707584] DHCP4_LEASE_ALLOC [hwtype=1 08:00:27:d1:b8: 5c], cid=[01:08:00:27:d1:b8:5c], tid=0xa9f404c6: lease 192.168.101.10 has been allocated for 4000 seconds
gen 06 12:48:45 kea kea-dhcp4[617]: 2023-01-06 12:48:45.078 INFO [kea-dhcp4.leases/617.140134650707584] DHCP4_: 5c], cid=[01:08:00:27:d1:b8:5c], tid=0xa9f404c6: address 192.168.101.10 was released properly.
~~~

