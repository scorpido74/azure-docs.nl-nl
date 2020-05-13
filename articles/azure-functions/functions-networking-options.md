---
title: Netwerkopties van Azure Functions
description: Een overzicht van alle beschik bare netwerk opties in Azure Functions.
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: ce1a214d39f958af36931192aad4561459ca0573
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121339"
---
# <a name="azure-functions-networking-options"></a>Netwerkopties van Azure Functions

In dit artikel worden de netwerk functies beschreven die beschikbaar zijn via de hosting opties voor Azure Functions. Alle volgende netwerk opties bieden u de mogelijkheid om toegang te krijgen tot bronnen zonder Internet Routeer bare adressen of om Internet toegang te beperken tot een functie-app.

De hosting modellen hebben verschillende niveaus van netwerk isolatie beschikbaar. Door de juiste oplossing te kiezen, kunt u voldoen aan de vereisten voor netwerk isolatie.

U kunt functie-apps op verschillende manieren hosten:

* U kunt kiezen uit plan opties die worden uitgevoerd op een multi tenant-infra structuur, met verschillende niveaus van connectiviteit voor virtuele netwerken en schaal opties:
    * Het [verbruiks abonnement](functions-scale.md#consumption-plan) wordt dynamisch geschaald in reactie op belasting en biedt minimale opties voor netwerk isolatie.
    * Het [Premium-abonnement](functions-scale.md#premium-plan) wordt ook dynamisch geschaald en biedt meer uitgebreide netwerk isolatie.
    * Het Azure [app service-abonnement](functions-scale.md#app-service-plan) werkt op een vaste schaal en biedt netwerk isolatie die vergelijkbaar is met het Premium-abonnement.
* U kunt functies uitvoeren in een [app service Environment](../app-service/environment/intro.md). Deze methode implementeert uw functie in uw virtuele netwerk en biedt volledige netwerk controle en-isolatie.

## <a name="matrix-of-networking-features"></a>Matrix van netwerk functies

|                |[Verbruiks abonnement](functions-scale.md#consumption-plan)|[Premium-abonnement](functions-scale.md#premium-plan)|[App Service plan](functions-scale.md#app-service-plan)|[App Service-omgeving](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Binnenkomende IP-beperkingen en toegang tot persoonlijke sites](#inbound-ip-restrictions)|✅Ja|✅Ja|✅Ja|✅Ja|
|[Integratie van virtueel netwerk](#virtual-network-integration)|❌Nee|✅Ja (regionaal)|✅Ja (regionaal en gateway)|✅Ja|
|[Virtuele netwerk triggers (niet-HTTP)](#virtual-network-triggers-non-http)|❌Nee| ✅Ja |✅Ja|✅Ja|
|[Hybride verbindingen](#hybrid-connections) (alleen Windows)|❌Nee|✅Ja|✅Ja|✅Ja|
|[Uitgaande IP-beperkingen](#outbound-ip-restrictions)|❌Nee| ✅Ja|✅Ja|✅Ja|

## <a name="inbound-ip-restrictions"></a>Binnenkomende IP-beperkingen

U kunt IP-beperkingen gebruiken om een lijst met door prioriteiten geordende IP-adressen te definiëren die geen toegang tot uw app hebben of geweigerd. De lijst kan IPv4-en IPv6-adressen bevatten. Als er sprake is van een of meer vermeldingen, bevindt zich een impliciete ' weigeren ' aan het einde van de lijst. IP-beperkingen werken met alle opties voor het hosten van functies.

> [!NOTE]
> Als er netwerk beperkingen zijn ingesteld, kunt u de portal Editor alleen gebruiken vanuit uw virtuele netwerk, of wanneer u het IP-adres van de computer die u gebruikt, hebt geplaatst voor toegang tot de Azure Portal in de lijst met veilige geadresseerden. U hebt echter nog steeds toegang tot alle functies op het tabblad **platform functies** vanaf een wille keurige computer.

Zie [Azure app service beperkingen voor statische toegang](../app-service/app-service-ip-restrictions.md)voor meer informatie.

## <a name="private-site-access"></a>Toegang tot privésite

Toegang via een persoonlijke site verwijst naar het toegankelijk maken van uw app vanaf een particulier netwerk, zoals een virtueel Azure-netwerk.

* Toegang tot persoonlijke sites is beschikbaar in de [Premium](./functions-premium-plan.md)-, [verbruiks](functions-scale.md#consumption-plan)-en [app service](functions-scale.md#app-service-plan) plannen wanneer service-eind punten zijn geconfigureerd.
    * Service-eind punten kunnen per app worden geconfigureerd onder **platform functies**  >  **netwerk**  >  **toegangs beperkingen configureren**  >  **regel toevoegen**. Virtuele netwerken kunnen nu worden geselecteerd als regel type.
    * Zie [service-eind punten voor virtuele netwerken](../virtual-network/virtual-network-service-endpoints-overview.md)voor meer informatie.
    * Houd er bij service-eind punten voor dat uw functie nog steeds volledige uitgaande toegang tot het internet heeft, zelfs als de virtuele netwerk integratie is geconfigureerd.
* Toegang tot privé-sites is ook beschikbaar in een App Service Environment dat is geconfigureerd met een interne load balancer (ILB). Zie [een interne Load Balancer met een app service Environment maken en gebruiken](../app-service/environment/create-ilb-ase.md)voor meer informatie.

Zie voor meer informatie over het instellen van toegang tot persoonlijke sites, [Azure functions toegang tot de persoonlijke site tot stand brengen](functions-create-private-site-access.md).

## <a name="virtual-network-integration"></a>Integratie van virtueel netwerk

Met virtuele netwerk integratie kan uw functie-app toegang krijgen tot bronnen in een virtueel netwerk.
Azure Functions ondersteunt twee soorten integratie van virtuele netwerken:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Virtuele netwerk integratie in Azure Functions gebruikt een gedeelde infra structuur met App Service web-apps. Zie voor meer informatie over de twee typen integratie van virtuele netwerken:

* [Regionale virtuele netwerk integratie](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Gateway-vereiste virtuele netwerk integratie](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Zie [een functie-app integreren met een virtueel Azure-netwerk](functions-create-vnet.md)voor meer informatie over het instellen van de integratie van virtuele netwerken.

## <a name="regional-virtual-network-integration"></a>Regionale virtuele netwerk integratie

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connect-to-service-endpoint-secured-resources"></a>Verbinding maken met de beveiligde resources van het service-eind punt

Als u een hoger beveiligings niveau wilt bieden, kunt u een aantal Azure-Services beperken tot een virtueel netwerk met behulp van service-eind punten. Vervolgens moet u uw functie-app met dat virtuele netwerk integreren om toegang te krijgen tot de resource. Deze configuratie wordt ondersteund op alle abonnementen die ondersteuning bieden voor de integratie van virtuele netwerken.

Zie [service-eind punten voor virtueel netwerk](../virtual-network/virtual-network-service-endpoints-overview.md)voor meer informatie.

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Uw opslag account beperken tot een virtueel netwerk

Wanneer u een functie-app maakt, moet u een Azure Storage-account voor algemeen gebruik maken of koppelen dat ondersteuning biedt voor blob-, wachtrij-en tabel opslag. U kunt momenteel geen beperkingen voor virtuele netwerken gebruiken voor dit account. Als u een service-eind punt voor een virtueel netwerk configureert op het opslag account dat u gebruikt voor uw functie-app, wordt de app door die configuratie verbroken.

Zie [vereisten voor opslag accounts](./functions-create-function-app-portal.md#storage-account-requirements)voor meer informatie.

## <a name="use-key-vault-references"></a>Key Vault verwijzingen gebruiken

U kunt Azure Key Vault verwijzingen gebruiken om geheimen van Azure Key Vault in uw Azure Functions toepassing te gebruiken zonder dat u code wijzigingen hoeft aan te brengen. Azure Key Vault is een service die gecentraliseerd geheimen beheer biedt, met volledige controle over het toegangs beleid en de controle geschiedenis.

[Key Vault verwijzingen](../app-service/app-service-key-vault-references.md) werken momenteel niet als uw sleutel kluis is beveiligd met Service-eind punten. Als u verbinding wilt maken met een sleutel kluis met behulp van virtuele netwerk integratie, moet u Key Vault aanroepen in de code van uw toepassing.

## <a name="virtual-network-triggers-non-http"></a>Virtuele netwerk triggers (niet-HTTP)

Op dit moment kunt u niet-HTTP-trigger functies vanuit een virtueel netwerk op twee manieren gebruiken:

+ Voer uw functie-app uit in een Premium-abonnement en schakel ondersteuning voor virtuele netwerk triggers in.
+ Voer uw functie-app uit in een App Service plan of App Service Environment.

### <a name="premium-plan-with-virtual-network-triggers"></a>Premium-abonnement met virtuele netwerk triggers

Wanneer u een Premium-abonnement uitvoert, kunt u niet-HTTP-trigger functies verbinden met services die binnen een virtueel netwerk worden uitgevoerd. Hiervoor moet u ondersteuning voor virtuele netwerk triggers inschakelen voor uw functie-app. De **ondersteunings** instelling voor het activeren van virtuele netwerken vindt u in de [Azure Portal](https://portal.azure.com) onder runtime-instellingen voor de **configuratie**-  >  **functie**.

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

U kunt ook virtuele netwerk triggers inschakelen met behulp van de volgende Azure CLI-opdracht:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

Virtuele netwerk triggers worden ondersteund in versie 2. x en hoger van de functions-runtime. De volgende niet-HTTP-trigger typen worden ondersteund.

| Toestelnummer | Minimale versie |
|-----------|---------| 
|[Micro soft. Azure. webjobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 of hoger |
|[Micro soft. Azure. webjobs. Extensions. Event hubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 of hoger|
|[Micro soft. Azure. webjobs. Extensions. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 of hoger|
|[Micro soft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 of hoger|
|[Micro soft. Azure. webjobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 of hoger|

> [!IMPORTANT]
> Wanneer u ondersteuning voor virtuele netwerk triggers inschakelt, worden alleen de trigger typen die in de vorige tabel worden weer gegeven, dynamisch met uw toepassing geschaald. U kunt nog steeds triggers gebruiken die zich niet in de tabel bevinden, maar ze worden niet groter dan het aantal vooraf gewarmde instanties. Zie [Triggers en bindingen](./functions-triggers-bindings.md#supported-bindings)voor de volledige lijst met triggers.

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>App Service plan en App Service Environment met virtuele netwerk triggers

Wanneer uw functie-app wordt uitgevoerd in een App Service plan of een App Service Environment, kunt u niet-HTTP-trigger functies gebruiken. Als u wilt dat uw functies correct worden geactiveerd, moet u verbinding hebben met een virtueel netwerk met toegang tot de bron die is gedefinieerd in de trigger verbinding.

Stel dat u Azure Cosmos DB wilt configureren om alleen verkeer van een virtueel netwerk te accepteren. In dit geval moet u uw functie-app implementeren in een App Service plan dat virtuele netwerk integratie met dat virtuele netwerk biedt. Met integratie kan een functie worden geactiveerd door die Azure Cosmos DB resource.

## <a name="hybrid-connections"></a>Hybride verbindingen

[Hybride verbindingen](../service-bus-relay/relay-hybrid-connections-protocol.md) is een functie van Azure relay die u kunt gebruiken om toegang te krijgen tot toepassings bronnen in andere netwerken. Het biedt toegang vanuit uw app tot een eind punt van de toepassing. U kunt deze niet gebruiken voor toegang tot uw toepassing. Hybride verbindingen is beschikbaar voor functies die alleen in Windows worden uitgevoerd in het verbruiks abonnement.

De hybride verbinding wordt gebruikt in Azure Functions en is afgestemd op één combi natie van TCP-host en poort. Dit betekent dat het eind punt van de hybride verbinding zich op elk besturings systeem en elke toepassing kan bevindt, mits u een TCP-Luister poort opent. Met de functie Hybride verbindingen weet u niet wat het toepassings protocol is of wat u toegang hebt. Het biedt alleen toegang tot het netwerk.

Zie de [app service-documentatie voor hybride verbindingen voor](../app-service/app-service-hybrid-connections.md)meer informatie. Deze zelfde configuratie stappen ondersteunen Azure Functions.

>[!IMPORTANT]
> Hybride verbindingen wordt alleen ondersteund op Windows-abonnementen. Linux wordt niet ondersteund.

## <a name="outbound-ip-restrictions"></a>Uitgaande IP-beperkingen

Uitgaande IP-beperkingen zijn beschikbaar in een Premium-abonnement, App Service plan of App Service Environment. U kunt uitgaande beperkingen configureren voor het virtuele netwerk waar uw App Service Environment wordt geïmplementeerd.

Wanneer u een functie-app integreert in een Premium-abonnement of een App Service plan met een virtueel netwerk, kan de app standaard nog steeds uitgaande oproepen naar Internet maken. Door de toepassings instelling toe `WEBSITE_VNET_ROUTE_ALL=1` te voegen, dwingt u af dat alle uitgaand verkeer naar uw virtuele netwerk moet worden verzonden, waarbij regels voor netwerk beveiligings groepen kunnen worden gebruikt om verkeer te beperken.

## <a name="troubleshooting"></a>Problemen oplossen

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over netwerken en Azure Functions:

* [Volg de zelf studie over het aan de slag gaan met virtuele netwerk integratie](./functions-create-vnet.md)
* [Lees de veelgestelde vragen over functies netwerken](./functions-networking-faq.md)
* [Meer informatie over de integratie van virtuele netwerken met App Service/functions](../app-service/web-sites-integrate-with-vnet.md)
* [Meer informatie over virtuele netwerken in azure](../virtual-network/virtual-networks-overview.md)
* [Meer netwerk functies en-beheer inschakelen met App Service omgevingen](../app-service/environment/intro.md)
* [Verbinding maken met afzonderlijke on-premises bronnen zonder Firewall wijzigingen met behulp van Hybride verbindingen](../app-service/app-service-hybrid-connections.md)
