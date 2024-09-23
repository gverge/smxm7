# DHCP : Configuració Dinàmica de Sistemes
DHCP és l’acrònim de dynamic host configuration protocol, en català, protocol de configuració dinàmica d’equips. 

### Què fa el DHCP?

Una forma planera d’entendre el DHCP és imaginar que els equips de client en arrencar fan un crit per la xarxa i pregunten “que hi ha algú?”, “qui sóc jo?”. El servidor de DHCP els contesta proporcionant-los tota la informació necessària perquè sàpiguen qui són i com han de configurar els seus paràmetres de xarxa.

### Identificació dels equips de xarxa

L’administrador de xarxa té la tasca de configurar TOTS els equips que la componen, identificant-los amb l’adreça IP corresponent, la màscara de xarxa, i generalment disposarà d’un camí d’accés a Internet (gateway) i els servidors DNS a consultar. 

## El servei DHCP
El servei DHCP simplifica l’administració de la configuració dels equips de xarxa fent-la centralitzada, dinàmica i amb concessions per períodes de temps finits. 
Els principals avantatges d’utilitzar DHCP són: 
- Evitar conflictes d’adreces IP
- Estalviar temps i de feina. 

