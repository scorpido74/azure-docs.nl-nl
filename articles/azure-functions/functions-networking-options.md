---
title: Azure Functions-netwerk opties
description: Een overzicht van alle beschik bare netwerk opties in Azure Functions
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 9fe7147325b2e14a7ae6bb4b31aa941fb4059b11
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72690829"
---
# <a name="azure-functions-networking-options"></a>Azure Functions-netwerk opties

In dit artikel worden de netwerk functies beschreven die beschikbaar zijn via de hosting opties voor Azure Functions. Alle volgende netwerk opties bieden u de mogelijkheid om toegang te krijgen tot bronnen zonder Internet routeerbaar-adressen te gebruiken, of de Internet toegang beperken tot een functie-app. 

De hosting modellen hebben verschillende niveaus van netwerk isolatie beschikbaar. Als u de juiste optie kiest, kunt u voldoen aan de vereisten voor netwerk isolatie.

U kunt functie-apps op verschillende manieren hosten:

* Er is een set plan opties die worden uitgevoerd op een multi tenant-infra structuur, met verschillende niveaus van connectiviteit voor virtuele netwerken en schaal opties:
    * Het [verbruiks abonnement](functions-scale.md#consumption-plan), dat dynamisch wordt geschaald in reactie op belasting en biedt minimale opties voor netwerk isolatie.
    * Het [Premium-abonnement](functions-scale.md#premium-plan), dat ook dynamisch kan worden geschaald, en biedt een uitgebreidere netwerk isolatie.
    * Het Azure [app service-abonnement](functions-scale.md#app-service-plan), dat op een vaste schaal werkt en een vergelijk bare netwerk isolatie biedt voor het Premium-abonnement.
* U kunt functies uitvoeren in een [app service Environment](../app-service/environment/intro.md). Deze methode implementeert uw functie in uw virtuele netwerk en biedt volledige netwerk controle en-isolatie.

## <a name="matrix-of-networking-features"></a>Matrix van netwerk functies

|                |[Verbruiks abonnement](functions-scale.md#consumption-plan)|[Premium-abonnement (preview-versie)](functions-scale.md#premium-plan)|[App Service-plan](functions-scale.md#app-service-plan)|[App Service-omgeving](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Binnenkomende IP-beperkingen & toegang tot de persoonlijke site](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[Integratie van virtueel netwerk](#virtual-network-integration)|❌No|✅Yes (regionaal)|✅Yes (regionaal en gateway)|✅Yes|
|[Virtuele netwerk triggers (niet-HTTP)](#virtual-network-triggers-non-http)|❌No| ❌No|✅Yes|✅Yes|
|[Hybride verbindingen](#hybrid-connections)|❌No|✅Yes|✅Yes|✅Yes|
|[Uitgaande IP-beperkingen](#outbound-ip-restrictions)|❌No| ❌No|❌No|✅Yes|


## <a name="inbound-ip-restrictions"></a>Binnenkomende IP-beperkingen

U kunt IP-beperkingen gebruiken voor het definiëren van een lijst met door prioriteiten geordende IP-adressen die geen toegang hebben tot uw app. De lijst kan IPv4-en IPv6-adressen bevatten. Wanneer er een of meer vermeldingen zijn, bevindt zich een impliciete ' weigeren ' aan het einde van de lijst. IP-beperkingen werken met alle opties voor het hosten van functies.

> [!NOTE]
> Als er netwerk beperkingen zijn ingesteld, kunt u de portal-Editor alleen gebruiken vanuit uw virtuele netwerk of wanneer u het IP-adres van de machine hebt white list die u gebruikt om toegang te krijgen tot de Azure Portal. U hebt echter nog steeds toegang tot alle functies op het tabblad **platform functies** vanaf een wille keurige computer.

Zie [Azure app service beperkingen voor statische toegang](../app-service/app-service-ip-restrictions.md)voor meer informatie.

## <a name="private-site-access"></a>Toegang tot privésite

Toegang via een persoonlijke site verwijst naar het toegankelijk maken van uw app vanaf een particulier netwerk, zoals vanuit een virtueel Azure-netwerk. 
* Toegang tot de persoonlijke site is beschikbaar in de [Premium](./functions-premium-plan.md), [verbruik], (functies-scale. MD # verbruiks plan) en [app service plan](functions-scale.md#app-service-plan) wanneer **service-eind punten** zijn geconfigureerd. 
    * Service-eind punten kunnen per app worden geconfigureerd onder platform functies > netwerk > toegangs beperkingen configureren > regel toevoegen. Virtuele netwerken kunnen nu worden geselecteerd als het type van een regel.
    * Zie [service-eind punten voor virtueel netwerk](../virtual-network/virtual-network-service-endpoints-overview.md) voor meer informatie.
        * Houd er bij service-eind punten voor dat uw functie nog steeds volledige uitgaande toegang tot het internet heeft, zelfs als de virtuele netwerk integratie is geconfigureerd.
* Toegang tot privé-sites is ook beschikbaar met een App Service Environment dat is geconfigureerd met een interne load balancer (ILB). Zie [een interne Load Balancer met een app service Environment maken en gebruiken](../app-service/environment/create-ilb-ase.md)voor meer informatie.

## <a name="virtual-network-integration"></a>Integratie van virtueel netwerk

Met virtuele netwerk integratie kan uw functie-app toegang krijgen tot bronnen in een virtueel netwerk. Deze functie is beschikbaar in het Premium-abonnement en het App Service-abonnement. Als uw app zich in een App Service Environment bevindt, is deze al aanwezig in een virtueel netwerk en is het gebruik van virtuele netwerk integratie niet vereist voor het bereiken van bronnen in hetzelfde virtuele netwerk.

U kunt virtuele netwerk integratie gebruiken om toegang te verlenen van apps tot data bases en webservices die worden uitgevoerd in uw virtuele netwerk. Met de integratie van virtuele netwerken hoeft u geen openbaar eind punt beschikbaar te maken voor toepassingen op uw VM. U kunt in plaats daarvan de persoonlijke en niet-Internet Routeer bare adressen gebruiken.

Er zijn twee vormen voor de integratie functie voor virtuele netwerken

1. Regionale virtuele netwerk integratie maakt integratie mogelijk met virtuele netwerken in dezelfde regio. Deze vorm van de functie vereist een subnet in een virtueel netwerk in dezelfde regio. Deze functie is nog steeds beschikbaar als preview, maar wordt wel ondersteund voor werk belastingen voor de Windows-app-productie met enkele aanvullende voor waarden die hieronder zijn vermeld.
2. Gateway vereiste virtuele netwerk integratie maakt integratie mogelijk met virtuele netwerken in externe regio's of met klassieke virtuele netwerken. Voor deze versie van de functie is een Virtual Network-gateway in uw VNet geïmplementeerd. Dit is de punt-naar-site-VPN-functie en wordt alleen ondersteund met Windows-apps.

Een app kan slechts één vorm van de VNet-integratie functie tegelijk gebruiken. De vraag is vervolgens welke functie u moet gebruiken. U kunt voor een groot aantal dingen gebruiken. De Clear verschillen zijn als volgt:

| Probleem  | Oplossing | 
|----------|----------|
| U wilt een RFC 1918-adres bereiken (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) in dezelfde regio | regionale VNet-integratie |
| U wilt bronnen bereiken in een klassiek VNet of een VNet in een andere regio | vereiste VNet-integratie voor gateway |
| U wilt RFC 1918-eind punten bereiken via ExpressRoute | regionale VNet-integratie |
| Resources willen bereiken tussen service-eind punten | regionale VNet-integratie |

Geen van beide functies biedt u de mogelijkheid om niet-RFC 1918-adressen te bereiken in ExpressRoute. Hiervoor moet u nu een ASE gebruiken.

Het gebruik van de regionale VNet-integratie verbindt uw VNet niet met on-premises of het configureren van service-eind punten. Dit is een afzonderlijke netwerk configuratie. Met de regionale VNet-integratie kan uw app eenvoudig aanroepen over deze verbindings typen.

VNet-integratie geeft uw functie-app toegang tot resources in uw virtuele netwerk, maar verleent geen persoonlijke toegang tot uw functie-app vanuit het virtuele netwerk, ongeacht de gebruikte versie. Toegang via een persoonlijke site verwijst naar het toegankelijk maken van uw app vanaf een particulier netwerk, zoals vanuit een virtueel Azure-netwerk. VNet-integratie is alleen voor het maken van uitgaande oproepen vanuit uw app naar uw VNet. 

De VNet-integratie functie:

* Vereist een Standard-, Premium-of PremiumV2-App Service plan
* ondersteunt TCP en UDP
* werkt met App Service-apps en functie-apps

Er zijn enkele zaken die VNet-integratie niet ondersteunt, waaronder:

* Koppelen van een schijf
* AD-integratie 
* NetBios

Virtuele netwerk integratie in functions maakt gebruik van een gedeelde infra structuur met App Service web-apps. Zie voor meer informatie over de twee typen integratie van virtuele netwerken:
* [Regionale VNET-integratie](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Vereiste VNet-integratie voor gateway](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Zie voor meer informatie over het gebruik van virtuele netwerk integratie [een functie-app integreren met een virtueel Azure-netwerk](functions-create-vnet.md).

## <a name="connecting-to-service-endpoint-secured-resources"></a>Verbinding maken met beveiligde bronnen van service-eind punten

> [!note] 
> Het kan tot 12 uur duren voordat nieuwe service-eind punten beschikbaar zijn voor uw functie-app nadat u de toegangs beperkingen voor de downstream-resource hebt geconfigureerd. Gedurende deze tijd is de resource volledig niet beschikbaar voor uw app.

Als u een hoger beveiligings niveau wilt bieden, kunt u een aantal Azure-Services beperken tot een virtueel netwerk met behulp van service-eind punten. Vervolgens moet u uw functie-app met dat virtuele netwerk integreren om toegang te krijgen tot de resource. Deze configuratie wordt ondersteund op alle abonnementen die ondersteuning bieden voor de integratie van virtuele netwerken.

[Meer informatie over de service-eind punten van Virtual Network vindt u hier.](../virtual-network/virtual-network-service-endpoints-overview.md)

### <a name="restricting-your-storage-account-to-a-virtual-network"></a>Uw opslag account beperken tot een virtueel netwerk
Wanneer u een functie-app maakt, moet u een Azure Storage-account voor algemeen gebruik maken of koppelen dat ondersteuning biedt voor blob-, wachtrij-en tabel opslag. Het is momenteel niet mogelijk om beperkingen voor het virtuele netwerk voor dit account te gebruiken. Als u een service-eind punt voor een virtueel netwerk configureert op het opslag account dat u gebruikt voor uw functie-app, wordt uw app verbroken.

[Lees hier meer over de vereisten voor opslag accounts.](./functions-create-function-app-portal.md#storage-account-requirements
) 

## <a name="virtual-network-triggers-non-http"></a>Virtuele netwerk triggers (niet-HTTP)

Als u op dit moment andere functie triggers dan HTTP wilt gebruiken vanuit een virtueel netwerk, moet u de functie-app uitvoeren in een App Service plan of in een App Service Environment.

Als u bijvoorbeeld Azure Cosmos DB wilt configureren om alleen verkeer van een virtueel netwerk te accepteren, moet u uw functie-app implementeren in een app service-plan met Virtual Network-integratie met dat virtuele netwerk om Azure Cosmos DB triggers te configureren van die resource. Tijdens de preview-versie kan het Premium-abonnement niet worden geactiveerd door de integratie van de VNET-Azure Cosmos DB.

Controleer [deze lijst op alle niet-http-triggers](./functions-triggers-bindings.md#supported-bindings) om te controleren wat er wordt ondersteund.

## <a name="hybrid-connections"></a>Hybride verbindingen

[Hybride verbindingen](../service-bus-relay/relay-hybrid-connections-protocol.md) is een functie van Azure relay die u kunt gebruiken om toegang te krijgen tot toepassings bronnen in andere netwerken. Het biedt toegang vanuit uw app tot een eind punt van de toepassing. U kunt deze niet gebruiken voor toegang tot uw toepassing. Hybride verbindingen is beschikbaar voor functies die alleen in het verbruiks abonnement worden uitgevoerd.

De hybride verbinding wordt gebruikt in Azure Functions en is afgestemd op één combi natie van TCP-host en poort. Dit betekent dat het eind punt van de hybride verbinding zich kan bevindt op elk besturings systeem en elke wille keurige toepassing, op voor waarde dat u toegang hebt tot een TCP-Luister poort. Met de functie Hybride verbindingen weet u niet of u weet wat het toepassings protocol is of wat u benadert. Het biedt gewoon toegang tot het netwerk.

Raadpleeg voor meer informatie de [documentatie over app service voor hybride verbindingen](../app-service/app-service-hybrid-connections.md), die ondersteuning biedt voor functies via dezelfde configuratie stappen.

## <a name="outbound-ip-restrictions"></a>Uitgaande IP-beperkingen

Uitgaande IP-beperkingen zijn alleen beschikbaar voor functies die zijn geïmplementeerd op een App Service Environment. U kunt uitgaande beperkingen configureren voor het virtuele netwerk waar uw App Service Environment wordt geïmplementeerd.

Wanneer u een functie-app in een Premium-abonnement integreert of App Service plan met een virtueel netwerk, kan de app nog steeds uitgaande oproepen naar Internet maken.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over netwerken en Azure Functions: 

* [Volg de zelf studie over het aan de slag gaan met virtuele netwerk integratie](./functions-create-vnet.md)
* [Lees de veelgestelde vragen over functies netwerken](./functions-networking-faq.md)
* [Meer informatie over de integratie van virtuele netwerken met App Service/functions](../app-service/web-sites-integrate-with-vnet.md)
* [Meer informatie over virtuele netwerken in azure](../virtual-network/virtual-networks-overview.md)
* [Meer netwerk functies en-beheer inschakelen met App Service omgevingen](../app-service/environment/intro.md)
* [Verbinding maken met afzonderlijke on-premises bronnen zonder Firewall wijzigingen met behulp van Hybride verbindingen](../app-service/app-service-hybrid-connections.md)
