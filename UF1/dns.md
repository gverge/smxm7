# DNS : Resolució de Noms

### Índex de Contingut
- [El Servei DNS]()

<hr>

## El Servei DNS

El sistema de noms de domini DNS (domain name system) proporciona un mecanisme eficaç per fer la resolució de noms de domini a adreces IP.

El servei DNS proporciona independència del nom de domini respecte a la IP. Així un domini pot canviar d’IP de manera transparent per als usuaris del domini. Fins i tot és usual que un domini s’identifiqui amb més d’una IP com a mesura de redundància contra la caiguda del sistema o com a balanceig de càrregues. 

Altres serveis proporcionats pel DNS són la identificació dels servidors de correu d’un domini, de cada un dels hosts que pertanyen a la xarxa, servidors d’impressió, etc.

### Sistemes de noms plans i jeràrquics

**Un sistema de noms pla** es basa en la utilització d’un fitxer de text que descriu cada host amb la seva corresponent adreça IP. Es pot usar per definir àlies per equips locals en xarxes petites, però no és escalable a xarxes grans i molt menys a Internet. 

Un exemple d'aquest tipus de sistems es **``/etc/hosts``**.

En una xarxa petita es pot generar un fitxer amb el nom i identificador IP de tots els hosts, centralitzat en un servidor, i encarregar-se de distribuir còpies d’aquest fitxer a tots els equips de la xarxa. Però aquest model de coneixement no és escalable.

**El domain name system (DNS) aporta una solució escalable i pràctica**. El DNS es basa en una base de dades de noms de domini **jeràrquica i distribuïda**. **Jeràrquica** perquè s’organitza en una estructura de dominis que es poden compondre de subdominis que també es poden dividir en subdominis i així fins a 127 nivells (originàriament). Aquests dominis són gestionats per **servidors DNS responsables de cada zona**. És una base de dades **distribuïda** perquè la informació no està tota junta en un sol repositori central, sinó que la informació es troba repartida per parts en els servidors DNS d’Internet. Cada servidor DNS autoritari conté la base de dades de la seva zona. 

#### Elements del sistema de noms de domini

El servei DNS es basa en una estructura jeràrquica de noms en forma d’arbre on l’arrel és el node o **domini arrel** del qual deriven tots els altres nodes. Aquest es divideix en **altres dominis**(TLD) com, per exemple, .com, .edu, .org, .cat, etc. Al seu torn, cada domini es pot dividir en **altres subdominis** i així successivament. Les rutes s’indiquen començant pel subdomini més intern cap al node arrel (pc1.informatica.insebre.cat).

Un **domini** és el node indicat i tota la resta de l’arbre que penja d’aquest node.S’entén per espai de noms el conjunt de tots els dominis que formen l’arbre DNS. 



El sistema de noms de domini d’Internet DNS utilitza els elements següents:

- **Espai de noms**. El conjunt de tots els dominis (l’arbre).
- **Domini**. Text identificatiu d’un domini.
- **FQDN**. Nom de domini absolut començant pel node i acabant en l’arrel.
- **Domini absolut**. (FQDN) Els dominis absoluts acaben en punt (.).
- **Domini relatiu**. Nom de domini sense qualificar.
- **Domini arrel**. Domini del qual deriven tots els altres. S’indica amb un punt o amb la cadena buida.

Un domini absolut o FQDN és el que inclou tots els nodes des del domini fins a l’arrel (inclosa en forma de punt final). Un domini relatiu no inclou l’arrel i pot ser relatiu al domini actual.

#### Els noms de domini d'Internet

El node arrel es va dividir en un conjunt de subdominis anomenats TLD (top level domains o dominis d’alt nivell). Aquests dominis eren com, edu, gov, mil, org, net i int. Posteriorment se’n van afegir d’altres. Es volien organitzar els dominis per funcionalitat (.com, .org), es va trobar la necessitar de poder agrupar dominis de manera geogràfica  i van sorgit els famosos identificadors de país. 

Els **servidors arrel** són crucials per al funcionament del DNS, ja que coneixen tots els dominis de primer nivell. 

### Zones primàries/secundàries i mecanisme de resolució

Els servidors de noms DNS són els programes que emmagatzemen i gestionen la informació en la base de dades d’una part de l’espai de noms anomenada zona. 

#### Les zones

**Una zona** és part de l’espai de noms de domini gestionada per un (o més) servidors DNS. Els servidors que gestionen la zona tenen informació completa sobre la zona i es diu que tenen autoritat respecte a ella. De bon principi podríem pensar que un servidor DNS gestiona un domini i que una zona és el mateix que un domini, però això no ha de ser necessàriament així.

Un **domini** es divideix en subdominis per facilitar-ne l’administració, i cada part administrada per un (o més) servidor DNS és una **zona**.

<img width=500 src="https://ioc.xtec.cat/materials/FP/Recursos/fp_smx_m07_/web/fp_smx_m07_htmlindex/WebContent/u1/media/smxm7uf1ud1_im6.png"/>

