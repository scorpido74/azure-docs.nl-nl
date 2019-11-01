---
title: Werken met bestaande on-premises proxy servers en Azure AD | Microsoft Docs
description: In dit artikel wordt beschreven hoe u kunt werken met bestaande on-premises proxy servers.
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
ms.openlocfilehash: d305f3354e7b1af6d43f31f0dd5fe9f54ef3e66f
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242275"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Werken met bestaande on-premises proxy servers

In dit artikel wordt uitgelegd hoe u de connectors van de toepassings proxy van Azure Active Directory (Azure AD) kunt configureren voor gebruik met uitgaande proxy servers. Het is bedoeld voor klanten met netwerk omgevingen die bestaande proxy's hebben.

We gaan nu naar deze belangrijkste implementatie scenario's kijken:

* Connectors configureren om uw on-premises uitgaande proxy's over te slaan.
* Connectors configureren voor het gebruik van een uitgaande proxy om toegang te krijgen tot Azure AD-toepassingsproxy.

Zie voor meer informatie over hoe connectors werken [begrijpen Azure AD-toepassingsproxy-connectors](application-proxy-connectors.md).

## <a name="bypass-outbound-proxies"></a>Uitgaande proxy's overs Laan

Connectors hebben onderliggende besturingssysteem onderdelen waarmee uitgaande aanvragen worden gemaakt. Deze onderdelen proberen automatisch een proxy server in het netwerk te vinden met behulp van Web Proxy Auto-Discovery (WPAD).

De OS-onderdelen proberen een proxy server te vinden door een DNS-zoek opdracht voor WPAD. domainsuffix uit te voeren. Als de zoek actie in DNS wordt opgelost, wordt er een HTTP-aanvraag verzonden naar het IP-adres voor WPAD. dat. Deze aanvraag wordt het configuratie script van de proxy in uw omgeving. De connector gebruikt dit script om een uitgaande proxy server te selecteren. Connector verkeer kan echter nog steeds niet door lopen vanwege aanvullende configuratie-instellingen die nodig zijn voor de proxy.

U kunt de connector configureren om uw on-premises proxy te omzeilen om ervoor te zorgen dat deze gebruikmaakt van een directe verbinding met de Azure-Services. We raden u aan deze aanpak uit te voeren, zolang uw netwerk beleid dit toestaat, omdat het betekent dat u een minder configuratie hebt om te onderhouden.

Als u het uitgaande proxy gebruik voor de connector wilt uitschakelen, bewerkt u het Connector\ApplicationProxyConnectorService.exe.config-bestand voor de C:\Program Files\Microsoft AAD-app-proxy en voegt u de sectie *System.net* toe, zoals weer gegeven in dit code voorbeeld:

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

Om ervoor te zorgen dat de Connector Updater-service ook de proxy omzeilt, maakt u een vergelijk bare wijziging in het bestand ApplicationProxyConnectorUpdaterService. exe. config. Dit bestand bevindt zich in C:\Program Files\Microsoft AAD app proxy Connector Updater.

Zorg ervoor dat u kopieën van de oorspronkelijke bestanden maakt, voor het geval u de standaard. config-bestanden moet herstellen.

## <a name="use-the-outbound-proxy-server"></a>De uitgaande proxy server gebruiken

In sommige omgevingen moet al het uitgaande verkeer via een uitgaande proxy zonder uitzonde ring worden door lopen. Als gevolg hiervan is het overs laan van de proxy geen optie.

