# DHCP : Configuració Dinàmica de Sistemes

### Índex de Contingut
- [El servei DHCP](#punt1)
- [El protocol DHCP i els seus components](#punt2)
- [Instal·lació d'un servei DHCP](#punt3)
  - [Instal·lació del servei DHCP a Ubuntu Serves](#punt3.1)
  - [Instal·lació del servei DHCP a Windows](#punt3.2)
- [Recursos DHCP](#punt4)

<hr>


## El servei DHCP <a name="punt1"></a>
DHCP és l’acrònim de dynamic host configuration protocol, en català, protocol de configuració dinàmica d’equips. 

#### Què fa el DHCP?

Una forma planera d’entendre el DHCP és imaginar que els equips de client en arrencar fan un crit per la xarxa i pregunten “que hi ha algú?”, “qui sóc jo?”. El servidor de DHCP els contesta proporcionant-los tota la informació necessària perquè sàpiguen qui són i com han de configurar els seus paràmetres de xarxa.

#### Identificació dels equips de xarxa
Tot equip de xarxa necessita disposar: 
- Una adreça IP que l’identifica de manera única a la xarxa. 
- Una màscara per poder separar de l’adreça IP quina és la part d’adreça de xarxa i quina la part d’adreça de host.
- Una adreça de la porta d’enllaç predeterminada o passarel·la per defecte (o gateway), per disposar d’accés a xarxes externes.
- Adreces dels servidors DNS a consultar.


El servei DHCP simplifica l’administració de la configuració dels equips de xarxa fent-la centralitzada, dinàmica i amb concessions per períodes de temps finits. 
Els principals avantatges d’utilitzar DHCP són: 
- Evitar conflictes d’adreces IP
- Estalviar temps i de feina. 

#### Configuració d'un equip de xarxa
Les opcions de configuració de xarxa es poden assignar a cada equip estàticament o es poden configurar de manera dinàmica utilitzant DHCP. 

#### Tipus d'assignacions d'adreces IP

Quan l’adreça IP i els altres paràmetres necessaris de configuració de la xarxa es configuren equip per equip, manualment, es diu que tenen **IP estàtica**. 

Quan la configuració de xarxa d’un equip es rep per mitjà d’un servidor DHCP es diu que utilitza una adreça **IP dinàmica**. Aquesta adreça pot variar dins d’un interval d’adreces disponibles per al servidor DHCP o pot ser fixa. 

## El protocol DHCP i els seus components<a name="punt2"></a>
Tot protocol implica un diàleg entre els equips que hi intervenen, ens caldrà doncs analitzar quin és i com es produeix aquest diàleg. Finalment es descriurà el significat de termes tan usuals en DHCP com intervals, exclusions, concessions i reserves. 

El protocol DHCP està basat en l’arquitectura de serveis **client-servidor** i utilitza com a transport el **protocol UDP** de la pila de protocols TCP/ IP. El servidor DHCP es comunica amb els clients utilitzant paquets UDP, que rep en el seu **port 67** i envia al **port 68 del client**. 

#### El model funcional del protocol DHCP
 El protocol DHCP descriu el diàleg que es produeix entre client i servidor per a la concessió de configuracions IP. En una xarxa amb configuració d’equips dinàmica, **un o més servidors DHCP escoltaran les peticions dels clients en el port 67**. Els clients DHCP sol·licitaran al servidor DHCP una **configuració IP**, i començarà un procés de **negociació** que ha d’acabar (si tot va bé) amb la concessió d’una adreça IP al client. **Els servidors parlen al port 68 dels clients**.

La negociació que s’estableix es pot definir a grans trets de la manera següent:

- El client sol·licita una adreça IP (de fet, una configuració de xarxa).
- El servidor mira les adreces IP disponibles dins de l’**interval d’adreces dinàmiques** de què disposa per concedir i n’ofereix una al client.
- Si el client l’accepta, envia una sol·licitud al servidor per fer-la seva.
- Si al servidor li sembla bé, accepta la petició del client i li confirma que pot utilitzar aquesta IP, que la hi concedeix per un **període de temps limitat**.

Transcorregut el període de lloguer, el client haurà de **renegociar** la concessió en un procés similar al descrit anteriorment.

<p align=center><image src="https://ioc.xtec.cat/materials/FP/Recursos/fp_smx_m07_/web/fp_smx_m07_htmlindex/WebContent/u1/media/smxm7uf1ud1_im1.png" width=200 /></p>

Els següents són els tipus de paquets DHCP:
- **DHCP discover**: L’envia el client per tal de demanar una configuració IP a algun servidor. El client no sap a quina xarxa pertany (no té adreça IP ni màscara de xarxa) i tampoc sap quins servidors DHCP hi ha en la xarxa (si n’hi ha cap), per tant, genera un paquet de difusió (broadcast).
- **DHCP offer**: Un servidor DHCP mira d’atendre les peticions dels clients proporcionant una IP de l’interval d’adreces dinàmiques que gestiona **(pool)**. Ha de mirar quines de les adreces li queden lliures i un cop assignada ho anota en un fitxer de registre de les concessions efectuades. Cada vegada que finalitza una **concessió (lease)**, i en cas de no ser renovada, el servidor pot tornar a utilitzar la IP per a un altre client. A un determinat client se li pot assignar una **IP concreta** a partir de la seva MAC, si aquesta relació està establerta al servidor. El servidor selecciona una IP disponible i la **reserva** per al client (encara no està assignada). Tot seguit envia un paquet **DHCP offer (unicast)** al client amb tota la informació de configuració requerida.
- **DHCP request**: Quan el client rep una oferta de configuració IP per part d’un servidor, la pot acceptar o rebutjar. Si el client no accepta l’oferta, simplement realitzarà un **DHCP discovery** de nou. Això és suficient perquè el servidor s’adoni que l’oferta ha estat rebutjada. Si el client accepta l’oferta, ho ha de comunicar al servidor. El mecanisme per fer-ho és mitjançant un paquet **DHCP request** enviat un altre cop per difusió fent públic a la xarxa que ha acceptat una oferta. A hores d’ara, el client encara no disposa de l’adreça IP per utilitzar-la.
- **DHCP ack/nack**: L’últim pas en una negociació DHCP bàsica el realitza el servidor quan finalment autoritza la concessió enviant el paquet **DHCPACK** (DHCP akcnowledgement, paquet unidestinació adreçat a la MAC del client). A partir d’aquest moment, el client sí pot fer ús de l’adreça IP i de la configuració de xarxa rebuda. DHCPACK inclou tota la informació referent a la durada de la concessió i les dades necessàries per gestionar quan expira. El servidor anotarà en el registre de concessions la que acaba de realitzar i detallarà tots els aspectes d’aquesta. Quan un servidor DHCP detecta que la IP que havia reservat per a un client i que li anava a concedir ja està en ús, el servidor envia al client un paquet **DHCPNACK** i indica la no-autorització de la concessió.
- **DHCP decline**: El client també pot examinar l’adreça IP oferta pel servidor per comprovar si està en ús o no. Pot fer altres proves per veure si li sembla correcta o no l’oferta rebuda del servidor. El client pot enviar un paquet DHCP decline al servidor per indicar que la seva oferta ha estat rebutjada.
- **DHCP release**: Quan un client ja no necessita més l’ús de la configuració IP que ha rebut, la pot alliberar enviant al servidor un paquet **DHCP release**. En fer-ho, el servidor afegeix l’adreça IP al conjunt d’adreces dinàmiques que té disponiblesi fa l’anotació pertinent en el registre de concessions (leases) per indicar que ha finalitzat l’ús de l’adreça.
- **DHCP information**: En tot moment el client pot sol·licitar més informació sobre la configuració de xarxa al servidor utilitzant un paquet DHCP information. En el paquet DHCP offer que el servidor envia al client, consten les informacions generals de configuració de xarxa que es trameten en l’oferta: adreça IP, màscara de xarxa, porta d’enllaç predeterminada, servidor DNS, fitxer a baixar i molts altres paràmetres que poden estar configurats per enviar-se en l’oferta.
- **Renovació d'una IP concreta**: El client pot demanar continuar usant la mateixa IP amb un paquet DHCP request, i el servidor li pot concedir amb el paquet DHCP ACK. Si el servidor no li pot concedir (està en ús, no és de l’interval que gestiona, etc.) envia un DHCP NACK. 

#### Intervals, exclusions, concessions i reserves
Descrivim ara alguns dels aspectes més importants que formen part de la configuració DHCP.

- **Interval**: conjunt d’adreces dinàmiques que el servidor té disponibles per assignar als clients. S’agrupen per oferir-se a les diverses subxarxes que atén el servidor. Una mateixa subxarxa pot disposar de diversos intervals.
~~~
subnet 140.220.191.0 netmask 255.255.255.0 {
 range 140.220.191.150 140.220.191.249;
}
subnet 239.252.197.0 netmask 255.255.255.0 {
 range 239.252.197.10 239.252.197.107;
 range 239.252.197.113 239.252.197.250;
}
~~~
 - **Exclusions**: adreces IP que no s’ofereixen dinàmicament per part del servidor. És a dir, que no formen part de cap interval.
 - **Concessions**: l’assignació d’una adreça IP i la resta de paràmetres de xarxa a un client és una concessió (o leasse). Els clients reben les concessions per períodes de temps finits, que en finalitzar, cal renegociar. Tant el client com el servidor s’anoten les concessions.
- **Reserves**: anomenem reserves aquelles adreces IP que s’assignen via DHCP però de manera fixa, normalment a un equip amb una MAC o nom de host concret. 
~~~
subnet 140.220.191.0 netmask 255.255.255.0 {
 host server {
  hardware ethernet 08:00:2b:4c:59:23;
  fixed-address 140.220.191.1;
 }
 range 140.220.191.150 140.220.191.249;
 }
~~~
## Instal·lació d'un servei DHCP <a name="punt3"></a>
El servei de xarxa DHCP està estructurat en forma de servei client/servidor; per tant, caldrà disposar del programari apropiat per fer cada un d’aquests rols. El programari que fa la funció de client usualment ja està integrat en el sistema operatiu (la part que gestiona la xarxa). Així doncs, quan parlem d’instal·lar un servei DHCP fem referència al procés d’instal·lació i configuració del programari del servidor DHCP. Evidentment també caldrà configurar els clients adequadament per fer ús d’aquest servei. 

### Instal·lació del servei DHCP a Ubuntu Server (isc-dhcp-server)<a name="punt3.1"></a>
Tot i que aquest servei DHCP ha deixat d'estar mantingut per ISC en favor de KEA, continua sent avui dia el mes implantat en sistemes Linux. 
- [Pàgina oficial ISC DHCP](https://www.isc.org/dhcp/)
- [Manual ISC DHCP 4.4](https://kb.isc.org/docs/isc-dhcp-44-manual-pages-dhcpd)

En primer lloc executem un update per obtenir els últims cadidats del paquet, posteriorment instal·lem el sercei i en comprovem l'estat (evidentment estarà caigut ja que encara no ha estat configurat):
~~~
# apt update
# apt install isc-dhcp-server
# systemcltl status isc-dhcp-server    
~~~
Iniciem la configuració del servei indicant quines interfícies s'utilitzaran per escoltar les peticions dels clients, al fitxer **`/etc/default/isc-dhcp-server`**:
~~~
# nano /etc/default/isc-dhcp-server
 ...
 INTERFACESv4=”enp0s….”
 ...
~~~
Realitzem una configuració bàsica del servei al fixer **`/etc/dhcp/dhcpd.conf`**:
~~~
option domain-name "example.local";
option domain-name-servers 8.8.8.8;
default-lease-time 3600;
max-lease-time 86400;
ddns-update-style none;
subnet 192.168.56.0 netmask 255.255.255.0 {
       range 192.168.56.220 192.168.56.240;
       option routers 192.168.56.10;
}
~~~
Provem ara a iniciar el servei i comprovar el seu funcionament des d'un client de la xarxa. 
Aquesta comprovació la podem realitzar des del client:
- Configurem la xarxa per tal de rebre la informació necessaria de forma automàtica (DHCP).
- Podem forçar l'alliberament d'una adreça ja asignada prèviament amb la comanda **`sudo dhclient -r -v`**
- Podem sol·licitar una nova adreça amb la comanda **`sudo dhclient -v`**

En cas de no aconseguir-ho podem comprovar els possibles errors amb la comanda **journalctl -xe**

I també podem comprovar des del servidor:
- Que la negociació s'ha realitzat correctament observant el log del servidor a **`/var/log/syslog`**
- Que el registre de la concessió ha estat creat al fitxer de concessions **`/var/lib/dhcp/dhcpd.leases`**

#### Habilitant l'encaminament a Ubuntu Server
Per tal que els nostres clients puguin sortir a internet a través del nostre servidor (que farà de porta d'enllaç) hem de realitzar dues configuracions adicionals.
- Habilitar el forwarding des del fitxer **`/etc/sysctl.conf`** descomentant la següent línia i fent un **reboot** al servidor:
  
   `net.ipv4.ip_forward=1`
  
- Afegir una regla NAT d'enmascarament als paquets que surtin cap a internet:
  
   `# sudo iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE`
  
Podem comprovar que aquesta regla s'ha afegit correctament amb la comanda:
~~~
# sudo iptables -t nat -L
~~~
Per aconseguir que aquestes regles no desapareixin al reiniciar el servidor instal·lem una eina que aconseguirà que siguin persistents:
~~~
# sudo apt install iptables-persistent
~~~
La regla es guardarà a: **`/etc/iptables/rules.v`**
En cas d'afegir més regles posteriorment i voler desar-les també com a persistents, podem executar la comanda:
~~~
# iptables-save > /etc/iptables/rules.v4
~~~
### Instal·lació del servei DHCP a Windows <a name="punt3.2"></a>

Per instal·lar el rol del servidor DHCP des de l'Administrador del Servidor, en l'arbre de característiques buscarem DHCP i el marcarem, afegint les característiques requerides.
Un cop instal·lat se'ns ofereix la possibilitat de completar la configuració bàsica del servei, durant aquest breu procés es crearan els grups de seguretat necessaris per gestionar el servei. En acbar reiniciarem el servei per aplicar els canvis.

Ara ja som en condicions de crear el nostre primer àmbit. ho farem des de **Inici>Eines Administratives>DHCP** on escollirem l'opció **Nou Àmbit** que apareixerà en fer botó dret sobre IPv4. A partir d'aquí podem definir una sèrie d'opcions com:
- Nom i descripció de l'àmbit.
- Rang d'adreces de concessió i la seva màscara.
- Rang d'exclusions.
- Durada de la concessió.
- Altres opcions DHCP com:
  - Porta d'enllaç.
  - Nom de domini i servidors DNS.
  - Servidors WINS.
  
Un cop configurades les opcions ja només ens quedarà activar l'àmbit i comprovar que el servei està actiu.

#### Habilitant l'encaminament a Ubuntu Server
Abans de començar ens hem d'assegurar de que les interfaces del server estan correctament configurades. Un cop realitzades les comprovacions anidrem a l'administrador del servidor per tal d'afegir un nou rol l'**Accés Remot**.
En la pantalla de selecció de reveis del Rol, escollim **Routing** (comprovarem com d'afegeix automàticament l'accés directe i VPN, que més endavant utilitzarem) i seguim, deixem per defecte els serveis proposats per al Servidor WEB i procedim a instal·lar.

Un cop instal·lat, el servei ja apareixera en el menú d'eines, el seleccionem i un cop accedim a la pantalla de configuració fem botó dret sobre el servidor i procedim a **Configurar i Habilitar el Servei**. Durant el procés d'instal·lació escollirem les opcions:
- Traducció d'adreces de Xarxa (NAT). Aquest és el nostre objectiu, ja que pretenem encaminar paquets des de les nostres xarxes internes cap a Internet.
- En la següent pantalla només ens queda seleccionar la interfície de sortida i finalitzar la configuració.

Ja només ens queda comprovar que els nostres clients poden connectar amb xarxes remotes.


## Recursos DHCP<a name="punt4"></a>
- [Tutorial del servei DHCP a FPGenred](https://www.fpgenred.es/DHCP/index.html)
- [Material DHCP al ioc](https://ioc.xtec.cat/materials/FP/Recursos/fp_smx_m07_/web/fp_smx_m07_htmlindex/WebContent/u1/a1/continguts.html)
- [Pàgina oficial ISC DHCP](https://www.isc.org/dhcp/)
- [Manual ISC DHCP 4.4](https://kb.isc.org/docs/isc-dhcp-44-manual-pages-dhcpd)
- [Habilitar encaminament a Windows Server 2022](https://witcherit.com/2020/09/20/instalar-servicio-de-enrutamiento-en-windows-server-2019/)
  
