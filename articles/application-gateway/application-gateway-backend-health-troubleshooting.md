---
title: Problemen met back-endservers in Azure-toepassing gateway oplossen
description: Dit artikel helpt u bij het oplossen van problemen met back-endservers voor Azure-toepassing gateway
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: ce1d0542530c4f190ace52d45e2369d6ecc18772
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384084"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Problemen met back-endservers oplossen in Application Gateway
==================================================

<a name="overview"></a>Overzicht
--------

Application Gateway, wordt standaard de back-endservers gecontroleerd om de status te controleren en als ze klaar zijn om aanvragen te behandelen. Gebruikers kunnen aangepaste tests maken en de hostnaam opgeven, het pad dat moet worden gecontroleerd en de status codes worden geaccepteerd als in orde. Als de back-endserver niet correct reageert Application Gateway, wordt in beide gevallen de server als beschadigd gemarkeerd en stopt het door sturen van de aanvraag naar de server. Zodra de server met succes is gestart, worden de aanvragen hervat.

### <a name="how-to-check-backend-health"></a>De status van de back-end controleren

Als u de status van uw back-end-pool wilt controleren, kunt u de pagina status van de back-end gebruiken in de Azure Portal. U kunt ook [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0), [cli](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)of [rest API](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth) gebruiken om de status op te halen. Controleer het gekoppelde artikel op elke methode om meer informatie te krijgen.

De status die wordt opgehaald met een van de bovenstaande methoden, kan uit drie typen bestaan:

1.  In orde

2.  Niet in orde

3.  Onbekend

Als de status van de back-end voor een server in orde is, geeft u aan dat Application Gateway de aanvragen naar die server wilt door sturen. Als de status van de back-end voor alle servers in een back-end-groep niet in orde is of onbekend is, is het mogelijk dat u een aantal problemen ondervindt tijdens de toegang tot de toepassing. In dit document worden het symptoom, de oorzaak en de oplossing beschreven van elk van de fouten die worden weer gegeven wanneer de back-endservers beschadigd zijn of onbekend.

<a name="backend-health-status-unhealthy"></a>De status van de back-end is beschadigd
-------------------------------

Als de status van de back-end wordt weer gegeven als beschadigd, ziet u deze op de portal, zoals in de onderstaande scherm afbeelding:

