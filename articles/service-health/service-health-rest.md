---
title: Azure resource Health-gebeurtenissen ophalen met behulp van de REST API | Microsoft Docs
description: Gebruik de Azure REST Api's om de status gebeurtenissen voor uw Azure-resources op te halen.
ms.topic: conceptual
ms.date: 06/06/2017
ms.openlocfilehash: 6964a6c4e85c38d532b12e730a02c4df73be76e5
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77653998"
---
# <a name="get-resource-health-using-the-rest-api"></a>Resource Health ophalen met behulp van de REST API 

In dit artikel wordt uitgelegd hoe u een lijst met status gebeurtenissen voor de Azure-resources in uw abonnement kunt ophalen met behulp van de [Azure-rest API](/rest/api/azure/).

Volledige referentie documentatie en aanvullende voor beelden voor de REST API zijn beschikbaar in de [Azure monitor rest-referentie](/rest/api/monitor). 

## <a name="build-the-request"></a>De aanvraag maken

Gebruik de volgende `GET` HTTP-aanvraag om de status gebeurtenissen voor uw abonnement op te geven voor de periode tussen `2018-05-16` en `2018-06-20`.

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&%24filter=eventTimestamp%20ge%20'2018-05-16T04%3A36%3A37.6407898Z'%20and%20eventTimestamp%20le%20'2018-06-20T04%3A36%3A37.6407898Z'
```

### <a name="request-headers"></a>Aanvraagheaders

De volgende headers zijn vereist: 

|Aanvraagheader|Beschrijving|  
|--------------------|-----------------|  
|*Content-Type:*|Vereist. Ingesteld op `application/json`.|  
|*Authorization:*|Vereist. Ingesteld op een geldig `Bearer` [toegangstoken](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>URI-parameters

| Name | Beschrijving |
| :--- | :---------- |
| subscriptionId | De abonnements-ID waarmee een Azure-abonnement wordt geïdentificeerd. Als u meerdere abonnementen hebt, raadpleegt u [werken met meerdere abonnementen](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| api-version | De API-versie die voor de aanvraag moet worden gebruikt.<br /><br /> In dit document worden de API-versie `2015-04-01`beschreven, die is opgenomen in de bovenstaande URL.  |
| $filter | De filter optie om de set geretourneerde resultaten te reduceren. De toegestane patronen voor deze para meter zijn beschikbaar [in de verwijzing voor de bewerking activiteiten logboeken](/rest/api/monitor/activitylogs/list#uri-parameters). In het voor beeld wordt weer gegeven, worden alle gebeurtenissen vastgelegd in een tijds bereik tussen 2018-05-16 en 2018-06-20 |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Aanvraagbody

Er is geen aanvraag tekst nodig voor deze bewerking.

## <a name="handle-the-response"></a>Het antwoord verwerken

De status code 200 wordt geretourneerd met een lijst met status waarden die overeenkomt met de filter parameter, samen met een `nextlink`-URI om de volgende pagina met resultaten op te halen.

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