U kunt het connector verkeer configureren om via de uitgaande proxy door te gaan, zoals wordt weer gegeven in het volgende diagram:

 ![Connector verkeer configureren om door te gaan met een uitgaande proxy naar Azure AD-toepassingsproxy](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

Als gevolg van alleen uitgaand verkeer, hoeft u geen inkomende toegang via uw firewalls te configureren.

> [!NOTE]
> Toepassings proxy biedt geen ondersteuning voor verificatie voor andere proxy's. De connector-en Updater netwerk service accounts moeten verbinding kunnen maken met de proxy zonder te worden bevraagd voor authenticatie.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Stap 1: de connector en gerelateerde services configureren om via de uitgaande proxy door te gaan

Als WPAD is ingeschakeld in de omgeving en op de juiste wijze is geconfigureerd, detecteert de connector automatisch de uitgaande proxy server en probeert deze te gebruiken. U kunt de connector echter expliciet configureren om door te gaan met een uitgaande proxy.

U doet dit door het Connector\ApplicationProxyConnectorService.exe.config-bestand van de C:\Program Files\Microsoft AAD-app te bewerken en de sectie *System.net* toe te voegen die wordt weer gegeven in dit code voorbeeld. Wijzig *proxy server: 8080* zodat deze overeenkomt met de naam van uw lokale proxy server of IP-adres en de poort waarop deze luistert. De waarde moet het voor voegsel http://bevatten, zelfs als u een IP-adres gebruikt.

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

Configureer vervolgens de Connector Updater-Service voor het gebruik van de proxy door een vergelijk bare wijziging aan te brengen in het bestand met de Updater\ApplicationProxyConnectorUpdaterService.exe.config voor de C:\Program Files\Microsoft AAD-app-connector.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Stap 2: Configureer de proxy zodanig dat verkeer van de connector en gerelateerde services wordt door lopen

Er zijn vier aspecten waarmee u rekening moet houden bij de uitgaande proxy:

* Regels voor uitgaande proxy
* Proxy verificatie
* Proxy poorten
* SSL-inspectie

#### <a name="proxy-outbound-rules"></a>Regels voor uitgaande proxy

Sta toegang tot de volgende URL's toe:

| URL | Hoe dat wordt gebruikt |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Communicatie tussen de connector en de Application Proxy-cloudservice |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Azure gebruikt deze URL's om certificaten te controleren |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>*. microsoftonline.com<br>* . microsoftonline-p.com<br>*. msauth.net<br>* . msauthimages.net<br>*. msecnd.net<br>* . msftauth.net<br>*. msftauthimages.net<br>* . phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net | De connector gebruikt deze URL's tijdens het registratieproces. |

Als uw firewall of proxy u toestaat om DNS-acceptatie lijsten te configureren, kunt u verbindingen met \*. msappproxy.net en \*. servicebus.windows.net toestaan. Als dat niet het geval is, moet u toegang toestaan tot de [IP-bereiken van Azure DataCenter](https://www.microsoft.com/download/details.aspx?id=41653), die overigens elke week worden bijgewerkt.

Als u geen verbinding kunt maken met FQDN en u in plaats daarvan IP-bereiken moet opgeven, gebruikt u de volgende opties:

* Hiermee staat u de uitgaande toegang van de connector tot alle doelen toe.
* Hiermee wordt de uitgaande toegang van de connector tot alle [IP-bereiken van het Azure-Data Center](https://www.microsoft.com//download/details.aspx?id=41653)toegestaan. De uitdaging bij het gebruik van de lijst met IP-bereiken van Azure Data Center is dat deze wekelijks wordt bijgewerkt. U moet een proces plaatsen om ervoor te zorgen dat uw toegangs regels dienovereenkomstig worden bijgewerkt. Alleen het gebruik van een subset van de IP-adressen kan ertoe leiden dat uw configuratie wordt verbroken.

#### <a name="proxy-authentication"></a>Proxy verificatie

Proxy authenticatie wordt momenteel niet ondersteund. Onze huidige aanbeveling is de connector anonieme toegang tot de Internet bestemmingen te geven.

#### <a name="proxy-ports"></a>Proxy poorten

De connector maakt uitgaande op SSL gebaseerde verbindingen met behulp van de methode CONNECT. Met deze methode wordt in feite een tunnel ingesteld via de uitgaande proxy. Configureer de proxy server voor het toestaan van tunneling naar poorten 443 en 80.

> [!NOTE]
> Als Service Bus over HTTPS wordt uitgevoerd, wordt poort 443 gebruikt. Service Bus probeert echter standaard TCP-verbindingen te sturen en terug te vallen op HTTPS als de directe verbinding mislukt.

#### <a name="ssl-inspection"></a>SSL-inspectie

Gebruik geen SSL-inspectie voor het connector verkeer, omdat dit problemen veroorzaakt voor het verkeer van de connector. De connector gebruikt een certificaat om te verifiëren bij de toepassings proxy service, en dat certificaat kan verloren gaan tijdens SSL-inspectie.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Problemen met Connector proxy en problemen met de service connectiviteit oplossen

Nu ziet u al het verkeer dat via de proxy stroom loopt. Als u problemen ondervindt, kunt u de volgende informatie over het oplossen van problemen ondervinden.

De beste manier om verbindings problemen met connectors op te sporen en op te lossen is het maken van een netwerk vastleggen tijdens het starten van de connector service. Hier volgen enkele tips voor het vastleggen en filteren van netwerk traceringen.

U kunt het controle programma van uw keuze gebruiken. Voor de doel einden van dit artikel hebben we micro soft Message Analyzer gebruikt. U kunt [het downloaden van micro soft](https://www.microsoft.com/download/details.aspx?id=44226).

De volgende voor beelden zijn specifiek voor Message Analyzer, maar de principes kunnen worden toegepast op elk analyse programma.

### <a name="take-a-capture-of-connector-traffic"></a>Neem een opname van connector verkeer

Voer de volgende stappen uit voor eerste probleem oplossing:

1. Stop de Azure AD-toepassingsproxy-connector service vanuit Services. msc.

   ![Azure AD-toepassingsproxy-connector service in Services. msc](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. Voer Message Analyzer uit als Administrator.
1. Selecteer **lokale tracering starten**.
1. Start de service Azure AD-toepassingsproxy-connector.
1. Stop de netwerk opname.

   ![Scherm afbeelding toont de knop netwerk opname stoppen](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Controleren of het connector verkeer uitgaande proxy's omzeilt

Als u de connector voor de toepassings proxy hebt geconfigureerd om de proxy servers te omzeilen en rechtstreeks verbinding te maken met de toepassings proxy service, wilt u zoeken in het netwerk vastleggen voor mislukte TCP-verbindings pogingen.

Gebruik het filter Message Analyzer om deze pogingen te identificeren. Geef `property.TCPSynRetransmit` op in het vak Filter en selecteer **Toep assen**.

Een SYN-pakket is het eerste pakket dat wordt verzonden om een TCP-verbinding tot stand te brengen. Als dit pakket geen antwoord retourneert, wordt de SYN opnieuw geprobeerd. U kunt het voor gaande filter gebruiken om alle opnieuw verzonden SYNs te bekijken. Vervolgens kunt u controleren of deze SYNs overeenkomen met elk verkeer dat betrekking heeft op de connector.

Als u verwacht dat de connector directe verbindingen met de Azure-Services maakt, geven SynRetransmit-antwoorden op poort 443 een indicatie dat er een probleem is met het netwerk of de firewall.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Controleren of het connector verkeer uitgaande proxy's gebruikt

Als u het verkeer van de toepassings proxy connector hebt geconfigureerd om de proxy servers te passeren, wilt u controleren of er mislukte HTTPS-verbindingen met uw proxy zijn.

Als u de netwerk opname voor deze verbindings pogingen wilt filteren, voert u `(https.Request or https.Response) and tcp.port==8080` in het filter Message Analyzer in, waarbij u 8080 vervangt door uw proxy service-poort. Selecteer **Toep assen** om de filter resultaten weer te geven.

In het voor gaande filter worden alleen de HTTPs-aanvragen en reacties van/naar de Proxy poort weer gegeven. U zoekt naar de VERBINDINGS aanvragen die communicatie met de proxy server weer geven. Wanneer dit is gelukt, krijgt u een HTTP OK (200)-antwoord.

Als u andere antwoord codes ziet, zoals 407 of 502, betekent dit dat de proxy verificatie vereist of het verkeer om een andere reden niet toestaat. Op dit moment kunt u uw ondersteunings team voor de proxy server benaderen.

## <a name="next-steps"></a>Volgende stappen

* [Azure AD-toepassingsproxy-connectors begrijpen](application-proxy-connectors.md)
* Als u problemen ondervindt met connector verbindings problemen, vraagt u uw vraag in het [Azure Active Directory Forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) of maakt u een ticket met het ondersteunings team.
