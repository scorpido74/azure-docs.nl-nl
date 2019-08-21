---
title: Schaal en hosting Azure Functions | Microsoft Docs
description: Meer informatie over hoe u kunt kiezen tussen het Azure Functions verbruiks abonnement en het Premium-abonnement.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure functions, functies, verbruiks abonnement, Premium-abonnement, gebeurtenis verwerking, webhooks, dynamische compute, serverloze architectuur
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 03/27/2019
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fdef1457254b6deb8a0b791b11c94154518b4301
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636441"
---
# <a name="azure-functions-scale-and-hosting"></a>Azure Functions schalen en hosten

Wanneer u een functie-app in azure maakt, moet u een hosting abonnement kiezen voor uw app. Er zijn drie hosting plannen beschikbaar voor Azure Functions: [Verbruiks abonnement](#consumption-plan), [Premium-abonnement](#premium-plan)en [app service plan](#app-service-plan).

Het hosting abonnement dat u kiest, bepaalt het volgende gedrag:

* Hoe de functie-app wordt geschaald.
* De bronnen die beschikbaar zijn voor elk exemplaar van de functie-app.
* Ondersteuning voor geavanceerde functies, zoals VNET-connectiviteit.

Zowel verbruik als Premium-abonnementen voegen automatisch reken kracht toe wanneer uw code wordt uitgevoerd. Uw app wordt uitgeschaald wanneer deze nodig is voor het verwerken van de belasting en wordt omlaag geschaald wanneer de uitvoering van de code wordt gestopt. Voor het verbruiks abonnement hoeft u ook niet vooraf te betalen voor niet-actieve Vm's of reserve ring van capaciteit.  

Premium-abonnement biedt extra functies, zoals Premium Compute-instanties, waarmee instanties voor onbepaalde tijd en VNet-connectiviteit kunnen worden bewaard.

Met App Service plan kunt u profiteren van de toegewezen infra structuur, die u beheert. De functie-app kan niet worden geschaald op basis van gebeurtenissen, wat betekent dat nooit wordt geschaald naar nul. (Vereist dat [Always on](#always-on) is ingeschakeld.)

> [!NOTE]
> U kunt scha kelen tussen verbruiks-en Premium-abonnementen door de eigenschap plan van de resource van de functie-app te wijzigen.

## <a name="hosting-plan-support"></a>Ondersteuning voor hosting abonnementen

Functie ondersteuning valt in de volgende twee categorieën:

* _Algemeen beschikbaar (ga)_ : volledig ondersteund en goedgekeurd voor productie gebruik.
* _Preview_: nog niet volledig ondersteund en goedgekeurd voor productie gebruik.

In de volgende tabel wordt het huidige ondersteunings niveau voor de drie hosting plannen aangegeven, wanneer dit wordt uitgevoerd op Windows of Linux:

| | Verbruiksabonnement | Premium-abonnement | Toegewezen plan |
|-|:----------------:|:------------:|:----------------:|
| Windows | Algemene beschikbaarheid | preview | Algemene beschikbaarheid |
| Linux | Algemene beschikbaarheid | preview | Algemene beschikbaarheid |

## <a name="consumption-plan"></a>Verbruiksabonnement

Wanneer u het verbruiks abonnement gebruikt, worden instanties van de Azure Functions-host dynamisch toegevoegd en verwijderd op basis van het aantal binnenkomende gebeurtenissen. Dit serverloze plan wordt automatisch geschaald en er worden alleen kosten in rekening gebracht voor reken resources wanneer uw functies worden uitgevoerd. Bij een verbruiks abonnement wordt een time-out van de functie uitgevoerd na een Configureer bare periode.

De facturering is gebaseerd op het aantal uitvoeringen, de uitvoerings tijd en het gebruikte geheugen. Facturering wordt samengevoegd over alle functies in een functie-app. Zie de pagina met prijzen voor [Azure functions](https://azure.microsoft.com/pricing/details/functions/)voor meer informatie.

Het verbruiks abonnement is het standaard hosting plan en biedt de volgende voor delen:

* Betaal alleen wanneer uw functies worden uitgevoerd
* Automatisch uitschalen, zelfs tijdens peri Oden van hoge belasting

Functie-apps in dezelfde regio kunnen worden toegewezen aan hetzelfde verbruiks plan. Er is geen nadeel of gevolgen voor het uitvoeren van meerdere apps in hetzelfde verbruiks abonnement. Het toewijzen van meerdere apps aan hetzelfde verbruiks plan heeft geen invloed op de flexibiliteit, schaal baarheid of betrouw baarheid van elke app.

## <a name="premium-plan"></a>Premium-abonnement (preview-versie)

Wanneer u het Premium-abonnement gebruikt, worden exemplaren van de Azure Functions-host toegevoegd en verwijderd op basis van het aantal binnenkomende gebeurtenissen, net als het verbruiks abonnement.  Premium-abonnement biedt ondersteuning voor de volgende functies:

* Ongebruikte en warme exemplaren om te voor komen dat ze koud worden gestart
* VNet-connectiviteit
* Onbeperkte uitvoerings duur
* Grootte van Premium-instanties (één kern, twee kernen en vier kern instanties)
* Meer voorspel bare prijzen
* App-toewijzing met hoge densiteit voor plannen met meerdere functie-apps

Informatie over hoe u deze opties kunt configureren, vindt u in het [document van het Azure functions Premium-abonnement](functions-premium-plan.md).

In plaats van de facturering per uitvoering en het verbruikte geheugen wordt de facturering voor het Premium-abonnement gebaseerd op het aantal kern seconden, uitvoerings tijd en het geheugen dat wordt gebruikt voor de benodigde en gereserveerde instanties.  Ten minste één exemplaar moet te allen tijde warm zijn. Dit betekent dat er een vaste maandelijkse prijs per actief abonnement is, ongeacht het aantal uitvoeringen.

Bekijk het Azure Functions Premium-abonnement in de volgende situaties:

* Uw functie-apps worden continu uitgevoerd, of bijna continu.
* U hebt meer CPU-of geheugen opties nodig dan in het verbruiks abonnement is opgenomen.
* De code moet langer worden uitgevoerd dan de [Maxi maal toegestane uitvoerings tijd](#timeout) voor het verbruiks abonnement.
* U hebt functies nodig die alleen beschikbaar zijn in een Premium-abonnement, zoals VNET/VPN-verbindingen.

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


Zelfs met Always ingeschakeld, wordt de time-out voor de uitvoering van afzonderlijke `functionTimeout` functies bepaald door de instelling in het JSON-project bestand van de [host.](functions-host-json.md#functiontimeout)

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Het hosting plan van een bestaande toepassing bepalen

Zie **app service plan/prijs categorie** in het tabblad **overzicht** voor de functie-app in de [Azure Portal](https://portal.azure.com)om het hosting plan te bepalen dat door uw functie-app wordt gebruikt. Voor App Service plannen wordt de prijs categorie ook aangegeven.

![Schaal plan weer geven in de portal](./media/functions-scale/function-app-overview-portal.png)

U kunt de Azure CLI ook gebruiken om het plan als volgt te bepalen:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Wanneer de uitvoer van deze opdracht is `dynamic`, bevindt uw functie-app zich in het verbruiks abonnement. Wanneer de uitvoer van deze opdracht is `ElasticPremium`, is uw functie-app in het Premium-abonnement. Alle andere waarden geven verschillende lagen van een App Service plan aan.

## <a name="storage-account-requirements"></a>Vereisten voor een opslagaccount

Voor een wille keurig abonnement is voor een functie-app een algemeen Azure Storage account vereist, dat Azure Blob, wachtrij, bestanden en tabel opslag ondersteunt. Dit komt omdat functies afhankelijk zijn van Azure Storage voor bewerkingen, zoals het beheren van triggers en de uitvoering van logboek functies, maar sommige opslag accounts bieden geen ondersteuning voor wacht rijen en tabellen. Deze accounts, waaronder alleen Blob Storage-accounts (inclusief Premium Storage) en opslag accounts voor algemeen gebruik met zone-redundante opslag replicatie, worden uitgefilterd op basis van uw bestaande **opslag** accounts geselecteerd bij het maken van een functie-app.

<!-- JH: Does using a Premium Storage account improve perf? -->

Zie [Inleiding tot de Azure Storage services](../storage/common/storage-introduction.md#azure-storage-services)voor meer informatie over typen opslag accounts.

## <a name="how-the-consumption-and-premium-plans-work"></a>Hoe de verbruiks-en Premium-abonnementen werken

In de verbruiks-en Premium-abonnementen schaalt de Azure Functions-infra structuur CPU-en geheugen bronnen door extra exemplaren van de functions-host toe te voegen op basis van het aantal gebeurtenissen waarvoor de functies zijn geactiveerd. Elk exemplaar van de functions-host in het verbruiks abonnement is beperkt tot 1,5 GB aan geheugen en één CPU.  Een exemplaar van de host is de volledige functie-app, wat betekent dat alle functies binnen een functie-app resources delen binnen een exemplaar en op hetzelfde moment kunnen worden geschaald. Functie-apps die hetzelfde verbruiks abonnement delen, worden onafhankelijk geschaald.  In het Premium-abonnement bepaalt de grootte van het abonnement het beschik bare geheugen en de CPU voor alle apps in dat exemplaar.  

Functie code bestanden worden opgeslagen op Azure Files shares op het belangrijkste opslag account van de functie. Wanneer u het belangrijkste opslag account van de functie-app verwijdert, worden de functie code bestanden verwijderd en kunnen deze niet worden hersteld.

> [!NOTE]
> Wanneer u een BLOB-trigger in een verbruiks abonnement gebruikt, kan er een vertraging van 10 minuten zijn bij het verwerken van nieuwe blobs. Deze vertraging treedt op wanneer een functie-app niet actief is geweest. Nadat de functie-app is uitgevoerd, worden de blobs onmiddellijk verwerkt. Gebruik het Premium-abonnement of gebruik de [Event grid trigger](functions-bindings-event-grid.md)als u wilt voor komen dat de koude-start vertraging optreedt. Zie voor meer informatie [het artikel binding verwijzing BLOB-trigger](functions-bindings-storage-blob.md#trigger).

### <a name="runtime-scaling"></a>Runtime schalen

Azure Functions gebruikt een onderdeel dat de *schaal controller* wordt genoemd om de frequentie van gebeurtenissen te controleren en te bepalen of u wilt schalen of schalen. De schaal controller maakt gebruik van heuristiek voor elk trigger type. Wanneer u bijvoorbeeld een Azure Queue-opslag trigger gebruikt, wordt deze geschaald op basis van de lengte van de wachtrij en de leeftijd van het oudste wachtrij bericht.

De eenheid van de schaal voor Azure Functions is de functie-app. Wanneer de functie-app is geschaald, worden er extra resources toegewezen om meerdere exemplaren van de Azure Functions host uit te voeren. Als de berekenings aanvraag daarentegen wordt verkleind, worden de instanties van de functie-host door de schaal controller verwijderd. Het aantal exemplaren wordt uiteindelijk naar nul geschaald wanneer er geen functies in een functie-app worden uitgevoerd.

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

### <a name="billing-model"></a>Factureringsmodel

De facturering voor de verschillende plannen wordt gedetailleerd beschreven op de [pagina met Azure functions prijzen](https://azure.microsoft.com/pricing/details/functions/). Het gebruik wordt geaggregeerd op het niveau van de functie-app en telt alleen de tijd waarop de functie code wordt uitgevoerd. Hier volgen enkele eenheden voor facturering:

* **Resource verbruik in Gigabyte-seconden (GB-s)** . Berekend als een combi natie van geheugen grootte en uitvoerings tijd voor alle functies in een functie-app. 
* **Uitvoeringen**. Geteld wanneer een functie wordt uitgevoerd in reactie op een gebeurtenis trigger.

Nuttige query's en informatie over het begrijpen van uw verbruiks factuur vindt u [in de veelgestelde vragen over facturering](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>Servicelimieten

In de volgende tabel worden de limieten aangegeven die van toepassing zijn op app-functies wanneer ze worden uitgevoerd in de verschillende hosting plannen:

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
