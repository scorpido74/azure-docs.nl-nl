---
title: Werken met bestaande on-premises proxyservers en Azure AD | Microsoft Documenten
description: Dekt hoe u werken met bestaande on-premises proxyservers.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fe3a63e119fed6825982b9de13bc78cb7da5415
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481395"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Werken met bestaande on-premises proxyservers

In dit artikel wordt uitgelegd hoe u Azure Active Directory -toepassingsproxyconnectors (Azure AD) configureert om te werken met uitgaande proxyservers. Het is bedoeld voor klanten met netwerkomgevingen met bestaande proxy's.

We beginnen met het bekijken van deze belangrijkste implementatiescenario's:

* Configureer connectors om uw on-premises uitgaande proxy's te omzeilen.
* Configureer connectors om een uitgaande proxy te gebruiken om toegang te krijgen tot Azure AD Application Proxy.

Zie [Meer informatie over Azure AD-toepassingsproxyconnectoren](application-proxy-connectors.md) voor meer informatie over de werking van connectoren.

## <a name="bypass-outbound-proxies"></a>Uitgaande proxy's overslaan

Connectoren hebben onderliggende besturingssysteemonderdelen waardoor de uitgaande aanvragen worden gemaakt. Deze onderdelen zoeken automatisch een proxyserver in het netwerk met behulp van Web Proxy Auto-Discovery (WPAD).

De besturingssysteemonderdelen zoeken een proxyserver door een DNS-zoekactie voor wpad.domainsuffix uit te voeren. Als de zoekopdracht wordt omgezet in DNS, wordt vervolgens een HTTP-aanvraag ingediend op het IP-adres voor wpad.dat. Deze aanvraag wordt het proxyconfiguratiescript in uw omgeving. Dit script wordt door de connector gebruikt om een uitgaande proxyserver te selecteren. Het is echter mogelijk dat verkeer op de connector nog steeds niet doorkomt, omdat er extra configuratie-instellingen nodig zijn op de proxy.

U kunt de connector configureren om uw on-premises proxy over te slaan, zodat u zeker weet dat de connector rechtstreeks is verbonden met de Azure-services. Dit is de aanbevolen methode zolang deze mogelijk is volgens uw netwerkbeleid, omdat u in dit geval één configuratie minder hoeft te onderhouden.

Als u uitgaand proxygebruik voor de connector wilt uitschakelen, bewerkt u het C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config bestand en voegt u de *system.net* sectie toe die in dit codevoorbeeld wordt weergegeven:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>
    <defaultProxy enabled="false"></defaultProxy>
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Als u ervoor wilt zorgen dat de connectorupdater-service ook de proxy omzeilt, voert u een vergelijkbare wijziging aan in het bestand ApplicationProxyConnectorUpdaterService.exe.config. Dit bestand bevindt zich op C:\Program Files\Microsoft AAD App Proxy Connector Updater.

Vergeet niet om kopieën van de oorspronkelijke bestanden te maken, in het geval u de .config-bestanden naar de standaardinstellingen moet terugzetten.

## <a name="use-the-outbound-proxy-server"></a>De uitgaande proxyserver gebruiken

Voor sommige omgevingen is al het uitgaande verkeer vereist dat deze zonder uitzondering door een uitgaande proxy gaat. Als gevolg hiervan is het omzeilen van de proxy geen optie.

