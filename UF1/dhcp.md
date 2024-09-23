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

![](https://ioc.xtec.cat/materials/FP/Recursos/fp_smx_m07_/web/fp_smx_m07_htmlindex/WebContent/u1/media/smxm7uf1ud1_im1.png){width='100px'}
