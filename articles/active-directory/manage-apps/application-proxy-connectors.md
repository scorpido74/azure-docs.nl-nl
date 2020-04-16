---
title: Informatie over Azure AD-toepassingsproxyconnectors | Microsoft Documenten
description: Dekt de basisprincipes van Azure AD Application Proxy-connectors.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f1b8b9af8f90629d087246edf0cb3426bd9b66c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406834"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Informatie over Azure AD-toepassingsproxyconnectors

Connectors maken Azure AD Application Proxy mogelijk. Ze zijn eenvoudig, eenvoudig te implementeren en te onderhouden, en super krachtig. In dit artikel wordt besproken wat connectors zijn, hoe ze werken en enkele suggesties voor het optimaliseren van uw implementatie.

## <a name="what-is-an-application-proxy-connector"></a>Wat is een Application Proxy-connector?

Connectors zijn lichtgewicht agents die on-premises zitten en de uitgaande verbinding met de Application Proxy-service vergemakkelijken. Connectors moeten worden geïnstalleerd op een Windows Server die toegang heeft tot de backend-toepassing. U connectoren ordenen in verbindingsgroepen, waarbij elke groep verkeer naar specifieke toepassingen verwerkt.

## <a name="requirements-and-deployment"></a>Vereisten en implementatie

Als u Application Proxy met succes wilt implementeren, hebt u ten minste één connector nodig, maar we raden twee of meer aan voor meer tolerantie. Installeer de connector op een machine met Windows Server 2012 R2 of hoger. De connector moet communiceren met de Application Proxy-service en de on-premises toepassingen die u publiceert.

### <a name="windows-server"></a>Windows-server
U hebt een server met Windows Server 2012 R2 of hoger nodig waarop u de application proxy-connector installeren. De server moet verbinding maken met de application proxy-services in Azure en de on-premises toepassingen die u publiceert.

De windows-server moet TLS 1.2 hebben ingeschakeld voordat u de Application Proxy-connector installeert. Ga als u TLS 1.2 op de server in:

1. Stel de volgende registersleutels in:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Start de server opnieuw

Zie [Aan de slag met Application Proxy en installeer een connector](application-proxy-add-on-premises-application.md)voor meer informatie over de netwerkvereisten voor de connectorserver.

## <a name="maintenance"></a>Onderhoud

De connectoren en de service zorgen voor alle taken met hoge beschikbaarheid. Ze kunnen dynamisch worden toegevoegd of verwijderd. Elke keer dat een nieuwe aanvraag binnenkomt, wordt deze doorgestuurd naar een van de connectors die momenteel beschikbaar is. Als een connector tijdelijk niet beschikbaar is, reageert deze niet op dit verkeer.

De connectors zijn stateloos en hebben geen configuratiegegevens op de machine. De enige gegevens die ze opslaan zijn de instellingen voor het aansluiten van de service en het verificatiecertificaat. Wanneer ze verbinding maken met de service, halen ze alle vereiste configuratiegegevens op en vernieuwen ze deze om de paar minuten.

Connectors peilen ook de server om erachter te komen of er een nieuwere versie van de connector is. Als er een wordt gevonden, worden de connectors zelf bijgewerkt.

U uw connectors controleren vanaf de machine waarop ze draaien, met behulp van het gebeurtenislogboek en de prestatiemeteritems. U hun status ook bekijken op de pagina Application Proxy van de Azure-portal:

![Voorbeeld: Azure AD Application Proxy connectors](./media/application-proxy-connectors/app-proxy-connectors.png)

U hoeft niet handmatig connectoren te verwijderen die ongebruikt zijn. Wanneer een connector wordt uitgevoerd, blijft deze actief wanneer deze verbinding maakt met de service. Ongebruikte connectors worden getagd als _inactief_ en worden verwijderd na 10 dagen inactiviteit. Als u echter een connector wilt verwijderen, verwijdert u zowel de Connector-service als de Updater-service van de server. Start de computer opnieuw op om de service volledig te verwijderen.

## <a name="automatic-updates"></a>Automatische updates

Azure AD biedt automatische updates voor alle connectors die u implementeert. Zolang de Application Proxy Connector Updater-service wordt uitgevoerd, worden uw connectors automatisch bijgewerkt. Als u de Connector Updater-service niet op uw server ziet, moet u [uw connector opnieuw installeren](application-proxy-add-on-premises-application.md) om updates te ontvangen.

