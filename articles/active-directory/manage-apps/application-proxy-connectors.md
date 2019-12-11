---
title: Meer informatie over Azure AD Application Proxy connectors | Microsoft Docs
description: Bevat informatie over de basisbeginselen van Azure AD Application Proxy connectors.
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
ms.openlocfilehash: 1c2036bf9995725e4bbef44e4c039f8336eb81a0
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997034"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Meer informatie over Azure AD Application Proxy connectors

Connectors zijn wat Azure AD Application Proxy mogelijk maken. Ze zijn eenvoudig, eenvoudig te implementeren en te onderhouden, en zeer krachtig. In dit artikel wordt beschreven welke connectors zijn, hoe deze werken, en enkele suggesties voor informatie over het optimaliseren van uw implementatie.

## <a name="what-is-an-application-proxy-connector"></a>Wat is er een Application Proxy-connector?

Connectors zijn lichtgewicht agents die zich on-premises en de uitgaande verbinding met de service voor toepassingsproxy te vergemakkelijken. Connectors moeten worden geïnstalleerd op een Windows-Server die toegang tot de back endtoepassing heeft. U kunt connectors indelen in connectorgroepen, met elke groep verwerken van verkeer voor specifieke toepassingen.

## <a name="requirements-and-deployment"></a>Vereisten en implementatie

Voor het implementeren van de toepassingsproxy is, moet u ten minste één connector, maar we raden aan twee of meer voor meer flexibiliteit. Installeer de connector op een computer met Windows Server 2012 R2 of hoger. De connector moet communiceren met de service toepassings proxy en de on-premises toepassingen die u publiceert.

### <a name="windows-server"></a>Windows-server
U moet een server met Windows Server 2012 R2 of hoger waarop kunt u de Application Proxy-connector installeren. De server moet verbinding maken met de toepassings proxy services in Azure en de on-premises toepassingen die u publiceert.

De windows-server moet TLS 1.2 is ingeschakeld voordat u de Application Proxy-connector installeert. TLS 1,2 inschakelen op de server:

1. Stel de volgende registersleutels in:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Start de server opnieuw

Zie voor meer informatie over de netwerkvereisten voor de connectorserver, [aan de slag met Application Proxy en installeren van een connector](application-proxy-add-on-premises-application.md).

## <a name="maintenance"></a>Onderhoud

De connectors en de service zorgen van alle taken die hoge beschikbaarheid. Ze kunnen worden toegevoegd of verwijderd dynamisch. Telkens wanneer die een nieuwe aanvraag binnenkomt wordt deze doorgestuurd naar een van de connectors die momenteel beschikbaar is. Als een connector tijdelijk niet beschikbaar is, heeft niet deze dit verkeer reageren.

De connectors zijn staatloos en geen configuratiegegevens op de computer hebt. De enige gegevens die ze opslaan is de instellingen voor het verbinden van de service en het certificaat voor clientverificatie. Wanneer ze met de service verbinden, ze alle vereiste configuratiegegevens ophalen en elke paar minuten vernieuwd.

Connectors pollen ook de server om erachter te komen of er is een nieuwere versie van de connector. Als er een is gevonden, de connectors worden bijgewerkt.

U kunt uw connectors van de machine waarop ze worden uitgevoerd, controleren met behulp van het gebeurtenislogboek en de prestatiemeteritems. Of u kunt de status van de Application Proxy-pagina van de Azure-portal weergeven:

![Voor beeld: Azure AD-toepassingsproxy-connectors](./media/application-proxy-connectors/app-proxy-connectors.png)

U hebt geen handmatig verwijderen van connectors die niet gebruikt worden. Wanneer een connector wordt uitgevoerd, blijft deze actief als deze verbinding met de service maakt. Niet-gebruikte connectors zijn gelabeld als _inactieve_ en worden verwijderd na tien dagen van inactiviteit. Als u verwijderen van een connector wilt, maar dan wel verwijdert u zowel de connectorservice en de Updaterservice van de server. Start de computer opnieuw op om de service volledig te verwijderen.

## <a name="automatic-updates"></a>Automatische updates

Azure AD biedt Automatische updates voor alle connectors die u implementeert. Als de Application Proxy Connector Updater-service wordt uitgevoerd, wordt uw connectors automatisch bijgewerkt. Als u niet de Connector Updater-service op uw server ziet, moet u [uw connector opnieuw installeren](application-proxy-add-on-premises-application.md) om eventuele updates te downloaden.

