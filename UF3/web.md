# Gestió de servidors WEB
### Índex de Contingut
- [El protocol HTTP](#punt1)
- [El servidor Apache](#punt2)
  - [Autenticació d'accés bàsic amb HTTP](#punt2.1)
  - [Diversos llocs amb la directiva VirtualHost](#punt2.2)
  - [Web segura amb HTTPS](#punt2.3)
  - [Rendiment amb HTTP/2](#punt2.4)
  - [Apache com a proxy invers](#punt2.5)
  
<hr>

Les pàgines web, en la seva majoria en format **HTML**, requereixen ser allotjades en màquines que disposin d’espai en disc per emmagatzemar arxius HTML, imatges, blocs de codi o arxius de vídeo en directoris específics i, alhora, han de ser capaces d’entendre tot tipus d’extensió dels arxius que són enviats en tots dos sentits de la comunicació. 

## El protocol HTTP <a name="punt1"></a>
El **protocol de transferència d’hipertext (HTTP)** és un protocol client-servidor força senzill que articula els intercanvis d’informació entre els clients web i els servidors HTTP. 
Des del punt de vista de les comunicacions, està suportat en els serveis de connexió TCP/IP i funciona de la mateixa manera que la resta de serveis propis dels entorns UNIX.
Tècnicament, un procés servidor escolta en un port de comunicacions TCP (per defecte, el 80) i espera les sol·licituds de connexió dels clients web. Una vegada establerta la connexió, el protocol TCP s’encarrega de mantenir la comunicació i garantir un intercanvi de dades lliure d’errors. 

El protocol de transferència d’hipertext es basa en operacions senzilles de sol·licitud/resposta. Quan un client estableix una connexió amb un servidor i envia un missatge amb les dades de la sol·licitud, el servidor respon amb un missatge similar, que conté l’estat de l’operació i el seu resultat possible. Totes les operacions poden adjuntar un objecte o recurs sobre el qual actuen; cada objecte web (document HTML, arxiu multimèdia o aplicació CGI) és conegut pel seu **localitzador uniforme de recursos (URL, uniform resource locator)**. Els recursos poden ser arxius, el resultat de l’execució d’un programa, una consulta a una base de dades, la traducció automàtica d’un document, etc. 

HTTP és un protocol sense estat, és a dir, no guarda cap informació sobre connexions anteriors. El desenvolupament d’aplicacions web freqüentment necessita mantenir estat. Per això s’utilitzen les **galetes (cookies)**, és a dir, la informació que un servidor pot emmagatzemar en el sistema client. Això permet que les aplicacions web institueixin la noció de “sessió”, i, alhora, permet rastrejar usuaris, ja que les galetes es poden emmagatzemar en el client durant un temps indeterminat. 

### Etapes d'una transmissió HTTP

Cada cop que un client fa una petició a un servidor, s’executa un ventall d’etapes:
<ol>
<li>Un usuari accedeix a una adreça d’Internet (URL) seleccionant un enllaç d’un document HTML o introduint-la directament a la barra de navegació d’un navegador web des de la perspectiva del client web.</li>
<li>El client web descodifica l’adreça d’Internet (URL) separant-ne les diferents parts. És així com s’identifiquen el protocol d’accés, l’adreça del servidor de noms de domini (DNS, Domain Name Server) o d’Internet (IP) del servidor, el possible port opcional (el valor per defecte és el 80) i l’objecte del servidor requerit.</li>
<li>S’obre una connexió TCP/IP amb el servidor cridant el port TCP corresponent. Es fa la petició. En conseqüència, s’envien l’ordre necessària (GET, POST, HEAD, etc.), l’adreça de l’objecte requerit (el contingut de l’adreça d’Internet del servidor), la versió del protocol HTTP utilitzada (en la major part de les ocasions és HTTP/1.0) i un conjunt variable d’informació que inclou dades sobre les capacitats del navegador web, dades opcionals per al servidor, etc.</li>
<li>El servidor retorna la resposta al client. Aquesta resposta consisteix en un codi d’estat i el tipus de dada amb extensions multipropòsit de correu d’Internet (MIME, Multipurpose Internet Mail Extension) de la informació de tornada, seguit de la mateixa informació.</li>
<li>Es tanca la connexió TCP.</li>
</ol>

### L’estàndard HTTP/1.0

L’estàndard HTTP/1.0 recull, únicament, tres ordres que representen les operacions de recepció, enviament de la informació i revisió de l’estat.
- **Comanda GET**. S’utilitza per recollir qualsevol tipus d’informació del servidor. Sempre que premem sobre un enllaç o escrivim una adreça d’Internet a la barra de navegació d’un navegador web, estem utilitzant aquesta comanda. Com a resultat, el servidor HTTP envia el document corresponent a l’adreça d’Internet seleccionada o bé activa un mòdul CGI que, al seu torn, generarà la informació de tornada.
- **Comanda HEAD**. Sol·licita informació sobre un objecte (arxiu) com, per exemple, la seva grandària, el tipus, la data de modificació, etc. Acostuma a ser utilitzat pels gestors de memòries cau de pàgines o pels servidors intermediaris (proxy server) per conèixer quan cal actualitzar la còpia que es manté d’un arxiu determinat.
- **Comanda POST**. S’utilitza per enviar informació al servidor com, per exemple, les dades contingudes en un formulari. El servidor passarà aquesta informació a un procés encarregat del seu tractament (acostuma a ser una aplicació CGI). L’operació que es durà a terme amb la informació proporcionada dependrà de l’adreça d’Internet (URL) utilitzada, principalment, en els formularis.

## El servidor Apache <a name="punt2"></a>

L’Apache és un servidor web flexible, ràpid i eficient, contínuament actualitzat i adaptat als nous protocols HTTP/1.1. De la gran quantitat de característiques destaquen:

- És multiplataforma.
- Segueix els protocols estàndard internacionals.
- És modular, és a dir, es pot adaptar a diferents entorns i necessitats que poden activar només aquelles parts que són necessàries. També disposa d’una interfície de programació d’aplicacions (API, application programming interface) de mòduls que permet que els usuaris puguin programar els seus propis mòduls específics.
- Es desenvolupa de manera oberta i es retroalimenta d’idees noves, registres (logs) d’errades i pegats per a la resolució dels problemes que vagin sorgint.
- És extensible, ja que gràcies a la seva característica modular s’han desenvolupat diverses extensions com, per exemple, les de programació (PHP, Python, Java, Perl, etc.), que es corresponen amb un llenguatge de programació que treballa simultàniament amb el servidor i que permeten connectar amb múltiples bases de dades (Oracle, MySQL, PostgreSQL, Adabas, etc.).

El servidor web Apache es troba als repositoris de qualsevol distribució que es preï i normalment aquesta és la manera recomanada per a la seva instal·lació.

Per exemple a Ubuntu es pot instal·lar així:
~~~
apt update 
apt install apache2
~~~
La instal·lació activa el servei i l'encén amb la configuració per defecte que escolteu al port 80 de totes les interfacesde xarxa.

El servidor publicarà a la web el contingut del directori DocumentRootque per defecte és **``/var/www/html``** i únicament conté una pàgina per comprovar que tot funciona bé.

Al directori **``/var/log/apache2``** es troben els següents fitxers de registre:
- **``access.log``** : Fitxer de registre d'accessos. Contindrà una línia per cada accés al servidor web per al VirtualHost per defecte.
- **``other_vhosts_access.log``**: Fitxer de registre d'accessos per a altres VirtualHosts. És possible indicar el fitxer de registre a utilitzar a la definició del VirtualHost.
- **``error.log``**: Fitxer de registre derrors. Aquí Apache HTTP Server mostrarà informació sobre els problemes que trobeu.

### Configuració d'Apache HTTP Server

La configuració del servidor depèn de la distribució de GNU/Linux que s'utilitzi. A Debian —i derivats com Ubuntu— està dividida en diferents fitxers a l'interior de **``/etc/apache2``**. Evidentment el fitxer principal de configuració és **``apache2.conf``**. Aquest fitxer conté valors globals de configuració per a tot el servidor amb uns valors per defecte molt raonables, a més inclou altres fitxers de configuració com són:

- **``ports.conf``**: Aquí s'indiquen els ports on escoltarà el servidor. Per defecte 80 TCPper HTTPi 443 TCPper HTTPS.
- Directori **``conf-available``**: Conté fragments per a la configuració global del servidor.
- Directori **``conf-enabled``**: Enllaços als fragments del directori actual que estan actius.
- Directori **``mods-available``**: Conté els mòduls —i la configuració— que estan instal·lats.
- Directori **``mods-enabled``**: Enllaços als mòduls del directori anterior que estan actius.
- Directori **``sites-available``**: Declaració de VirtualHosts.
- Directori **``sites-enabled``**: Enllaços als llocs actius.

### Eines per a la gestió de la configuració: **``a2enconf``**, **``a2enmod``**, **``a2ensite``**

La configuració de l'Apache HTTP Server desa els fragments per a la configuració general, els mòduls i els llocs (VirtualHosts) en directoris diferenciats. Per a cadascun d'aquests elements s'utilitzen dos directoris, un guarda tots els elements disponibles i l'altre conté enllaços simbòlics per a aquells elements actius.

Durant l'arrencada Apache HTTP Server llegirà tots els fragments que estiguin a l'interior dels directoris *-enabled/i els tindrà en compte durant la configuració.

Encara que és possible gestionar aquests enllaços de manera manual, hi ha unes eines específiques que creen o eliminen aquests enllaços (activant o desactivant la configuració, el mòdul o el lloc que correspongui).

Aquestes eines són:

- **``a2enconf—a2disconf``**: Habilita, o desactiva, el fragment de configuració indicat.
- **``a2enmod—a2dismod``**: Habilita, o deshabilita, el mòdul indicat.
- **``a2ensite—a2dissite``**: Habilita, o deshabilita, el lloc indicat.

Per exemple, per habilitar el VirtualHost default-ssl.confque publica continguts amb HTTPS només cal fer:

1. Activar el mòdul SSL que necessita HTTPS:a2enmod ssl
2. Activar el lloc:a2ensite default-ssl
3. Demaneu a Apache HTTP Server que torneu a carregar la configuració:systemctl reload apache2

Després es podrà accedir mitjançant HTTPS al servidor.

### Mòduls d'Apache HTTP Server

Les funcions d'Apache HTTP Server estan implementades en diferents mòduls que es poden habilitar o deshabilitar segons convingui. En general no convé tenir actius mòduls que no siguin necessaris ja que augmentarà el consum de recursos i estarem exposant funcions potser sense configurar degudament.

La instal·lació bàsica del paquet apache2 ja ha instal·lat diversos mòduls al directori **``/etc/apache2/mods-available``** i n'ha activat alguns amb enllaços simbòlics al directori **``/etc/apache2/mods-enabled``**.

Molts mòduls tenen el seu propi fitxer de configuració on s'especifiquen els paràmetres generals de funcionament. D'aquests mòduls en podem destacar:
<table border="1" class="vertical listing" style="border-collapse: collapse; width: 95%; height: 169px;">
            <tbody>
             <tr style="height: 21px;">
              <td style="width: 23%; height: 21px;"><strong><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Mòdul</font></font></strong></td>
              <td style="width: 75%; height: 21px;"><strong><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Funció</font></font></strong></td>
             </tr>
             <tr style="height: 21px;">
              <td style="width: 23%; height: 21px;"><code><a href="https://translate.google.com/website?sl=es&amp;tl=ca&amp;hl=ca&amp;client=webapp&amp;u=https://httpd.apache.org/docs/2.4/mod/mod_alias.html">mod_alias</a></code></td>
              <td style="width: 75%; height: 21px;"><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Mapeja peticions a fitxers o directoris en disc. Utilitzant aquest mòdul es pot, per exemple, fer que les peticions web que continguin </font></font><code>/documents</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">no vagin al directori </font></font><code>documents</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">dins del </font></font><code>DocumentRoot</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">sinó a un altre directori.</font></font></p><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Exemple:</font></font><code>Alias "/documents" "/mnt/documents"</code></p><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">També permet </font></font><a href="https://translate.google.com/website?sl=es&amp;tl=ca&amp;hl=ca&amp;client=webapp&amp;u=https://httpd.apache.org/docs/2.4/mod/mod_alias.html%23redirect" title="Apache: Directiva redirect"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">redireccions HTTP</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;"> .</font></font></p><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Exemple:</font></font><code>Redirect "/service" "https://172.16.0.26/"</code></p></td>
             </tr>
             <tr style="height: 21px;">
              <td style="width: 23%; height: 21px;"><code><a href="https://translate.google.com/website?sl=es&amp;tl=ca&amp;hl=ca&amp;client=webapp&amp;u=https://httpd.apache.org/docs/2.4/mod/mod_auth_basic.html">mod_auth_basic</a></code></td>
              <td style="width: 75%; height: 21px;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Proporciona </font></font><a href="https://translate.google.com/website?sl=es&amp;tl=ca&amp;hl=ca&amp;client=webapp&amp;u=https://es.wikipedia.org/wiki/Autenticaci%25C3%25B3n_de_acceso_b%25C3%25A1sica" title="Wikipedia: Autenticació d'accés bàsica."><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">autenticació d'accés bàsica</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;"> . Permet sol·licitar usuari i contrasenya per accedir a alguns recursos i a la pràctica s'ha de combinar amb </font></font><code>HTTPS</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">.</font></font></td>
             </tr>
             <tr style="height: 21px;">
              <td style="width: 23%; height: 21px;"><code><a href="https://translate.google.com/website?sl=es&amp;tl=ca&amp;hl=ca&amp;client=webapp&amp;u=https://httpd.apache.org/docs/2.4/mod/mod_autoindex.html">mod_autoindex</a></code></td>
              <td style="width: 75%; height: 21px;"><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Genera llistats de fitxers per a aquells directoris que no tenen una pàgina </font></font><code>index.html</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">.</font></font></p><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Aquest mòdul és molt convenient per publicar tot un arbre de fitxers per on pugui navegar l'usuari.</font></font></p></td>
             </tr>
             <tr style="height: 21px;">
              <td style="width: 23%; height: 21px;"><code><a href="https://translate.google.com/website?sl=es&amp;tl=ca&amp;hl=ca&amp;client=webapp&amp;u=https://httpd.apache.org/docs/2.4/mod/mod_deflate.html">mod_deflate</a></code></td>
              <td style="width: 75%; height: 21px;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Implementa un filtre per a la sortida de l'Apache HTTP Server que comprimeix la informació abans de transmetre-la.</font></font></td>
             </tr>
             <tr style="height: 21px;">
              <td style="width: 23%; height: 21px;"><code><a href="https://translate.google.com/website?sl=es&amp;tl=ca&amp;hl=ca&amp;client=webapp&amp;u=https://httpd.apache.org/docs/2.4/mod/mod_dir.html">mod_dir</a></code></td>
              <td style="width: 75%; height: 21px;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Permet configurar quines pàgines es consideraran un índex per al directori.</font></font></td>
             </tr>
             <tr style="height: 21px;">
              <td style="width: 23%; height: 21px;"><code><a href="https://translate.google.com/website?sl=es&amp;tl=ca&amp;hl=ca&amp;client=webapp&amp;u=https://httpd.apache.org/docs/2.4/mod/event.html">mpm_event</a></code></td>
              <td style="width: 75%; height: 21px;"><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">És el mòdul de multiprocessament d'alt rendiment utilitzat a GNU/Linux. Les connexions dels clients són ateses per processos fills que contenen un pool de fils.</font></font></p></td>
             </tr>
             <tr style="height: 21px;">
              <td style="width: 23%; height: 21px;"><code><a href="https://translate.google.com/website?sl=es&amp;tl=ca&amp;hl=ca&amp;client=webapp&amp;u=https://httpd.apache.org/docs/2.4/mod/mod_status.html">mod_status</a></code></td>
              <td style="width: 75%; height: 21px;"><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Si s'activa amb:</font></font></p><pre class="prettyprint lang-config prettyprinted"><span class="pun"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">&lt; </font></font></span><span class="tag"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Location </font></font></span><font style="vertical-align: inherit;"><span class="str"><font style="vertical-align: inherit;">"/server-status" </font></span><span class="pun"><font style="vertical-align: inherit;">&gt; </font></span><span class="kwd"><font style="vertical-align: inherit;">SetHandler </font></span><span class="pln"><font style="vertical-align: inherit;">server-status </font></span><span class="pln"><font style="vertical-align: inherit;">    Require ip 192.168.0.0/16 </font></span><span class="pun"><font style="vertical-align: inherit;">&lt;/ </font></span><span class="tag"><font style="vertical-align: inherit;">Location </font></span><span class="pun"><font style="vertical-align: inherit;">&gt;</font></span></font><span class="pln"> </span><span class="str"><font style="vertical-align: inherit;"></font></span><span class="pun"><font style="vertical-align: inherit;"></font></span><span class="pln">
    </span><span class="kwd"><font style="vertical-align: inherit;"></font></span><span class="pln"><font style="vertical-align: inherit;"></font><br><font style="vertical-align: inherit;"></font></span><span class="pln">
</span><span class="pun"><font style="vertical-align: inherit;"></font></span><span class="tag"><font style="vertical-align: inherit;"></font></span><span class="pun"><font style="vertical-align: inherit;"></font></span></pre><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Permet obtenir informació sobre el funcionament d'Apache en visitar </font></font><code>/server-status</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">des de la xarxa </font></font><code>192.168.0.0/16</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">.</font></font></p></td>
             </tr>
            </tbody>
           </table>
           
### Autenticació d'accés bàsic amb HTTP <a name="punt2.1"></a>

Apache HTTP Server pot protegir amb autenticació daccés bàsic HTTP alguns recursos. Així és possible, per exemple, demanar usuari i contrasenya per accedir al contingut de cert directori i permetre l'accés únicament en el cas d'algun usuari concret o d'un grup.

**L'autenticació bàsica d'HTTP transmet l'usuari i la contrasenya com a text clar així que a la pràctica sempre s'ha de combinar amb HTTPS.**

Per posar en marxa l'autenticació bàsica convé crear un fitxer de contrasenyes on es registraran els usuaris i contrasenyes. Aquest fitxer es gestiona amb l'ordre **``htpasswd``** i hauria d'estar fora de qualsevol **DocumentRoot** per evitar que el servidor el publiqui a la web.

Per exemple, per crear el fitxer de contrasenyes **``/var/www/passwords``** i desar-hi l'usuari:
~~~
root@apache:/var/www# htpasswd -c passwords usuari1
New password: 
Re-type new password: 
Adding password per a usuari1 
~~~

Un cop creat el fitxer es podran afegir més usuaris en tornar a executar **``htpasswd``** sense utilitzar l'argument **``-c``** que només serveix per crear un fitxer nou (o esborrar els continguts d'un fitxer existent).

Després podreu utilitzar una directiva **``<Directory>``** per canviar la configuració d'accés als continguts d'un directori. Per exemple, per protegir amb autenticació bàsica el directori **``/var/www/html/secret``**, es pot afegir la configuració següent per al **VirtualHost** d'un lloc web i recarregar la configuració.
~~~
...
< Directory "/var/www/html/secret" >
            AuthType Basic
            AuthName "Fitxers restringits"
            AuthBasicProvider file
            AuthUserFile "/var/www/passwords"
            Require usuari1 usuari2
</ Directory > 
~~~

‼️ **Si nencessiteu agrupar usuaris recordeu habilitar el mòdul d'autorització de grups ``authz_groupfile``** i afegir les directives apropiades a ``Directory``:
~~~
	AuthGroupFile "/var/www/groups"
	Require group usersgroup
~~~
La directiva Require també ens permetrà entre altres funcions:
- Utilitzar **``Require valid-user``** per acceptar qualsevol usuari del fitxer de contrasenyes.
- Especificar una llista d'usuaris vàlids: **``Require user usuari1 usuari2 usuari3``**
- Filtrar per una adreça (IP, fragment d'IP o llista d'IPs): **``Require ip 192.168.0.50``**

També és possible utilitzar un fitxer on es poden declarar grups d'usuaris a conveniència (groups).

### Diversos llocs amb la directiva **``VirtualHost``** <a name="punt2.2"></a>

Un servidor Apache HTTP pot publicar diferents llocs web, cadascun amb el seu propi DocumentRoot i configuració. A l'interior d'un **VirtualHost** s'hereten els valors de configuració global, però és possible redefinir de manera particular aquells que hagin de canviar.

Els **VirtualHost** poden ser:
- Basats en IP: atenen en una adreça IP diferent per a cada VirtualHost
- Basat en nom: són més flexibles perquè no cal afegir més adreces al servidor web, Apache HTTP Server utilitzarà el **VirtualHost** adequat basant-se en el nom que va utilitzar el navegador per arribar fins al servidor. Funcionen a Internet gràcies al DNS però en un entorn de desenvolupament es poden utilitzar "substituint" el DNS per entrades del fitxer /etc/hosts tant al servidor com al client.

Normalment VirtualHostes defineixen en un fitxer propi del directori sites-available/i s'activen amb l'ordre a2ensitequan és necessari.

La definició d'un **VirtualHost** basat en nom senzill pot ser:
~~~
<VirtualHost *:80> 
    ServerName test.smx2.cat
    DocumentRoot /var/www/html/testsmx
</VirtualHost>
~~~
La directiva **``ServerName``** s'utilitza per indicar el domini per al qual respondrà aquest lloc, i **``*:80``** indica el port on escoltarà (de qualsevol interfície).


### Web segura amb HTTPS <a name="punt2.3"></a>

Actualment qualsevol servei web ha de permetre connexions amb la seguretat que proporciona el protocol **HTTPS**. Aquest protocol empra **TLS** per xifrar la informació que es transmet per la xarxa utilitzant **criptografia asimètrica**. Al servidor s'ha d'instal·lar el certificat web que podrà ser autogenerat o generat per una autoritat de certificació reconeguda per evitar alertes als clients.

Durant la instal·lació d'**Apache HTTP Server** s'ha generat un certificat autosignat per provar HTTPS. Es pot veure el seu ús a **``sites-available/default-ssl.conf``**.

Aquí es mostra el contingut del fitxer sense comentaris:
~~~
<IfModule mod_ssl.c>
 	<VirtualHost _default_:443 >
		ServerAdmin webmaster@localhost
		DocumentRoot /var/www/html
		ErrorLog ${APACHE_LOG_DIR}/error.log
		CustomLog ${APACHE_LOG_DIR}/access.log combined
		SSLEngine on
		SSLCertificateFile /etc/ssl/certs/ssl-cert-snakeoil.pem 
		SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key
		<FilesMatch "\.(cgi|shtml|phtml|php)$">
	            	SSLOptions +StdEnvVars
                        </FilesMatch> 
                        <Directory /usr/lib/cgi-bin> 
			SSLOptions +StdEnvVars 
		</Directory> 
	</VirtualHost> 
</IfModule>
~~~
Les directives més rellevants són:

- **``<IfModule mod_ssl.c>``**: Es la condició per tal que la resta de configuració sigue vigent, cal que el mòdul ``mod_ssl`` estigui actiu.
- **``<VirtualHost _default_:443 >``**: El servidor esperarà les connexions dels clients al port 443.
- **``SSLEngine on``**: Activa el suport SSL/TLS.
- **``SSLCertificateFile /etc/ssl/certs/ssl-cert-snakeoil.pem``**: Certificat autogenerat pel paquet ssl-cert.
- **``SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key``**: Clau privada del certificat anterior generada pel paquet  ssl-cert.

La definició d'un **VirtualHost** HTTPS senzill pot ser:
~~~
<VirtualHost *:443>
        SSLEngine on
        SSLCertificateFile      /etc/ssl/certs/ssl-cert-snakeoil.pem
        SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key
        ServerName test.smx2.cat
        ServerAdmin webmaster@smx2.cat
        DocumentRoot /var/www/html/testsmx
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
~~~
Aquesta configuració la podem afegir als fitxers de lloc ja creats per a HTTP, així podem respondre peticions amb els dos protocols.

‼️ **Per tal que el nostre servidor permti connexions SSL/TLS pel port 443 hem d'habilitar el modul SSL.**

### Rendiment amb HTTP/2 <a name="punt2.4"></a>

**HTTP/2** és la darrera versió del protocol HTTP i introdueix molts canvis orientats a aconseguir un millor rendiment. La nova versió del protocol està disponible combinada amb TLS i sense **TLS**, però els navegadors només accepten la versió xifrada. Així que a la pràctica, per a un servidor web, utilitzar HTTP/2 vol dir utilitzar també TLS.

Els noms que reben aquests protocols són:
- **h2**: versió del protocol xifrada amb TLS.
- **h2c**: versió del protocol en text clar sense xifrar.

Naturalment el servidor web Apache HTTP Server suporta la darrera versió del protocol HTTP ( h2 i h2c). Per utilitzar h2 serà necessari:
- Tenir activat el mòdul **``mod_ssl``**.
- Activar el mòdul **``mod_http2``**.
- Utilitzar la directiva **``Protocols``** per indicar els protocols suportats. Per exemple, afegint la següent línia al nostre VirtualHost:
~~~
<VirtualHost _default_:443 >
	...
        Protocols h2 http/1.1 #Permetem atendre amb HTTP/2 xifrat els clients que el suportin i amb HTTP/1.1 els que no ho suportin.
	...
</VirtualHost>
~~~
Podem comprovar el correcte funcionament del nostre servidor amb HTTP/2 utilitzant la següent comanda:
~~~
curl -vso /dev/null --http2 https://smx2.cat --insecure
~~~
El resultat esperat ha d'incloure entre la resta de línies:
~~~
* Trying 192.168.56.10:443
...
* Connected to smx2.cat (192.168.56.10) port 443 (#0)
* ALPN, offering h2  						# El client ofereix iniciar la comunicació amb HTTP/2
* ALPN, offering http/1.1					# o HTTP/1.1
* ...
* SSL connection using TLSv1.3 / TLS_AES_256_GCM_SHA384
* ALPN, server accepted to use h2				# El servidor accepta HTTP/2
...
* Using HTTP2, server supports multiplexing
* Connection state changed (HTTP/2 confirmed)
~~~
### Apache com a proxy invers <a name="punt2.5"></a>

Quan Apache funciona com un servidor intermediari invers sembla el servidor que publica els continguts però, en realitat, cada petició rebuda es trasllada a un altre servidor web —ocult per als clients— que torna el contingut sol·licitat.

La raó més habitual per tenir un servidor intermediari invers és la seguretat, per exemple per publicar amb HTTPS o HTTP/2 una aplicació web interna que només ofereix HTTP.

Una altra raó pot ser millorar el rendiment en balancejar les peticions dels clients entre un grup de servidors interns.

El mòdul bàsic per fer de proxy és **``mod_proxy``**. Però aquest mòdul necessitarà que també s'activi **``mod_proxy_http``** si es contactarà amb el servidor intern amb HTTP/0.9, HTTP/1.0 o HTTP/1.1 i **``mod_proxy_http2``** si s'utilitzarà HTTP/2.

Per exemple, suposant que volem preparar un VirtualHost per servir amb HTTP/2 i HTTPS una aplicació web HTTP/1.1 que es troba al servidor 172.16.0.19 podríem utilitzar aquesta configuració bàsica:
~~~
<VirtualHost *:443>
	ServerAdmin webmaster@localhost
        Protocols h2 http/1.1
	SSLEngine on
	SSLCertificateFile /etc/ssl/certs/ssl-cert-snakeoil.pem
	SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key
	ProxyPass "/" "http://172.16.0.19/"
	ProxyPassReverse "/" "http://172.16.0.19/" 
</VirtualHost>
~~~
Encara que depenent de l'aplicació poden ser necessaris altres ajustaments.