Als u niet wilt wachten tot er een automatische update naar uw connector komt, u een handmatige upgrade uitvoeren. Ga naar de [pagina voor het downloaden van connectoren](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) op de server waar uw connector zich bevindt en selecteer **Downloaden.** Dit proces start een upgrade voor de lokale connector.

Voor tenants met meerdere connectors is de automatische update gericht op één connector tegelijk in elke groep om downtime in uw omgeving te voorkomen.

U downtime ervaren wanneer uw connector wordt bijgewerkt als:
  
- U hebt slechts één connector, we raden u aan een tweede connector te installeren en [een verbindingsgroep te maken.](application-proxy-connector-groups.md) Dit voorkomt downtime en zorgt voor een hogere beschikbaarheid.  
- Er zat een connector in het midden van een transactie toen de update begon. Hoewel de eerste transactie verloren gaat, moet uw browser de bewerking automatisch opnieuw proberen of u uw pagina vernieuwen. Wanneer het verzoek wordt verweten, wordt het verkeer doorgestuurd naar een back-upconnector.

Zie Releasegeschiedenis van [de toepassingsproxy](application-proxy-release-version-history.md)voor eerder uitgebrachte versies en welke wijzigingen deze bevatten.

## <a name="creating-connector-groups"></a>Verbindingsgroepen maken

Met verbindingsgroepen u specifieke connectors toewijzen om specifieke toepassingen te serveren. U een aantal connectors groeperen en vervolgens elke toepassing aan een groep toewijzen.

Connectorgroepen maken het eenvoudiger om grote implementaties te beheren. Ze verbeteren ook de latentie voor tenants met toepassingen die in verschillende regio's worden gehost, omdat u op locatie gebaseerde connectorgroepen maken om alleen lokale toepassingen te bieden.

Zie Toepassingen publiceren op [afzonderlijke netwerken en locaties met behulp van verbindingsgroepen](application-proxy-connector-groups.md)voor meer informatie over verbindingsgroepen.

## <a name="capacity-planning"></a>Capaciteitsplanning

Het is belangrijk om ervoor te zorgen dat u voldoende capaciteit tussen connectoren hebt gepland om het verwachte verkeersvolume te verwerken. We raden aan dat elke connectorgroep ten minste twee connectoren heeft om hoge beschikbaarheid en schaal te bieden. Het hebben van drie connectoren is optimaal voor het geval u een machine op elk gewenst moment moet bedienen.

Over het algemeen, hoe meer gebruikers je hebt, hoe groter een machine die je nodig hebt. Hieronder vindt u een tabel met een overzicht van het volume en de verwachte latentie die verschillende machines aankunnen. Let op: het is allemaal gebaseerd op verwachte Transacties per seconde (TPS) in plaats van per gebruiker, omdat gebruikspatronen variëren en niet kunnen worden gebruikt om belasting te voorspellen. Er zullen ook enkele verschillen zijn op basis van de grootte van de antwoorden en de responstijd van de backend-toepassing - grotere responsformaten en tragere responstijden zullen resulteren in een lagere Max TPS. We raden ook aan om extra machines te hebben, zodat de gedistribueerde belasting over de machines altijd voldoende buffer biedt. De extra capaciteit zorgt ervoor dat u een hoge beschikbaarheid en veerkracht heeft.

|Kernen|RAM|Verwachte latentie (MS)-P99|Max.|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|

\*Deze machine heeft een aangepaste instelling gebruikt om een aantal van de standaardverbindingslimieten te verhogen die verder gaan dan de aanbevolen .NET-instellingen. We raden u aan een test uit te voeren met de standaardinstellingen voordat u contact opneemt met de ondersteuning om deze limiet voor uw tenant te wijzigen.

