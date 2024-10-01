# DHCP : Configuració Dinàmica de Sistemes
DHCP és l’acrònim de dynamic host configuration protocol, en català, protocol de configuració dinàmica d’equips. 

#### Què fa el DHCP?

Una forma planera d’entendre el DHCP és imaginar que els equips de client en arrencar fan un crit per la xarxa i pregunten “que hi ha algú?”, “qui sóc jo?”. El servidor de DHCP els contesta proporcionant-los tota la informació necessària perquè sàpiguen qui són i com han de configurar els seus paràmetres de xarxa.

#### Identificació dels equips de xarxa
Tot equip de xarxa necessita disposar: 
- Una adreça IP que l’identifica de manera única a la xarxa. 
- Una màscara per poder separar de l’adreça IP quina és la part d’adreça de xarxa i quina la part d’adreça de host.
- Una adreça de la porta d’enllaç predeterminada o passarel·la per defecte (o gateway), per disposar d’accés a xarxes externes.
- Adreces dels servidors DNS a consultar.

## El servei DHCP
El servei DHCP simplifica l’administració de la configuració dels equips de xarxa fent-la centralitzada, dinàmica i amb concessions per períodes de temps finits. 
Els principals avantatges d’utilitzar DHCP són: 
- Evitar conflictes d’adreces IP
- Estalviar temps i de feina. 

#### Configuració d'un equip de xarxa
Les opcions de configuració de xarxa es poden assignar a cada equip estàticament o es poden configurar de manera dinàmica utilitzant DHCP. 

#### Tipus d'assignacions d'adreces IP

Quan l’adreça IP i els altres paràmetres necessaris de configuració de la xarxa es configuren equip per equip, manualment, es diu que tenen **IP estàtica**. 

Quan la configuració de xarxa d’un equip es rep per mitjà d’un servidor DHCP es diu que utilitza una adreça **IP dinàmica**. Aquesta adreça pot variar dins d’un interval d’adreces disponibles per al servidor DHCP o pot ser fixa. 

## El protocol DHCP i els seus components
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

## Intervals, exclusions, concessions i reserves
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