![Status van Application Gateway-back-end-slechte status](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Of als u Azure PowerShell, CLI of Azure REST API query gebruikt, ziet u een antwoord zoals hieronder wordt weer gegeven:
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
Zodra u de status van de back-endserver als beschadigd hebt weer gegeven voor alle servers in een back-end-pool, worden de aanvragen niet doorgestuurd naar de-server en 502 retourneert Application Gateway een onjuiste gateway fout naar de client die de aanvraag heeft ingediend. Om het probleem op te lossen, raadpleegt u de kolom Details van het tabblad back-end.

Het bericht dat wordt weer gegeven in de kolom Details van het tabblad back-end bevat meer gedetailleerde informatie over het probleem en op basis hiervan, kunnen we beginnen met het oplossen van het probleem.

> [!NOTE]
> De standaard test aanvraag wordt verzonden met de indeling <protocol>://127.0.0.1:<port> <http://127.0.0.1/> /, bijvoorbeeld voor een http-test op poort 80 en beschouwt alleen een reactie van HTTP-status codes 200-399 als in orde als antwoord. Het protocol en de doel poort worden overgenomen van HTTP-instellingen. Als u wilt dat Application Gateway op een ander protocol, hostnaam of pad moet testen en een andere status code als in orde kunt accepteren, configureert u een aangepaste test en koppelt u deze aan de HTTP-instellingen.

### <a name="error-messages"></a>Foutberichten

#### <a name="backend-server-timeout"></a>Time-out van back-endserver

**Bericht:** De tijd die de back-end heeft genomen om\'te reageren op de status test van de toepassings gateway, is hoger dan de drempel waarde voor de time-out in de test instelling.

**Wordt** Zodra Application Gateway een HTTP (S)-test aanvraag naar de back-endserver verzendt, wordt gewacht op de reactie van de back-endserver voor een bepaalde tijds duur die is geconfigureerd. Als de back-end-server niet reageert binnen de time-outwaarde, wordt deze gemarkeerd als niet-gezond totdat deze opnieuw reageert binnen de time-out.

**Opgelost** Controleer of de back-endserver of toepassing niet reageert binnen de geconfigureerde time-out en Controleer ook de toepassings afhankelijkheden, bijvoorbeeld als de data base problemen bevat, die kunnen leiden tot de vertraging in reactie. Als u op de hoogte bent van het gedrag van de toepassing en deze alleen moet reageren na de time-outwaarde, verhoogt u de time-outwaarde van de aangepaste test instellingen.
U moet een aangepaste test hebben om de waarde van de time-out te wijzigen. [Raadpleeg de documentatie pagina](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)als u wilt weten hoe u een aangepaste test kunt configureren.

Volg de onderstaande stappen om de time-out te verhogen:

1.  Direct toegang tot de back-endserver en controleer de tijd die nodig is om op de pagina op de server te reageren. U kunt elk hulp programma gebruiken om toegang te krijgen, inclusief een browser met behulp van de hulpprogram ma's voor ontwikkel aars.

2.  Als u eenmaal hebt gezien hoe lang het duurt voordat de toepassing reageert, klikt u op het tabblad Status controles en selecteert u de test die aan uw HTTP-instellingen is gekoppeld.

3.  Voer in seconden een hogere time-outwaarde in dan de reactie tijd van de toepassing.

4.  Sla de instellingen voor de aangepaste test op en controleer de status van de back-end als deze nu in orde wordt weer gegeven.

#### <a name="dns-resolution-error"></a>DNS-omzettings fout

**Bericht:** Application Gateway kan geen test maken voor deze back-end. Dit gebeurt meestal wanneer de FQDN van de back-end niet correct is ingevoerd. 

**Wordt** Als de back-end-pool van het type IP-adres/FQDN of App Service is, wordt Application Gateway omgezet naar het IP-adres van de FQDN die is ingevoerd via de DNS-server (aangepaste of Azure-standaard) en wordt geprobeerd verbinding te maken met de server op de TCP-poort die wordt vermeld in de HTTP-instellingen. Maar als dit bericht wordt weer gegeven, wordt voorgesteld dat Application Gateway het IP-adres van de ingevoerde FQDN niet kon omzetten.

**Opgelost**

1.  Controleer of de FQDN die is opgegeven in de back-end-groep juist is en of het een openbaar domein is, probeer het probleem op te lossen vanaf uw lokale computer.

2.  Als u het IP-adres kunt omzetten, kan er iets mis zijn met de DNS-configuratie in het VNet.

3.  Controleer of het VNet is geconfigureerd met een aangepaste DNS-server. Als dit het geval is, controleert u de DNS-server op waarom deze niet kan worden omgezet in het IP-adres van de opgegeven FQDN.

4.  Als de standaard waarde voor de DNS-server van Azure is, controleert u of de domein naam is geregistreerd bij de registratie van een record of CNAME-record.

5.  Als het een persoonlijk of intern domein is, probeert u het probleem op te lossen vanaf een virtuele machine in hetzelfde VNet. Als u dit probleem kunt oplossen, start u Application Gateway opnieuw op en controleert u het opnieuw. Als u Application Gateway opnieuw wilt opstarten, moet u de Power shell-opdrachten die zijn opgegeven in de respectieve document koppelingen, [stoppen](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0) en [starten](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0) .

#### <a name="tcp-connect-error"></a>TCP-verbindings fout

**Bericht:** Application Gateway kan geen verbinding maken met de back-end.
Controleer of de back-end reageert op de poort die wordt gebruikt voor de test.
Controleer ook of NSG/UDR/firewall de toegang tot het IP-adres en de poort van deze back-end blokkeert

**Wordt** Na de DNS-omzettings fase probeert Application Gateway verbinding te maken met de back-end-server op de TCP-poort die is geconfigureerd in de HTTP-instellingen. Als Application Gateway niet in staat is een TCP-sessie te maken op de opgegeven poort, wordt de test gemarkeerd als beschadigd met dit bericht.

**Oplossing:** Als u deze fout ziet, controleert u het volgende:

1.  Controleer of u verbinding kunt maken met de back-endserver op de poort die wordt vermeld in de HTTP-instellingen met behulp van een browser of met behulp van Power shell. u kunt bijvoorbeeld de volgende opdracht gebruiken: Test-NetConnection-computer naam www.bing.com-poort 443

2.  Als de vermelde poort niet de gewenste poort is, voert u het juiste poort nummer in voor Application Gateway verbinding maken met de back-end-server

3.  Als u ook geen verbinding kunt maken met de poort op de lokale computer, dan:

    a.  Controleer de NSG-instellingen van de NIC en het subnet van de back-endserver en controleer of de binnenkomende verbindingen met de geconfigureerde poort zijn toegestaan. Als ze niet zijn toegestaan, maakt u een nieuwe regel om de verbindingen toe te staan. [Raadpleeg de documentatie pagina](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules)voor meer informatie over het maken van NSG-regels.

    b.  Controleer de NSG-instellingen van het subnet van Application Gateway als uitgaand openbaar en privé verkeer is toegestaan, zodat de verbinding kan worden gemaakt. Controleer het document dat is gekoppeld aan (a) voor meer informatie over het maken van NSG-regels.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Controleer de UDR-instellingen van het subnet van de Application Gateway en de back-endserver voor eventuele afwijkingen van de route ring. Zorg ervoor dat de UDR niet het verkeer vanuit het back-end-subnet doorstuurt. U kunt bijvoorbeeld controleren op routes naar virtuele netwerk apparaten of standaard routes die worden geadverteerd naar het subnet van de toepassings gateway via ExpressRoute/VPN.

    d.  U kunt de volgende Power shell-opdrachten gebruiken om de juiste routes en regels voor een NIC te controleren.
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
4.  Als u geen problemen met NSG of UDR kunt vinden, controleert u de back-endserver op toepassings problemen als gevolg waarvan clients geen TCP-sessie op de geconfigureerde poort (en) kunnen maken. Een aantal dingen om te controleren:

    e.  Open de opdracht prompt (Win + R\> -cmd) en Voer netstat in en druk op ENTER

    f.  Controleer of de server luistert op de poort die is geconfigureerd.
        Bijvoorbeeld:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    g.  Als dat niet het geval is, controleert u de webserver instellingen, bijvoorbeeld site bindingen in IIS, Server blok in NGINX en virtual host in Apache

    h.  Controleer de firewall instellingen van het besturings systeem om ervoor te zorgen dat binnenkomend verkeer naar de poort is toegestaan

#### <a name="http-status-code-mismatch"></a>HTTP-status code komt niet overeen

**Bericht:** De status code van het\'http-antwoord van de back-end komt niet overeen met de test instelling. Verwacht: {HTTPStatusCode0} ontvangen: {HTTPStatusCode1}.

**Wordt** Zodra de TCP-verbinding tot stand is gebracht en de SSL-handshake is voltooid (als SSL is ingeschakeld), stuurt Application Gateway de test als een HTTP GET-aanvraag naar de back-endserver. Zoals hierboven vermeld, is de standaard test \<een protocol\>://127.0.0.1:\<poort\>/, en worden de antwoord status codes in de rage 200-399 als gezond beschouwd. Als de server een andere status code retourneert, wordt deze als beschadigd gemarkeerd met dit bericht.

**Oplossing:** Afhankelijk van de reactie code van de back-endserver kunt u de volgende stappen uitvoeren. Enkele van de algemene status codes worden hier weer gegeven:

| **Optreedt** | **Acties** |
| --- | --- |
| Status code van test komt niet overeen: Ontvangen 401 | Controleer of verificatie is vereist voor de back-endserver. De test van de toepassings gateway kan op dit moment geen referenties door geven voor authenticatie. Sta toe \"dat http\" 401 in een test status code overeenkomt of test naar een pad waarvoor geen verificatie is vereist voor de server. | |
| Status code van test komt niet overeen: Ontvangen 403 | Toegang verboden. Controleer of de toegang tot het pad is toegestaan op de back-endserver. | |
| Status code van test komt niet overeen: Ontvangen 404 | Pagina is niet gevonden. Controleer het pad van de hostnaam als het toegankelijk is op de back-endserver. Wijzig de para meter hostnaam of pad in een toegankelijke waarde. | |
| Status code van test komt niet overeen: Ontvangen 405 | Test aanvragen van Application Gateway gebruiken HTTP GET-methode. Controleer of de server dit toestaat. | |
| Status code van test komt niet overeen: Ontvangen 500 | Interne server fout. Controleer de status van de back-endserver en als de services worden uitgevoerd. | |
| Status code van test komt niet overeen: Ontvangen 503 | De service is niet beschikbaar. Controleer de status van de back-endserver en als de services worden uitgevoerd. | |

Of als u denkt dat het antwoord legit is en u wilt dat Application Gateway andere status codes als in orde accepteert, kunt u een aangepaste test maken. Het wijzigen van dit is nuttig in situaties waarin de back-end-website verificatie vereist en omdat de test aanvragen geen gebruikers referenties hebben, de fouten zullen mislukken en een HTTP 401-status code wordt geretourneerd door de back-endserver.

Als u een aangepaste test wilt maken, volgt u de stappen die [hier](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)worden beschreven.

#### <a name="http-response-body-mismatch"></a>Onjuiste hoofd tekst van HTTP-antwoord

**Bericht:** De hoofd tekst van\'het HTTP-antwoord van de back-end-s komt niet overeen met de test instelling. De ontvangen antwoord tekst bevat geen {string}.

**Wordt** Wanneer u een aangepaste test maakt, hebt u de mogelijkheid om een back-endserver te markeren in orde door een teken reeks te zoeken in de hoofd tekst van het antwoord. U kunt bijvoorbeeld Application Gateway zodanig configureren dat "niet-geautoriseerd" als teken reeks wordt geaccepteerd. Als het antwoord van de back-endserver voor de test aanvraag de teken reeks "niet-geautoriseerd" bevat, wordt deze in orde gemarkeerd.
Anders wordt het gemarkeerd als beschadigd met dit bericht.

**Oplossing:** U kunt dit probleem oplossen door de volgende stappen uit te voeren:

1.  Open de back-endserver lokaal of van een client computer op het pad probe en controleer de tekst van het antwoord.

2.  Controleer Application Gateway aangepaste test configuratie om te controleren of de antwoord tekst overeenkomt met wat er is geconfigureerd.

3.  Als ze niet overeenkomen, wijzigt u de test configuratie met de juiste teken reeks waarde om te accepteren.

U kunt [hier](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)meer lezen over Application Gateway probe match.

#### <a name="backend-server-certificate-invalid-ca"></a>Back-end-server certificaat ongeldig CA

**Bericht:** Het server certificaat dat door de back-end wordt gebruikt, is niet ondertekend door een bekende certificerings instantie (CA). White List de back-end op de toepassings gateway door het basis certificaat van het server certificaat te uploaden dat wordt gebruikt door de back-end.

**Wordt** End-to-end SSL met Application Gateway v2 vereist dat het certificaat van de back-endserver wordt geverifieerd om te voor komt dat de server in orde is.
Een SSL-certificaat kan alleen worden vertrouwd als dat certificaat van de back-endserver is uitgegeven door een certificerings instantie die is opgenomen in het vertrouwde archief van de Application Gateway. Als het certificaat niet is uitgegeven door een vertrouwde certificerings instantie, bijvoorbeeld zelfondertekende certificaten, moeten gebruikers het certificaat van de verlener uploaden naar Application Gateway.

**Oplossing:** Volg de onderstaande stappen om het vertrouwde basis certificaat te exporteren en te uploaden naar de Application Gateway (de hieronder vermelde stappen gelden voor Windows-clients)

1.  Meld u aan bij de computer waarop uw toepassing wordt gehost

2.  Open run door te drukken op Win + R of met de rechter muisknop op de knop Start te klikken en uitvoeren te selecteren.

3.  Voer certmgr. msc in en druk op ENTER. U kunt ook zoeken naar Certificate manager in het menu Start.

4.  Zoek het certificaat, meestal in \'certificaten, persoonlijke\\certificaten\\\'van de huidige gebruiker en open het certificaat

5.  Selecteer in het eigenschappen certificaat het tabblad Certificeringspad

6.  Selecteer het basis certificaat en selecteer de optie certificaat weer geven

7.  Ga in de certificaat eigenschappen naar het tabblad Details

8.  Selecteer op het tabblad Details de optie ' kopiëren naar bestand ' en sla het bestand op in de base-64 Encoded X. 509 (. CER)-indeling

9.  Nadat het is opgeslagen, opent u de pagina Application Gateway HTTP-instellingen in het Azure Portal

10. Open de HTTP-instellingen en klik op certificaat toevoegen en zoek het certificaat bestand dat we onlangs hebben opgeslagen

11. Klik op Opslaan om de HTTP-instellingen op te slaan

U kunt het basis certificaat ook exporteren vanaf een client computer door rechtstreeks toegang te krijgen tot de server (de Application Gateway over te slaan) met behulp van een browser en het basis certificaat te exporteren vanuit de browser.

Zie [hier](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)voor gedetailleerde stappen voor het extra heren en uploaden van vertrouwde basis certificaten in Application Gateway.

#### <a name="trusted-root-certificate-mismatch"></a>Niet-overeenkomende vertrouwde basis certificaten

**Bericht:** Het basis certificaat van het server certificaat dat door de back-end wordt gebruikt, komt niet overeen met het vertrouwde basis certificaat dat is toegevoegd aan de toepassings gateway. Zorg ervoor dat u het juiste basis certificaat toevoegt aan White List de back-end

**Wordt** End-to-end SSL met Application Gateway v2 vereist dat het certificaat van de back-endserver wordt geverifieerd om te voor komt dat de server in orde is.
Een SSL-certificaat kan alleen worden vertrouwd als dat certificaat van de back-endserver is uitgegeven door een certificerings instantie die is opgenomen in het vertrouwde archief van de Application Gateway. Als het certificaat niet is uitgegeven door een vertrouwde certificerings instantie, bijvoorbeeld zelfondertekende certificaten, moeten gebruikers het certificaat van de verlener uploaden naar Application Gateway.

Het certificaat dat is geüpload naar de Application Gateway HTTP-instellingen, moet overeenkomen met het basis certificaat van het back-endserver certificaat.

**Oplossing:** Als het bovenstaande fout bericht wordt weer gegeven, betekent dit dat het certificaat dat is geüpload naar de Application Gateway en het bestand dat naar de back-endserver is geüpload, niet overeenkomen.

Volg de bovenstaande stappen 1-11 om het juiste vertrouwde basis certificaat te uploaden naar de Application Gateway.

Zie [hier](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)voor gedetailleerde stappen voor het extra heren en uploaden van vertrouwde basis certificaten in Application Gateway.
> [!NOTE]
> De genoemde fout kan ook optreden als de back-endserver de volledige keten van het certificaat niet uitwisselt met inbegrip van het tussenliggende > (indien van toepassing)-> blad tijdens de TLS-handshake. Als u wilt controleren, kunt u OpenSSL-opdrachten van elke client gebruiken en verbinding maken met de back-endserver met behulp van de geconfigureerde instellingen in de Application Gateway-test.

Bijvoorbeeld:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Als voor de uitvoer niet de volledige keten van het certificaat wordt weer gegeven, exporteert u het certificaat opnieuw met de volledige keten met inbegrip van het basis certificaat en configureert u dit op uw back-endserver. 

VERBONDEN (00000188) \
diepte = 0 OE = domein controle gevalideerd, CN = \*. example.com \
Controleer de volgende fout: NUM = 20: kan het lokale certificaat van de verlener niet ophalen \
Controleer de retour waarde: 1 \
diepte = 0 OE = domein controle gevalideerd, CN = \*. example.com \
Controleer de volgende fout: NUM = 21: kan het eerste certificaat niet verifiëren \
Controleer de retour waarde: 1 \
\-\-\-\
Certificaat keten \
 0 s:/OE = Domain Control Validated/CN = *. example. com \
   i:/C = vs/St = Arizona/L = Scottsdale/O = GoDaddy. com, Inc./ou =http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority-G2 \
\-----BEGIN CERTIFICAAT-----\
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
\-----EIND CERTIFICAAT-----

#### <a name="backend-certificate-invalid-common-name-cn"></a>Back-end-certificaat ongeldige common name (CN)

**Bericht:** De algemene naam (CN) van het back-end-certificaat komt niet overeen met de host-header van de test.

**Wordt** Application Gateway valideert of de hostnaam die is opgegeven in de back-end-http-instelling overeenkomt met die van de algemene naam (CN) die wordt weer gegeven door het SSL-certificaat van de back-endserver. Dit is alleen een Standard_v2-en WAF_v2-SKU-gedrag. De standaard-en WAF-SNI van de SKU worden ingesteld als de FQDN in het adres van de back-end-pool.

Als er in de v2-SKU een standaard test is (er is geen aangepaste test geconfigureerd/gekoppeld), wordt SNI ingesteld op basis van de hostnaam die wordt vermeld in de HTTP-instellingen of als ' hostnaam kiezen van het back-upadres ' wordt vermeld in de HTTP-instellingen, waarbij de back-mailadres groep een geldige FQDN.

Als er een aangepaste test is gekoppeld aan de HTTP-instellingen, wordt SNI ingesteld op basis van de hostnaam die wordt vermeld in de aangepaste test configuratie of als "hostnaam kiezen van back-end-HTTP-instellingen" is ingeschakeld in de aangepaste test, wordt deze ingesteld op basis van de hostnaam die wordt vermeld in de HTTP Instellingen.

In het geval ' hostnaam kiezen vanuit het back-upadres ' is ingesteld in de HTTP-instellingen, moet de back-end-adres groep een geldige FQDN bevatten.

Als het hierboven vermelde fout bericht wordt weer gegeven, betekent dit dat de algemene naam (CN) van het back-end-certificaat niet overeenkomt met de hostnaam die is geconfigureerd in de aangepaste test of de HTTP-instellingen (in het geval "hostnaam kiezen van back-end-HTTP-instellingen" is gekozen). Als u een standaard test gebruikt, wordt de hostnaam ingesteld op ' 127.0.0.1 '. Als dat geen gewenste waarde is, maakt u een aangepaste test en koppelt u deze aan de HTTP-instellingen.

**Oplossing:**

Volg de onderstaande stappen om het probleem op te lossen:

Voor Windows:

1.  Meld u aan bij de computer waarop uw toepassing wordt gehost

2.  Open run door te drukken op Win + R of met de rechter muisknop op de knop Start te klikken en uitvoeren te selecteren.

3.  Voer certmgr. msc in en druk op ENTER. U kunt ook zoeken naar Certificate manager in het menu Start.

4.  Zoek het certificaat, meestal in \'certificaten, persoonlijke\\certificaten\\\'van de huidige gebruiker en open het certificaat

5.  Controleer op het tabblad Details het onderwerp van het certificaat

6.  Controleer de CN van het certificaat op basis van de gegevens en geef hetzelfde op in het veld hostnaam van de aangepaste test of de HTTP-instellingen (in het geval ' hostnaam kiezen van de back-end-HTTP-instellingen ' is gekozen). Als dat niet de gewenste hostnaam voor uw website is, moet u een certificaat voor dat domein ophalen of de juiste hostnaam opgeven in de configuratie van de aangepaste test/http-instelling.

Voor Linux met behulp van OpenSSL

1.  Voer deze opdracht uit in OpenSSL 
```
openssl x509 -in certificate.crt -text -noout
```

2.  Zoek de CN van het certificaat in de weer gegeven eigenschappen en voer in het veld hostnaam van de HTTP-instellingen dezelfde in. Als dat niet de gewenste hostnaam voor uw website is, moet u een certificaat voor dat domein ophalen of de juiste hostnaam opgeven in de configuratie van de aangepaste test/http-instelling.

#### <a name="backend-certificate-is-invalid"></a>Back-end certificaat is ongeldig

**Bericht:** Back-end certificaat is ongeldig. De huidige datum valt niet binnen \"het geldig\" van \"-en\" geldig tot-datum bereik op het certificaat.

**Wordt** Elk certificaat wordt geleverd met een geldigheids duur en de HTTPS-verbinding is niet beveiligd tenzij het SSL-certificaat van de server geldig is.
De huidige gegevens moeten binnen de geldige waarde van en geldig tot het bereik liggen. Als dat niet het geval is, wordt het certificaat beschouwd als ongeldig en is het een beveiligings probleem. Op dit punt markeert Application Gateway de back-endserver als beschadigd.

**Oplossing:** Als uw SSL-certificaat is verlopen, verlengt u het certificaat met uw leverancier en werkt u de server instellingen bij met het nieuwe certificaat. Als het een zelfondertekend certificaat is, moet u een geldig certificaat genereren en het basis certificaat uploaden naar de HTTP-instellingen van de Application Gateway. Volg hiervoor de volgende stappen:

1.  Open de Application Gateway HTTP-instellingen in de portal

2.  Selecteer de HTTP-instellingen die het verlopen certificaat hebben, selecteer certificaat toevoegen en open het nieuwe certificaat bestand.

3.  Verwijder het oude certificaat met behulp van het pictogram verwijderen naast het certificaat en klik op opslaan.

#### <a name="certificate-verification-failed"></a>Verificatie van certificaat is mislukt

**Bericht:** De geldigheid van het back-end-certificaat kan niet worden geverifieerd. Als u de reden wilt achterhalen, controleert u de diagnostische gegevens van het open SSL-bericht dat is gekoppeld aan de fout code {error codes}

**Wordt** Deze fout treedt op wanneer Application Gateway de geldigheid van het certificaat niet kunt controleren.

**Oplossing:** U kunt dit probleem oplossen door het certificaat op uw server te controleren als het correct is gemaakt. U kunt bijvoorbeeld [openssl](https://www.openssl.org/docs/man1.0.2/man1/verify.html) gebruiken om het certificaat en de bijbehorende eigenschappen te controleren en het certificaat opnieuw te uploaden naar de HTTP-instellingen van de Application Gateway.

<a name="backend-health-status-unknown"></a>Status van back-end is onbekend
-------------------------------
Als de status van de back-end wordt weer gegeven als onbekend, wordt deze weer gegeven in de portal, zoals in de onderstaande scherm afbeelding:

![Status van Application Gateway-back-end-onbekend](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Als de back-end wordt weer gegeven als onbekend, kan dit een of meer van de volgende oorzaken hebben:

1.  NSG op het Application Gateway-subnet blokkeert de inkomende toegang tot de poorten 65503-65534 (v1 SKU) of 65200-65535 (v2 SKU) van Internet
2.  UDR op het Application Gateway subnet met de standaard route (0.0.0.0/0) met de volgende hop, niet als Internet
3.  Standaard route geadverteerd door ExpressRoute/VPN-verbinding met het VNet via BGP
4.  Aangepaste DNS-server is geconfigureerd in het VNet dat geen open bare domein namen kan omzetten
5.  Application Gateway heeft een slechte status

**Oplossing:**

1.  Controleer of uw NSG toegang tot de poorten 65503-65534 (v1 SKU) of 65200-65535 (v2-SKU) van Internet blokkeert

    a.  Selecteer op het tabblad Application Gateway ' overzicht ' de koppeling Virtual Network/subnet

    b.  Selecteer op het tabblad subnetten van uw Virtual Network het subnet waarop de Application Gateway is geïmplementeerd

    c.  Controleren of er een NSG is geconfigureerd

    d.  Als dat het geval is, zoekt u naar die NSG-resource op het tabblad zoeken of onder alle resources

    e.  Voeg in de sectie regels voor binnenkomende verbindingen een regel voor binnenkomende verbindingen toe om het doel poort bereik 65503-65534 voor v1 SKU of een 65200-65535 v2-SKU met bron als een of Internet toe te staan.

    f.  Klik op opslaan en controleer of de back-end probleemloos kan worden weer gegeven

    g.  U kunt dit ook doen via [Power shell/cli](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

2.  Controleer of uw UDR een standaard route (0.0.0.0/0) heeft met de volgende hop, niet als Internet
    
    a.  Volg stap 1. a en 1. b om uw subnet te bepalen

    b.  Controleer of er UDR zijn geconfigureerd. Als dat het geval is, zoekt u naar de resource op de zoek balk of onder alle resources

    c.  Controleer of er standaard routes (0.0.0.0/0) zijn met de volgende hop, niet als Internet. Als het virtuele apparaat of Virtual Network gateway is, moet u ervoor zorgen dat uw virtuele apparaat of het on-premises apparaten het pakket op de juiste wijze naar de Internet bestemming kunnen door sturen zonder het pakket te wijzigen

    d.  Anders wijzigt u de volgende hop naar Internet, klikt u op opslaan en controleert u de status van de back-end

3.  Standaard route geadverteerd door ExpressRoute/VPN-verbinding met het VNet via BGP

    a.  Als u een ExpressRoute/VPN-verbinding met het VNet via BGP hebt en u een standaard route adverteert, moet u ervoor zorgen dat het pakket wordt teruggestuurd naar de Internet bestemming zonder het te wijzigen

    b.  U kunt controleren met behulp van de optie ' problemen met verbinding oplossen ' in Application Gateway portal

    c.  Kies de bestemming hand matig als een IP-adres voor Internet Routeer, zoals ' 1.1.1.1 ' en de doel poort als wille keurig en controleer de connectiviteit.

    d.  Als de volgende hop Virtual Network gateway is, is er mogelijk een standaard route geadverteerd via ExpressRoute of VPN

4.  Als er een aangepaste DNS-server is geconfigureerd in het VNet, controleert u of de server (s) open bare domeinen kan omzetten. Het kan zijn dat naam omzetting van open bare domeinen vereist is in scenario's waarbij Application Gateway moet worden bereikt naar externe domeinen als OCSP-servers of om de intrekkings status van het certificaat te controleren, enzovoort.

5.  Als u wilt controleren of de Application Gateway in orde is en wordt uitgevoerd, gaat u naar Resource Health optie in de portal en controleert u of deze ' in orde ' is. [Neem contact op met de ondersteuning](https://azure.microsoft.com/support/options/)als u een slechte of slechte status ziet.

<a name="next-steps"></a>Volgende stappen
----------

Zie [hier](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)voor meer informatie over Application Gateway diagnostische gegevens en logboek registratie.
