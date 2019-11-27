---
title: Azure Functions-netwerk opties
description: Een overzicht van alle beschik bare netwerk opties in Azure Functions.
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: a3df48115dde27478446614c0446d64709adbc6f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226802"
---
# <a name="azure-functions-networking-options"></a>Azure Functions-netwerk opties

In dit artikel worden de netwerk functies beschreven die beschikbaar zijn via de hosting opties voor Azure Functions. Alle volgende netwerk opties bieden u de mogelijkheid om toegang te krijgen tot bronnen zonder Internet Routeer bare adressen of om Internet toegang te beperken tot een functie-app.

De hosting modellen hebben verschillende niveaus van netwerk isolatie beschikbaar. Als u de juiste optie kiest, kunt u voldoen aan de vereisten voor netwerk isolatie.

U kunt functie-apps op verschillende manieren hosten:

* Er is een set plan opties die worden uitgevoerd op een multi tenant-infra structuur, met verschillende niveaus van connectiviteit voor virtuele netwerken en schaal opties:
    * Het [verbruiks abonnement](functions-scale.md#consumption-plan), dat dynamisch wordt geschaald in reactie op belasting en biedt minimale opties voor netwerk isolatie.
    * Het [Premium-abonnement](functions-scale.md#premium-plan), dat ook dynamisch kan worden geschaald, en biedt een uitgebreidere netwerk isolatie.
    * Het Azure [app service-abonnement](functions-scale.md#app-service-plan), dat op een vaste schaal werkt en een vergelijk bare netwerk isolatie biedt als het Premium-abonnement.
* U kunt functies uitvoeren in een [app service Environment](../app-service/environment/intro.md). Deze methode implementeert uw functie in uw virtuele netwerk en biedt volledige netwerk controle en-isolatie.

## <a name="matrix-of-networking-features"></a>Matrix van netwerk functies

|                |[Verbruiks abonnement](functions-scale.md#consumption-plan)|[Premium-abonnement](functions-scale.md#premium-plan)|[App Service-plan](functions-scale.md#app-service-plan)|[App Service-omgeving](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Binnenkomende IP-beperkingen & toegang tot de persoonlijke site](#inbound-ip-restrictions)|✅Ja|✅Ja|✅Ja|✅Ja|
|[Integratie van virtueel netwerk](#virtual-network-integration)|❌Nee|✅Ja (regionaal)|✅Ja (regionaal en gateway)|✅Ja|
|[Virtuele netwerk triggers (niet-HTTP)](#virtual-network-triggers-non-http)|❌Nee| ❌Nee|✅Ja|✅Ja|
|[Hybride verbindingen](#hybrid-connections)|❌Nee|✅Ja|✅Ja|✅Ja|
|[Uitgaande IP-beperkingen](#outbound-ip-restrictions)|❌Nee| ❌Nee|❌Nee|✅Ja|

## <a name="inbound-ip-restrictions"></a>Binnenkomende IP-beperkingen

U kunt IP-beperkingen gebruiken om een lijst met door prioriteiten geordende IP-adressen te definiëren die geen toegang tot uw app hebben of geweigerd. De lijst kan IPv4-en IPv6-adressen bevatten. Als er sprake is van een of meer vermeldingen, bevindt zich een impliciete ' weigeren ' aan het einde van de lijst. IP-beperkingen werken met alle opties voor het hosten van functies.

> [!NOTE]
> Als er netwerk beperkingen zijn ingesteld, kunt u de portal Editor alleen gebruiken vanuit uw virtuele netwerk, of wanneer u het IP-adres van de computer die u gebruikt, hebt geplaatst voor toegang tot de Azure Portal in de lijst met veilige geadresseerden. U hebt echter nog steeds toegang tot alle functies op het tabblad **platform functies** vanaf een wille keurige computer.

Zie [Azure app service beperkingen voor statische toegang](../app-service/app-service-ip-restrictions.md)voor meer informatie.

## <a name="private-site-access"></a>Toegang tot privésite

Toegang tot de persoonlijke site verwijst naar het toegankelijk maken van uw app vanaf een particulier netwerk, zoals een virtueel Azure-netwerk.

* Toegang tot persoonlijke sites is beschikbaar in de [Premium](./functions-premium-plan.md)-, [verbruiks](functions-scale.md#consumption-plan)-en [app service](functions-scale.md#app-service-plan) plannen wanneer service-eind punten zijn geconfigureerd.
    * Service-eind punten kunnen per app worden geconfigureerd onder **platform functies** > **netwerk** > **toegangs beperkingen configureren** > **regel toevoegen**. Virtuele netwerken kunnen nu worden geselecteerd als regel type.
    * Zie [service-eind punten voor virtuele netwerken](../virtual-network/virtual-network-service-endpoints-overview.md)voor meer informatie.
    * Houd er bij service-eind punten voor dat uw functie nog steeds volledige uitgaande toegang tot het internet heeft, zelfs als de virtuele netwerk integratie is geconfigureerd.
* Toegang tot privé-sites is ook beschikbaar in een App Service Environment dat is geconfigureerd met een interne load balancer (ILB). Zie [een interne Load Balancer met een app service Environment maken en gebruiken](../app-service/environment/create-ilb-ase.md)voor meer informatie.

## <a name="virtual-network-integration"></a>Integratie van virtueel netwerk

Met virtuele netwerk integratie kan uw functie-app toegang krijgen tot bronnen in een virtueel netwerk. Deze functie is beschikbaar in het Premium-abonnement en het App Service-abonnement. Als uw app zich in een App Service Environment bevindt, is deze al in een virtueel netwerk en is de integratie met het virtuele netwerk niet vereist voor het bereiken van bronnen in hetzelfde virtuele netwerk.

U kunt virtuele netwerk integratie gebruiken om toegang te verlenen van apps tot data bases en webservices die worden uitgevoerd in uw virtuele netwerk. Met de integratie van virtuele netwerken hoeft u geen openbaar eind punt beschikbaar te maken voor toepassingen op uw VM. U kunt in plaats daarvan persoonlijke en niet-Internet Routeer bare adressen gebruiken.

Er zijn twee soorten integratie van virtuele netwerken:

+ **Regionale virtuele netwerk integratie (preview)** : maakt integratie mogelijk met virtuele netwerken in dezelfde regio. Voor dit type integratie is een subnet in een virtueel netwerk in dezelfde regio vereist. Deze functie bevindt zich nog in de preview-versie, maar wordt ondersteund voor functie-apps die worden uitgevoerd in Windows, met de voor behoud die worden beschreven na het volgende probleem/oplossings tabel.
+ **Gateway vereist virtuele netwerk integratie**: maakt integratie mogelijk met virtuele netwerken in externe regio's of met klassieke virtuele netwerken. Voor dit type integratie is de implementatie van een virtuele netwerk gateway in uw VNet vereist. Dit is een punt-naar-site-VPN-functie, die alleen wordt ondersteund voor functie-apps die worden uitgevoerd op Windows.

Een app kan slechts één type functie voor de integratie van virtuele netwerken tegelijk gebruiken. Hoewel beide handig zijn voor veel scenario's, wordt in de volgende tabel aangegeven waar elke moet worden gebruikt:

| Probleem  | Oplossing |
|----------|----------|
| U wilt een RFC 1918-adres bereiken (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) in dezelfde regio | Regionale virtuele netwerk integratie |
| U wilt bronnen bereiken in een klassiek virtueel netwerk of een virtueel netwerk in een andere regio | Gateway vereist virtuele netwerk integratie |
| U wilt RFC 1918-eind punten bereiken in azure ExpressRoute | Regionale virtuele netwerk integratie |
| Resources willen bereiken tussen service-eind punten | Regionale virtuele netwerk integratie |

Met geen van beide functies kunt u niet-RFC 1918-adressen bereiken in ExpressRoute. Hiervoor moet u momenteel een App Service Environment gebruiken.

Het gebruik van regionale virtuele netwerk integratie verbindt uw virtuele netwerk niet met on-premises eind punten of configureert service-eind punten. Dat is een afzonderlijke netwerk configuratie. Dankzij de regionale integratie van virtuele netwerken kan uw app aanroepen over deze verbindings typen.

Ongeacht de versie die wordt gebruikt, biedt integratie van virtuele netwerken uw functie-app toegang tot resources in uw virtuele netwerk, maar verleent de persoonlijke site geen toegang tot uw functie-app vanuit het virtuele netwerk. Toegang tot persoonlijke sites houdt in dat uw app alleen toegankelijk is vanuit een particulier netwerk als een virtueel Azure-netwerk. virtuele netwerk integratie is alleen voor het maken van uitgaande oproepen vanuit uw app in het virtuele netwerk.

De functie voor integratie van virtuele netwerken:

* Vereist een Standard-, Premium-of PremiumV2-App Service plan
* Ondersteunt TCP en UDP
* Werkt met App Service apps en functie-apps

Er zijn enkele dingen die de integratie van virtuele netwerken niet ondersteunt, waaronder:

* Koppelen van een schijf
* Active Directory-integratie
* Naamgeving

Virtuele netwerk integratie in Azure Functions gebruikt een gedeelde infra structuur met App Service web-apps. Zie voor meer informatie over de twee typen integratie van virtuele netwerken:

* [Regionale virtuele netwerk integratie](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Gateway vereist virtuele netwerk integratie](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Zie voor meer informatie over het gebruik van virtuele netwerk integratie [een functie-app integreren met een virtueel Azure-netwerk](functions-create-vnet.md).

## <a name="connecting-to-service-endpoint-secured-resources"></a>Verbinding maken met beveiligde bronnen van service-eind punten

> [!NOTE]
> Het kan tot 12 uur duren voordat nieuwe service-eind punten beschikbaar zijn voor uw functie-app nadat u de toegangs beperkingen voor de downstream-resource hebt geconfigureerd. Gedurende deze tijd is de resource volledig niet beschikbaar voor uw app.

Als u een hoger beveiligings niveau wilt bieden, kunt u een aantal Azure-Services beperken tot een virtueel netwerk met behulp van service-eind punten. Vervolgens moet u uw functie-app met dat virtuele netwerk integreren om toegang te krijgen tot de resource. Deze configuratie wordt ondersteund op alle abonnementen die ondersteuning bieden voor de integratie van virtuele netwerken.

[Meer informatie over service-eind punten voor virtuele netwerken.](../virtual-network/virtual-network-service-endpoints-overview.md)

### <a name="restricting-your-storage-account-to-a-virtual-network"></a>Uw opslag account beperken tot een virtueel netwerk

Wanneer u een functie-app maakt, moet u een Azure Storage-account voor algemeen gebruik maken of koppelen dat ondersteuning biedt voor blob-, wachtrij-en tabel opslag. U kunt momenteel geen beperkingen voor virtuele netwerken gebruiken voor dit account. Als u een service-eind punt voor een virtueel netwerk configureert op het opslag account dat u gebruikt voor uw functie-app, waardoor uw app wordt verbroken.

[Meer informatie over vereisten voor opslag accounts.](./functions-create-function-app-portal.md#storage-account-requirements)

### <a name="using-key-vault-references"></a>Key Vault verwijzingen gebruiken 

Met Key Vault verwijzingen kunt u geheimen van Azure Key Vault gebruiken in uw Azure Functions-toepassing, zonder dat u code wijzigingen hoeft aan te brengen. Azure Key Vault is een service die gecentraliseerd geheimen beheer biedt, met volledige controle over het toegangs beleid en de controle geschiedenis.

Als uw Key Vault is beveiligd met Service-eind punten, werkt [Key Vault verwijzingen](../app-service/app-service-key-vault-references.md) momenteel niet. Als u verbinding wilt maken met een Key Vault met behulp van virtuele netwerk integratie, moet u de sleutel kluis aanroepen in de code van uw toepassing.

## <a name="virtual-network-triggers-non-http"></a>Virtuele netwerk triggers (niet-HTTP)

Als u op dit moment andere functie triggers dan HTTP wilt gebruiken vanuit een virtueel netwerk, moet u uw functie-app uitvoeren in een App Service plan of in een App Service Environment.

Stel dat u Azure Cosmos DB wilt configureren om alleen verkeer van een virtueel netwerk te accepteren. U moet uw functie-app implementeren in een app service-plan dat virtuele netwerk integratie met dat virtuele netwerk biedt om Azure Cosmos DB triggers van die bron te configureren. Tijdens de preview staat het configureren van de integratie van virtuele netwerken niet toe dat het Premium-abonnement wordt geactiveerd door die Azure Cosmos DB bron.

Zie [deze lijst voor alle niet-http-triggers](./functions-triggers-bindings.md#supported-bindings) om te controleren wat er wordt ondersteund.

## <a name="hybrid-connections"></a>Hybride verbindingen

[Hybride verbindingen](../service-bus-relay/relay-hybrid-connections-protocol.md) is een functie van Azure relay die u kunt gebruiken om toegang te krijgen tot toepassings bronnen in andere netwerken. Het biedt toegang vanuit uw app tot een eind punt van de toepassing. U kunt deze niet gebruiken voor toegang tot uw toepassing. Hybride verbindingen is beschikbaar voor functies die alleen in het verbruiks abonnement worden uitgevoerd.

De hybride verbinding wordt gebruikt in Azure Functions en is afgestemd op één combi natie van TCP-host en poort. Dit betekent dat het eind punt van de hybride verbinding zich op elk besturings systeem en elke toepassing kan bevindt, mits u een TCP-Luister poort opent. Met de functie Hybride verbindingen weet u niet wat het toepassings protocol is of wat u toegang hebt. Het biedt alleen toegang tot het netwerk.

Zie de [app service-documentatie voor hybride verbindingen voor](../app-service/app-service-hybrid-connections.md)meer informatie. Deze zelfde configuratie stappen ondersteunen Azure Functions.

## <a name="outbound-ip-restrictions"></a>Uitgaande IP-beperkingen

Uitgaande IP-beperkingen zijn alleen beschikbaar voor functies die zijn geïmplementeerd op een App Service Environment. U kunt uitgaande beperkingen configureren voor het virtuele netwerk waar uw App Service Environment wordt geïmplementeerd.

Wanneer u een functie-app integreert in een Premium-abonnement of een App Service plan met een virtueel netwerk, kan de app nog steeds uitgaande gesp rekken met Internet tot stand brengen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over netwerken en Azure Functions:

* [Volg de zelf studie over het aan de slag gaan met virtuele netwerk integratie](./functions-create-vnet.md)
* [Lees de veelgestelde vragen over functies netwerken](./functions-networking-faq.md)
* [Meer informatie over de integratie van virtuele netwerken met App Service/functions](../app-service/web-sites-integrate-with-vnet.md)
* [Meer informatie over virtuele netwerken in azure](../virtual-network/virtual-networks-overview.md)
* [Meer netwerk functies en-beheer inschakelen met App Service omgevingen](../app-service/environment/intro.md)
* [Verbinding maken met afzonderlijke on-premises bronnen zonder Firewall wijzigingen met behulp van Hybride verbindingen](../app-service/app-service-hybrid-connections.md)