> [!NOTE]
> Er is niet veel verschil in de maximale TPS tussen 4, 8 en 16 kernmachines. Het belangrijkste verschil tussen deze is in de verwachte latentie.
>
> Deze tabel richt zich ook op de verwachte prestaties van een connector op basis van het type machine waarop deze is geïnstalleerd. Dit staat los van de beperkingslimieten van de application proxy-service, zie [Servicelimieten en -beperkingen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="security-and-networking"></a>Beveiliging en netwerken

Connectors kunnen overal in het netwerk worden geïnstalleerd waarmee ze aanvragen kunnen verzenden naar de Application Proxy-service. Wat belangrijk is, is dat de computer waarop de connector wordt uitgevoerd, ook toegang heeft tot uw apps. U connectoren installeren in uw bedrijfsnetwerk of op een virtuele machine die in de cloud wordt uitgevoerd. Connectors kunnen worden uitgevoerd binnen een perimeternetwerk, ook wel een gedemilitariseerde zone (DMZ) genoemd, maar dit is niet nodig omdat al het verkeer uitgaand is, zodat uw netwerk veilig blijft.

Connectors verzenden alleen uitgaande verzoeken. Het uitgaande verkeer wordt verzonden naar de Application Proxy-service en naar de gepubliceerde toepassingen. U hoeft geen binnenkomende poorten te openen omdat het verkeer beide kanten op stroomt zodra een sessie is ingesteld. U hoeft ook geen binnenkomende toegang te configureren via uw firewalls.

Zie [Werken met bestaande on-premises proxyservers](application-proxy-configure-connectors-with-proxy-servers.md)voor meer informatie over het configureren van uitgaande firewallregels.

## <a name="performance-and-scalability"></a>Prestaties en schaalbaarheid

Schalen voor de Application Proxy-service is transparant, maar schaal is een factor voor connectors. Je moet genoeg connectoren hebben om piekverkeer te verwerken. Aangezien connectors stateloos zijn, worden ze niet beïnvloed door het aantal gebruikers of sessies. In plaats daarvan reageren ze op het aantal aanvragen en hun laadvermogengrootte. Met standaard webverkeer kan een gemiddelde machine een paar duizend aanvragen per seconde verwerken. De specifieke capaciteit is afhankelijk van de exacte machinekenmerken.

De prestaties van de connector zijn gebonden aan CPU en netwerken. CPU-prestaties zijn nodig voor TLS-versleuteling en decryptie, terwijl netwerken belangrijk is om snelle connectiviteit met de toepassingen en de online service in Azure te krijgen.

Geheugen is daarentegen minder een probleem voor connectors. De online service zorgt voor een groot deel van de verwerking en alle niet-geverifieerde verkeer. Alles wat in de cloud gedaan kan worden, gebeurt in de cloud.

Als deze connector of machine om welke reden dan ook niet meer beschikbaar is, gaat het verkeer naar een andere connector in de groep. Deze veerkracht is ook de reden waarom we raden het hebben van meerdere connectoren.

Een andere factor die de prestaties beïnvloedt, is de kwaliteit van het netwerken tussen de connectors, waaronder:

- **De onlineservice**: Langzame of hoge latentieverbindingen met de application proxy-service in Azure beïnvloeden de prestaties van de connector. Voor de beste prestaties verbindt u uw organisatie met Azure met Express Route. Laat uw netwerkteam er anders voor zorgen dat verbindingen met Azure zo efficiënt mogelijk worden afgehandeld.
- **De backend-toepassingen**: In sommige gevallen zijn er extra proxy's tussen de connector en de backend-toepassingen die verbindingen kunnen vertragen of voorkomen. Als u dit scenario wilt oplossen, opent u een browser vanaf de connectorserver en probeert u toegang te krijgen tot de toepassing. Als u de connectors in Azure uitvoert, maar de toepassingen on-premises zijn, is de ervaring mogelijk niet wat uw gebruikers verwachten.
- **De domeincontrollers:** Als de connectors eenmalige aanmelding (SSO) uitvoeren met Kerberos Constrained Delegation, nemen ze contact op met de domeincontrollers voordat ze het verzoek naar de backend verzenden. De connectors hebben een cache van Kerberos-tickets, maar in een drukke omgeving kan de responsiviteit van de domeincontrollers de prestaties beïnvloeden. Dit probleem komt vaker voor voor connectors die in Azure worden uitgevoerd, maar die communiceren met domeincontrollers die on-premises zijn.

Zie [Overwegingen voor netwerktopologie bij het gebruik van Azure Active Directory Application Proxy](application-proxy-network-topology.md)voor meer informatie over het optimaliseren van uw netwerk.

## <a name="domain-joining"></a>Domeinsamenvoegen

Connectors kunnen worden uitgevoerd op een machine die niet is aangesloten op het domein. Als u echter eenmalige aanmelding (SSO) wilt voor toepassingen die geïntegreerde Windows-verificatie (IWA) gebruiken, hebt u een machine met domeinverbonden machine nodig. In dit geval moeten de verbindingsmachines worden samengevoegd met een domein dat [Kerberos](https://web.mit.edu/kerberos) Constrained Delegation namens de gebruikers voor de gepubliceerde toepassingen kan uitvoeren.

Connectors kunnen ook worden samengevoegd met domeinen of forests die een gedeeltelijke vertrouwensrelatie hebben, of met alleen-lezen domeincontrollers.

## <a name="connector-deployments-on-hardened-environments"></a>Connectorimplementaties op geharde omgevingen

Meestal is de implementatie van de connector eenvoudig en vereist u geen speciale configuratie. Er zijn echter een aantal unieke voorwaarden die moeten worden overwogen:

- Organisaties die het uitgaande verkeer beperken, moeten [vereiste poorten openen.](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)
- Fips-compatibele machines moeten mogelijk hun configuratie wijzigen, zodat de connectorprocessen een certificaat kunnen genereren en opslaan.
- Organisaties die hun omgeving vergrendelen op basis van de processen die de netwerkaanvragen uitgeven, moeten ervoor zorgen dat beide verbindingsservices toegang hebben tot alle vereiste poorten en IP's.
- In sommige gevallen kunnen uitgaande voorwaartse proxy's de tweerichtingscertificaatverificatie verbreken en ervoor zorgen dat de communicatie mislukt.

## <a name="connector-authentication"></a>Verificatie voor connector

Om een veilige service te bieden, moeten connectors zich verifiëren in de richting van de service en moet de service zich verifiëren in de richting van de connector. Deze verificatie gebeurt met behulp van client- en servercertificaten wanneer de connectors de verbinding starten. Op deze manier worden de gebruikersnaam en het wachtwoord van de beheerder niet opgeslagen op de connectormachine.

De gebruikte certificaten zijn specifiek voor de application proxy-service. Ze worden gemaakt tijdens de eerste registratie en worden automatisch vernieuwd door de connectoren om de paar maanden.

Na de eerste succesvolle certificaatvernieuwing heeft de Azure AD Application Proxy Connector-service (Network Service) geen toestemming om het oude certificaat uit het lokale machinearchief te verwijderen. Als het certificaat is verlopen of als het niet meer door de service wordt gebruikt, u het veilig verwijderen.

Om problemen met de certificaatvernieuwing te voorkomen, moet u ervoor zorgen dat de netwerkcommunicatie van de connector naar de [gedocumenteerde bestemmingen](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment) is ingeschakeld.

Als een connector gedurende enkele maanden niet op de service is aangesloten, zijn de certificaten mogelijk verouderd. Verwijder in dit geval de connector en installeer deze opnieuw om de registratie te activeren. U de volgende PowerShell-opdrachten uitvoeren:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```
Zie [Ondersteuning voor machine- en backendcomponenten verifiëren voor het vertrouwenscertificaat voor toepassingsproxy voor](application-proxy-connector-installation-problem.md#verify-machine-and-backend-components-support-for-application-proxy-trust-certificate)meer informatie over het verifiëren van het certificaat en het oplossen van problemen.

## <a name="under-the-hood"></a>Onderhuids

Connectors zijn gebaseerd op Windows Server Web Application Proxy, dus ze hebben de meeste van dezelfde beheertools, waaronder Windows Event Logs

![Gebeurtenislogboeken beheren met de logboeken](./media/application-proxy-connectors/event-view-window.png)

en Windows-prestatiemeteritems.

![Tellers toevoegen aan de connector met de prestatiemonitor](./media/application-proxy-connectors/performance-monitor.png)

De connectors hebben zowel admin- als sessielogboeken. De beheerderslogboeken bevatten belangrijke gebeurtenissen en hun fouten. De sessielogboeken bevatten alle transacties en hun verwerkingsgegevens.

Als u de logboeken wilt zien, gaat u naar de logboeken, opent u het menu **Weergave** en schakelt **u Analytische en foutopsporingslogboeken**weergeven in. Stel ze vervolgens in staat om evenementen te verzamelen. Deze logboeken worden niet weergegeven in Web Application Proxy in Windows Server 2012 R2, omdat de connectors zijn gebaseerd op een recentere versie.

U de status van de service bekijken in het venster Services. De connector bestaat uit twee Windows Services: de werkelijke connector en de updater. Ze moeten allebei de hele tijd rennen.

 ![Voorbeeld: venster Services met azure AD-services lokaal](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Volgende stappen

- [Toepassingen publiceren op afzonderlijke netwerken en locaties met behulp van verbindingsgroepen](application-proxy-connector-groups.md)
- [Werken met bestaande on-premises proxyservers](application-proxy-configure-connectors-with-proxy-servers.md)
- [Problemen met toepassingsproxy- en connectorfouten oplossen](application-proxy-troubleshoot.md)
- [De Azure AD Application Proxy Connector in stilte installeren](application-proxy-register-connector-powershell.md)
