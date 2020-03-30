---
title: Backend-statusproblemen in Azure Application Gateway oplossen
description: Beschrijft hoe u backendstatusproblemen voor Azure Application Gateway oplossen
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: 71e1f8be2af5556d86996175e8a1ddbccc9c7de1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72001665"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Backend-statusproblemen in Application Gateway oplossen
==================================================

<a name="overview"></a>Overzicht
--------

Azure Application Gateway sondes standaard backendservers om hun status te controleren en te controleren of ze klaar zijn om aanvragen te serveren. Gebruikers kunnen ook aangepaste sondes maken om de hostnaam, het te onderzoeken pad en de statuscodes te vermelden die als gezond moeten worden geaccepteerd. Als de backendserver niet reageert, markeert Application Gateway de server in elk geval als ongezond en stopt de server met het doorsturen van aanvragen naar de server. Nadat de server is begonnen met reageren, wordt De aanvraaggateway opnieuw doorgestuurd.

### <a name="how-to-check-backend-health"></a>Backend-status controleren

Als u de status van uw backendpool wilt controleren, u de pagina **Backend-status** op de Azure-portal gebruiken. U ook [Azure PowerShell,](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0) [CLI](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)of [REST API](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth)gebruiken.

De status die door een van deze methoden wordt opgehaald, kan een van de volgende opties zijn:

- Goed

- Slechte status

- Onbekend

Als de backendstatus voor een server in orde is, betekent dit dat Application Gateway de aanvragen naar die server stuurt. Maar als de backend-status voor alle servers in een backend-groep niet in orde of onbekend is, u problemen ondervinden wanneer u toegang probeert te krijgen tot toepassingen. In dit artikel worden de symptomen, oorzaak en oplossing beschreven voor elk van de weergegeven fouten.

<a name="backend-health-status-unhealthy"></a>Backend status: Ongezond
-------------------------------

Als de backendstatus niet in orde is, lijkt de portalweergave op de volgende schermafbeelding:

