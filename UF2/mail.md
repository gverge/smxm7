# Correu electrònic
### Índex de Contingut
- [Els servei de Correu Electrònic](#punt1)
- [Recursos Correu Electrònic](#punt4)

<hr>
## El serveis de Correu Electrònic <a name="punt1"></a>

L’enviament i la recepció de correus electrònics comporta la intervenció de més d’un protocol d’aplicació i, en conseqüència, de més d’un tipus de servidor. Aquesta separació en funció de diferents protocols es basa en el fet que la comunicació via correu electrònic entre dos usuaris no és un procés client-servidor síncron. És a dir, quan un usuari envia un correu electrònic no té la certesa que el destinatari estigui connectat i, en conseqüència, el missatge haurà de ser emmagatzemat per poder ser llegit més endavant.

Dins d’aquesta comunicació asíncrona intervenen algunes funcionalitats ben diferenciades com són:
 - L’agent de transferència de correu (MTA, Mail Transfer Agent). Utilitzen el protocol simple de transferència de fitxers (SMTP, Simple Mail transfer Protocol) per comunicar-se amb altres agents de transferència de correu i amb els clients de correu electrònic. 
 - L’agent de lliurament de correu (MDA, Mail Delivery Agent). Són els “servidors de recollida de correu”, és a dir, la seva missió és copiar els missatges del servidor de correu a la bústia de correu de l’usuari. Considerant que aquests agents permetran als clients de correu la recol·lecció dels missatges emmagatzemats en bústies remotes, és possible afirmar que els protocols POP i IMAP són agents MDA.
 - L’agent d’usuari de correu (MUA, Mail User Agent). Són programes que executen l’usuari per la lectura del correu entrant, o bé, enviar missatges de correu electrònic. Es comuniquen amb els agents de transferència de correu (MTA) mitjançant el protocol simple de transferència de correu. Aquests programes són els anomenats clients de correu i posseeixen una interfície que faciliten a l’usuari l’edició, recepció i enviament de correus electrònics. A mode d’exemple, podríem destacar clients de correu com l’Outlook, Pegasus, Thunderbird, Eudora, etc. 

<img src="https://ioc.xtec.cat/materials/FP/Recursos/fp_smx_m07_/web/fp_smx_m07_htmlindex/WebContent/u2/media/smxm7uf1ud2_im46.png" alt="" style="">


## Recursos Correu Electrònic<a name="punt4"></a>

- [Enviar correus des de Terminal](https://www.digitalocean.com/community/tutorials/send-email-linux-command-line)