Als u niet wilt wachten totdat een automatische update naar uw connector komt, kunt u een hand matige upgrade uitvoeren. Ga naar de [connector-downloadpagina](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) op de server waarop de connector zich bevindt en selecteert u een **downloaden**. Een upgrade voor de lokale connector begint dit proces.

Voor tenants met meerdere connectors gericht op de automatische updates één connector op een tijdstip in elke groep om te voorkomen dat downtime in uw omgeving.

U ondervindt mogelijk downtime tijdens uw connector bijgewerkt als:
  
- U hebt slechts één connector aanbevolen om een tweede connector te installeren en [een connector groep te maken](application-proxy-connector-groups.md). Hierdoor wordt uitval tijd voor komen en wordt er een hogere Beschik baarheid geboden.  
- Er is een connector in het midden van een transactie wanneer de update is begonnen. Hoewel de initiële transactie verloren gegaan is, uw browser moet automatisch probeer het opnieuw of u kunt de pagina vernieuwen. Wanneer de aanvraag is verzonden, wordt het verkeer wordt doorgestuurd naar een back-connector.

Zie [toepassings proxy-versie geschiedenis](application-proxy-release-version-history.md)van de release voor meer informatie over eerder uitgebrachte versies en de wijzigingen die ze bevatten.

## <a name="creating-connector-groups"></a>Het maken van connectorgroepen

Connectorgroepen kunnen u specifieke connectors voor het bieden van specifieke toepassingen toewijzen. U kunt een aantal connectors groeperen en vervolgens elke toepassing toewijzen aan een groep.

Connectorgroepen wordt het eenvoudiger voor het beheren van grote implementaties. Ze ook verbeteren latentie voor tenants met toepassingen die worden gehost in verschillende regio's, omdat u kunt maken op basis van locatie connectorgroepen om van dienst alleen lokale toepassingen te.

