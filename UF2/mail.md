# Correu electrònic
### Índex de Contingut
- [Els serveis de Correu Electrònic](#punt1)
- [Protocols i serveis de descàrrega de correu](#punt2)
- [Recursos Correu Electrònic](#punt3)

<hr>

## El serveis de Correu Electrònic <a name="punt1"></a>

L’enviament i la recepció de correus electrònics comporta la intervenció de més d’un protocol d’aplicació i, en conseqüència, de més d’un tipus de servidor. Aquesta separació en funció de diferents protocols es basa en el fet que la comunicació via correu electrònic entre dos usuaris no és un procés client-servidor síncron. És a dir, quan un usuari envia un correu electrònic no té la certesa que el destinatari estigui connectat i, en conseqüència, el missatge haurà de ser emmagatzemat per poder ser llegit més endavant.

Dins d’aquesta comunicació asíncrona intervenen algunes funcionalitats ben diferenciades com són:
 - **L’agent de transferència de correu (MTA, Mail Transfer Agent)**. Utilitzen el protocol simple de transferència de fitxers (SMTP, Simple Mail transfer Protocol) per comunicar-se amb altres agents de transferència de correu i amb els clients de correu electrònic. 
 - **L’agent de lliurament de correu (MDA, Mail Delivery Agent)**. Són els “servidors de recollida de correu”, és a dir, la seva missió és copiar els missatges del servidor de correu a la bústia de correu de l’usuari. Considerant que aquests agents permetran als clients de correu la recol·lecció dels missatges emmagatzemats en bústies remotes, és possible afirmar que els protocols POP i IMAP són agents MDA.
 - **L’agent d’usuari de correu (MUA, Mail User Agent)**. Són programes que executen l’usuari per la lectura del correu entrant, o bé, enviar missatges de correu electrònic. Es comuniquen amb els agents de transferència de correu (MTA) mitjançant el protocol simple de transferència de correu. Aquests programes són els anomenats clients de correu i posseeixen una interfície que faciliten a l’usuari l’edició, recepció i enviament de correus electrònics. A mode d’exemple, podríem destacar clients de correu com l’Outlook, Pegasus, Thunderbird, Eudora, etc. 

<p align=center><img src="https://ioc.xtec.cat/materials/FP/Recursos/fp_smx_m07_/web/fp_smx_m07_htmlindex/WebContent/u2/media/smxm7uf1ud2_im46.png" width=400></p>


## Protocols i serveis de descàrrega de correu <a name="punt2"></a>

Considerant que el correu electrònic es basa en l’arquitectura client-servidor on el servidor ofereix un recurs de qualsevol tipus a l’altre (el client) perquè aquest pugui obtenir un profit o avantatge, podem afirmar que és en l’equip servidor on s’allotjaran els diferents comptes d’usuari i mitjançant els clients de correu es gestionarà la descàrrega dels missatges.

Amb la intenció d’aprofundir en la gestió del correu electrònic haurem de conèixer, principalment, quins són els protocols de correu electrònic existents i, alhora, els més utilitzats.

- **Protocol simple de transferència de correu (SMTP, Simple Mail Transfer Protocol)**. Es tracta de l’estàndard d’Internet adreçat a l’intercanvi de correus electrònics. La seva funció és la transmissió del correu electrònic que surt de la màquina de l’usuari remetent fins al servidor que emmagatzema els missatges dels usuaris destinataris.

Tècnicament, quan l’usuari remetent envia un missatge, aquest és adreçat al seu servidor de correu. A continuació, des del servidor s’enviarà el missatge al servidor de correu de l’usuari destinatari. I, finalment, l’usuari destinatari descarregarà el correu a la seva bústia a la màquina local.

Val a dir que és important que aquest protocol estigui dotat de certes eines d’autenticació per estalviar l’enviament i recepció de correu no desitjat o correu brossa (spam).
    
- **Protocol d’oficina de correus (POP, Post Office Protocol)**. Aquest tipus de protocol fa referència al tipus de servidor que s’utilitzarà per rebre els correus electrònics. De fet, el protocol d’oficina de correus permet la gestió, accés i transferència de missatges de correu electrònic entre el servidor i el client de correus electrònics. A diferència del protocol simple de transferència de correu (SMTP, Simple Mail Transfer Protocol), el protocol d’oficina de correus (POP, Post Office Protocol) s’utilitza per a la descàrrega de correus electrònics.

 L’origen del protocol d’oficina de correus (POP, Post Office Protocol) és determinat per la necessitat de molts usuaris de consultar els seus correus electrònics mitjançant la connexió puntual a Internet per, en conseqüència, connectar amb el seu servidor de correu i descarregar els missatges. Un cop descarregat els missatges, el protocol d’oficina de correus (POP,Post Office Protocol) també facilita la desconnexió amb el servidor.
    
- **Protocol d’accés a missatges d’Internet (IMAP, Internet Message Access Protocol)**. És mitjançant aquest protocol que els clients de correu electrònic poden accedir als missatges emmagatzemats en els servidors de correu des de qualsevol màquina amb accés a Internet.

 Normalment, el protocol d’accés a missatges d’Internet (IMAP, Internet Message Access Protocol) és utilitzat pels servidors i pels clients de correu electrònic via web. Aquests tipus de servidors i clients faciliten als usuaris la gestió de les seves bústies de correu electrònic des de qualsevol ordinador amb accés a Internet.

 Val a dir que el protocol d’accés a missatges d’Internet (IMAP, Internet Message Access Protocol) es diferencia del protocol d’oficina de correus (POP, Post Office Protocol) en el fet que els correus electrònics estan emmagatzemats en el servidor i es queden allí fins i tot si es descarreguen el cas més típic són els servidors de correu electrònic via web. Per contra, en el protocol d’oficina de correus (POP, Post Office Protocol) comporta que els correus electrònics es descarreguin en l’ordinador del client directament i no quedin emmagatzemats en el servidor.


## Recursos Correu Electrònic<a name="punt3"></a>

- [Enviar correus des de Terminal](https://www.digitalocean.com/community/tutorials/send-email-linux-command-line)

