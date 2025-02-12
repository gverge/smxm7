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

## El servidor Apache <a name="punt1"></a>

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
La instal·lació activa el servei i l'encén amb la configuració per defecte que escolteu al port 80de totes les interfacesde xarxa.

El servidor publicarà a la web el contingut del directori DocumentRootque per defecte és /var/www/htmli únicament conté una pàgina per comprovar que tot funciona bé.

Al directori /var/log/apache2es troben els següents fitxers de registre:
- access.log: Fitxer de registre d'accessos. Contindrà una línia per cada accés al servidor web per al VirtualHost per defecte.
- other_vhosts_access.log: Fitxer de registre d'accessos per a altres VirtualHosts. És possible indicar el fitxer de registre a utilitzar a la definició del VirtualHost.
- error.log: Fitxer de registre derrors. Aquí Apache HTTP Server mostrarà informació sobre els problemes que trobeu.

