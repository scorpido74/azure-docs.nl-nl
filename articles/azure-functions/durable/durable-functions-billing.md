---
title: Betaal Azure Functions van duurzame functies
description: Meer informatie over het interne gedrag van duurzame functies en hoe deze invloed hebben op de facturering van Azure Functions.
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: b79d50adf932bd5c316fbda9d0964eea7c0484ca
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935885"
---
# <a name="durable-functions-billing"></a>Facturering Durable Functions

[Durable functions](durable-functions-overview.md) worden gefactureerd op dezelfde Azure functions. Zie voor meer informatie, [prijzen van Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Bij het uitvoeren van Orchestrator-functies in het Azure Functions [verbruiks abonnement](../functions-scale.md#consumption-plan), zijn er enkele facturerings gedrag van belang. In de volgende secties worden deze gedragingen en de invloed ervan beschreven.

## <a name="orchestrator-function-replay-billing"></a>Facturering van Orchestrator-functie replay

[Orchestrator-functies](durable-functions-orchestrations.md) kunnen meerdere keren worden herhaald gedurende de levens duur van de indeling. Elke herhaling wordt weer gegeven door de Azure Functions runtime als een afzonderlijke functie aanroep. Daarom wordt in het verbruiks abonnement van Azure Functions in rekening gebracht voor elk herhaling van de Orchestrator-functie. Andere plan typen worden niet in rekening gebracht voor Orchestrator-functie herhalingen.

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>Wachtende en opleveren in Orchestrator-functies

Wanneer een Orchestrator-functie wacht op een asynchrone actie om te volt ooien `await` metC#() `yield` of (Java script), beschouwt de runtime dat bepaalde uitvoering is voltooid. De facturering voor de Orchestrator-functie stopt op dat moment en wordt pas hervat als de volgende Orchestrator-functie opnieuw wordt afgespeeld. Er worden geen kosten in rekening gebracht voor elke tijd die nodig is om een Orchestrator-functie te gebruiken of te leveren.

> [!NOTE]
> Functies die andere functies aanroepen, worden beschouwd als een anti-patroon. Dit komt door een probleem met de _dubbele facturering_. Wanneer een functie rechtstreeks een functie aanroept, beide tegelijk uitvoeren. De aanroeper is actief bezig met het uitvoeren van code terwijl de aanroeper wacht op een antwoord. In dit geval moet u betalen voor de tijd die de beller wacht voordat de aanroep wordt uitgevoerd. Orchestrator-functies hebben geen last van dit dubbele facturering omdat de facturering van de Orchestrator-functie wordt gestopt tijdens het wachten op het resultaat van een activiteit functie (of sub-indeling).

## <a name="durable-http-polling"></a>Duurzame HTTP-polling

Met Orchestrator-functies kunt u langlopende HTTP-aanroepen naar externe eind punten maken, zoals beschreven in het artikel [http-functies](durable-functions-http-features.md) . De `CallHttpAsync` methode (C#) en de `callHttp` methode (Java script) kunnen een http-eind punt intern controleren bij het volgen van het [asynchrone 202-patroon](durable-functions-http-features.md#http-202-handling). Er wordt momenteel niet rechtstreeks gefactureerd voor de interne HTTP-polling bewerkingen. De interne polling kan er echter voor zorgen dat de Orchestrator-functie regel matig opnieuw wordt afgespeeld en er worden standaard kosten in rekening gebracht voor deze interne functie herhalingen.

## <a name="azure-storage-transactions"></a>Azure-opslag transacties

Durable Functions gebruikt Azure Storage standaard om de status te behouden, berichten te verwerken en partities te beheren via BLOB-leases. Dit opslag account is eigendom van u, dus de transactie kosten worden in rekening gebracht voor uw Azure-abonnement. Zie het artikel over de [taak hubs](durable-functions-task-hubs.md) voor meer informatie over de Azure Storage artefacten die worden gebruikt door Durable functions.

Verschillende factoren dragen bij aan de werkelijke Azure Storage kosten die door uw Durable Functions toepassing worden gemaakt.

* Een app met één functie is gekoppeld aan één Task hub, die een set Azure Storage resources deelt. Deze resources worden gebruikt door alle duurzame functies in een functie-app. Het werkelijke aantal functies in de functie-app heeft geen invloed op de kosten voor Azure Storage trans acties.
* Elk exemplaar van de functie-app pollt intern meerdere wacht rijen in het opslag account met behulp van een exponentieel uitstel polling-algoritme. Een exemplaar van een niet-actieve toepassing vraagt de wacht rijen minder regel matig aan dan een actieve toepassing, waardoor er minder transactie kosten in rekening worden gezet. Zie de [sectie polling van de wachtrij van het artikel prestaties en schalen](durable-functions-perf-and-scale.md#queue-polling) voor meer informatie over Durable functions polling voor de wachtrij.
* Bij het uitvoeren van de Azure Functions verbruiks-of Premium-abonnementen, pollt de [Azure functions Scale controller](../functions-scale.md#how-the-consumption-and-premium-plans-work) regel matig alle taak hub-wacht rijen op de achtergrond. Onder licht naar gematigd schalen, wordt deze wacht rijen door slechts één schaalbaar controller-exemplaar gecontroleerd. Als de functie-app wordt geschaald naar een groot aantal exemplaren, kunnen er meer instanties van de schaal controller worden toegevoegd. Deze extra schaal controller-instanties kunnen de totale transactie kosten van de wachtrij verhogen.
* Elk functie-app-exemplaar is een concurrentie positie voor een set BLOB-leases. Met deze instanties worden regel matig aanroepen naar de Azure-Blob service om de aangehouden leases te vernieuwen, wordt geprobeerd nieuwe leases te verkrijgen. Het aantal BLOB-leases wordt bepaald door het geconfigureerde aantal partities van de taak hub. Als u naar een groter aantal functie-app-exemplaren uitbreidt, worden de Azure Storage transactie kosten die zijn gekoppeld aan deze lease bewerkingen waarschijnlijk verhoogd.

Meer informatie over Azure Storage prijzen vindt u in de [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/) documentatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure Functions prijzen](https://azure.microsoft.com/pricing/details/functions/)
