---
title: Facturering van duurzame functies - Azure-functies
description: Meer informatie over het interne gedrag van duurzame functies en hoe deze van invloed zijn op facturering voor Azure-functies.
author: cgillum
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 504ef93a0002895bc5662d95ad269c8593170ee2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74233014"
---
# <a name="durable-functions-billing"></a>Facturering over duurzame functies

[Duurzame functies](durable-functions-overview.md) worden op dezelfde manier gefactureerd als Azure-functies. Zie [Prijzen voor Azure Functions](https://azure.microsoft.com/pricing/details/functions/) voor meer informatie.

Wanneer u orchestrator-functies uitvoert in het Azure Functions [Consumption-plan,](../functions-scale.md#consumption-plan)moet u op de hoogte zijn van sommige factureringsgedrag. De volgende secties beschrijven dit gedrag en hun effect in meer detail.

## <a name="orchestrator-function-replay-billing"></a>Orchestrator-functie re-facturering opnieuw afspelen

[Orchestrator-functies](durable-functions-orchestrations.md) kunnen gedurende de gehele levensduur van een orkestratie meerdere keren worden afgespeeld. Elke herhaling wordt door de runtime van Azure-functies gezien als een afzonderlijke functieaanroep. Daarom worden in het Azure Functions Consumption-plan u gefactureerd voor elke herhaling van een orchestrator-functie. Andere typen abonnementen brengen geen kosten in rekening voor het opnieuw afspelen van de orchestrator-functie.

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>In afwachting en toegeven in orchestrator functies

Wanneer een orchestrator-functie wacht tot een asynchrone actie is voltooid met **wachten** in C# of **opbrengst** in JavaScript, is de runtime van mening dat bepaalde uitvoering moet worden voltooid. De facturering voor de orchestrator-functie stopt op dat punt. Het wordt pas hervat als de volgende orchestrator-functie opnieuw wordt afgespeeld. U wordt niet gefactureerd voor enige tijd die u in een orchestrator-functie hebt gewacht of opgeeft.

> [!NOTE]
> Functies die andere functies aanroepen, worden door sommigen beschouwd als een antipatroon. Dit is vanwege een probleem dat bekend staat als _dubbele facturering_. Wanneer een functie een andere functie rechtstreeks aanroept, worden beide tegelijkertijd uitgevoerd. De aangeroepen functie voert actief code uit terwijl de aanroepende functie wacht op een antwoord. In dit geval moet u betalen voor de tijd die de aanroepende functie besteedt aan het wachten op de aangeroepen functie.
>
> Er is geen dubbele facturering in orchestrator-functies. De facturering van een orchestrator-functie stopt terwijl deze wacht op het resultaat van een activiteitsfunctie of suborchestration.

## <a name="durable-http-polling"></a>Duurzame HTTP-polling

Orchestrator-functies kunnen langlopende HTTP-aanroepen naar externe eindpunten zoals beschreven in het [http-functiesartikel](durable-functions-http-features.md). De **methode CallhttpAsync** in C# en de **methode aanroepenHttp** in JavaScript kan een HTTP-eindpunt intern peilen terwijl u het [asynchrone 202-patroon volgt.](durable-functions-http-features.md#http-202-handling)

Er is momenteel geen directe facturering voor interne HTTP-pollingbewerkingen. Interne polling kan er echter voor zorgen dat de orchestrator-functie periodiek wordt afgespeeld. Er worden standaardkosten in rekening gebracht voor deze interne functieherhalingen.

## <a name="azure-storage-transactions"></a>Azure Storage-transacties

Duurzame functies maken standaard gebruik van Azure Storage om statuspersistent te houden, berichten te verwerken en partities te beheren via blobleases. Omdat u eigenaar bent van dit opslagaccount, worden transactiekosten in rekening gebracht voor uw Azure-abonnement. Zie het [artikel Taakhubs](durable-functions-task-hubs.md)voor meer informatie over de Azure Storage-artefacten die worden gebruikt door duurzame functies.

Verschillende factoren dragen bij aan de werkelijke Azure Storage-kosten van de app Duurzame functies:

* Eén functie-app is gekoppeld aan één taakhub, die een set Azure Storage-bronnen deelt. Deze bronnen worden gebruikt door alle duurzame functies in een functie-app. Het werkelijke aantal functies in de functie-app heeft geen effect op de transactiekosten van Azure Storage.
* Elke functie-app-instantie peilt intern meerdere wachtrijen in het opslagaccount met behulp van een exponentieel-backoff polling-algoritme. Een inactieve app-instantie peilt de wachtrijen minder vaak dan een actieve app, wat resulteert in minder transactiekosten. Zie het [gedeelte wachtrij-polling van het artikel Prestaties en schaal](durable-functions-perf-and-scale.md#queue-polling)voor meer informatie over het wachtrijpollinggedrag voor duurzame functies.
* Wanneer u de Azure Functions Consumption- of Premium-abonnementen uitvoert, wordt de [schaalcontroller voor Azure Functions](../functions-scale.md#how-the-consumption-and-premium-plans-work) regelmatig alle taakhubwachtrijen op de achtergrond gepeild. Als een functie-app onder lichte tot matige schaal staat, wordt deze wachtrijen slechts door één schaalcontroller gepeild. Als de functie-app wordt geschaald naar een groot aantal exemplaren, kunnen er meer exemplaren van de schaalcontroller worden toegevoegd. Deze extra schaalbeheerinstanties kunnen de totale kosten voor wachtrijtransacties verhogen.
* Elke functie-app-instantie concurreert om een set blob-leases. Deze instanties zullen periodiek oproepen naar de Azure Blob-service om gehouden leases te verlengen of om te proberen nieuwe leases te verkrijgen. Het geconfigureerde partitieaantal van de taakhub bepaalt het aantal blobleases. Door uit te schalen naar een groter aantal functie-app-exemplaren worden waarschijnlijk de transactiekosten van Azure Storage in verband met deze leasebewerkingen verhoogd.

Meer informatie over azure storage-prijzen vindt u in de [prijsdocumentatie voor Azure Storage.](https://azure.microsoft.com/pricing/details/storage/) 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over de prijzen van Azure-functies](https://azure.microsoft.com/pricing/details/functions/)
