# DNS : Resolució de Noms

### Índex de Contingut
- [El Servei DNS](#punt1)
- [Instal·lació del servidor DNS a Ubuntu Server](#punt2)
- [Transports DNS](#punt2.1)

<hr>

## El Servei DNS <a name="punt1"></a>

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

<p align=center><img width=500 src="https://ioc.xtec.cat/materials/FP/Recursos/fp_smx_m07_/web/fp_smx_m07_htmlindex/WebContent/u1/media/smxm7uf1ud1_im6.png"/></p>

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

Podem comprovar aquesta informació realitzant una consulta amb la comanda ``host -a insebre.cat`` 

### Resolució de noms al client
Quan volem comunicar-nos amb un host del que coneixem el seu FQDN (per exemple moodle.iesebre.com), el primer que fem fer és obtenir l'adreça IP associada amb el nom de domini. Per això, depenent del contingut del fitxer ``/etc/host.conf`` es consulta el fitxer local ``/etc/hosts`` o bé es consulta als servidors DNS.

Exemple de fitxer/etc/host.conf
~~~
 **order hosts,bind** 
 multi on 
 ...
~~~

El fitxer ``/etc/hosts`` pot contenir una llista d'adreces (una per línia) amb els noms respectius, el que ens permet resoldre en local (tot i que les seves limitacions son clares). Podem assignar nous noms de domini o fins i tot emmascarar-ne altres-

En canvi, quan el client DNS s'utilitza per obtenir l'adreça IP d'un nom de domini cal examinar el fitxer de configuració ``/etc/resolv.conf`` per obtenir:

- La llista de servidors DNS a utilitzar (un per línia precedit per la directiva nameserver)
- El domini a utilitzar per a les consultes que no són un FQDN indicat per la directiva search

#### Servei ``systemd-resolved`` i ordre ``resolvectl``

La majoria de les distribucions actuals de GNU/Linux utilitzen systemdaixí que solen executar el servei systemd-resolvedcom a stub DNS local de la màquina. L'avantatge d'utilitzar systemd-resolvedés que les aplicacions trobaran un millor rendiment gràcies a la memòria cau.

Un stub DNS reenvia totes les consultes que no tenen en memòria cau a un servidor DNS recursiu.

La comanda ``resolvectl`` (o ``systemd-resolve``) permet:
- Mostrar informació sobre la configuració: ``resolvectl status``
- Mostra estadístiques sobre els encerts de memòria cau: ``resolvectl statistics``
- Mostra els DNS utilitzats: ``resolvectl dns``
- Fer resolucions DNS: ``resolvectl query ca.wikipedia.org`` o ``systemd-resolve ca.wikipedia.org``
- Esborrar la cau DNS: ``resolvectl flush-caches`` o ``systemd-resolve --flush-caches``

Altres ordres per a realitzar consultes son ``host``. ``nslookup`` o ``dig``.

## Instal·lació del servidor DNS <a name="punt2"></a>

**``BIND``** és el programari més utilitzat com a servidor DNS a Internet, com així ho demostra que ho estiguin executant la majoria dels servidors arrel .

Aquest servei es pot instal·lar mitjançant:
~~~
apt install bind9
~~~
Un cop instal·lats els seus fitxers de configuració es trobaran al directori **``/etc/bind``**. Entre aquests fitxers destaca el fitxer de configuració principal ``named.conf`` que únicament serveix per incloure: 

- ``named.conf.options``, on configurem les opcions del servidor, com els servidors de reenviament o la configuació d'escolta (listen).
- ``named.conf.local``, on podem declarar les nostres zones.
--
  ~~~
  zone "ies.net" IN {
	 type master;
	 file "/etc/bind/db.ies.net";
	 };
  zone "10.168.192.in-addr.arpa" IN {
	 type master;
	 file "/etc/bind/db.192.168.10";
	 }; 
  ~~~
  - La la directiva ``zone`` indica el domini o l'adreça de xarxa a les zones inverses.
  - La directiva ``type`` indica si és una zona mestra (escrita per l'administrador) o una esclava (descarregada automàticament d'un servidor mestre).
  - La directiva ``file`` indican el fitxer on s'inlcouran els registres de la zona.

- ``named.conf.default-zones``. Amb zones per defecte com localhost o in-addr.arpa

### Arxiu de dades per a una zona directa

Com hem vist, cada zona necessita un fitxer de dades on desar els registres. Com en l'exemple anterior:
~~~
$ORIGIN ies.net.
$TTL	604800
@	IN	SOA	ies.net. root.ies.net. (
			      1		; Serial
			 604800		; Refresh
			  86400		; Retry
			2419200		; Expire
			 604800 )	; Negative Cache TTL
;
@       IN	NS	ns.ies.net.
@       IN	A	192.168.10.3
~~~
- El caràcter ``@`` equival al domini que s'estigui definint (ies.net.).
- El camp ``root.ies.net.`` correspon al correu de contacte per indicar errors a la zona.
- És important incrementar el valor ``Serial`` cada cop que es fa una modificació.
- En aquesta zona només declarem el servidor de noms.

### Arxiu de dades per a una zona inversa
També cal definir una zona inversa per al domini:
~~~
$TTL 604800
@ IN SOA 10.168.192.in-addr.arpa. root.ies.net.  (
                        1
                        10800
                        3600
                        604800
                        60 )

@ IN NS ns.ies.net.
3 IN PTR ns.ies.net.
~~~
- La ``@`` substitueix a 10.168.192.in-addr.arpa.
- S'associen les IP amb noms de domini.
- Les adreces que no acaben en punt com ``3`` s'interpreten com ``3.10.168.192.in-addr.arpa.``.

#### Capçaleres dels arxius de zona

Es tracti d'una zona directa o d'una zona inversa, el fitxer ha de començar amb una capçalera que especifica alguns paràmetres de configuració.

- TTL ( Time To Live ) període de validesa per a la informació continguda a la zona, passat el temps s'ha de refrescar o actualitzar.
- Registre SOA ( Start Of Authority ), que conté:

<table border="1" class="listing" style="border-collapse: collapse; width: 100%;">
            <tbody>
             <tr>
              <td style="width: 20.4011%;"><strong><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Camp</font></font></strong></td>
              <td style="width: 79.599%;"><strong><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Funció</font></font></strong></td>
             </tr>
             <tr>
              <td style="width: 20.4011%;"><code>serial-number</code></td>
              <td style="width: 79.599%;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">S'ha d'incrementar amb cada modificació del fitxer de zona, així els servidors esclaus poden detectar els canvis als fitxers de zona.</font></font></td>
             </tr>
             <tr>
              <td style="width: 20.4011%;"><code>time-to-refresh</code></td>
              <td style="width: 79.599%;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Temps que els servidors esclaus (o secundaris) deixen passar abans de consultar el servidor mestre (o principal) si ha passat algun canvi a la zona.</font></font></td>
             </tr>
             <tr>
              <td style="width: 20.4011%;"><code>time-to-retry</code></td>
              <td style="width: 79.599%;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Temps que l'esclau deixa passar abans de tornar a intentar una transferència de zona.</font></font></td>
             </tr>
             <tr>
              <td style="width: 20.4011%;"><code>time-to-expire</code></td>
              <td style="width: 79.599%;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Si un servidor esclau no aconsegueix actualitzar les seves zones mitjançant la transferència de zona corresponent, passat aquest temps ha de deixar de considerar vàlida la informació de la zona.</font></font></td>
             </tr>
            </tbody>
           </table>

#### Tipus de registres DNS

Als fitxers de zona s'utilitzen registres per declarar els recursos que coneix el servidor DNS. Aquests registres estan especialitzats de manera que hi ha un tipus de registre DNS diferent per a cada recurs. Alguns dels tipus de registre bàsics són:

<table border="1" class="listing" style="border-collapse: collapse; width: 100%;">
            <tbody>
             <tr>
              <td style="width: 10.4426%;"><strong><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Registre</font></font></strong></td>
              <td style="width: 89.5574%;"><strong><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Funció</font></font></strong></td>
             </tr>
             <tr>
              <td style="width: 10.4426%;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">A</font></font></td>
              <td style="width: 89.5574%;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">IPV4, traducció directa de nom a adreça</font></font></td>
             </tr>
             <tr>
              <td style="width: 10.4426%;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">AAAA</font></font></td>
              <td style="width: 89.5574%;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">IPV6, traducció directa de nom a adreça</font></font></td>
             </tr>
             <tr>
              <td style="width: 10.4426%;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">PTR</font></font></td>
              <td style="width: 89.5574%;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Punter, traducció inversa de direcció a nom</font></font></td>
             </tr>
             <tr>
              <td style="width: 10.4426%;"><a class="external-link" href="https://translate.google.com/website?sl=es&amp;tl=ca&amp;hl=ca&amp;client=webapp&amp;u=https://es.wikipedia.org/wiki/MX_%2528registro%2529" target="_self" title=""><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">MX</font></font></a></td>
              <td style="width: 89.5574%;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Mail eXchanger, intercanviador de correu per a un domini</font></font></td>
             </tr>
             <tr>
              <td style="width: 10.4426%;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">NS</font></font></td>
              <td style="width: 89.5574%;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Name Server, identifica els servidors d'una zona, permet delegar subdominis</font></font></td>
             </tr>
             <tr>
              <td style="width: 10.4426%;"><a class="external-link" href="https://translate.google.com/website?sl=es&amp;tl=ca&amp;hl=ca&amp;client=webapp&amp;u=https://en.wikipedia.org/wiki/CNAME_record" target="_self" title=""><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">CNAME</font></font></a></td>
              <td style="width: 89.5574%;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Canonical Name, permet definir noms alternatius</font></font></td>
             </tr>
             <tr>
              <td style="width: 10.4426%;"><a class="external-link" href="https://translate.google.com/website?sl=es&amp;tl=ca&amp;hl=ca&amp;client=webapp&amp;u=https://en.wikipedia.org/wiki/SRV_record" target="_self" title=""><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">SRV</font></font></a></td>
              <td style="width: 89.5574%;">
               <dl>
                <dd><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">
                 Service, permet </font></font><a href="https://translate.google.com/website?sl=es&amp;tl=ca&amp;hl=ca&amp;client=webapp&amp;u=http://bulma.net/body.phtml?nIdNoticia%3D1334%26nIdPage%3Dlast%23localizaciondeservicios" target="_self"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">declarar serveis</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;"> . D'aquesta manera, un client pot descobrir de manera automàtica els serveis que estan disponibles en un domini.
                </font></font></dd>
               </dl></td>
             </tr>
             <tr>
              <td style="width: 10.4426%;"><a class="external-link" href="https://translate.google.com/website?sl=es&amp;tl=ca&amp;hl=ca&amp;client=webapp&amp;u=https://en.wikipedia.org/wiki/TXT_Record" target="_self" title=""><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">TXT</font></font></a></td>
              <td style="width: 89.5574%;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Text permet associar un text arbitrari amb un nom de domini.</font></font></td>
             </tr>
            </tbody>
           </table>

### Rèpliques mestre - esclau
Es convenient muntar diverses màquines amb autoritat per mantenir una zona. En aquest cas l'administrador configurarà manualment el fitxer de zona en una (el servidor mestre) i els servidors esclaus realitzaran una transferència de zona descarregant les dades des del mestre.

Per exemple, al servidor mestre es pot declarar una zona tal que:
~~~
	zone "domini.prova" {
             type master;
             file "domini.prova.hosts";
             allow-transfer { <IPs dels servidors esclaus>; };
        };
~~~
I als servidors esclaus:
~~~
        zone "domini.prova" {
             type slave;
             file "domini.prova.hosts";
             màsters { <IP de Servidor-A>; }; 
             masterfile-format text;
        };
~~~
La directiva ``masterfile-format text`` és opcional però recomanable ja que utilitzar un fitxer de zona en format text a l'esclau facilita comprovar les dades descarregades.
Per suposat a l'arxiu de zona convindrà declarar amb registres NS tots els servidors (siguin mestres o esclaus).

### Transports DNS <a name="punt2.1"></a>

El 1983 el protocol DNS va aparèixer utilitzant el protocol de transport UDP i el port 53. Encara avui dia la majoria de les consultes al DNS es fan utilitzant UDP però hi ha altres transports alternatius. El principal d'ells és TCP  que també utilitza el port 53.

Aquestes dues versions clàssiques de DNS no incorporen cap mesura de seguretat ni xifratge. Per això han aparegut altres alternatives que xifren el protocol DNS per oferir seguretat.

- **DoT**( DNS over TLS ) utilitza el port 853.
- **DoH**( DNS over HTTPS ) utilitza el port 443.

D'aquestes dues opcions **DoH** està guanyant ràpidament suport entre els navegadors. Per exemple, Firefox avui dia utilitza per defecte **DoH** amb el servidor de Cloudflare 1.1.1.1 encara que és possible configurar un altre servidor.

La comanda **`dig`** ens permetrà fer consultes **DoH** des de l'intèrpret d'ordres:

`# dig +https @1.1.1.1 www.iesebre.com`

#### DNS over HTTPS (DoH) amb BIND

Per activar el suport **DoH** a BIND necessitarem un certificat. Normalment s'utilitzarà un certificat d'una autoritat de certificació reconeguda (com Let's Encrypt ) perquè els clients ho acceptin sense problemes. Però també es podrà utilitzar un certificat autosignat si al navegador (o client utilitzat) s'hi afegeix l'excepció de seguretat corresponent.

Podem crear un certificat autosignat:

`openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/bind/bind-selfsigned.key -out /etc/bind/bind-selfsigned.crt`

Canviem el propietari dels fitxers perquè BIND els pugui llegir:

`chown bind /etc/bind/bind-selfsigned.*`

Editeu el fitxer de configuració `named.conf.options` per afegir el bloc **tls** abans del bloc options.
~~~
tls local-tls { 
   key-file "/etc/bind/bind-selfsigned.key"; 
   cert-file "/etc/bind/bind-selfsigned.crt"; 
};
~~~
I afegir a l'interior del bloc `options` les línies:
~~~
// Volem la IP dels clients que fan consultes
querylog yes;
// Configurem l'accés DoH
listen-on port 443 tls local-tls http default {any;};
~~~
Abans de configurar el DNS al navegador serà convenient visitar l'adreça https://<dirección ip>/ per trobar l'advertiment que s'utilitza un certificat autosignat i acceptar-lo. Finalment es podrà configurar al navegador el DoH utilitzant la URL: `https://<dirección ip>/dns-query`