![Backend-status van application gateway - Niet in orde](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Of als u een Azure PowerShell-, CLI- of Azure REST API-query gebruikt, krijgt u een antwoord dat lijkt op het volgende:
```azurepowershell
PS C:\Users\testuser\> Get-AzApplicationGatewayBackendHealth -Name "appgw1" -ResourceGroupName "rgOne"
BackendAddressPools :
{Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayBackendHealthPool}
BackendAddressPoolsText : [
{
                              "BackendAddressPool": {
                                "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appgw1/b
                          ackendAddressPools/appGatewayBackendPool"
                              },
                              "BackendHttpSettingsCollection": [
                                {
                                  "BackendHttpSettings": {
                                    "TrustedRootCertificates": [],
                                    "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appg
                          w1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
                                  },
                                  "Servers": [
                                    {
                                      "Address": "10.0.0.5",
                                      "Health": "Healthy"
                                    },
                                    {
                                      "Address": "10.0.0.6",
                                      "Health": "Unhealthy"
                                    }
                                  ]
                                }
                              ]
                            }
                        ]
```
Nadat u een status van een niet-gezonde backendserver hebt ontvangen voor alle servers in een backendpool, worden aanvragen niet doorgestuurd naar de servers en retourneert Application Gateway een fout van '502 Slechte gateway' naar de aanvragende client. Als u dit probleem wilt oplossen, schakelt u de kolom **Details** in op het tabblad **Backend Status.**

Het bericht in de kolom **Details** biedt meer gedetailleerde inzichten over het probleem en op basis daarvan u het probleem oplossen.

> [!NOTE]
> De standaardsondeaanvraag wordt \<verzonden in de indeling van\>protocol\<\>://127.0.0.1: poort /. Bijvoorbeeld http://127.0.0.1:80 voor een http-sonde op poort 80. Alleen HTTP-statuscodes van 200 tot en met 399 worden als gezond beschouwd. Het protocol en de doelpoort worden overgenomen van de HTTP-instellingen. Als u wilt dat Application Gateway een ander protocol, hostnaam of pad gebruikt en een andere statuscode als Gezond herkent, configureert u een aangepaste sonde en koppelt u deze aan de HTTP-instellingen.

<a name="error-messages"></a>Foutberichten
------------------------
#### <a name="backend-server-timeout"></a>Time-out voor backendserver

**Bericht:** De tijd die de backend\'nodig heeft om te reageren op de statusssonde van de toepassingsgateway is meer dan de time-outdrempel in de instelling van de sonde.

**Oorzaak:** Nadat Application Gateway een HTTP(S)-sondeverzoek naar de backendserver heeft verstuurt, wacht deze een antwoord van de backendserver gedurende een geconfigureerde periode. Als de backendserver niet reageert binnen de geconfigureerde periode (de time-outwaarde), wordt deze gemarkeerd als Niet-inord totdat deze binnen de geconfigureerde time-outperiode opnieuw reageert.

**Oplossing:** Controleer waarom de backendserver of -toepassing niet reageert binnen de geconfigureerde time-outperiode en controleer ook de toepassingsafhankelijkheden. Controleer bijvoorbeeld of de database problemen heeft die een reactievertraging kunnen veroorzaken. Als u zich bewust bent van het gedrag van de toepassing en deze pas moet reageren na de time-outwaarde, verhoogt u de time-outwaarde van de aangepaste sondeinstellingen. U moet een aangepaste sonde hebben om de time-outwaarde te wijzigen. [Zie de documentatiepagina voor](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)informatie over het configureren van een aangepaste sonde.

Voer de volgende stappen uit om de time-outwaarde te verhogen:

1.  Krijg rechtstreeks toegang tot de backendserver en controleer de tijd die nodig is om de server op die pagina te laten reageren. U elk hulpprogramma gebruiken om toegang te krijgen tot de backendserver, inclusief een browser met behulp van hulpprogramma's voor ontwikkelaars.

1.  Nadat u hebt uitgezocht hoe lang het is voordat de toepassing heeft gereageerd, selecteert u het tabblad **Statussondes** en selecteert u de sonde die is gekoppeld aan uw HTTP-instellingen.

1.  Voer binnen enkele seconden een time-outwaarde in die groter is dan de reactietijd van de toepassing.

1.  Sla de aangepaste sonde-instellingen op en controleer of de backendstatus nu als Gezond wordt weergegeven.

#### <a name="dns-resolution-error"></a>DNS-resolutiefout

**Bericht:** Application Gateway kan geen sonde maken voor deze backend. Dit gebeurt meestal wanneer de FQDN van de back-end niet correct is ingevoerd. 

**Oorzaak:** Als de backendpool van het type IP-adres/FQDN of App Service is, wordt Application Gateway ophet IP-adres van de FQDN die is ingevoerd via DNS (Domain Name System) (aangepast of Azure-standaard) en probeert verbinding te maken met de server op de TCP-poort die wordt vermeld in de HTTP-instellingen. Maar als dit bericht wordt weergegeven, suggereert dit dat Application Gateway het ingevoerde IP-adres van de ingevoerde FQDN niet kan oplossen.

**Resolutie:**

1.  Controleer of de FQDN die is ingevoerd in de backendpool juist is en of het een openbaar domein is en probeer het vervolgens op te lossen vanaf uw lokale machine.

1.  Als u het IP-adres oplossen, is er mogelijk iets mis met de DNS-configuratie in het virtuele netwerk.

1.  Controleer of het virtuele netwerk is geconfigureerd met een aangepaste DNS-server. Controleer als dit het is, controleer dan de DNS-server over waarom deze niet kan worden opgelost naar het IP-adres van de opgegeven FQDN.

1.  Als u Azure default DNS gebruikt, controleert u met uw domeinnaamregistrar of de juiste A-record- of CNAME-recordtoewijzing is voltooid.

1.  Als het domein privé of intern is, probeert u het op te lossen vanaf een vm in hetzelfde virtuele netwerk. Als u dit oplossen, start u Application Gateway opnieuw en controleert u deze opnieuw. Als u Application Gateway opnieuw wilt starten, moet u [stoppen](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0) en [beginnen](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0) met de PowerShell-opdrachten die in deze gekoppelde bronnen zijn beschreven.

#### <a name="tcp-connect-error"></a>TCP connect-fout

**Bericht:** Application Gateway kan geen verbinding maken met de backend.
Controleer of de backend reageert op de poort die voor de sonde wordt gebruikt.
Controleer ook of een NSG/UDR/Firewall de toegang tot de Ip en de poort van deze backend blokkeert

**Oorzaak:** Na de dns-resolutiefase probeert Application Gateway verbinding te maken met de backendserver op de TCP-poort die is geconfigureerd in de HTTP-instellingen. Als Application Gateway geen TCP-sessie kan instellen op de opgegeven poort, wordt de sonde gemarkeerd als Niet-inord met dit bericht.

**Oplossing:** Als u deze fout ontvangt, voert u de volgende stappen uit:

1.  Controleer met een browser of PowerShell of u verbinding maken met de backendserver op de poort die in de HTTP-instellingen wordt genoemd. Voer bijvoorbeeld de volgende opdracht uit:`Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  Als de genoemde poort niet de gewenste poort is, voert u het juiste poortnummer in voor Application Gateway om verbinding te maken met de backendserver

1.  Als u ook geen verbinding maken met de poort van uw lokale machine, dan:

    a.  Controleer de NSG-instellingen (Network Security Group) van de netwerkadapter en het subnet van de backendserver en of inkomende verbindingen met de geconfigureerde poort zijn toegestaan. Als dit niet het zo is, maakt u een nieuwe regel om de verbindingen toe te staan. [Zie de documentatiepagina voor](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules)meer informatie over het maken van NSG-regels.

    b.  Controleer of de NSG-instellingen van het subnet Application Gateway uitgaand openbaar en privéverkeer toestaan, zodat er een verbinding kan worden gemaakt. Controleer de documentpagina die in stap 3a is opgegeven voor meer informatie over het maken van NSG-regels.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Controleer de door de gebruiker gedefinieerde routes (UDR)-instellingen van Application Gateway en het subnet van de backendserver op routeringsafwijkingen. Zorg ervoor dat de UDR het verkeer niet wegleidt van het backend-subnet. Controleer bijvoorbeeld of routes naar virtuele netwerkapparaten of standaardroutes worden geadverteerd naar het subnet Application Gateway via Azure ExpressRoute en/of VPN.

    d.  Als u de effectieve routes en regels voor een netwerkadapter wilt controleren, u de volgende PowerShell-opdrachten gebruiken:
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  Als u geen problemen met NSG of UDR vindt, controleert u uw backendserver op toepassingsgerelateerde problemen waardoor clients geen TCP-sessie kunnen instellen op de geconfigureerde poorten. Een paar dingen om te controleren:

    a.  Open een opdrachtprompt (Win+R\> - `netstat`cmd), voer enter in en selecteer Enter.

    b.  Controleer of de server luistert op de geconfigureerde poort. Bijvoorbeeld:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  Als er niet wordt geluisterd op de geconfigureerde poort, controleert u de instellingen van de webserver. Bijvoorbeeld: sitebindingen in IIS, serverblok in NGINX en virtuele host in Apache.

    d.  Controleer de firewall-instellingen van het besturingssysteem om te controleren of binnenkomend verkeer naar de poort is toegestaan.

#### <a name="http-status-code-mismatch"></a>HTTP-statuscode mismatch

**Bericht:** De statuscode van\'het HTTP-antwoord van de backend kwam niet overeen met de instelling van de sonde. Verwacht:{HTTPStatusCode0} Ontvangen:{HTTPStatusCode1}.

**Oorzaak:** Nadat de TCP-verbinding is ingesteld en een SSL-handshake is uitgevoerd (als SSL is ingeschakeld), stuurt Application Gateway de sonde als een HTTP GET-verzoek naar de backendserver. Zoals eerder beschreven, de standaard \<sonde zal zijn om protocol\>\<://127.0.0.1: poort\>/, en het beschouwt reactie status codes in de rage 200 tot en met 399 als gezond. Als de server een andere statuscode retourneert, wordt deze gemarkeerd als Niet-inord met dit bericht.

**Oplossing:** Afhankelijk van de reactiecode van de backendserver u de volgende stappen uitvoeren. Een paar van de gemeenschappelijke statuscodes worden hier vermeld:

| **Fout** | **Acties** |
| --- | --- |
| Mismatch van de statuscode van probe: ontvangen 401 | Controleer of de backendserver verificatie vereist. Application Gateway-sondes kunnen op dit moment geen referenties doorgeven voor verificatie. Sta \"HTTP 401\" toe in een sondestatuscode match of sonde naar een pad waar de server geen verificatie vereist. | |
| Mismatch van de statuscode van probe: ontvangen 403 | Toegang verboden. Controleer of toegang tot het pad is toegestaan op de backendserver. | |
| Mismatch van de statuscode van probe: ontvangen 404 | Pagina niet gevonden. Controleer of het pad met de hostnaam toegankelijk is op de backendserver. Wijzig de hostnaam of padparameter in een toegankelijke waarde. | |
| Mismatch van de statuscode van probe: ontvangen 405 | De sondeaanvragen voor Application Gateway gebruiken de HTTP GET-methode. Controleer of uw server deze methode toestaat. | |
| Mismatch van de sondestatuscode: 500 ontvangen | Interne serverfout. Controleer de status van de back-endserver en of de services worden uitgevoerd. | |
| Mismatch van de statuscode van probe: 503 ontvangen | Service niet beschikbaar. Controleer de status van de back-endserver en of de services worden uitgevoerd. | |

Of als u denkt dat het antwoord legitiem is en u wilt dat Application Gateway andere statuscodes als Gezond accepteert, u een aangepaste sonde maken. Deze aanpak is handig in situaties waarin de backend-website verificatie nodig heeft. Omdat de sondeaanvragen geen gebruikersreferenties dragen, mislukken ze en wordt een HTTP 401-statuscode geretourneerd door de backendserver.

Als u een aangepaste sonde wilt maken, voert u [de volgende stappen uit](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

#### <a name="http-response-body-mismatch"></a>HTTP-reactiebody mismatch

**Bericht:** Body of the\'backend s HTTP response kwam niet overeen met de sonde instelling. Ontvangen antwoordbody bevat geen {string}.

**Oorzaak:** Wanneer u een aangepaste sonde maakt, hebt u een optie om een backendserver als gezond te markeren door een tekenreeks van de antwoordbody aan te passen. U bijvoorbeeld Application Gateway configureren om 'ongeautoriseerd' te accepteren als een tekenreeks die overeenkomt. Als het antwoord van de backendserver voor de sondeaanvraag de tekenreeks **ongeautoriseerd**bevat, wordt deze gemarkeerd als Gezond. Anders wordt het gemarkeerd als Ongezond met dit bericht.

**Oplossing:** Voer de volgende stappen uit om dit probleem op te lossen:

1.  Krijg lokaal toegang tot de backendserver of vanaf een clientmachine op het sondepad en controleer de antwoordbody.

1.  Controleer of de antwoordbody in de aangepaste sondeconfiguratie van de toepassingsgateway overeenkomt met wat is geconfigureerd.

1.  Als ze niet overeenkomen, wijzigt u de configuratie van de sonde, zodat de juiste tekenreekswaarde moet worden geaccepteerd.

Meer informatie over [application gateway probe matching](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching).

#### <a name="backend-server-certificate-invalid-ca"></a>Backendservercertificaat ongeldig CA

**Bericht:** Het servercertificaat dat door de backend wordt gebruikt, is niet ondertekend door een bekende Certificate Authority (CA). Whitelist de backend op de Application Gateway door het uploaden van het rootcertificaat van de server certificaat gebruikt door de backend.

**Oorzaak:** End-to-end SSL met Application Gateway v2 vereist dat het certificaat van de backendserver wordt geverifieerd om de server als gezond te achten.
Als u een SSL-certificaat wilt vertrouwen, moet dat certificaat van de backendserver worden uitgegeven door een CA die is opgenomen in de vertrouwde winkel van Application Gateway. Als het certificaat niet is uitgegeven door een vertrouwde CA (bijvoorbeeld als een zelfondertekend certificaat is gebruikt), moeten gebruikers het certificaat van de uitgever uploaden naar Application Gateway.

**Oplossing:** Volg deze stappen om het vertrouwde hoofdcertificaat te exporteren en te uploaden naar Application Gateway. (Deze stappen zijn voor Windows-clients.)

1.  Meld u aan bij de machine waar uw toepassing wordt gehost.

1.  Selecteer Win+R of klik met de rechtermuisknop op de knop **Start** en selecteer **Uitvoeren**.

1.  Voer `certmgr.msc` Enter in en selecteer deze. U ook zoeken naar Certificaatbeheer in het menu **Start.**

1.  Zoek het certificaat, `\Certificates - Current User\\Personal\\Certificates\`meestal in , en open het.

1.  Selecteer het basiscertificaat en selecteer **Vervolgens Certificaat weergeven**.

1.  Selecteer in de eigenschappen van het certificaat het tabblad **Details.**

1.  Selecteer op het tabblad **Details** de optie **Kopiëren naar bestand** en sla het bestand op in de X.509 basis-64 gecodeerd (. CER) formaat.

1.  Open de pagina **HTTP-instellingen van** de toepassingsgateway in de Azure-portal.

1. Open de HTTP-instellingen, selecteer **Certificaat toevoegen**en zoek het certificaatbestand dat u zojuist hebt opgeslagen.

1. Selecteer **Opslaan** om de HTTP-instellingen op te slaan.

U het basiscertificaat ook exporteren vanuit een clientmachine door rechtstreeks toegang te krijgen tot de server (Application Gateway omzeilen) via de browser en het rootcertificaat uit de browser te exporteren.

Zie [Vertrouwde basiscertificaat exporteren (voor v2 SKU) voor](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)meer informatie over het extraheren en uploaden van vertrouwde basiscertificaten in Application Gateway.

#### <a name="trusted-root-certificate-mismatch"></a>Fout in vertrouwde rootcertificaat

**Bericht:** Het hoofdcertificaat van het servercertificaat dat door de backend wordt gebruikt, komt niet overeen met het vertrouwde rootcertificaat dat aan de toepassingsgateway is toegevoegd. Zorg ervoor dat u het juiste basiscertificaat toevoegt om de backend op de witte lijst te zetten

**Oorzaak:** End-to-end SSL met Application Gateway v2 vereist dat het certificaat van de backendserver wordt geverifieerd om de server als gezond te achten.
Om een SSL-certificaat te kunnen vertrouwen, moet het backendservercertificaat worden uitgegeven door een CA die is opgenomen in de vertrouwde winkel van Application Gateway. Als het certificaat niet is uitgegeven door een vertrouwde CA (er is bijvoorbeeld een zelfondertekend certificaat gebruikt), moeten gebruikers het certificaat van de uitgever uploaden naar Application Gateway.

Het certificaat dat is geüpload naar http-instellingen van de toepassingsgateway, moet overeenkomen met het basiscertificaat van het backendservercertificaat.

**Oplossing:** Als u dit foutbericht ontvangt, is er een mismatch tussen het certificaat dat is geüpload naar Application Gateway en het certificaat dat is geüpload naar de backendserver.

Volg stappen 1-11 in de vorige methode om het juiste vertrouwde basiscertificaat naar Application Gateway te uploaden.

Zie [Vertrouwde basiscertificaat exporteren (voor v2 SKU) voor](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)meer informatie over het extraheren en uploaden van vertrouwde basiscertificaten in Application Gateway.
> [!NOTE]
> Deze fout kan ook optreden als de backendserver niet de volledige keten van het cert uitwisselt, inclusief de Root > Intermediate (indien van toepassing) > Leaf tijdens de TLS-handshake. Als u wilt controleren, u OpenSSL-opdrachten van elke client gebruiken en verbinding maken met de backendserver met behulp van de geconfigureerde instellingen in de sonde van de toepassingsgateway.

Bijvoorbeeld:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Als de uitvoer niet de volledige keten van het geretourneerde certificaat weergeeft, exporteert u het certificaat opnieuw met de volledige keten, inclusief het basiscertificaat. Configureer dat certificaat op uw backendserver. 

```
  CONNECTED(00000188)\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=20:unable to get local issuer certificate\
  verify return:1\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=21:unable to verify the first certificate\
  verify return:1\
  \-\-\-\
  Certificate chain\
   0 s:/OU=Domain Control Validated/CN=*.example.com\
     i:/C=US/ST=Arizona/L=Scottsdale/O=GoDaddy.com, Inc./OU=http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority - G2\
  \-----BEGIN CERTIFICATE-----\
  xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
  \-----END CERTIFICATE-----
```

#### <a name="backend-certificate-invalid-common-name-cn"></a>Backendcertificaat ongeldige algemene naam (CN)

**Bericht:** De algemene naam (CN) van het backend-certificaat komt niet overeen met de hostkopvan de sonde.

**Oorzaak:** Application Gateway controleert of de hostnaam die is opgegeven in de backend HTTP-instellingen overeenkomt met die van de CN die wordt gepresenteerd door het SSL-certificaat van de backendserver. Dit is Standard_v2 en WAF_v2 SKU-gedrag. De Servername-aanduiding (SNI) van standard en WAF SKU is ingesteld als de FQDN in het adres van de backendpool.

Als er in de v2-SKU een standaardsonde is (er is geen aangepaste sonde geconfigureerd en gekoppeld), wordt SNI ingesteld op basis van de hostnaam die in de HTTP-instellingen wordt genoemd. Als 'Hostnaam kiezen uit backend-adres' wordt vermeld in de HTTP-instellingen, waar de backend-adresgroep een geldige FQDN bevat, wordt deze instelling toegepast.

Als er een aangepaste sonde is gekoppeld aan de HTTP-instellingen, wordt SNI ingesteld op basis van de hostnaam die wordt genoemd in de aangepaste sondeconfiguratie. Als **Hostname kiezen van backend HTTP-instellingen** is geselecteerd in de aangepaste sonde, wordt SNI ingesteld op de hostnaam die in de HTTP-instellingen wordt genoemd.

Als **Hostname van Pick van backend-adres** is ingesteld in de HTTP-instellingen, moet de backend-adresgroep een geldige FQDN bevatten.

Als u dit foutbericht ontvangt, komt de CN van het backend-certificaat niet overeen met de hostnaam die is geconfigureerd in de aangepaste sonde of de HTTP-instellingen (als **Hostname kiezen van backend HTTP-instellingen** is geselecteerd). Als u een standaardsonde gebruikt, wordt de hostnaam ingesteld als **127.0.0.1**. Als dat geen gewenste waarde is, moet u een aangepaste sonde maken en deze koppelen aan de HTTP-instellingen.

**Oplossing:**

Volg deze stappen om het probleem op te lossen.

Voor Windows:

1.  Meld u aan bij de machine waar uw toepassing wordt gehost.

1.  Selecteer Win+R of klik met de rechtermuisknop op de knop **Start** en selecteer **Uitvoeren**.

1.  Voer **certmgr.msc in** en selecteer Enter. U ook zoeken naar Certificaatbeheer in het menu **Start.**

1.  Zoek het certificaat (meestal in) `\Certificates - Current User\\Personal\\Certificates`en open het certificaat.

1.  Controleer op het tabblad **Details** het **certificaatonderwerp**.

1.  Controleer de GN van het certificaat op basis van de details en voer hetzelfde in in het veld hostnaam van de aangepaste sonde of in de HTTP-instellingen (als **Hostname kiezen van backend HTTP-instellingen** is geselecteerd). Als dit niet de gewenste hostnaam voor uw website is, moet u een certificaat voor dat domein krijgen of de juiste hostnaam invoeren in de aangepaste sonde of HTTP-instellingsconfiguratie.

Voor Linux met OpenSSL:

1.  Voer deze opdracht uit in OpenSSL:
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  Zoek in het veld Hostname van de http-instellingen het cn van het certificaat en voer hetzelfde in het veld hostnaam in. Als dit niet de gewenste hostnaam voor uw website is, moet u een certificaat voor dat domein krijgen of de juiste hostnaam invoeren in de aangepaste sonde of HTTP-instellingsconfiguratie.

#### <a name="backend-certificate-is-invalid"></a>Backend-certificaat is ongeldig

**Bericht:** Het backendcertificaat is ongeldig. De huidige datum \"valt\" niet \"binnen\" het bereik Geldig van en Geldig tot op heden op het certificaat.

**Oorzaak:** Elk certificaat wordt geleverd met een geldigheidsbereik en de HTTPS-verbinding is niet veilig, tenzij het SSL-certificaat van de server geldig is. De huidige gegevens moeten binnen het **geldige** en **geldig tot** het bereik zijn. Als dit niet het is, wordt het certificaat als ongeldig beschouwd en wordt er een beveiligingsprobleem ontstaan waarbij Application Gateway de backendserver markeert als Niet-inord.

**Oplossing:** Als uw SSL-certificaat is verlopen, verlengt u het certificaat met uw leverancier en werkt u de serverinstellingen bij met het nieuwe certificaat. Als het een zelfondertekend certificaat is, moet u een geldig certificaat genereren en het basiscertificaat uploaden naar de HTTP-instellingen van de toepassingsgateway. Voer hiervoor de volgende stappen uit:

1.  Open de HTTP-instellingen van de toepassingsgateway in de portal.

1.  Selecteer de instelling met het verlopen certificaat, selecteer **Certificaat toevoegen**en open het nieuwe certificaatbestand.

1.  Verwijder het oude certificaat met het pictogram **Verwijderen** naast het certificaat en selecteer **Opslaan**.

#### <a name="certificate-verification-failed"></a>Certificaatverificatie is mislukt

**Bericht:** De geldigheid van het backendcertificaat kon niet worden geverifieerd. Als u de reden wilt achterhalen, schakelt u SSL-diagnose openen in voor het bericht dat is gekoppeld aan foutcode {errorCode}

**Oorzaak:** Deze fout treedt op wanneer Application Gateway de geldigheid van het certificaat niet kan verifiëren.

**Oplossing:** Als u dit probleem wilt oplossen, controleert u of het certificaat op uw server correct is gemaakt. U bijvoorbeeld [OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) gebruiken om het certificaat en de eigenschappen ervan te verifiëren en vervolgens proberen het certificaat opnieuw te uploaden naar de HTTP-instellingen van de toepassingsgateway.

<a name="backend-health-status-unknown"></a>Backend status: onbekend
-------------------------------
Als de backendstatus als Onbekend wordt weergegeven, lijkt de portalweergave op de volgende schermafbeelding:

![Application Gateway backend health - Onbekend](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Dit gedrag kan optreden om een of meer van de volgende redenen:

1.  Het NSG-subnet van de Application Gateway blokkeert de inkomende toegang tot poorten 65503-65534 (v1 SKU) of 65200-65535 (v2 SKU) van 'Internet'.
1.  De UDR op het subnet Application Gateway is ingesteld op de standaardroute (0.0.0.0/0) en de volgende hop is niet opgegeven als 'Internet'.
1.  De standaardroute wordt geadverteerd door een ExpressRoute/VPN-verbinding met een virtueel netwerk via BGP.
1.  De aangepaste DNS-server is geconfigureerd op een virtueel netwerk dat geen namen van openbare domeinen kan oplossen.
1.  Application Gateway is in een niet-gezonde staat.

**Oplossing:**

1.  Controleer of uw NSG de toegang tot de poorten 65503-65534 (v1 SKU) of 65200-65535 (v2 SKU) van **internet**blokkeert:

    a.  Selecteer op het tabblad **Toepassingsgatewayoverzicht** de koppeling **Virtueel netwerk/subnet.**

    b.  Selecteer op het tabblad **Subnetten** van uw virtuele netwerk het subnet waarin Application Gateway is geïmplementeerd.

    c.  Controleer of een NSG is geconfigureerd.

    d.  Als een NSG is geconfigureerd, zoekt u naar die NSG-bron op het tabblad **Zoeken** of onder **Alle resources**.

    e.  Voeg in de sectie **Binnenkomende regels** een inkomende regel toe om het doelpoortbereik 65503-65534 toe te staan voor v1 SKU of 65200-65535 v2 SKU met de **bronset** als **Internet** **.**

    f.  Selecteer **Opslaan** en controleer of u de backend als gezond weergeven. U dat ook doen via [PowerShell/CLI.](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

1.  Controleer of uw UDR een standaardroute heeft (0.0.0.0/0) waarbij de volgende hop niet is ingesteld als **internet:**
    
    a.  Volg stappen 1a en 1b om uw subnet te bepalen.

    b.  Controleer of er een UDR is geconfigureerd. Als dat het is, zoekt u naar de resource op de zoekbalk of onder **Alle resources**.

    c.  Controleer of er standaardroutes (0.0.0.0/0) zijn waarbij de volgende hop niet is ingesteld als **internet.** Als de instelling **Virtual Appliance** of Virtual Network **Gateway**is, moet u ervoor zorgen dat uw virtuele toestel of het on-premises apparaat het pakket correct terug kan leiden naar de internetbestemming zonder het pakket te wijzigen.

    d.  Wijzig anders de volgende hop in **Internet,** selecteer **Opslaan**en controleer de backendstatus.

1.  Standaardroute geadverteerd door de ExpressRoute/VPN-verbinding met het virtuele netwerk via BGP:

    a.  Als u via BGP een ExpressRoute/VPN-verbinding met het virtuele netwerk hebt en als u een standaardroute adverteert, moet u ervoor zorgen dat het pakket wordt doorgestuurd naar de internetbestemming zonder het te wijzigen. U dit verifiëren door de optie **Problemen met verbinding** te gebruiken in de portal van de toepassingsgateway.

    b.  Kies de bestemming handmatig als elk ip-adres zoals 1.1.1.1 via internet. Stel de doelpoort in als iets en controleer de verbinding.

    c.  Als de volgende hop een virtuele netwerkgateway is, wordt er mogelijk een standaardroute geadverteerd via ExpressRoute of VPN.

1.  Als er een aangepaste DNS-server is geconfigureerd in het virtuele netwerk, controleert u of de server (of servers) openbare domeinen kunnen oplossen. Het oplossen van openbare domeinnaamen kan nodig zijn in scenario's waarin Application Gateway externe domeinen zoals OCSP-servers moet bereiken of om de intrekkingsstatus van het certificaat te controleren.

1.  Als u wilt controleren of de toepassingsgateway in orde is en wordt uitgevoerd, gaat u naar de optie **Resourcestatus** in de portal en controleert u of de status **in orde**is. Als u een **status niet-in orde** of **gedegradeerd ziet,** neemt [u contact op met ondersteuning](https://azure.microsoft.com/support/options/).

<a name="next-steps"></a>Volgende stappen
----------

Meer informatie over [diagnose en logboekregistratie](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)van application gateway.
