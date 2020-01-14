---
title: Azure Functions schalen en hosten
description: Meer informatie over hoe u kunt kiezen tussen het Azure Functions verbruiks abonnement en het Premium-abonnement.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 03/27/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6a07d019893e69308b35b4a941fe50d2736efe01
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2020
ms.locfileid: "75921917"
---
# <a name="azure-functions-scale-and-hosting"></a>Azure Functions schalen en hosten

Wanneer u een functie-app in azure maakt, moet u een hosting abonnement kiezen voor uw app. Er zijn drie hosting plannen beschikbaar voor Azure Functions: [verbruiks abonnement](#consumption-plan), [Premium plan](#premium-plan)en [toegewezen (app service)-abonnement](#app-service-plan).

Het hosting abonnement dat u kiest, bepaalt het volgende gedrag:

* Hoe de functie-app wordt geschaald.
* De bronnen die beschikbaar zijn voor elk exemplaar van de functie-app.
* Ondersteuning voor geavanceerde functies, zoals Azure Virtual Network-connectiviteit.

Zowel verbruik als Premium-abonnementen voegen automatisch reken kracht toe wanneer uw code wordt uitgevoerd. Uw app wordt uitgeschaald wanneer dit nodig is om de belasting te verwerken, en geschaald wanneer de uitvoering van de code wordt gestopt. Voor het verbruiks abonnement hoeft u ook niet vooraf te betalen voor niet-actieve Vm's of reserve ring van capaciteit.  

Premium-abonnement biedt extra functies, zoals Premium Compute-instanties, waarmee instanties voor onbepaalde tijd en VNet-connectiviteit kunnen worden bewaard.

Met App Service plan kunt u profiteren van de toegewezen infra structuur, die u beheert. De functie-app kan niet worden geschaald op basis van gebeurtenissen, wat betekent dat nooit wordt geschaald naar nul. (Vereist dat [Always on](#always-on) is ingeschakeld.)

## <a name="hosting-plan-support"></a>Ondersteuning voor hosting abonnementen

Functie ondersteuning valt in de volgende twee categorieën:

* _Algemeen beschikbaar (ga)_ : volledig ondersteund en goedgekeurd voor productie gebruik.
* _Preview_: nog niet volledig ondersteund of goedgekeurd voor productie gebruik.

In de volgende tabel wordt het huidige ondersteunings niveau voor de drie hosting plannen aangegeven, wanneer dit wordt uitgevoerd op Windows of Linux:

| | Verbruiksabonnement | Premium-plan | Toegewezen plan |
|-|:----------------:|:------------:|:----------------:|
| Windows | Algemene beschikbaarheid | Algemene beschikbaarheid | Algemene beschikbaarheid |
| Linux | Algemene beschikbaarheid | Algemene beschikbaarheid | Algemene beschikbaarheid |

## <a name="consumption-plan"></a>Verbruiksabonnement

Wanneer u het verbruiks abonnement gebruikt, worden instanties van de Azure Functions-host dynamisch toegevoegd en verwijderd op basis van het aantal binnenkomende gebeurtenissen. Dit serverloze abonnement wordt automatisch geschaald en u betaalt alleen voor rekenresources wanneer uw functies worden uitgevoerd. Bij een verbruiksabonnement treedt er na een ingestelde periode een time-out op voor een functie-uitvoering.

De facturering is dan ook gebaseerd op het aantal uitvoeringen, de uitvoeringstijd en het gebruikte geheugen. De facturering wordt samengevoegd voor alle functies in een functie-app. Zie de pagina met prijzen voor [Azure functions](https://azure.microsoft.com/pricing/details/functions/)voor meer informatie.

Het verbruiks abonnement is het standaard hosting plan en biedt de volgende voor delen:

* Betaal alleen wanneer uw functies worden uitgevoerd
* Automatisch uitschalen, zelfs tijdens peri Oden van hoge belasting

Functie-apps in dezelfde regio kunnen worden toegewezen aan hetzelfde verbruiks plan. Er is geen nadeel of gevolgen voor het uitvoeren van meerdere apps in hetzelfde verbruiks abonnement. Het toewijzen van meerdere apps aan hetzelfde verbruiks plan heeft geen invloed op de flexibiliteit, schaal baarheid of betrouw baarheid van elke app.

Zie voor meer informatie over het schatten van kosten bij het uitvoeren van een verbruiks abonnement de [kosten van verbruiks plan](functions-consumption-costs.md).

## <a name="premium-plan"></a>Premium-abonnement

Wanneer u het Premium-abonnement gebruikt, worden exemplaren van de Azure Functions-host toegevoegd en verwijderd op basis van het aantal binnenkomende gebeurtenissen, net als het verbruiks abonnement.  Premium-abonnement biedt ondersteuning voor de volgende functies:

* Ongebruikte en warme exemplaren om te voor komen dat ze koud worden gestart
* VNet-connectiviteit
* Onbeperkte uitvoerings duur
* Grootte van Premium-instanties (één kern, twee kernen en vier kern instanties)
* Meer voorspel bare prijzen
* App-toewijzing met hoge densiteit voor plannen met meerdere functie-apps

Informatie over hoe u deze opties kunt configureren, vindt u in het [document van het Azure functions Premium-abonnement](functions-premium-plan.md).

In plaats van de facturering per uitvoering en het verbruikte geheugen, wordt de facturering voor het Premium-abonnement gebaseerd op het aantal kern seconden en het geheugen dat voor de benodigde en vooraf gewarmte instanties wordt gebruikt. Ten minste één exemplaar moet op elk moment per plan warm zijn. Dit betekent dat er een mini maal maandelijks bedrag per actief abonnement is, ongeacht het aantal uitvoeringen. Houd er rekening mee dat alle functie-apps in een Premium-abonnement vooraf gewarmde en actieve instanties delen.

Bekijk het Azure Functions Premium-abonnement in de volgende situaties:

* Uw functie-apps worden continu uitgevoerd, of bijna continu.
* U hebt een groot aantal kleine uitvoeringen en beschikt over een hoog uitvoerings bedrag, maar een laag van minder dan een tweede factuur in het verbruiks abonnement.
* U hebt meer CPU-of geheugen opties nodig dan in het verbruiks abonnement is opgenomen.
* De code moet langer worden uitgevoerd dan de [Maxi maal toegestane uitvoerings tijd](#timeout) voor het verbruiks abonnement.
* U hebt functies nodig die alleen beschikbaar zijn in een Premium-abonnement, zoals verbinding met het virtuele netwerk.

Wanneer u Java script-functies uitvoert op een Premium-abonnement, kiest u een instantie met minder Vcpu's. Zie voor meer informatie het [kiezen van single-core Premium-abonnementen](functions-reference-node.md#considerations-for-javascript-functions).  

## <a name="app-service-plan"></a>Speciaal plan (App Service)

Uw functie-apps kunnen ook worden uitgevoerd op dezelfde specifieke Vm's als andere App Service-apps (Basic-, Standard-, Premium-en geïsoleerde Sku's).

Houd rekening met een App Service-abonnement in de volgende situaties:

* U hebt al bestaande, te weinig gebruikte virtuele machines waarop andere exemplaren van App Service worden uitgevoerd.
* U wilt een aangepaste installatie kopie opgeven waarop uw functies moeten worden uitgevoerd.

U betaalt hetzelfde voor functie-apps in een App Service plan zoals u zou doen voor andere App Service resources, zoals web-apps. Zie het [overzicht van Azure app service plannen](../app-service/overview-hosting-plans.md)voor meer informatie over de werking van het app service plan.

Met een App Service-abonnement kunt u hand matig uitschalen door meer VM-exemplaren toe te voegen. U kunt ook automatisch schalen inschakelen. Zie [aantal exemplaren hand matig of automatisch schalen](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json)voor meer informatie. U kunt ook omhoog schalen door een ander App Service plan te kiezen. Zie [een app omhoog schalen in azure](../app-service/manage-scale-up.md)voor meer informatie. 

Wanneer u Java script-functies uitvoert op een App Service-abonnement, moet u een abonnement kiezen dat minder Vcpu's heeft. Zie voor meer informatie [single-core app service-abonnementen kiezen](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a>Altijd aan

Als u uitvoert met een App Service-abonnement, moet u de instelling **altijd aan** inschakelen, zodat de functie-app correct wordt uitgevoerd. Bij een App Service-abonnement wordt de runtime van functions na een paar minuten inactiviteit niet-actief, zodat alleen HTTP-triggers uw functies kunnen activeren. Always on is alleen beschikbaar voor een App Service plan. In een verbruiks abonnement activeert het platform automatisch functie-apps.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Zelfs met Always ingeschakeld, wordt de time-out voor uitvoering voor afzonderlijke functies bepaald door de `functionTimeout` instelling in het JSON-project bestand van de [host.](functions-host-json.md#functiontimeout)

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Het hosting plan van een bestaande toepassing bepalen

Zie **app service plan/prijs categorie** in het tabblad **overzicht** voor de functie-app in de [Azure Portal](https://portal.azure.com)om het hosting plan te bepalen dat door uw functie-app wordt gebruikt. Voor App Service plannen wordt de prijs categorie ook aangegeven.

![Schaal plan weer geven in de portal](./media/functions-scale/function-app-overview-portal.png)

U kunt de Azure CLI ook gebruiken om het plan als volgt te bepalen:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Wanneer de uitvoer van deze opdracht wordt `dynamic`, bevindt uw functie-app zich in het verbruiks abonnement. Wanneer de uitvoer van deze opdracht is `ElasticPremium`, bevindt uw functie-app zich in het Premium-abonnement. Alle andere waarden geven verschillende lagen van een App Service plan aan.

## <a name="storage-account-requirements"></a>Vereisten voor een opslagaccount

Voor een wille keurig abonnement is voor een functie-app een algemeen Azure Storage account vereist, dat Azure Blob, wachtrij, bestanden en tabel opslag ondersteunt. Dit komt omdat functies afhankelijk zijn van Azure Storage voor bewerkingen, zoals het beheren van triggers en de uitvoering van logboek functies, maar sommige opslag accounts bieden geen ondersteuning voor wacht rijen en tabellen. Deze accounts, waaronder alleen Blob Storage-accounts (inclusief Premium Storage) en opslag accounts voor algemeen gebruik met zone-redundante opslag replicatie, worden uitgefilterd op basis van uw bestaande **opslag** accounts geselecteerd wanneer u een functie-app maakt.

Het opslag account dat wordt gebruikt door uw functie-app kan ook worden gebruikt door uw triggers en bindingen om uw toepassings gegevens op te slaan. Voor Storage-intensieve bewerkingen moet u echter een afzonderlijk opslag account gebruiken.  

Het is zeker mogelijk dat meerdere functie-apps hetzelfde opslag account delen zonder problemen. (Een goed voor beeld hiervan is wanneer u meerdere apps in uw lokale omgeving ontwikkelt met behulp van de Azure Storage-emulator, die fungeert als een opslag account.) 

<!-- JH: Does using a Premium Storage account improve perf? -->

Zie [Inleiding tot de Azure Storage services](../storage/common/storage-introduction.md#azure-storage-services)voor meer informatie over typen opslag accounts.

## <a name="how-the-consumption-and-premium-plans-work"></a>Hoe de verbruiks-en Premium-abonnementen werken

In de verbruiks-en Premium-abonnementen schaalt de Azure Functions-infra structuur CPU-en geheugen bronnen door extra exemplaren van de functions-host toe te voegen op basis van het aantal gebeurtenissen waarvoor de functies zijn geactiveerd. Elk exemplaar van de functions-host in het verbruiks abonnement is beperkt tot 1,5 GB aan geheugen en één CPU.  Een exemplaar van de host is de volledige functie-app, wat betekent dat alle functies binnen een functie-app resources delen binnen een exemplaar en op hetzelfde moment kunnen worden geschaald. Functie-apps die hetzelfde verbruiks abonnement delen, worden onafhankelijk geschaald.  In het Premium-abonnement bepaalt de grootte van het abonnement het beschik bare geheugen en de CPU voor alle apps in dat exemplaar.  

Functie code bestanden worden opgeslagen op Azure Files shares op het belangrijkste opslag account van de functie. Wanneer u het belangrijkste opslag account van de functie-app verwijdert, worden de functie code bestanden verwijderd en kunnen deze niet worden hersteld.

### <a name="runtime-scaling"></a>Runtime schalen

Azure Functions gebruikt een onderdeel dat de *schaal controller* wordt genoemd om de frequentie van gebeurtenissen te controleren en te bepalen of u wilt schalen of schalen. De schaal controller maakt gebruik van heuristiek voor elk trigger type. Wanneer u bijvoorbeeld een Azure Queue-opslag trigger gebruikt, wordt deze geschaald op basis van de lengte van de wachtrij en de leeftijd van het oudste wachtrij bericht.

De eenheid van de schaal voor Azure Functions is de functie-app. Wanneer de functie-app is geschaald, worden er extra resources toegewezen om meerdere exemplaren van de Azure Functions host uit te voeren. Als de berekenings aanvraag daarentegen wordt verkleind, worden de instanties van de functie-host door de schaal controller verwijderd. Het aantal exemplaren wordt uiteindelijk naar nul *geschaald* wanneer er geen functies in een functie-app worden uitgevoerd.

![Controller bewakings gebeurtenissen schalen en instanties maken](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Meer informatie over het schalen van gedrag

Schalen kan variëren op basis van een aantal factoren en op verschillende manieren schalen, afhankelijk van de geselecteerde trigger en taal. Er zijn een aantal complexiteit waarmee u rekening moet houden:

* Een functie-app kan zelfstandig omhoog worden geschaald naar maximaal 200 exemplaren. Eén exemplaar kan echter meer dan één bericht of aanvraag tegelijk verwerken, dus er is geen limiet ingesteld voor het aantal gelijktijdige uitvoeringen.
* Voor HTTP-triggers worden nieuwe instanties slechts elke 1 seconde toegewezen.
* Voor niet-HTTP-triggers worden nieuwe instanties Maxi maal elke 30 seconden toegewezen.

Andere triggers kunnen ook verschillende schaal limieten hebben, zoals hieronder wordt beschreven:

* [Event Hub](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Aanbevolen procedures en patronen voor schaal bare apps

Er zijn veel aspecten van een functie-app die van invloed zijn op de manier waarop deze worden geschaald, met inbegrip van hostconfiguratie, runtime footprint en resource-efficiëntie.  Zie de [sectie schaal baarheid in het artikel over prestatie overwegingen](functions-best-practices.md#scalability-best-practices)voor meer informatie. U moet ook weten hoe verbindingen zich gedragen als uw functie-app wordt geschaald. Zie [verbindingen beheren in azure functions](manage-connections.md)voor meer informatie.

Voor meer informatie over schalen in Python en node. js raadpleegt u de [Azure functions python-ontwikkelaars handleiding voor het schalen en gelijktijdigheid](functions-reference-python.md#scaling-and-concurrency) en [het Azure functions van node. js-ontwikkelaars handleiding-schalen en gelijktijdigheid](functions-reference-node.md#scaling-and-concurrency).

### <a name="billing-model"></a>Factureringsmodel

De facturering voor de verschillende plannen wordt gedetailleerd beschreven op de [pagina met Azure functions prijzen](https://azure.microsoft.com/pricing/details/functions/). Het gebruik wordt geaggregeerd op het niveau van de functie-app en telt alleen de tijd waarop de functie code wordt uitgevoerd. Hier volgen enkele eenheden voor facturering:

* **Resource verbruik in Gigabyte-seconden (GB-s)** . Berekend als een combi natie van geheugen grootte en uitvoerings tijd voor alle functies in een functie-app. 
* **Uitvoeringen**. Geteld wanneer een functie wordt uitgevoerd in reactie op een gebeurtenis trigger.

Nuttige query's en informatie over het begrijpen van uw verbruiks factuur vindt u [in de veelgestelde vragen over facturering](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>Servicebeperkingen

In de volgende tabel worden de limieten aangegeven die van toepassing zijn op app-functies wanneer ze worden uitgevoerd in de verschillende hosting plannen:

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
