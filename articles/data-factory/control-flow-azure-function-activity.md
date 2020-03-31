---
title: Azure-functieactiviteit in Azure Data Factory
description: Meer informatie over het gebruik van de Azure-functieactiviteit om een Azure-functie uit te voeren in een pijplijn voor gegevensfabrieken
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: ffb610634399594788afcb9b600ba00c6803dfdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77207022"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure-functieactiviteit in Azure Data Factory

Met de azure-functieactiviteit u [Azure-functies](../azure-functions/functions-overview.md) uitvoeren in een pijplijn voor gegevensfabrieken. Als u een Azure-functie wilt uitvoeren, moet u een gekoppelde serviceverbinding en een activiteit maken die de Azure-functie opgeeft die u van plan bent uit te voeren.

Bekijk de volgende video voor een acht minuten durende introductie en demonstratie van deze functie:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Gekoppelde Azure-functieservice

Het retourtype van de Azure-functie `JObject`moet geldig zijn. (Houd er rekening mee *not* dat `JObject` [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) geen .) Elk retourtype `JObject` dat niet is mislukt en de inhoud van de reactievan de gebruikersfout *verhoogt, is geen geldig JObject*.

| **Eigenschap** | **Beschrijving** | **Vereist** |
| --- | --- | --- |
| type   | De eigenschap type moet zijn ingesteld op: **AzureFunction** | ja |
| url van functie-app | URL voor de Azure Function App. Opmaak `https://<accountname>.azurewebsites.net`is . Deze URL is de waarde onder **de SECTIE URL** bij het bekijken van uw functie-app in de Azure-portal  | ja |
| functiesleutel | Toegangssleutel voor de Azure-functie. Klik op de sectie **Beheren** voor de betreffende functie en kopieer de **functiesleutel** of de **hostsleutel.** Lees hier meer: [Http-triggers en bindingen voor Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | ja |
|   |   |   |

## <a name="azure-function-activity"></a>Azure-functieactiviteit

| **Eigenschap**  | **Beschrijving** | **Toegestane waarden** | **Vereist** |
| --- | --- | --- | --- |
| name  | Naam van de activiteit in de pijplijn  | Tekenreeks | ja |
| type  | Type activiteit is 'AzureFunctionActivity' | Tekenreeks | ja |
| gekoppelde service | De azure-functie gekoppelde service voor de bijbehorende Azure-functie-app  | Gekoppelde serviceverwijzing | ja |
| functienaam  | Naam van de functie in de Azure Function App die deze activiteit aanroept | Tekenreeks | ja |
| method  | REST API-methode voor de functieaanroep | Ondersteunde typen tekenreeksen: "GET", "POST", "PUT"   | ja |
| koptekst  | Kopteksten die naar de aanvraag worden verzonden. Bijvoorbeeld om de taal en het type in te stellen op een verzoek: "headers": { "Accepteren-taal": "en-us", "Content-Type": "application/json" } | Tekenreeks (of expressie met resultaatType van tekenreeks) | Nee |
| body  | lichaam dat samen met het verzoek naar de functie-api-methode wordt verzonden  | Tekenreeks (of expressie met resultaatType van tekenreeks) of object.   | Vereist voor PUT/POST-methoden |
|   |   |   | |

Zie het schema van de payload van het verzoek in de sectie [Payload-schema](control-flow-web-activity.md#request-payload-schema) aanvragen.

## <a name="routing-and-queries"></a>Routering en query's

De Azure-functieactiviteit ondersteunt **routering**. Als uw Azure-functie bijvoorbeeld het `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`eindpunt `functionName` heeft, is `<functionName>/<value>`het te gebruiken in de Azure-functieactiviteit . U deze functie parameteriseren `functionName` om de gewenste functie te bieden bij runtime.

De Azure-functieactiviteit ondersteunt ook **query's.** Een query moet worden opgenomen `functionName`als onderdeel van de . Als de functienaam zich `HttpTriggerCSharp` bijvoorbeeld bevindt en de `name=hello`query die u `functionName` wilt opnemen, `HttpTriggerCSharp?name=hello`kunt u de in de Azure-functieactiviteit als . Deze functie kan worden geparameteriseerd, zodat de waarde kan worden bepaald bij runtime.

## <a name="timeout-and-long-running-functions"></a>Time-out en langlopende functies

Azure Functions is na 230 seconden `functionTimeout` een keer uit, ongeacht de instelling die u in de instellingen hebt geconfigureerd. Raadpleeg [dit artikel](../azure-functions/functions-versions.md#timeout) voor meer informatie. Als u dit gedrag wilt omzeilen, volgt u een synchronisatiepatroon of gebruikt u Duurzame functies. Het voordeel van duurzame functies is dat ze hun eigen state-tracking mechanisme bieden, zodat u niet hoeft te uw eigen implementeren.

Meer informatie over duurzame functies in [dit artikel](../azure-functions/durable/durable-functions-overview.md). U een Azure-functieactiviteit instellen om de duurzame functie aan te roepen, waarmee een antwoord wordt weergegeven met een andere URI, zoals [dit voorbeeld.](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery) Omdat `statusQueryGetUri` http-status 202 wordt geretourneerd terwijl de functie wordt uitgevoerd, u de status van de functie peilen met behulp van een webactiviteit. Stel eenvoudig een webactiviteit `url` in `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`met het veld ingesteld op . Wanneer de duurzame functie is voltooid, wordt de uitvoer van de functie de uitvoer van de webactiviteit.


## <a name="sample"></a>Voorbeeld

Hier vindt u een voorbeeld van een gegevensfabriek die een Azure-functie gebruikt om de inhoud van een [teerbestand](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction)te extraheren.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over activiteiten in Data Factory in [Pipelines en activiteiten in Azure Data Factory](concepts-pipelines-activities.md).
