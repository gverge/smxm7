# DHCP amb Kea

Durant molts anys el servidor DHCP canònic ha estat ISC DHCP Server . Un complet servidor DHCP per a IPv4 i IPv6 amb totes les funcions possibles, incloent un client i un relay.

Però amb el final del 2022 també arriba el final de la vida per a ISC DHC Server.

Afortunadament tenim una nova eina disponible per implementar el servei DCHP a xarxes IPv4 i IPv6. Es tracta de Kea , també desenvolupat per l'ISC com un servidor DHCP modern.

Característiques de Kea

El desenvolupament de Kea obeeix que ISC DHCP server es va publicar el 1999 i durant aquest temps les xarxes han canviat molt. Per això, s'ha decidit reescriure el servidor seguint pràctiques modernes.

Kea proporciona 4 serveis controlats per unitats de systemd.

<table border="1" style="border-collapse: collapse; width: 100%; height: 105px;">
            <tbody>
             <tr style="height: 21px;">
              <td style="width: 17.4418%; height: 21px;"><strong><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Unitat de systemd</font></font></strong></td>
              <td style="width: 26.8412%; height: 21px;"><strong><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Fitxer de configuració</font></font></strong></td>
              <td style="width: 55.717%; height: 21px;"><strong><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Servei</font></font></strong></td>
             </tr>
             <tr style="height: 21px;">
              <td style="width: 17.4418%; height: 21px;"><code>kea-dhcp4-server</code></td>
              <td style="width: 26.8412%; height: 21px;"><code>/etc/kea/kea-dhcp4.conf</code></td>
              <td style="width: 55.717%; height: 21px;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Servidor DHCP IPv4</font></font></td>
             </tr>
             <tr style="height: 21px;">
              <td style="width: 17.4418%; height: 21px;"><code>kea-dhcp6-server</code></td>
              <td style="width: 26.8412%; height: 21px;"><code>/etc/kea/kea-dhcp6.conf</code></td>
              <td style="width: 55.717%; height: 21px;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Servidor DHCP IPv6</font></font></td>
             </tr>
             <tr style="height: 21px;">
              <td style="width: 17.4418%; height: 21px;"><code>kea-dhcp-ddns-server</code></td>
              <td style="width: 26.8412%; height: 21px;"><code>/etc/kea/kea-dhcp-ddns.conf</code></td>
              <td style="width: 55.717%; height: 21px;"><a href="https://translate.google.com/website?sl=es&amp;tl=ca&amp;hl=ca&amp;client=webapp&amp;u=https://en.wikipedia.org/wiki/Dynamic_DNS"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Servidor DDNS (DNS Dinàmic)</font></font></a></td>
             </tr>
             <tr style="height: 21px;">
              <td style="width: 17.4418%; height: 21px;"><code>kea-ctrl-agent</code></td>
              <td style="width: 26.8412%; height: 21px;"><code>/etc/kea/kea-ctrl-agent.conf</code></td>
              <td style="width: 55.717%; height: 21px;"><font style="vertical-align: inherit;"><font style="vertical-align: inherit;">Agent de control de Kea. Interfície REST per a configuració dinàmica.</font></font></td>
             </tr>
            </tbody>
           </table>
