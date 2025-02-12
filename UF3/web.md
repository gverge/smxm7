# Gestió de servidors WEB
### Índex de Contingut
- [El protocol HTTP](#punt1)
- [Mode Actiu i Passiu](#punt2)

<hr>

Les pàgines web, en la seva majoria en format **HTML**, requereixen ser allotjades en màquines que disposin d’espai en disc per emmagatzemar arxius HTML, imatges, blocs de codi o arxius de vídeo en directoris específics i, alhora, han de ser capaces d’entendre tot tipus d’extensió dels arxius que són enviats en tots dos sentits de la comunicació. 

## El protocol HTTP <a name="punt1"></a>
El **protocol de transferència d’hipertext (HTTP)** és un protocol client-servidor força senzill que articula els intercanvis d’informació entre els clients web i els servidors HTTP. 
Des del punt de vista de les comunicacions, està suportat en els serveis de connexió TCP/IP i funciona de la mateixa manera que la resta de serveis propis dels entorns UNIX.
Tècnicament, un procés servidor escolta en un port de comunicacions TCP (per defecte, el 80) i espera les sol·licituds de connexió dels clients web. Una vegada establerta la connexió, el protocol TCP s’encarrega de mantenir la comunicació i garantir un intercanvi de dades lliure d’errors. 

El protocol de transferència d’hipertext es basa en operacions senzilles de sol·licitud/resposta. Quan un client estableix una connexió amb un servidor i envia un missatge amb les dades de la sol·licitud, el servidor respon amb un missatge similar, que conté l’estat de l’operació i el seu resultat possible. Totes les operacions poden adjuntar un objecte o recurs sobre el qual actuen; cada objecte web (document HTML, arxiu multimèdia o aplicació CGI) és conegut pel seu **localitzador uniforme de recursos (URL, uniform resource locator)**. Els recursos poden ser arxius, el resultat de l’execució d’un programa, una consulta a una base de dades, la traducció automàtica d’un document, etc. 

HTTP és un protocol sense estat, és a dir, no guarda cap informació sobre connexions anteriors. El desenvolupament d’aplicacions web freqüentment necessita mantenir estat. Per això s’utilitzen les **galetes (cookies)**, és a dir, la informació que un servidor pot emmagatzemar en el sistema client. Això permet que les aplicacions web institueixin la noció de “sessió”, i, alhora, permet rastrejar usuaris, ja que les galetes es poden emmagatzemar en el client durant un temps indeterminat. 





