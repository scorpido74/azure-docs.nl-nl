---
title: Overzicht van Durable Functions-versies-Azure Functions
description: Meer informatie over Durable Functions-versies.
author: cgillum
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 789af25cc37183e9eeae253e1e8529615abdd308
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849799"
---
# <a name="durable-functions-versions-overview"></a>Overzicht van Durable Functions versies

*Durable functions* is een uitbrei ding van [Azure functions](../functions-overview.md) en [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) waarmee u stateful functies in een serverloze omgeving kunt schrijven. Met de extensie worden status, controlepunten en het opnieuw opstarten voor u beheerd. Als u nog niet bekend bent met Durable Functions, raadpleegt u de documentatie van het [overzicht](durable-functions-overview.md).

## <a name="new-features-in-2x"></a>Nieuwe functies in 2. x

In deze sectie worden de functies beschreven van Durable Functions die zijn toegevoegd in versie 2. x.

### <a name="durable-entities"></a>Duurzame entiteiten

In Durable Functions 2. x hebben we een nieuw concept voor [entiteits functies](durable-functions-entities.md) geïntroduceerd.

Met entiteits functies worden bewerkingen gedefinieerd voor het lezen en bijwerken van kleine stukjes status, ook wel *duurzame entiteiten*genoemd. Net als Orchestrator functions zijn entiteits functies functies met een speciaal trigger type, *entiteits trigger*. In tegens telling tot Orchestrator-functies hebben entiteits functies geen specifieke code beperkingen. Met entiteits functies wordt ook de status expliciet beheerd in plaats van impliciet de status via de controle stroom te vertegenwoordigen.

Zie het artikel [duurzame entities](durable-functions-entities.md) voor meer informatie.

### <a name="durable-http"></a>Duurzame HTTP

In Durable Functions 2. x is een nieuwe [duurzame HTTP-](durable-functions-http-features.md#consuming-http-apis) functie geïntroduceerd waarmee u het volgende kunt doen:

* HTTP-Api's rechtstreeks aanroepen vanuit Orchestration-functies (met enkele gedocumenteerde beperkingen).
* Implementeer automatische polling van HTTP 202-status aan client zijde.
* Ingebouwde ondersteuning voor door [Azure beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md).

Zie het artikel [http-functies](durable-functions-http-features.md#consuming-http-apis) voor meer informatie.

## <a name="migrate-from-1x-to-2x"></a>Migreren van 1. x naar 2. x

In deze sectie wordt beschreven hoe u uw bestaande versie 1. x Durable Functions naar versie 2. x kunt migreren om te profiteren van de nieuwe functies.

### <a name="upgrade-the-extension"></a>De extensie upgraden

Installeer versie 2. x van de [uitbrei ding voor de Durable functions bindingen](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) in uw project. Zie [Azure functions bindings uitbreidingen registreren](../functions-bindings-register.md) voor meer informatie.

### <a name="update-your-code"></a>Uw code bijwerken

Durable Functions 2. x introduceert diverse belang rijke wijzigingen. Durable Functions 1. x-toepassingen zijn niet compatibel met Durable Functions 2. x zonder code wijzigingen. In deze sectie vindt u enkele van de wijzigingen die u moet aanbrengen bij het upgraden van de functies van versie 1. x naar 2. x.

#### <a name="hostjson-schema"></a>Host. json-schema

Durable Functions 2. x maakt gebruik van een nieuw host. json-schema. De belangrijkste wijzigingen van 1. x zijn onder andere:

* `"storageProvider"` (en de Subsectie `"azureStorage"`) voor Storage-specifieke configuratie.
* `"tracing"` voor configuratie van tracering en logboek registratie.
* `"notifications"` (en de Subsectie `"eventGrid"`) voor configuratie van gebeurtenis raster meldingen.

Zie de [referentie documentatie voor Durable functions host. json](durable-functions-bindings.md#durable-functions-2-0-host-json) voor meer informatie.

#### <a name="public-interface-changes-net-only"></a>Wijzigingen in de open bare interface (alleen .NET)

In versie 1. x hebben de verschillende _context_ objecten die door Durable functions worden ondersteund, abstracte basis klassen die zijn bedoeld voor gebruik in eenheids testen. Als onderdeel van Durable Functions 2. x worden deze abstracte basis klassen vervangen door interfaces.

De volgende tabel bevat de belangrijkste wijzigingen:

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` of `IDurableClient` |
| `DurableOrchestrationContext` of `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` of `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

Als een abstracte basis klasse virtuele methoden bevatte, zijn deze virtuele methoden vervangen door extensie methoden die zijn gedefinieerd in `DurableContextExtensions`.

#### <a name="functionjson-changes-javascript-and-c-script"></a>de functie. json-wijzigingen ( C# java script en script)

In Durable Functions 1. x gebruikt de Orchestrator-client binding een `type` van `orchestrationClient`. Versie 2. x maakt gebruik van `durableClient` in plaats daarvan.
