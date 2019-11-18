---
title: Status van asynchrone bewerkingen
description: Beschrijft hoe u voor het bijhouden van asynchrone bewerkingen in Azure. Het bevat de waarden die u gebruiken om de status van een langdurige bewerking.
ms.topic: conceptual
ms.date: 12/09/2018
ms.custom: seodec18
ms.openlocfilehash: 39174010e5d5c71e1b9f09827f92a2c540d98e78
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149535"
---
# <a name="track-asynchronous-azure-operations"></a>Azure-asynchrone bewerkingen bijhouden
Sommige Azure REST-bewerkingen worden asynchroon uitgevoerd omdat de bewerking snel kan niet worden voltooid. Dit artikel wordt beschreven hoe u kunt de status van asynchrone bewerkingen via waarden in het antwoord geretourneerd bijhouden.  

## <a name="status-codes-for-asynchronous-operations"></a>Statuscodes voor asynchrone bewerkingen
Een asynchrone bewerking retourneert in eerste instantie van een HTTP-statuscode:

* 201 (aangemaakt)
* 202 (aanvaard) 

Als de bewerking is voltooid, retourneert een:

* 200 (OK)
* 204 (geen inhoud) 

Raadpleeg de [REST API-documentatie](/rest/api/) om te zien van de antwoorden voor de bewerking die u wilt uitvoeren.

## <a name="monitor-status-of-operation"></a>Monitor status van bewerking
De asynchrone REST-bewerkingen retourneren headerwaarden, die u gebruiken om de status van de bewerking te bepalen. Er zijn mogelijk drie headerwaarden om te controleren:

* `Azure-AsyncOperation` -URL voor het controleren van de doorlopende status van de bewerking. Als de bewerking voor deze waarde retourneert, altijd deze gebruiken (in plaats van locatie) om bij te houden van de status van de bewerking.
* `Location` -URL om te bepalen wanneer een bewerking is voltooid. Deze waarde gebruikt alleen wanneer de Azure-AsyncOperation niet geretourneerd.
* `Retry-After` -Het aantal seconden dat moet worden gewacht voordat het controleren van de status van de asynchrone bewerking.

Niet elke asynchrone bewerking retourneert echter al deze waarden. U wilt bijvoorbeeld de waarde van de Azure-AsyncOperation-header voor één bewerking, en de waarde van de locatie-header voor een andere bewerking evalueren. 

U haalt de headerwaarden als u een headerwaarde voor een aanvraag wilt ophalen. In C#, haalt u bijvoorbeeld de waarde van de header van een `HttpWebResponse` object met de naam `response` met de volgende code:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Azure-AsyncOperation aanvragen en reacties

Als u de status van de asynchrone bewerking, door een GET-aanvraag te verzenden naar de URL in de Azure-AsyncOperation header-waarde.

De hoofdtekst van het antwoord van deze bewerking bevat informatie over het opnieuw. Het volgende voorbeeld ziet u de mogelijke waarden geretourneerd door de bewerking:

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

Alleen `status` wordt geretourneerd voor alle antwoorden. Het foutobject wordt geretourneerd wanneer de status mislukt of geannuleerd is. Alle andere waarden zijn optioneel. daarom het antwoord u ontvangt zien er mogelijk anders dan in het voorbeeld.

## <a name="provisioningstate-values"></a>provisioningState waarden

Bewerkingen maken, bijwerken of verwijderen (PUT, PATCH, DELETE) een resource is doorgaans retourneren een `provisioningState` waarde. Wanneer een bewerking is voltooid, wordt een van de volgende drie waarden geretourneerd: 

* Geslaagd
* Mislukt
* Geannuleerd

Alle andere waarden geven aan dat de bewerking wordt nog steeds uitgevoerd. De resourceprovider kan een aangepaste waarde die aangeeft van de status retourneren. Bijvoorbeeld: u ontvangt mogelijk **geaccepteerde** wanneer de aanvraag is ontvangen en wordt uitgevoerd.

## <a name="example-requests-and-responses"></a>Voorbeeld van de aanvragen en antwoorden

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Virtuele machine (202 met Azure-AsyncOperation) starten
In dit voorbeeld laat zien hoe bepalen van de status van **start** bewerking voor virtuele machines. De eerste aanvraag is in de volgende indeling:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

Het resultaat-statuscode 202. Onder de kop-waarden ziet u de:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Controleer de status van de asynchrone bewerking een nieuwe aanvraag verzenden naar deze URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

De antwoordtekst bevat de status van de bewerking:

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Resources (201 met Azure-AsyncOperation) implementeren

In dit voorbeeld laat zien hoe bepalen van de status van **implementaties** bewerking voor het implementeren van resources naar Azure. De eerste aanvraag is in de volgende indeling:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

Deze retourneert statuscode 201. De hoofdtekst van het antwoord bevat:

```json
"provisioningState":"Accepted",
```

Onder de kop-waarden ziet u de:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Controleer de status van de asynchrone bewerking een nieuwe aanvraag verzenden naar deze URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

De antwoordtekst bevat de status van de bewerking:

```json
{"status":"Running"}
```

Wanneer de implementatie is voltooid, wordt het antwoord bevat:

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Storage-account (202 met locatie en opnieuw proberen na) maken

In dit voorbeeld laat zien hoe bepalen van de status van de **maken** bewerking voor storage-accounts. De eerste aanvraag is in de volgende indeling:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

De aanvraagtekst bevat eigenschappen voor het opslagaccount en:

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

Het resultaat-statuscode 202. Onder de kop-waarden ziet u de volgende twee waarden:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

Na wachten op aantal, opgegeven in Retry-After, Controleer de status van de asynchrone bewerking seconden door een andere aanvraag te verzenden naar deze URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

Als de aanvraag wordt nog steeds uitgevoerd, ontvangt u een statuscode 202. Als de aanvraag is voltooid, het ontvangen van een 200-statuscode en de hoofdtekst van het antwoord bevat de eigenschappen van het opslagaccount dat is gemaakt.

## <a name="next-steps"></a>Volgende stappen

* Zie voor documentatie over de REST-bewerking, [REST API-documentatie](/rest/api/).
* Zie voor meer informatie over het implementeren van sjablonen met de REST-API voor Resource Manager [resources implementeren met Resource Manager-sjablonen en Resource Manager REST API](resource-group-template-deploy-rest.md).