U het verbindingsverkeer zo configureren dat deze door de uitgaande proxy gaat, zoals in het volgende diagram wordt weergegeven:

 ![Connectorverkeer configureren om via een uitgaande proxy naar Azure AD-toepassingsproxy te gaan](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

Als gevolg van alleen uitgaand verkeer, is het niet nodig om binnenkomende toegang via uw firewalls te configureren.

> [!NOTE]
> Application Proxy ondersteunt geen verificatie naar andere proxy's. De netwerkserviceaccounts van de connector/updater moeten verbinding kunnen maken met de proxy zonder te worden uitgedaagd voor verificatie.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Stap 1: De connector en gerelateerde services configureren om door de uitgaande proxy te gaan

Als WPAD is ingeschakeld in de omgeving en op de juiste manier is geconfigureerd, detecteert de connector automatisch de uitgaande proxyserver en probeert deze te gebruiken. U de connector echter expliciet configureren om via een uitgaande proxy te gaan.

Bewerk hiervoor het C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config bestand en voeg de *system.net* sectie toe die in dit codevoorbeeld wordt weergegeven. Wijzig *proxyserver:8080* om de naam of het IP-adres van uw lokale proxyserver weer te geven en de poort waarop deze luistert. De waarde moet het voorvoegsel hebben http://, zelfs als u een IP-adres gebruikt.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Configureer vervolgens de connectorupdater-service om de proxy te gebruiken door een vergelijkbare wijziging aan te brengen in het c:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config bestand.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Stap 2: De proxy configureren zodat verkeer van de connector en gerelateerde services door kan stromen

Er zijn vier aspecten te overwegen op de uitgaande proxy:

* Uitgaande proxyregels
* Proxyverificatie
* Proxypoorten
* TLS-inspectie

#### <a name="proxy-outbound-rules"></a>Uitgaande proxyregels

Sta toegang tot de volgende URL's toe:

| URL | Hoe dat wordt gebruikt |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Communicatie tussen de connector en de Application Proxy-cloudservice |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | De connector gebruikt deze URL's om certificaten te verifiëren |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>*.microsoftonline.com<br>*.microsoftonline-p.com<br>*.msauth.net<br>*.msauthimages.net<br>*.msecnd.net<br>*.msftauth.net<br>*.msftauthimages.net<br>*.phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctdl.windowsupdate.com:80 | De connector gebruikt deze URL's tijdens het registratieproces. |

Als u met uw firewall of proxy DNS-lijst \*kunt configureren, u verbindingen toestaan om .msappproxy.net en \*.servicebus.windows.net. Als dat niet het geval is, moet u toegang toestaan tot de [IP-bereiken van Azure DataCenter](https://www.microsoft.com/download/details.aspx?id=41653), die overigens elke week worden bijgewerkt.

Als u geen connectiviteit per FQDN toestaan en in plaats daarvan IP-bereiken moet opgeven, gebruikt u de volgende opties:

* Geef de connector uitgaande toegang tot alle bestemmingen.
* Geef de connector uitgaande toegang tot alle [IP-bereiken van Azure-datacenters.](https://www.microsoft.com//download/details.aspx?id=41653) De uitdaging met het gebruik van de lijst met AZURE datacenter IP-bereiken is dat het wekelijks wordt bijgewerkt. U moet een proces invoeren om ervoor te zorgen dat uw toegangsregels dienovereenkomstig worden bijgewerkt. Alleen het gebruik van een subset van de IP-adressen kan ertoe leiden dat uw configuratie wordt afgebroken.

#### <a name="proxy-authentication"></a>Proxyverificatie

Proxyverificatie wordt momenteel niet ondersteund. Onze huidige aanbeveling is om de connector anonieme toegang tot de internetbestemmingen.

#### <a name="proxy-ports"></a>Proxypoorten

De connector maakt uitgaande TLS-verbindingen met behulp van de CONNECT-methode. Deze methode stelt in wezen een tunnel in via de uitgaande proxy. Configureer de proxyserver om tunneling naar poorten 443 en 80 mogelijk te maken.

> [!NOTE]
> Wanneer Service Bus over HTTPS rijdt, gebruikt deze poort 443. Service Bus probeert echter standaard directe TCP-verbindingen en valt alleen terug naar HTTPS als de directe verbinding mislukt.

#### <a name="tls-inspection"></a>TLS-inspectie

Gebruik geen TLS-inspectie voor het verbindingsverkeer, omdat dit problemen veroorzaakt voor het verbindingsverkeer. De connector gebruikt een certificaat om te verifiëren aan de Application Proxy-service en dat certificaat kan verloren gaan tijdens tls-inspectie.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Problemen met de proxy van de connector oplossen en problemen met de serviceconnectiviteit oplossen

Nu moet je zien al het verkeer stroomt door de proxy. Als u problemen hebt, moet de volgende informatie over het oplossen van problemen helpen.

De beste manier om verbindingsproblemen te identificeren en op te lossen, is door een netwerkopname te maken tijdens het starten van de connectorservice. Hier volgen enkele snelle tips over het vastleggen en filteren van netwerksporen.

U de monitoringtool van uw keuze gebruiken. Voor de toepassing van dit artikel hebben we Microsoft Message Analyzer gebruikt. U [het downloaden van Microsoft](https://www.microsoft.com/download/details.aspx?id=44226).

De volgende voorbeelden zijn specifiek voor Message Analyzer, maar de principes kunnen worden toegepast op elke analysetool.

### <a name="take-a-capture-of-connector-traffic"></a>Neem een opname van verbindingsverkeer

Voer de volgende stappen uit voor de eerste probleemoplossing:

1. Stop vanuit services.msc de Azure AD Application Proxy Connector-service.

   ![Azure AD Application Proxy Connector-service in services.msc](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. Voer Message Analyzer uit als beheerder.
1. Selecteer **Lokaal traceren starten**.
1. Start de Azure AD Application Proxy Connector-service.
1. Stop de netwerkopname.

   ![Schermafbeelding van de knop Netwerkvastleggen stoppen](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Controleren of het verbindingsverkeer uitgaande proxy's omzeilt

Als u de toepassingsproxyconnector hebt geconfigureerd om de proxyservers te omzeilen en rechtstreeks verbinding te maken met de application proxy-service, wilt u in de netwerkopname kijken naar mislukte TCP-verbindingspogingen.

Gebruik het filter Message Analyzer om deze pogingen te identificeren. Voer `property.TCPSynRetransmit` het filtervak in en selecteer **Toepassen**.

Een SYN-pakket is het eerste pakket dat wordt verzonden om een TCP-verbinding tot stand te brengen. Als dit pakket geen antwoord retourneert, wordt de SYN opnieuw geprobeerd. U het vorige filter gebruiken om opnieuw verzonden SYN's te bekijken. Vervolgens u controleren of deze SYN's overeenkomen met verbindingsverkeer.

Als u verwacht dat de connector directe verbindingen maakt met de Azure-services, zijn SynRetransmit-antwoorden op poort 443 een indicatie dat u een netwerk- of firewallprobleem hebt.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Controleren of het verbindingsverkeer uitgaande proxy's gebruikt

Als u het netwerkverkeer van de toepassingsproxy hebt geconfigureerd om via de proxyservers te gaan, wilt u zoeken naar mislukte https-verbindingen met uw proxy.

Als u de netwerkopname voor `(https.Request or https.Response) and tcp.port==8080` deze verbindingspogingen wilt filteren, voert u het filter Message Analyzer in en vervangt u 8080 door uw proxyservicepoort. Selecteer **Toepassen** om de filterresultaten te bekijken.

Het vorige filter toont alleen de HTTPs-aanvragen en antwoorden naar/van de proxypoort. U bent op zoek naar de CONNECT-aanvragen die communicatie met de proxyserver weergeven. Bij succes krijg je een HTTP OK (200) reactie.

Als u andere antwoordcodes ziet, zoals 407 of 502, betekent dit dat de proxy verificatie vereist of het verkeer om een andere reden niet toestaat. Op dit moment schakel je je proxy server support team in.

## <a name="next-steps"></a>Volgende stappen

* [Informatie over Azure AD-toepassingsproxyconnectors](application-proxy-connectors.md)
* Als u problemen hebt met verbindingsproblemen met de connector, stelt u uw vraag in het [Azure Active Directory-forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) of maakt u een ticket met ons ondersteuningsteam.
