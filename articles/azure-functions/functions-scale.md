---
title: Schaal en hosting van Azure Functions
description: Meer informatie over hoe u kiezen tussen het Azure-abonnement voor het gebruik van functies en het Premium-abonnement.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 03/27/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0a54d7490fb306bfbc8e1b111e7b7d64c09d2292
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276606"
---
# <a name="azure-functions-scale-and-hosting"></a>Schaal en hosting van Azure Functions

Wanneer u een functie-app maakt in Azure, moet u een hostingplan voor uw app kiezen. Er zijn drie hostingplannen beschikbaar voor Azure-functies: [verbruiksplan,](#consumption-plan) [Premium-abonnement](#premium-plan)en [speciaal (App Service)-abonnement](#app-service-plan).

Het hostingplan dat u kiest, bepaalt het volgende gedrag:

* Hoe uw functie-app wordt geschaald.
* De bronnen die beschikbaar zijn voor elk exemplaar van de functie-app.
* Ondersteuning voor geavanceerde functies, zoals Azure Virtual Network-connectiviteit.

Zowel verbruiks- als Premium-abonnementen voegen automatisch rekenkracht toe wanneer uw code wordt uitgevoerd. Uw app wordt opgeschaald wanneer dat nodig is om de belasting te verwerken en wordt ingeschaald wanneer de code niet meer wordt uitgevoerd. Voor het verbruiksplan hoeft u ook niet vooraf te betalen voor niet-actieve VM's of capaciteit te reserveren.  

Premium-abonnement biedt extra functies, zoals premium compute-exemplaren, de mogelijkheid om instanties voor onbepaalde tijd warm te houden en VNet-connectiviteit.

Met App Service-abonnement u profiteren van de speciale infrastructuur die u beheert. Uw functie-app schaalt niet op basis van gebeurtenissen, wat betekent dat het nooit wordt geschaald naar nul. (Vereist dat [Always on](#always-on) is ingeschakeld.)

## <a name="hosting-plan-support"></a>Ondersteuning voor hostingplannen

Functieondersteuning valt in de volgende twee categorieën:

* _Algemeen beschikbaar (GA):_ volledig ondersteund en goedgekeurd voor productiegebruik.
* _Preview_: nog niet volledig ondersteund of goedgekeurd voor productiegebruik.

De volgende tabel geeft het huidige ondersteuningsniveau voor de drie hostingplannen aan wanneer u op Windows of Linux wordt uitgevoerd:

| | Verbruiksabonnement | Premium-abonnement | Speciaal plan |
|-|:----------------:|:------------:|:----------------:|
| Windows | Algemene beschikbaarheid | Algemene beschikbaarheid | Algemene beschikbaarheid |
| Linux | Algemene beschikbaarheid | Algemene beschikbaarheid | Algemene beschikbaarheid |

## <a name="consumption-plan"></a>Verbruiksabonnement

Wanneer u het verbruiksplan gebruikt, worden exemplaren van de Azure Functions-host dynamisch toegevoegd en verwijderd op basis van het aantal binnenkomende gebeurtenissen. Dit serverloze abonnement wordt automatisch geschaald en u betaalt alleen voor rekenresources wanneer uw functies worden uitgevoerd. Bij een verbruiksabonnement treedt er na een ingestelde periode een time-out op voor een functie-uitvoering.

De facturering is dan ook gebaseerd op het aantal uitvoeringen, de uitvoeringstijd en het gebruikte geheugen. De facturering wordt samengevoegd voor alle functies in een functie-app. Zie de [prijspagina Azure Functions voor](https://azure.microsoft.com/pricing/details/functions/)meer informatie.

Het verbruiksplan is het standaardhostingplan en biedt de volgende voordelen:

* Alleen betalen wanneer uw functies worden uitgevoerd
* Automatisch uitschalen, zelfs tijdens perioden van hoge belasting

Functie-apps in dezelfde regio kunnen aan hetzelfde verbruiksplan worden toegewezen. Er is geen nadeel of impact op het hebben van meerdere apps die in hetzelfde verbruiksplan worden uitgevoerd. Het toewijzen van meerdere apps aan hetzelfde verbruiksplan heeft geen invloed op de veerkracht, schaalbaarheid of betrouwbaarheid van elke app.

Zie [Kosten van het verbruiksplan begrijpen](functions-consumption-costs.md)voor meer informatie over het schatten van kosten bij het uitvoeren in een verbruiksplan.

## <a name="premium-plan"></a><a name="premium-plan"></a>Premium-abonnement

Wanneer u het Premium-abonnement gebruikt, worden exemplaren van de Azure Functions-host toegevoegd en verwijderd op basis van het aantal binnenkomende gebeurtenissen, net als het verbruiksplan.  Premium-abonnement ondersteunt de volgende functies:

* Voortdurend warme exemplaren om een koude start te voorkomen
* VNet-connectiviteit
* Onbeperkte uitvoeringsduur (60 minuten gegarandeerd)
* Premium-instantiegroottes (één kern, twee kern- en vier kernexemplaren)
* Meer voorspelbare prijzen
* Toewijzing van apps met hoge dichtheid voor abonnementen met meerdere functie-apps

Informatie over hoe u deze opties configureren, vindt u in het [Azure Functions Premium-abonnementdocument](functions-premium-plan.md).

In plaats van facturering per uitvoering en geheugen verbruikt, is facturering voor het Premium-abonnement gebaseerd op het aantal kernseconden en geheugen dat wordt gebruikt in de benodigde en vooraf verwarmde exemplaren. Ten minste één exemplaar moet te allen tijde warm zijn per plan. Dit betekent dat er een minimale maandelijkse kosten per actief plan zijn, ongeacht het aantal uitvoeringen. Houd er rekening mee dat alle functie-apps in een Premium-abonnement vooraf opgewarmde en actieve exemplaren delen.

Overweeg het Azure Functions Premium-abonnement in de volgende situaties:

* Uw functie-apps worden continu of bijna continu uitgevoerd.
* U hebt een groot aantal kleine uitvoeringen en een hoge uitvoeringsfactuur, maar een lage GB tweede factuur in het verbruiksplan.
* U hebt meer CPU- of geheugenopties nodig dan wat wordt geleverd door het verbruiksplan.
* Uw code moet langer duren dan de [maximale uitvoeringstijd die](#timeout) is toegestaan in het verbruiksplan.
* U hebt functies nodig die alleen beschikbaar zijn op een Premium-abonnement, zoals virtuele netwerkconnectiviteit.

Wanneer u JavaScript-functies uitvoert op een Premium-abonnement, moet u een instantie kiezen met minder vCPU's. Zie [de Single-core Premium-abonnementen kiezen](functions-reference-node.md#considerations-for-javascript-functions)voor meer informatie .  

## <a name="dedicated-app-service-plan"></a><a name="app-service-plan"></a>Speciaal (App Service) abonnement

Uw functie-apps kunnen ook worden uitgevoerd op dezelfde speciale VM's als andere App Service-apps (Basic, Standard, Premium en Geïsoleerde SKU's).

Overweeg een App Service-abonnement in de volgende situaties:

* U hebt bestaande, onderbenutte VM's die al andere App Service-exemplaren uitvoeren.
* U wilt een aangepaste afbeelding geven waarop u uw functies uitvoeren.

U betaalt hetzelfde voor functie-apps in een App Service-abonnement als voor andere App Service-bronnen, zoals web-apps. Zie het uitgebreide overzicht van azure [app service-abonnementen voor](../app-service/overview-hosting-plans.md)meer informatie over de werking van het App Service-abonnement.

Met een App Service-abonnement u handmatig uitschalen door meer VM-exemplaren toe te voegen. U ook automatisch schalen inschakelen. Zie [Het aantal instance per eer handmatig of automatisch schalen](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json)voor meer informatie . U ook opschalen door een ander App Service-abonnement te kiezen. Zie [Een app opschalen in Azure](../app-service/manage-scale-up.md)voor meer informatie. 

Wanneer u JavaScript-functies uitvoert op een App Service-abonnement, moet u een abonnement kiezen met minder vCPU's. Zie [Single-core App Service-abonnementen kiezen](functions-reference-node.md#choose-single-vcpu-app-service-plans)voor meer informatie . 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a><a name="always-on"></a>Altijd aan

Als u een App-service-abonnement uitvoert, moet u de instelling **Altijd inschakelen,** zodat uw functie-app correct wordt uitgevoerd. Op een App Service-abonnement wordt de runtime van de functies na een paar minuten inactiviteit niet actief, dus alleen HTTP-triggers zullen uw functies "wakker maken". Altijd aan is alleen beschikbaar op een App Service-abonnement. Op een verbruiksplan activeert het platform automatisch functie-apps.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Zelfs als Always On is ingeschakeld, wordt de uitvoeringstime-out voor afzonderlijke functies geregeld door de `functionTimeout` instelling in het projectbestand [host.json.](functions-host-json.md#functiontimeout)

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Het hostingplan van een bestaande toepassing bepalen

Zie **App Service-plan /prijscategorie** op het tabblad **Overzicht** voor de functie-app in de [Azure-portal](https://portal.azure.com)voor het bepalen van het hostingplan dat door uw functie-app wordt gebruikt. Voor App Service-abonnementen wordt ook de prijscategorie aangegeven.

![Schaalplan weergeven in de portal](./media/functions-scale/function-app-overview-portal.png)

U de Azure CLI ook als volgt gebruiken om het abonnement te bepalen:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Wanneer de uitvoer van `dynamic`deze opdracht is, bevindt uw functie-app zich in het verbruiksplan. Wanneer de uitvoer van `ElasticPremium`deze opdracht is, bevindt uw functie-app zich in het Premium-abonnement. Alle andere waarden geven verschillende lagen van een App Service-abonnement aan.

## <a name="storage-account-requirements"></a>Vereisten voor een opslagaccount

Voor elk abonnement is een functie-app vereist, waarvoor een algemeen Azure Storage-account is vereist, dat Azure Blob, Queue, Files en Tabelopslag ondersteunt. Dit komt omdat Functions afhankelijk is van Azure Storage voor bewerkingen zoals het beheren van triggers en beheerfunctieuitvoeringen, maar sommige opslagaccounts ondersteunen geen wachtrijen en tabellen. Deze accounts, waaronder opslagaccounts met bloballeen (inclusief premium opslag) en opslagaccounts voor algemene doeleinden met zoneredundante opslagreplicatie, worden uit gefilterd uit uw bestaande **selecties voor opslagaccounts** wanneer u een functie-app maakt.

Hetzelfde opslagaccount dat door uw functie-app wordt gebruikt, kan ook worden gebruikt door uw triggers en bindingen om uw toepassingsgegevens op te slaan. Voor opslagintensieve bewerkingen moet u echter een apart opslagaccount gebruiken.  

Het is zeker mogelijk voor meerdere functie-apps om hetzelfde opslagaccount te delen zonder problemen. (Een goed voorbeeld hiervan is wanneer u meerdere apps in uw lokale omgeving ontwikkelt met behulp van de Azure Storage Emulator, die werkt als één opslagaccount.) 

<!-- JH: Does using a Premium Storage account improve perf? -->

Zie [De azure storageservices introduceren](../storage/common/storage-introduction.md#azure-storage-services)voor meer informatie over opslagaccounttypen.

## <a name="how-the-consumption-and-premium-plans-work"></a>Hoe de consumptie- en premieplannen werken

In de abonnementen Voor verbruik en Premium worden de infrastructuur voor Azure-functies geschaald door cpu- en geheugenbronnen toe te voegen door extra exemplaren van de functiehost toe te voegen, op basis van het aantal gebeurtenissen waarop de functies worden geactiveerd. Elk exemplaar van de functiehost in het verbruiksplan is beperkt tot 1,5 GB geheugen en één CPU.  Een instantie van de host is de volledige functie-app, wat betekent dat alle functies binnen een functie-app bron delen binnen een instantie en schalen op hetzelfde moment. Functie-apps die hetzelfde verbruiksplan delen, worden onafhankelijk van elkaar geschaald.  In het Premium-abonnement bepaalt uw abonnementsgrootte het beschikbare geheugen en de CPU voor alle apps in dat abonnement op dat exemplaar.  

Functiecodebestanden worden opgeslagen in Azure Files-shares op het hoofdopslagaccount van de functie. Wanneer u het hoofdopslagaccount van de functie-app verwijdert, worden de functiecodebestanden verwijderd en kunnen ze niet worden hersteld.

### <a name="runtime-scaling"></a>Runtime-schaling

Azure Functions gebruikt een component genaamd de *schaalcontroller* om de snelheid van gebeurtenissen te controleren en te bepalen of u wilt uitschalen of schalen. De schaalcontroller gebruikt heuristiek voor elk triggertype. Wanneer u bijvoorbeeld een Azure Queue-opslagtrigger gebruikt, wordt deze geschaald op basis van de wachtrijlengte en de leeftijd van het oudste wachtrijbericht.

De schaaleenheid voor Azure-functies is de functie-app. Wanneer de functie-app is uitgeschaald, worden extra resources toegewezen om meerdere exemplaren van de Azure Functions-host uit te voeren. Als de rekenvraag daarentegen wordt verminderd, verwijdert de schaalcontroller functiehost-exemplaren. Het aantal exemplaren wordt uiteindelijk naar nul *geschaald* wanneer er geen functies worden uitgevoerd binnen een functie-app.

![Controlegebeurtenissen voor controllerschalen en instanties maken](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Inzicht in schaalgedrag

Schalen kan variëren op een aantal factoren en anders schalen op basis van de geselecteerde trigger en taal. Er zijn een paar fijne kneepjes van schaalgedrag om bewust te zijn van:

* Een app met één functie schaalt alleen uit op maximaal 200 exemplaren. Eén instantie kan echter meer dan één bericht of verzoek tegelijk verwerken, dus er is geen vaste limiet voor het aantal gelijktijdige uitvoeringen.
* Voor HTTP-triggers worden nieuwe exemplaren hoogstens één keer per seconde toegewezen.
* Voor niet-HTTP-triggers worden nieuwe exemplaren hoogstens één keer per 30 seconden toegewezen. Schalen gaat sneller bij het uitvoeren in een [Premium-abonnement.](#premium-plan)
* Gebruik Voor ServiceBus-triggers De rechten _beheren_ op resources voor de meest efficiënte schaling. Met _Luisterrechten_ is schalen niet zo nauwkeurig omdat de wachtrijlengte niet kan worden gebruikt om schaalbeslissingen te bepalen. Zie Beleid voor toegang [tot](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies)servicebussen voor meer informatie over het instellen van rechten in toegangsbeleid voor servicebussen.
* Zie voor gebeurtenishubtriggers de [schaalrichtlijnen](functions-bindings-event-hubs-trigger.md#scaling) in het referentieartikel. 

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Aanbevolen procedures en patronen voor schaalbare apps

Er zijn veel aspecten van een functie-app die van invloed zijn op hoe goed deze wordt geschaald, waaronder hostconfiguratie, runtime-footprint en resource-efficiëntie.  Zie voor meer informatie het [gedeelte schaalbaarheid van het artikel prestatieoverwegingen](functions-best-practices.md#scalability-best-practices). U moet zich ook bewust zijn van hoe verbindingen zich gedragen als uw functie-app schaalt. Zie [Verbindingen beheren in Azure-functies](manage-connections.md)voor meer informatie.

Zie [Azure Functions Python-ontwikkelaarshandleiding - Schalen en gelijktijdigheid](functions-reference-python.md#scaling-and-concurrency) en [Azure Functions Node.js-ontwikkelaarshandleiding - Schalen en gelijktijdigheid](functions-reference-node.md#scaling-and-concurrency)voor meer informatie over schalen in Python en Node.js .

### <a name="billing-model"></a>Factureringsmodel

Facturering voor de verschillende abonnementen wordt in detail beschreven op de [prijspagina azure-functies](https://azure.microsoft.com/pricing/details/functions/). Het gebruik wordt geaggregeerd op het niveau van de functie-app en telt alleen de tijd dat de functiecode wordt uitgevoerd. De volgende zijn eenheden voor facturering:

* **Resourceverbruik in gigabyteseconden (GB-s).** Berekend als een combinatie van geheugengrootte en uitvoeringstijd voor alle functies binnen een functie-app. 
* **Executies.** Elke keer geteld dat een functie wordt uitgevoerd als reactie op een gebeurtenistrigger.

Nuttige vragen en informatie over hoe u uw verbruiksfactuur begrijpen, vindt u [in de veelgestelde vragen over facturering.](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>Servicelimieten

In de volgende tabel worden de limieten aangegeven die van toepassing zijn op functie-apps wanneer deze worden uitgevoerd in de verschillende hostingplannen:

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
