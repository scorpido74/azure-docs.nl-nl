---
title: Betaal Azure Functions van duurzame functies
description: Meer informatie over het interne gedrag van Durable Functions en hoe deze van invloed zijn op facturering voor Azure Functions.
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: f2de6bdf24aa1a0a11349c8f0ec9b3995b026a47
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694883"
---
# <a name="durable-functions-billing"></a>Facturering Durable Functions

[Durable functions](durable-functions-overview.md) wordt op dezelfde manier gefactureerd als Azure functions. Zie voor meer informatie, [prijzen van Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Bij het uitvoeren van Orchestrator-functies in Azure Functions [verbruiks abonnement](../functions-scale.md#consumption-plan)moet u rekening houden met een aantal facturerings gedrag. In de volgende secties worden deze gedragingen en hun effect in meer detail beschreven.

## <a name="orchestrator-function-replay-billing"></a>Facturering van Orchestrator-functie replay

[Orchestrator-functies](durable-functions-orchestrations.md) kunnen meerdere keren worden herhaald gedurende de levens duur van een indeling. Elke herhaling wordt weer gegeven door de Azure Functions runtime als een afzonderlijke functie aanroep. Daarom wordt in het Azure Functions verbruiks plan voor elk herhaling van een Orchestrator-functie gefactureerd. Andere typen abonnementen worden niet in rekening gebracht voor de Orchestrator-functie replay.

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>Wachtende en opleveren in Orchestrator-functies

Wanneer een Orchestrator-functie wacht op het volt ooien van een asynchrone actie door te gebruiken C# in een **wacht** tijd in of in te **leveren** in Java script, beschouwt de runtime dat bepaalde uitvoeringen zijn voltooid. De facturering voor de Orchestrator-functie stopt op dat moment. Het wordt pas hervat als de volgende Orchestrator-functie opnieuw wordt afgespeeld. Er worden geen kosten in rekening gebracht voor elke tijd die nodig is om een Orchestrator-functie te gebruiken of te leveren.

> [!NOTE]
> Functies die andere functies aanroepen, worden beschouwd als een anti patroon. Dit komt door een probleem met de _dubbele facturering_. Wanneer een functie rechtstreeks een functie aanroept, wordt beide tegelijk uitgevoerd. De aangeroepen functie voert code actief uit terwijl de aanroepende functie wacht op een antwoord. In dit geval moet u betalen voor het tijdstip waarop de aanroepende functie wacht voordat de aangeroepen functie wordt uitgevoerd.
>
> Er is geen dubbele facturering in Orchestrator-functies. De facturering van een Orchestrator-functie wordt gestopt tijdens het wachten op het resultaat van een activiteit functie of suborchestration.

## <a name="durable-http-polling"></a>Duurzame HTTP-polling

Met Orchestrator-functies kunt u langlopende HTTP-aanroepen naar externe eind punten maken, zoals beschreven in het [artikel http-functies](durable-functions-http-features.md). De methode **CallHttpAsync** in C# en de methode **callHttp** in Java script kunnen een http-eind punt intern pollen tijdens het [asynchrone 202-patroon](durable-functions-http-features.md#http-202-handling).

Er wordt momenteel geen directe facturering uitgevoerd voor interne HTTP-polling bewerkingen. Interne polling kan er echter toe leiden dat de Orchestrator-functie regel matig opnieuw speelt. Er worden standaard kosten in rekening gebracht voor deze interne functie herhalingen.

## <a name="azure-storage-transactions"></a>Azure Storage trans acties

Durable Functions gebruikt Azure Storage standaard om de status permanent te houden, berichten te verwerken en partities te beheren via BLOB-leases. Omdat u eigenaar bent van dit opslag account, worden de transactie kosten in rekening gebracht voor uw Azure-abonnement. Zie het artikel over de [taak hubs](durable-functions-task-hubs.md)voor meer informatie over de Azure Storage artefacten die worden gebruikt door Durable functions.

Verschillende factoren dragen bij aan de werkelijke Azure Storage kosten die worden gemaakt door uw Durable Functions-app:

* Een app met één functie is gekoppeld aan één Task hub, die een set Azure Storage resources deelt. Deze resources worden gebruikt door alle duurzame functies in een functie-app. Het werkelijke aantal functies in de functie-app heeft geen invloed op de kosten van Azure Storage trans acties.
* Elk exemplaar van de functie-app pollt intern meerdere wacht rijen in het opslag account met behulp van een exponentieel uitstel polling-algoritme. Een exemplaar van een niet-actieve app controleert de wacht rijen minder vaak dan een actieve app, wat leidt tot minder transactie kosten. Zie de [sectie wachtrij-polling in het artikel prestaties en schalen](durable-functions-perf-and-scale.md#queue-polling)voor meer informatie over Durable functions polling van de wachtrij.
* Bij het uitvoeren van de Azure Functions verbruiks-of Premium-abonnementen, pollt de [Azure functions Scale controller](../functions-scale.md#how-the-consumption-and-premium-plans-work) regel matig alle taak-hub-wacht rijen op de achtergrond. Als een functie-app onder licht wordt geschaald, wordt deze wacht rijen alleen door een exemplaar van een enkele schaal controller gecontroleerd. Als de functie-app wordt geschaald naar een groot aantal exemplaren, kunnen er meer instanties van de schaal controller worden toegevoegd. Deze extra schaal controller-instanties kunnen de totale transactie kosten van de wachtrij verhogen.
* Elk functie-app-exemplaar is een concurrentie positie voor een set BLOB-leases. Met deze instanties worden regel matig aanroepen naar de Azure-Blob service voor het vernieuwen van leases of het verkrijgen van nieuwe leases. Met het geconfigureerde aantal partities van de taak hub wordt het aantal BLOB-leases bepaald. Als u uitschaalt naar een groter aantal functie-app-exemplaren, worden de Azure Storage transactie kosten die zijn gekoppeld aan deze lease bewerkingen waarschijnlijk verhoogd.

Meer informatie over Azure Storage prijzen vindt u in de [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/) documentatie. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure Functions prijzen](https://azure.microsoft.com/pricing/details/functions/)
