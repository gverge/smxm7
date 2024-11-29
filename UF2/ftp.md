# Transmissió d'arxius
### Índex de Contingut
- [El servei FTP](#punt1)
- [Mode Actiu i Passiu](#punt2)

<hr>

## El servei FTP <a name="punt1"></a>
FTP és l’acrònim de dynamic File Transfer Protocol, en català, Protocol de Transmissió d'Arxius. 

#### Què fa FTP?

A grans trets, **FTP permet l’accés i còpia d’arxius entre ordinadors** de forma interactiva i a qualsevol hora. Aquest servei es basa en l’establiment d’una connexió entre l’ordinador que actua com a client (l’ordinador des d’on s’executen les ordres FTP) i l’ordinador servidor , és a dir, l’ordinador al qual es connecten els diferents clients de forma remota.

Els servidors acostumen a utilitzar-lo sobre els **ports 20 i 21**. El **port 20** envia el **flux de dades** entre client i servidor, mentre que el **port 21** s'encarrega de transportar **el flux de control** i les ordres.

Quan s'està enviant informació pel canal de dades (port 20), **el canal de control es queda pausat**, fet que pot provocar **problemes** amb transferències de fitxers molt grans. Això és degut al fet que certs tallafocs donen per fet que ha passat massa temps des de l'última comanda i tanquen la connexió.

Altres dels seus desavantatges principals són que les **contrasenyes viatgen sense xifrar** i que s'utilitzen **moltes sessions TCP/IP** per a gestionar cadascuna de les seves funcionalitats. 

Si bé aquest servei ha tingut una gran difusió avui en dia ha estat superat per opcions més modernes, segures i eficients. El protocol HTTP/HTTPS s'ha fet tan popular que els serveis web han passat a reemplaçar als antics servidors FTP. Històricament els navegadors web a més d'HTTP/HTTPS han suportat el protocol FTP per poder interactuar amb els servidors existents però recentment els navegadors han començat a abandonar el suport per a FTP.

Avui no hi ha cap bona raó tècnica per muntar un nou servidor FTP a no ser que s'hagi d'interactuar amb serveis o equips que no suporten una altra alternativa.

Altres maneres més actuals de distribuir fitxes poden ser:
- Utilitzar un servei web amb HTTPS.
- Utilitzar les eines [scp](https://en.wikipedia.org/wiki/Secure_copy_protocol) o [sftp](https://en.wikipedia.org/wiki/Secure_file_transfer_program) d'OpenSSH.
- Eines de sincronizació com [rsync](https://en.wikipedia.org/wiki/Rsync) o [rclone](https://en.wikipedia.org/wiki/Rclone).
- Serveis de distribució de fitxes P2P:
  - [BitTorrent](https://es.wikipedia.org/wiki/BitTorrent)
  - [Syncthing](https://en.wikipedia.org/wiki/Syncthing)
  - [IPFS](https://en.wikipedia.org/wiki/InterPlanetary_File_System)
- Utilitzar un [object storage](https://en.wikipedia.org/wiki/Object_storage):
  - [MinIO](https://min.io/)
 
## Mode actiu i passiu <a name="punt2"></a>

Com hem vist, FTP necessita utilitzar múltiples ports de xarxa per establir les comunicacions i les diferents transferències de dades.

<p align=center><image src="https://ioc.xtec.cat/materials/FP/Recursos/fp_smx_m07_/web/fp_smx_m07_htmlindex/WebContent/u2/media/smxm7uf1ud2_im16.png" width=400 /></p>

Cal destacar que tant el port escollit per a la connexió de dades com la modalitat en què s’inicien les connexions depenen de l’opció que el client seleccioni per a la transferència de dades des del servidor d’arxius, això és, una transferència en mode **actiu o passiu**.

Quan establim una comunicació amb el servidor d’arxius en **mode actiu** s’estableixen dues connexions diferents on **el servidor estableix la connexió des del port 20 i un port aleatori sense privilegis al client**.

Atès que molts usuaris utilitzen tallafocs per protegir-se de possibles atacs provinents de la xarxa d’Internet, va sorgir el mode **passiu**. Cal destacar que els tallafocs presents en els ordinadors client tendeixen a rebutjar les connexions entrants des dels servidors d’arxius, ja que les interpreten com a processos desconeguts.

En el **mode passiu**, és l’ordinador **client qui inicia** la connexió adreçant-se al servidor d’arxius per sol·licitar l’accés a les dades i l’obertura dels ports corresponents per a l’establiment de les connexions de control i dades. En conseqüència, els tallafocs presents en els ordinadors client no detecten cap mena de connexió des de l’exterior que pugui ser considerada perillosa. 

**Mode Actiu (PORT)**
- El client obre el canal d'ordres a través del port 1500 (per exemple).
- Envia la comanda PORT per al port 21 del servidor.
- El servidor confirma la connexió del canal d'ordres.
- Obre el canal de dades al port 20 per al client al port 1501 (1500+1).
- El client confirma la connexió pel canal de dades.
- Els canals d'ordres i dades estan oberts i llestos per a la seva activitat (sempre i quan el Firewall ho permeti).

**Mode Passiu (PASV)**
- El client obre el canal d'ordres a través del port 1500 (per exemple).
- Envia l'ordre PASV al servidor dirigit al port 21.
- La comanda canvia la transmissió al mode passiu en el servidor.
- A través del canal d'ordres, el servidor envia al client el port que escoltarà el canal de dades, per exemple 2345.
- El client obre el canal de dades al port 1501 per al port 2345 del servidor.
- El servidor confirma la connexió del canal de dades.
- Els canals d'ordres i dades estan oberts i llestos per a la seva activitat.

## Regles IPtables
~~~
sudo iptables -t nat -A PREROUTING -p tcp --dport 21 -i enp0s8 -j DNAT --to 172.16.1.2
sudo iptables -t nat -A PREROUTING -m conntrack --ctstate NEW -p tcp --match multiport --dport 10000:10100 -i enp0s8 -j DNAT --to 172.16.1.2 (adreça del vostre server)
sudo su
iptables-save > /etc/iptables/rules.v4
~~~
