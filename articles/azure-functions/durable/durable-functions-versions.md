---
title: Overzicht van versies duurzame functies - Azure-functies
description: Meer informatie over versies met duurzame functies.
author: cgillum
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 4a117e7f69647af3ad82f9013bfa40556ccc0dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152887"
---
# <a name="durable-functions-versions-overview"></a>Overzicht van versies duurzame functies

*Duurzame functies* is een uitbreiding van [Azure Functions](../functions-overview.md) en [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) waarmee u stateful-functies schrijven in een serverloze omgeving. Met de extensie worden status, controlepunten en het opnieuw opstarten voor u beheerd. Als u nog niet bekend bent met duurzame functies, raadpleegt u de [overzichtsdocumentatie.](durable-functions-overview.md)

## <a name="new-features-in-2x"></a>Nieuwe functies in 2.x

In deze sectie worden de functies van duurzame functies beschreven die in versie 2.x zijn toegevoegd.

### <a name="durable-entities"></a>Duurzame entiteiten

In Durable Functions 2.x introduceerden we een nieuw [entiteitsfunctiesconcept.](durable-functions-entities.md)

Entiteitsfuncties definiëren bewerkingen voor het lezen en bijwerken van kleine stukjes staat, bekend als *duurzame entiteiten*. Net als orchestrator-functies zijn entiteitsfuncties functies met een speciaal triggertype, *entiteitstrigger*. In tegenstelling tot orchestrator-functies hebben entiteitsfuncties geen specifieke codebeperkingen. Entiteitsfuncties beheren ook expliciet status in plaats van impliciet status te vertegenwoordigen via controlestroom.

Zie het artikel [over duurzame entiteiten](durable-functions-entities.md) voor meer informatie.

### <a name="durable-http"></a>Duurzaam HTTP

In Duurzame functies 2.x hebben we een nieuwe [Duurzame HTTP-functie](durable-functions-http-features.md#consuming-http-apis) geïntroduceerd waarmee u:

* Bel HTTP API's rechtstreeks vanuit orchestration-functies (met enkele gedocumenteerde beperkingen).
* Implementeer automatische HTTP 202-statuspolling aan de clientzijde.
* Ingebouwde ondersteuning voor [Azure Managed Identities.](../../active-directory/managed-identities-azure-resources/overview.md)

Zie het [http-functiesartikel](durable-functions-http-features.md#consuming-http-apis) voor meer informatie.

## <a name="migrate-from-1x-to-2x"></a>Migreren van 1,x naar 2,x

In dit gedeelte wordt beschreven hoe u uw bestaande versie 1.x Duurzame functies migreert naar versie 2.x om te profiteren van de nieuwe functies.

### <a name="upgrade-the-extension"></a>De extensie bijwerken

Installeer versie 2.x van de [extensie Duurzame functies bindingen](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) in uw project. Zie [Azure Functions-bindingsextensies registreren](../functions-bindings-register.md) voor meer informatie.

### <a name="update-your-code"></a>Uw code bijwerken

Duurzame functies 2.x introduceert verschillende brekende veranderingen. Duurzame functies 1.x-toepassingen zijn niet compatibel met duurzame functies 2.x zonder codewijzigingen. In deze sectie worden enkele wijzigingen weergegeven die u moet aanbrengen bij het upgraden van uw versie 1.x-functies naar 2.x.

#### <a name="hostjson-schema"></a>Host.json-schema

Duurzame functies 2.x maakt gebruik van een nieuw host.json-schema. De belangrijkste wijzigingen van 1.x zijn:

* `"storageProvider"`(en `"azureStorage"` de onderafdeling) voor opslagspecifieke configuratie.
* `"tracing"`voor het traceren en registreren van configuraties.
* `"notifications"`(en `"eventGrid"` de onderafdeling) voor de configuratie van de melding van het gebeurtenisraster.

Zie de [referentiedocumentatie Duurzame functies host.json](durable-functions-bindings.md#durable-functions-2-0-host-json) voor meer informatie.

#### <a name="default-taskhub-name-changes"></a>Standaardtaakhub-naamwijzigingen

Als in versie 1.x een naam van de taakhub niet is opgegeven in host.json, wordt deze standaard ingesteld op 'DurableFunctionsHub'. In versie 2.x wordt de standaardnaam van de taakhub nu afgeleid van de naam van de functie-app. Als u daarom geen naam van de taakhub hebt opgegeven bij het upgraden naar 2.x, werkt uw code met een nieuwe taakhub en worden alle in-flight orchestrations niet langer door een toepassing verwerkt. Om dit mogelijk te maken, u de naam van uw taakhub expliciet instellen op de standaardv1.x van 'DurableFunctionsHub', of u onze [implementatierichtlijnen](durable-functions-zero-downtime-deployment.md) voor downtime volgen voor meer informatie over het afhandelen van baanbrekende wijzigingen voor orkestraties tijdens de vlucht.

#### <a name="public-interface-changes-net-only"></a>Wijzigingen in de openbare interface (alleen.NET)

In versie 1.x hebben de verschillende _contextobjecten_ die worden ondersteund door duurzame functies abstracte basisklassen die zijn bedoeld voor gebruik bij het testen van eenheden. Als onderdeel van Duurzame Functies 2.x worden deze abstracte basisklassen vervangen door interfaces.

De volgende tabel vertegenwoordigt de belangrijkste wijzigingen:

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` of `IDurableClient` |
| `DurableOrchestrationContext` of `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` of `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

In het geval dat een abstracte basisklasse virtuele methoden bevatte, zijn deze virtuele `DurableContextExtensions`methoden vervangen door uitbreidingsmethoden die zijn gedefinieerd in .

#### <a name="functionjson-changes-javascript-and-c-script"></a>function.json changes (JavaScript en C# Script)

In Duurzame functies 1.x maakt de `type` orchestration client binding gebruik van een van `orchestrationClient`. Versie 2.x `durableClient` gebruikt in plaats daarvan.