Convé tenir clar en tot moment que domini i zona no són equivalents (tot i que poden coincidir).

- El domini és l’arbre de l’espai de noms.
- La zona és la part de l’arbre administrada per un servidor DNS concret.
- La base de dades de zona la formen els fitxers que emmagatzemen la descripció dels equips que pertanyen a la zona.
- La delegació consisteix a passar l’autoritat de la gestió d’un subdomini a una altra entitat.

**Delegar** l’administració d’un subdomini no és més que passar **l’autoritat** sobre aquest subdomini a una altra entitat (a uns altres servidors DNS). Aquesta entitat és la responsable de l’administració de la zona delegada. Té tota l’autoritat per fer i desfer al seu criteri. La zona pare perd el control administratiu de la zona delegada i simplement apunta als servidors de noms de la zona delegada per obtenir informació quan la requereix.

L’estàndard que defineix el DNS estableix que cal configurar dos o més servidors autoritaris per a cada zona anomenats servidor primari i servidor secundari.

Els **servidors primari** i **secundari** són autoritat. Només el primari té els fitxers de zona. El secundari n’obté una còpia per transferència. 

#### La resolució de noms

El mecanisme de resolució de noms DNS consta d’un client o resolver que realitzarà les consultes (o querys) a resoldre a uns servidors DNS. 

Si el servidor disposa de la informació perquè forma part de la base de dades de la seva zona, emetrà una resposta **autoritativa**. Si disposa de la resposta perquè la té emmagatzemada temporalment (en un procés anomenat cache) també emetrà la resposta però aquest cop de manera **no autoritativa**. Si no té informació del domini buscat, el servidor pot fer a altres servidors la mateixa consulta en un procés que pot ser **recursiu o iteratiu**. Sempre existeix un camí per trobar el domini buscat, que és preguntar als nodes arrel (root servers) de l’espai de noms de domini. Partint dels nodes arrel i recorrent l’arbre cap avall, es pot arribar al domini buscat, si és que existeix. 

*Quina adreça IP té el domini ns1.ioc.cat?*

*Si un estudiant australià intenta esbrinar això des del seu servidor de noms de Sindney, probablement acabarà preguntant a un   dels nodes arrel per aquest domini. El node arrel desconeix el host ns1 del domini de l’IOC, però sí que coneix tots els dominis de primer nivell (TLD). Proporcionarà una llista amb els servidors de noms del domini .cat. Preguntant a algun d’aquests servidors (del domini .cat) s’obtindrà la llista de servidors DNS del dominiioc.cat. Preguntant als servidors d’aquest domini s’obtindrà l’adreça IP del host ns1 pel qual el domini ioc.cat és autoritari (forma part de la seva zona).*

#### Recursió i Iteració

En mode **iteratiu** el servidor retorna la millor resposta possible basada en la seva informació local, sense preguntar a ningú més. En el mode **recursiu** el servidor intenta trobar la resposta preguntant a tants altres servidors com calgui per tal d’obtenir-la. 

El client **resolver** fa una consulta **recursiva** al seu servidor DNS local. Si el servidor DNS disposa de la resposta, la torna . Pot ser de la seva zona i serà una resposta autoritativa o pot tenir-la en cache i serà no autoritativa.

Si no disposa de la resposta, consulta **iterativament** altres servidors apropant-se al domini buscat. Cada servidor que consulta iterativament li pot proporcionar la resposta (autoritativa o no) si la coneix, o una llista de servidors DNS autoritatius per al domini indicat.

La **resolució inversa** permet obtenir el nom de domini a què correspon una adreça IP. Aquest mecanisme, es basa en un domini especial anomenat IN-ADDR.ARPA. Hi ha protocols de xarxa que requereixen una resolució inversa correcta per funcionar bé i sovint s’utilitza com a mesura de seguretat per verificar l’existència de l’adreça IP en un domini. 

### El protocol DNS

El protocol DNS és usualment **UDP**, però pot ser **TCP i UDP**. Es tracta d’un protocol de capa d’aplicació i utilitza el **port 53**.

 Els apartats que componen un missatge DNS són:

- **HEADER**. Capçalera del missatge indicant si és una consulta o una resposta. Conté l’id (identificador) del missatge, flags i un resum de quines seccions del missatge porten informació i quanta.
- **QUESTION**. Aquesta secció conté la consulta que s’ha efectuat. És a dir, quina dada s’ha demanat al servidor. Pot ser una resolució d’adreça IP a un domini, demanar la llista de servidors d’impressió, etc.
- **ANSWER**. Secció que conté la resposta obtinguda del servidor. S’entén que aquesta secció conté la resposta no autoritativa. A vegades en les utilitats de consulta aquesta secció es mostra com a non-autority answer.
- **AUTHORITY**. Aquesta secció conté les respostes que són autoritatives per a la consulta efectuada. Evidentment pot ser buida.
- **ADITIONAL**. Conté informació addicional per completar la resposta. En l’exemple s’observa que completa la resolució dels noms de màquina que hi ha a la secció asnwer tot indicant la seva adreça IP corresponent.