Zie voor meer informatie over connectorgroepen [publiceren van toepassingen op afzonderlijke netwerken en locaties met connectorgroepen](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>Capaciteitsplanning

Het is belang rijk om ervoor te zorgen dat u voldoende capaciteit hebt gepland tussen connectors om het verwachte verkeers volume af te handelen. U wordt aangeraden elke connector groep ten minste twee connectors te bieden voor hoge Beschik baarheid en schaal. Wanneer u drie connectors hebt, is het mogelijk dat u op elk gewenst moment een machine moet onderhouden.

Over het algemeen, hoe meer gebruikers u hebt, hoe groter de computer die u nodig hebt. Hieronder volgt een tabel met een overzicht van het volume en de verwachte latentie die verschillende computers kunnen verwerken. Houd er rekening mee dat deze allemaal is gebaseerd op de verwachte trans acties per seconde (TPS) in plaats van door de gebruiker omdat de gebruiks patronen variëren en niet kunnen worden gebruikt om de belasting te voors pellen. Er zijn ook enkele verschillen op basis van de grootte van de reacties en de reactie tijd van de back-end-toepassing-grotere antwoord groottes en tragere reactie tijden, waardoor de maximale TPS wordt berekend. We raden u ook aan extra machines te plaatsen, zodat de gedistribueerde belasting over de machines altijd een ruimere buffer biedt. De extra capaciteit zorgt ervoor dat u beschikt over hoge Beschik baarheid en tolerantie.

|Cores|RAM|Verwachte latentie (MS)-P99|Max TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|

\* deze computer een aangepaste instelling heeft gebruikt om een aantal van de standaard verbindings limieten van meer dan .NET aanbevolen instellingen te verhogen. Het is raadzaam om een test uitgevoerd met de standaardinstellingen voor het contact opnemen met ondersteuning om deze limiet voor uw tenant is gewijzigd.

> [!NOTE]
> Er is veel prioriteitsbereik bij met de maximale TPS 4, 8 en 16 kernen-machines. Het belangrijkste verschil tussen die zich in de verwachte latentie.
>
> Deze tabel is ook gericht op de verwachte prestaties van een connector op basis van het type computer waarop het is geïnstalleerd. Dit is gescheiden van de beperkings limieten voor de toepassings proxy service, Zie [service limieten en beperkingen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="security-and-networking"></a>Beveiliging en netwerken

Connectors kunnen overal worden geïnstalleerd op het netwerk waarmee ze voor het verzenden van aanvragen naar de Application Proxy-service. Wat is het belangrijk is dat de computer met de connector ook toegang tot uw apps. U kunt connectors installeren op uw bedrijfsnetwerk of op een virtuele machine die wordt uitgevoerd in de cloud. Connectors kunnen worden uitgevoerd binnen een perimeter netwerk, ook wel bekend als een gedemilitariseerde zone (DMZ), maar dit is niet nodig omdat al het verkeer uitgaand is, zodat uw netwerk beveiligd blijft.

Connectors alleen verzenden uitgaande aanvragen. Het uitgaande verkeer wordt verzonden naar de Application Proxy-service en de gepubliceerde toepassingen. U hebt geen inkomende poorten openen omdat verkeer beide richtingen stroomt wanneer een sessie tot stand is gebracht. U hoeft ook geen inkomende toegang via uw firewalls te configureren.

Zie voor meer informatie over het configureren van uitgaande firewallregels [werken met bestaande on-premises proxy-servers](application-proxy-configure-connectors-with-proxy-servers.md).

## <a name="performance-and-scalability"></a>Prestaties en schaalbaarheid

Schaal voor de service voor toepassingsproxy is transparant, maar de schaal is een factor voor connectors. U moet voldoende connectors voor het afhandelen van verkeer. Omdat connectors stateless zijn, worden ze niet beïnvloed door het aantal gebruikers of sessies. In plaats daarvan reageren ze op het aantal aanvragen en de grootte van de nettolading. Met standaard webverkeer te genereren, kan een gemiddelde virtuele machine een paar duizend aanvragen per seconde verwerken. De opgegeven capaciteit is afhankelijk van de kenmerken van de exacte machine.

De connectorprestaties is gebonden aan CPU- en netwerkresources. CPU-prestaties nodig is voor SSL-versleuteling en ontsleuteling, terwijl de netwerken is het belangrijk om snelle verbinding met de toepassingen en de online-service in Azure.

Daarentegen is geheugen kleiner van een probleem voor connectors. De online-service zorgt voor veel van de verwerking en niet-geverifieerde al het verkeer. Alles dat kan worden uitgevoerd in de cloud wordt uitgevoerd in de cloud.

Als voor een bepaalde reden dat connector of de machine niet meer beschikbaar is, het verkeer naar een andere connector in de groep gaat. Deze flexibiliteit is ook waarom we raden u aan meerdere connectors.

Een andere factor die van invloed op prestaties wordt de kwaliteit van de netwerken tussen de connectors, met inbegrip van:

- **De onlineservice**: traag of hoge latentie verbindingen met de Application Proxy-service in Azure invloed van de connectorprestaties. Voor de beste prestaties, verbinding maken met uw organisatie naar Azure met Express Route. Anders hebt u uw netwerken team ervoor te zorgen dat de verbindingen met Azure zo efficiënt mogelijk worden verwerkt.
- **De back-endtoepassingen**: In sommige gevallen moet u er extra proxy tussen de connector en de back endtoepassingen die kunnen vertragen of te voorkomen dat de verbindingen zijn. Open een browser van de connectorserver voor het oplossen van dit scenario, en probeert te krijgen tot de toepassing. Als u de connectors in Azure uitvoert, maar de toepassingen die zich on-premises, de ervaring mogelijk niet wat uw gebruikers verwachten.
- **De domein controllers**: als de connectors eenmalige aanmelding (SSO) uitvoeren met beperkte Kerberos-delegering, nemen ze contact op met de domein controllers voordat ze de aanvraag verzenden naar de back-end. De connectors hebben een cache van Kerberos-tickets, maar in een omgeving met Bezig de reactietijd van de domeincontrollers kan beïnvloeden. Dit probleem komt vaker voor voor connectors die worden uitgevoerd in Azure, maar communiceren met domeincontrollers die zich on-premises.

Zie voor meer informatie over het optimaliseren van uw netwerk [topologie overwegingen met betrekking tot het netwerk bij het gebruik van Azure Active Directory-toepassingsproxy](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Toevoegen aan het domein

Connectors kunnen uitvoeren op een computer die is niet-domein. Als u eenmalige aanmelding (SSO) voor toepassingen die gebruikmaken van geïntegreerde Windows-verificatie (IWA) wilt, moet u echter een domein-machine. In dit geval de connector-machines moeten worden toegevoegd aan een domein dat u kunt uitvoeren [Kerberos](https://web.mit.edu/kerberos) beperkte delegering namens de gebruikers voor de gepubliceerde toepassingen.

Connectors kunnen ook worden gekoppeld, domeinen of forests die u een gedeeltelijk vertrouwen hebt, of alleen-lezen domeincontrollers.

## <a name="connector-deployments-on-hardened-environments"></a>Connector-implementaties op beveiligde omgevingen

Implementatie van de connector wordt normaal gesproken is eenvoudig en is geen speciale configuratie vereist. Er zijn echter enkele unieke voorwaarden die u moeten overwegen:

- Organisaties die het uitgaande verkeer beperken moeten [vereiste poorten openen](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).
- FIPS-compatibele computers kunnen worden verplicht om hun configuratie zodat de connector-processen voor het genereren en opslaan van een certificaat te wijzigen.
- Organisaties die hun omgeving op basis van de processen die het netwerk aanvragen vergrendelen hebben om ervoor te zorgen dat beide connectorservices voor toegang tot alle vereiste poorten en IP-adressen zijn ingeschakeld.
- In sommige gevallen kunnen uitgaande forward proxy's de wederzijdse certificaatverificatie opsplitsen en ervoor zorgen dat de communicatie mislukt.

## <a name="connector-authentication"></a>Connector-verificatie

Voor een veilige service connectors hebben om te verifiëren en de service en de service is om te verifiëren naar de connector. Deze verificatie wordt gedaan met behulp van de client en server certificaten wanneer de connectors de verbinding initieert. Op deze manier de gebruikersnaam en het wachtwoord van de beheerder worden niet opgeslagen op de computer van de connector.

De certificaten die worden gebruikt, zijn specifiek voor de Application Proxy-service. Ze worden gemaakt tijdens de registratie en worden automatisch vernieuwd door de connectors elke aantal maanden.

Als u een connector is niet verbonden met de service voor enkele maanden, is het mogelijk dat de certificaten verouderd. In dit geval verwijderen en opnieuw installeren van de connector triggerregistratie. U kunt de volgende PowerShell-opdrachten uitvoeren:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

## <a name="under-the-hood"></a>Achter de schermen

Connectors zijn gebaseerd op Windows Server Web Application Proxy, zodat ze hebben de meeste van de dezelfde beheerhulpprogramma's waaronder Windows-gebeurtenislogboeken

![Logboeken met de Event Viewer beheren](./media/application-proxy-connectors/event-view-window.png)

en Windows-prestatiemeteritems.

![Items toevoegen aan de connector met de Prestatiemeter](./media/application-proxy-connectors/performance-monitor.png)

De connectors zijn zowel de beheerder als de sessie Logboeken. De beheerder-logboeken bevatten belangrijke gebeurtenissen en hun fouten. De sessielogboeken bevatten alle transacties en de bijbehorende verwerkingsgegevens.

Als u wilt de logboeken ziet, gaat u naar de Event Viewer, open de **weergave** menu en schakelt u **logboeken en foutopsporing weergeven analytische**. Vervolgens kunnen ze beginnen met het verzamelen van gebeurtenissen. Deze logboeken worden niet weergegeven in de Web Application Proxy in Windows Server 2012 R2, zoals de connectors zijn gebaseerd op een meer recente versie.

U kunt de status van de service in het venster Services controleren. De connector bestaat uit twee Windows-Services: de daad werkelijke connector en de Updater. Van beide moeten altijd worden uitgevoerd.

 ![Voor beeld: het venster Services met de lokale Azure AD-Services](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Volgende stappen

- [Publiceren van toepassingen op afzonderlijke netwerken en locaties met behulp van connectorgroepen](application-proxy-connector-groups.md)
- [Werken met bestaande on-premises proxy-servers](application-proxy-configure-connectors-with-proxy-servers.md)
- [Application Proxy en de connector-fouten oplossen](application-proxy-troubleshoot.md)
- [De Azure AD Application Proxy Connector op de achtergrond installeren](application-proxy-register-connector-powershell.md)
