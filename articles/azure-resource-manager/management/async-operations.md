---
title: Status van asynchrone bewerkingen
description: Beschrijft hoe u asynchrone bewerkingen in Azure bijhouden. Het toont de waarden die u gebruikt om de status van een langdurige bewerking te krijgen.
ms.topic: conceptual
ms.date: 12/09/2018
ms.custom: seodec18
ms.openlocfilehash: 1cf8898e5fd63e35447f6580e13347ba6d7fc413
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485440"
---
# <a name="track-asynchronous-azure-operations"></a>Asynchrone Azure-bewerkingen bijhouden
Sommige Azure REST-bewerkingen worden asynchroon uitgevoerd omdat de bewerking niet snel kan worden voltooid. In dit artikel wordt beschreven hoe u de status van asynchrone bewerkingen bijhouden via waarden die in het antwoord worden geretourneerd.  

## <a name="status-codes-for-asynchronous-operations"></a>Statuscodes voor asynchrone bewerkingen
Een asynchrone bewerking retourneert in eerste instantie een HTTP-statuscode van:

* 201 (Gemaakt)
* 202 (Geaccepteerd) 

Wanneer de bewerking is voltooid, wordt het resultaat geretourneerd:

* 200 (OK)
* 204 (Geen inhoud) 

Raadpleeg de [REST API-documentatie](/rest/api/) om de antwoorden te zien voor de bewerking die u uitvoert.

## <a name="monitor-status-of-operation"></a>Status van de werking controleren
De asynchrone REST-bewerkingen retourneren kopwaarden, die u gebruikt om de status van de bewerking te bepalen. Er zijn mogelijk drie kopwaarden om te onderzoeken:

* `Azure-AsyncOperation`- URL voor het controleren van de doorlopende status van de bewerking. Als uw bewerking deze waarde retourneert, gebruikt u deze altijd (in plaats van locatie) om de status van de bewerking bij te houden.
* `Location`- URL om te bepalen wanneer een bewerking is voltooid. Gebruik deze waarde alleen wanneer Azure-AsyncOperation niet wordt geretourneerd.
* `Retry-After`- Het aantal seconden dat moet worden gewacht voordat de status van de asynchrone bewerking wordt gecontroleerd.

Niet elke asynchrone bewerking retourneert echter al deze waarden. Mogelijk moet u bijvoorbeeld de waarde van de Azure-AsyncOperation-koptekst voor één bewerking en de waarde van de locatiekopwaarde voor een andere bewerking evalueren. 

U haalt de kopwaarden op zoals u de kopwaarde voor een aanvraag zou ophalen. In C#haalt u bijvoorbeeld de kopwaarde `HttpWebResponse` op `response` van een object met de volgende code:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Aanvragen en respons van Azure-AsyncOperation

Als u de status van de asynchrone bewerking wilt ophalen, stuurt u een GET-aanvraag naar de URL in Azure-AsyncOperation-kopwaarde.

De hoofdtekst van de reactie van deze bewerking bevat informatie over de bewerking. In het volgende voorbeeld worden de mogelijke waarden weergegeven die zijn geretourneerd uit de bewerking:

```json
{
    "id": "{resource path from GET operation}",
    "name": "{operation-id}", 
    "status" : "Succeeded | Failed | Canceled | {resource provider values}", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "percentComplete": {double between 0 and 100 },
    "properties": {
        /* Specific resource provider values for successful operations */
    },
    "error" : { 
        "code": "{error code}",  
        "message": "{error description}" 
    }
}
```

Alleen `status` wordt geretourneerd voor alle reacties. Het foutobject wordt geretourneerd wanneer de status is mislukt of geannuleerd. Alle andere waarden zijn optioneel; Daarom kan het antwoord dat u ontvangt er anders uitzien dan het voorbeeld.

## <a name="provisioningstate-values"></a>provisioningStaatswaarden

Bewerkingen die een bron maken, bijwerken of verwijderen (PUT, `provisioningState` PATCH, DELETE) geven doorgaans een waarde. Wanneer een bewerking is voltooid, wordt een van de volgende drie waarden geretourneerd: 

* Geslaagd
* Mislukt
* Geannuleerd

Alle andere waarden geven aan dat de bewerking nog steeds wordt uitgevoerd. De resourceprovider kan een aangepaste waarde retourneren die de status ervan aangeeft. U bijvoorbeeld **Geaccepteerd** ontvangen wanneer de aanvraag wordt ontvangen en wordt uitgevoerd.

## <a name="example-requests-and-responses"></a>Voorbeeldverzoeken en antwoorden

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Virtuele machine starten (202 met Azure-AsyncOperation)
In dit voorbeeld ziet u hoe u de status van **de startbewerking** voor virtuele machines bepalen. De eerste aanvraag is in de volgende indeling:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

Het retourneert statuscode 202. Onder de kopwaarden ziet u:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Als u de status van de asynchrone bewerking wilt controleren, stuurt u een ander verzoek naar die URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

De antwoordgroep bevat de status van de bewerking:

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Resources implementeren (201 met Azure-AsyncOperation)

In dit voorbeeld ziet u hoe u de status van **implementatieskunt** bepalen voor het implementeren van resources naar Azure. De eerste aanvraag is in de volgende indeling:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

Het retourneert statuscode 201. Het lichaam van de respons omvat:

```json
"provisioningState":"Accepted",
```

Onder de kopwaarden ziet u:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Als u de status van de asynchrone bewerking wilt controleren, stuurt u een ander verzoek naar die URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

De antwoordgroep bevat de status van de bewerking:

```json
{"status":"Running"}
```

Wanneer de implementatie is voltooid, bevat het antwoord:

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Opslagaccount maken (202 met locatie en opnieuw proberen-na)

In dit voorbeeld ziet u hoe u de status van de **bewerking maken** voor opslagaccounts bepalen. De eerste aanvraag is in de volgende indeling:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

En de aanvraaginstantie bevat eigenschappen voor het opslagaccount:

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

Het retourneert statuscode 202. Onder de kopwaarden ziet u de volgende twee waarden:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

Controleer na het wachten op het aantal seconden dat is opgegeven in Retry-After de status van de asynchrone bewerking door een ander verzoek naar die URL te verzenden.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

Als het verzoek nog steeds loopt, ontvangt u een statuscode 202. Als het verzoek is voltooid, ontvangt u een statuscode 200 en bevat de hoofdtekst van het antwoord de eigenschappen van het opslagaccount dat is gemaakt.

## <a name="next-steps"></a>Volgende stappen

* Zie [REST API-documentatie](/rest/api/)voor documentatie over elke REST-bewerking.
* Zie [Resources implementeren met Resource Manager-sjablonen en RestAPI voor](../templates/deploy-rest.md)informatie over het implementeren van sjablonen via de REST-API voor ResourceBeheer.