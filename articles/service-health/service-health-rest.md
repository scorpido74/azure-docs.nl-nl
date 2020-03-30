---
title: Statusgebeurtenissen voor Azure-bronnen bijwerken met de REST-API | Microsoft Documenten
description: Gebruik de Azure REST API's om de statusgebeurtenissen voor uw Azure-bronnen op te halen.
ms.topic: conceptual
ms.date: 06/06/2017
ms.openlocfilehash: 6964a6c4e85c38d532b12e730a02c4df73be76e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77653998"
---
# <a name="get-resource-health-using-the-rest-api"></a>Resourcestatus ophalen met de REST-API 

In dit voorbeeldartikel ziet u hoe u een lijst met statusgebeurtenissen voor de Azure-resources in uw abonnement ophaalt met behulp van de [Azure REST API.](/rest/api/azure/)

Volledige referentiedocumentatie en aanvullende voorbeelden voor de REST-API zijn beschikbaar in de [Azure Monitor REST-referentie.](/rest/api/monitor) 

## <a name="build-the-request"></a>De aanvraag maken

Gebruik de `GET` volgende HTTP-aanvraag om de statusgebeurtenissen voor `2018-05-16` uw `2018-06-20`abonnement weer te geven voor het tijdsbereik tussen en .

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&%24filter=eventTimestamp%20ge%20'2018-05-16T04%3A36%3A37.6407898Z'%20and%20eventTimestamp%20le%20'2018-06-20T04%3A36%3A37.6407898Z'
```

### <a name="request-headers"></a>Aanvraagheaders

De volgende headers zijn vereist: 

|Aanvraagheader|Beschrijving|  
|--------------------|-----------------|  
|*Inhoudstype:*|Vereist. Ingesteld op `application/json`.|  
|*Authorization:*|Vereist. Ingesteld op een geldig `Bearer` [toegangstoken](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>URI-parameters

| Name | Beschrijving |
| :--- | :---------- |
| subscriptionId | De abonnements-ID die een Azure-abonnement identificeert. Zie Werken met meerdere abonnementen als u meerdere abonnementen [hebt.](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest) |
| api-versie | De API-versie die u voor de aanvraag moet gebruiken.<br /><br /> Dit document heeft `2015-04-01`betrekking op api-versie , opgenomen in de bovenstaande URL.  |
| $filter | De filteroptie om de set geretourneerde resultaten te verminderen. De toegestane patronen voor deze parameter zijn beschikbaar [in de verwijzing naar de bewerking Activiteitslogboeken](/rest/api/monitor/activitylogs/list#uri-parameters). In het getoonde voorbeeld worden alle gebeurtenissen in een tijdsbereik tussen 2018-05-16 en 2018-06-20 |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Aanvraagbody

Er is geen aanvraaginstantie nodig voor deze operatie.

## <a name="handle-the-response"></a>Het antwoord verwerken

Statuscode 200 wordt geretourneerd met een lijst met waarden voor `nextlink` statusgebeurtenissen die overeenkomen met de filterparameter, samen met een URI om de volgende pagina met resultaten op te halen.

## <a name="example-response"></a>Voorbeeld van een antwoord 

```json
{
  "value": [
    {
      "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
      "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
      },
      "id": "/subscriptions/{subscription-id}/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
      "resourceGroupName": "MSSupportGroup",
      "resourceProviderName": {
        "value": "microsoft.support",
        "localizedValue": "microsoft.support"
      },
      "operationName": {
        "value": "microsoft.support/supporttickets/write",
        "localizedValue": "microsoft.support/supporttickets/write"
      },
      "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
      },
      "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
      "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
      "level": "Informational"
    }
  ],
  "nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```
