# Gestió de servidors WEB
### Índex de Contingut
- [El protocol HTTP](#punt1)
- [El servidor Apache](#punt2)

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
<table border="1" class="vertical listing" style="border-collapse: collapse; width: 100%; height: 169px;">
            <tbody>
             <tr style="height: 21px;">
              <td style="width: 23.0502%; height: 21px;"><strong><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Mòdul</font></font></strong></td>
              <td style="width: 76.9498%; height: 21px;"><strong><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Funció</font></font></strong></td>
             </tr>
             <tr style="height: 21px;">
              <td style="width: 23.0502%; height: 21px;"><code><a href="https://translate.google.com/website?sl=es&amp;tl=ca&amp;hl=ca&amp;client=webapp&amp;u=https://httpd.apache.org/docs/2.4/mod/mod_alias.html">mod_alias</a></code></td>
              <td style="width: 76.9498%; height: 21px;"><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Mapeja peticions a fitxers o directoris en disc. Utilitzant aquest mòdul es pot, per exemple, fer que les peticions web que continguin </font></font><code>/ficheros</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">no vagin al directori </font></font><code>ficheros</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">dins del </font></font><code>DocumentRoot</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">sinó a un altre directori.</font></font></p><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Exemple:</font></font><code>Alias "/ficheros" "/mnt/ficheros"</code></p><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">També permet </font></font><a href="https://translate.google.com/website?sl=es&amp;tl=ca&amp;hl=ca&amp;client=webapp&amp;u=https://httpd.apache.org/docs/2.4/mod/mod_alias.html%23redirect" title="Apache: Directiva redirect"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">redireccions HTTP</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;"> .</font></font></p><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Exemple:</font></font><code>Redirect "/service" "https://172.16.0.26/"</code></p></td>
             </tr>
             <tr style="height: 21px;">
              <td style="width: 23.0502%; height: 21px;"><code><a href="https://translate.google.com/website?sl=es&amp;tl=ca&amp;hl=ca&amp;client=webapp&amp;u=https://httpd.apache.org/docs/2.4/mod/mod_auth_basic.html">mod_auth_basic</a></code></td>
              <td style="width: 76.9498%; height: 21px;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Proporciona </font></font><a href="https://translate.google.com/website?sl=es&amp;tl=ca&amp;hl=ca&amp;client=webapp&amp;u=https://es.wikipedia.org/wiki/Autenticaci%25C3%25B3n_de_acceso_b%25C3%25A1sica" title="Wikipedia: Autenticació d'accés bàsica."><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">autenticació d'accés bàsica</font></font></a><font style="vertical-align: inherit;"><font style="vertical-align: inherit;"> . Permet sol·licitar usuari i contrasenya per accedir a alguns recursos ia la pràctica s'ha de combinar amb </font></font><code>HTTPS</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">.</font></font></td>
             </tr>
             <tr style="height: 21px;">
              <td style="width: 23.0502%; height: 21px;"><code><a href="https://translate.google.com/website?sl=es&amp;tl=ca&amp;hl=ca&amp;client=webapp&amp;u=https://httpd.apache.org/docs/2.4/mod/mod_autoindex.html">mod_autoindex</a></code></td>
              <td style="width: 76.9498%; height: 21px;"><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Genera llistats de fitxers per a aquells directoris que no tenen una pàgina </font></font><code>index.html</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">.</font></font></p><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Aquest mòdul és molt convenient per publicar tot un arbre de fitxers per on podeu navegar l'usuari.</font></font></p></td>
             </tr>
             <tr style="height: 21px;">
              <td style="width: 23.0502%; height: 21px;"><code><a href="https://translate.google.com/website?sl=es&amp;tl=ca&amp;hl=ca&amp;client=webapp&amp;u=https://httpd.apache.org/docs/2.4/mod/mod_deflate.html">mod_deflate</a></code></td>
              <td style="width: 76.9498%; height: 21px;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Implementa un filtre per a la sortida de l'Apache HTTP Server que comprimeix la informació abans de transmetre-la.</font></font></td>
             </tr>
             <tr style="height: 21px;">
              <td style="width: 23.0502%; height: 21px;"><code><a href="https://translate.google.com/website?sl=es&amp;tl=ca&amp;hl=ca&amp;client=webapp&amp;u=https://httpd.apache.org/docs/2.4/mod/mod_dir.html">mod_dir</a></code></td>
              <td style="width: 76.9498%; height: 21px;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Permet configurar quines pàgines es consideraran un índex per al directori.</font></font></td>
             </tr>
             <tr style="height: 21px;">
              <td style="width: 23.0502%; height: 21px;"><code><a href="https://translate.google.com/website?sl=es&amp;tl=ca&amp;hl=ca&amp;client=webapp&amp;u=https://httpd.apache.org/docs/2.4/mod/event.html">mpm_event</a></code></td>
              <td style="width: 76.9498%; height: 21px;"><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">És el mòdul de multiprocessament d'alt rendiment utilitzat a GNU/Linux. Les connexions dels clients són ateses per processos fills que contenen un pool de fils.</font></font></p><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">És una evolució que </font></font><code>mpm_worker</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">al seu torn va ser una evolució del clàssic </font></font><code>mpm_prefork</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">.</font></font></p></td>
             </tr>
             <tr style="height: 21px;">
              <td style="width: 23.0502%; height: 21px;"><code><a href="https://translate.google.com/website?sl=es&amp;tl=ca&amp;hl=ca&amp;client=webapp&amp;u=https://httpd.apache.org/docs/2.4/mod/mod_status.html">mod_status</a></code></td>
              <td style="width: 76.9498%; height: 21px;"><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Si s'activa amb:</font></font></p><pre class="prettyprint lang-config prettyprinted"><span class="pun"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">&lt; </font></font></span><span class="tag"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Location </font></font></span><font style="vertical-align: inherit;"><span class="str"><font style="vertical-align: inherit;">"/server-status" </font></span><span class="pun"><font style="vertical-align: inherit;">&gt; </font></span><span class="kwd"><font style="vertical-align: inherit;">SetHandler </font></span><span class="pln"><font style="vertical-align: inherit;">server-status </font></span><span class="pln"><font style="vertical-align: inherit;">    Requereix ip 192.168.0.0/16 </font></span><span class="pun"><font style="vertical-align: inherit;">&lt;/ </font></span><span class="tag"><font style="vertical-align: inherit;">Location </font></span><span class="pun"><font style="vertical-align: inherit;">&gt;</font></span></font><span class="pln"> </span><span class="str"><font style="vertical-align: inherit;"></font></span><span class="pun"><font style="vertical-align: inherit;"></font></span><span class="pln">
    </span><span class="kwd"><font style="vertical-align: inherit;"></font></span><span class="pln"><font style="vertical-align: inherit;"></font><br><font style="vertical-align: inherit;"></font></span><span class="pln">
</span><span class="pun"><font style="vertical-align: inherit;"></font></span><span class="tag"><font style="vertical-align: inherit;"></font></span><span class="pun"><font style="vertical-align: inherit;"></font></span></pre><p><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Permet obtenir informació sobre el funcionament d'Apache en visitar </font></font><code>/server-status</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">des de la xarxa </font></font><code>192.168.0.0/16</code><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">.</font></font></p></td>
             </tr>
            </tbody>
           </table>
           


