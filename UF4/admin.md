# Administració Remota
### Índex de Contingut
- [El protocol SSH](#punt1)
- [Wireguard VPN](#punt2)

## Wireguard VPN <a name="punt2"></a>
<p align=center><img src="https://elpuig.xeill.net/Members/vcarceler/articulos/introduccion-a-wireguard/2000px-logo_of_wireguard-svg.png" width=600 background-color="white"></p>

Una VPN (Virtual Private Network o xarxa privada virtual) recrea mitjançant programari i xifrat una xarxa que no existeix a nivell físic però que permet comunicar de manera segura als equips que hi participen.
<p align=center><img src="https://elpuig.xeill.net/Members/vcarceler/articulos/introduccion-a-wireguard/1280px-vpn_overview-en-svg.png/@@images/2d6a706b-3787-450c-b03a-e51df2c12422.png" width=600 background-color="white"></p>


El trànsit de la VPN acaba transmetent-se pels adaptadors de xarxa i la infraestructura física que comunica els equips, però abans de transmetre aquest trànsit es xifra i s'encapsula en paquets que el transporten fins a la destinació. A la destinació, quan es reben les dades, es desxifren i es recomponen els paquets virtuals transmesos per la VPN.

Així, a l'esquema s'aprecia que els dos ordinadors ja estan connectats a la xarxa física (de color negre) però en executar una VPN sembla que, a més de la interfície física, els ordinadors tenen una altra interfície de xarxa amb un enllaç privat (de color vermell) per a ells.

### Característiques de WireGuard

WireGuard és una eina lliure i moderna per implementar VPNs. L'eina clàssica de GNU/Linux per implementar VPN ha estat OpenVPN, una eina modular i molt completa. WireGuard no aspira a substituir OpenVPN en tots els modes de funcionament, aspira a facilitar la creació de VPNs amb bona seguretat i rendiment.

En utilitzar WireGuard: 

- S'utilitza UDP com a transport. 
- Un equip WireGuard que accepti connexions ha de tenir obert un port UDP i una IP coneguda. 
- Es fa servir criptografia de clau pública. Cada equip tindrà dues claus: la privada i la pública. La clau privada no ha de sortir mai de l'equip on es va generar, la clau pública s'haurà de portar als altres equips que vulguin participar a la VPN. 
- L'equip client que connecti amb el servidor no necessita tenir cap IP pública ni cap port obert i pot estar darrere de NAT. 
- WireGuard no s'encarrega d'assignar adreces a la VPN, ni repartir les claus, ni de cap altra tasca que no sigui mantenir el túnel.

### Instal·lació

WireGuard està disponible als repositoris de les principals distribucions de GNU/Linux i també es pot instal·lar en altres sistemes operatius.

La instal·lació a Ubuntu es pot realitzar amb una ordre:

```# apt install wireguard```

Un cop instal·lat es podrà utilitzar l'eina wg.

### Configuració d'una VPN entre dos equips

La configuració més bàsica consta de dos equips: un que actua com a servidor (i té una IP i un port UDP obert) i un altre que actua com a client. A partir d'aquesta configuració es podran afegir nous clients que es connectin al servidor perquè tots els equips participin a una mateixa VPN o bé definir noves interfícies de xarxa per mantenir diferents VPNs.

La configuració del servidor inclourà: 

- Una secció ```Interface``` amb: 
  - La clau privada a utilitzar. 
  - L'adreça IP de la interfície a la VPN. 
  - El port on s'acceptaran connexions dels clients. 
- La declaració d'un ```Peer``` (el client) amb: 
  - La clau pública del client. 
  - La directiva ```AllowedIPs``` amb la llista d'adreces que podeu utilitzar el parell o que es poden assolir a través del parell.

La configuració del client inclourà: 

- Una secció ```Interface``` amb: 
  - La clau privada a utilitzar. 
  - L'adreça IP de la interfície a la VPN. 
- La declaració d'un ```Peer``` (el servidor) amb: 
  - La clau pública del servidor 
  - La directiva ```AllowedIPs``` amb la llista d'adreces que podeu utilitzar el parell o que es poden assolir a través del parell. 
  - La directiva Endpoint indicant l'adreça IP i el port UDP on atén el servidor. 
  - Possiblement la directiva ```PersistentKeepalive``` per enviar un paquet a través de la VPN cada cert nombre de segons quan no hi ha activitat per evitar que els tallafocs i els encaminadors intermedis tanquin la connexió.

### Creació d'un parell de claus a cada equip

Cada equip a la VPN utilitzarà dues claus: pública i privada.

Aquestes claus es poden generar amb la següent ordre:

```wg genkey | tee privatekey | wg pubkey > publickey```

Després d'executar l'ordre s'hauran creat els fitxers: 

- ```privatekey``` amb la clau privada. 
- ```publickey``` amb la clau pública.

En realitat, com es pot veure, la generació de les claus es fa en dos passos: 

1. S'utilitza wg genkey per generar la clau privada. 
2. S'utilitza wg pubkey per derivar la clau pública a partir de la clau privada.

Les claus es representen mitjançant una cadena de 45 caràcters alfanumèrics. Idealment les claus es poden generar a cada equip, de manera que la clau privada no s'hagi de transportar mai per la xarxa ja que únicament s'utilitzarà a l'equip on es va crear. La clau pública s'haurà d'utilitzar al fitxer de configuració de l'altre equip.

### Configuració al servidor

Un cop instal·lat ```wireguard``` es podrà accedir al directori ```/etc/wireguard``` per crear al seu interior el parell de claus.

Després es podrà editar el fitxer ```/etc/wireguard/wg0.conf``` per a que contingui:
```
[Interface]
# IP de la interfíci wg0
Address = 192.168.6.1/24
 
# Port d'escolta al server
ListenPort = 41194
 
# Clau privada del servidor
PrivateKey = hshshsh...

[Peer]
# Clau Pública del client.
PublicKey = adfadf...

# IP exacta (/32) del client a la VPN 
AllowedIPs = 192.168.6.2/32
```
Després es podrà activar la VPN:

```wg-quick up wg0```

### Configuració al client

Un cop instal·lat wireguard es podrà accedir al directori ```/etc/wireguard``` per crear al seu interior el parell de claus.

Després es podrà editar el fitxer ```/etc/wireguard/wg0.conf``` perquè contingui:

```
[Interface]
# Clau privada del client
PrivateKey = ZzZz...

# IP per al client a la VPN
Address = 192.168.6.2/24

[Peer]
# Clau pública del servidor
PublicKey = XxXxX...
# Trànsit que s'acceptarà (tota la xarxa /24)
AllowedIPs = 192.168.6.0/24

# IP i port UDP on escolta el servidor
Endpoint = 125.239.76.212:41194

# Si no hi ha trànsit transmetre un paquet cada 30 segons
PersistentKeepalive = 30
```
Després es podrà activar la VPN:

```wg-quick up wg0```
