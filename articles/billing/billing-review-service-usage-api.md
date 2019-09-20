---
title: Resourcegebruik van Azure-service controleren met REST-API | Microsoft Docs
description: Lees hier hoe u met behulp van REST-API's van Azure het resourcegebruik van Azure-services kunt controleren.
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: banders
ms.openlocfilehash: 47e19fae26d6e3bc465799980c587d7bb7ed5e92
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "68443069"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Gebruik van Azure-resources controleren met behulp van de REST-API

Met behulp van de API's van Azure Cost Management kunt u het verbruik van uw Azure-resources controleren en beheren.

In dit artikel leert u hoe u een dagelijks rapport maakt waarmee een document met door komma's gescheiden waarden wordt gegenereerd met gebruiksgegevens per uur. Er wordt ook uitgelegd hoe u met filters het rapport kunt aanpassen zodat u het gebruik kunt opvragen van virtuele machines, databases en getagde resources in een Azure-resourcegroep.

>[!NOTE]
> De API van Cost Management bevindt zich momenteel in beperkte preview.

## <a name="create-a-basic-cost-management-report"></a>Een eenvoudig rapport voor kostenbeheer maken

Gebruik de bewerking `reports` in de Cost Management-API om te definiëren hoe de kostenrapportage wordt gegenereerd en waar de rapporten worden gepubliceerd.

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

De parameter `{subscriptionGuid}` is vereist en moet een abonnements-id bevatten die kan worden gelezen met behulp van de referenties die zijn opgegeven in het API-token. De `{reportName}`

De volgende headers zijn vereist: 

|Aanvraagheader|Beschrijving|  
|--------------------|-----------------|  
|*Content-Type:*| Vereist. Ingesteld op `application/json`. |  
|*Authorization:*| Vereist. Ingesteld op een geldig `Bearer`-token. |

Configureer de parameters van het rapport in de body van de HTTP-aanvraag. Het rapport in het onderstaande voorbeeld wordt elke dag gegenereerd als het op actief is gezet. Er wordt dan een CSV-bestand weggeschreven naar een blobcontainer van Azure Storage. Dit bestand bevat informatie over uurkosten voor alle resources in de resourcegroep `westus`.

```json
{
    "properties": {
        "schedule": {
            "status": "Inactive",
            "recurrence": "Daily",
            "recurrencePeriod": {
                "from": "2018-08-21",
                "to": "2019-10-31"
            }
        },
        "deliveryInfo": {
            "destination": {
                "resourceId": "/subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
                "container": "MyReportContainer",
                "rootFolderPath": "MyScheduleTest"
            }
        },
        "format": "Csv",
        "definition": {
            "type": "Usage",
            "timeframe": "MonthToDate",
            "dataSet": {
                "granularity": "Hourly",
                "filter": {
                    "dimensions": {
                        "name": "ResourceLocation",
                        "operator": "In",
                        "values": [
                            "westus"
                        ]
                    }
                }
            }
        }
    }
}
```

Kan

## <a name="filtering-reports"></a>Rapporten filteren

Gebruik de secties `filter` en `dimensions` van de body van de aanvraag bij het maken van een rapport om u te richten op de kosten voor specifieke resourcetypen. In de vorige body van de aanvraag ziet u hoe u kunt filteren op alle resources in een regio. 

### <a name="get-all-compute-usage"></a>Gebruik van rekenresources ophalen

Gebruik de dimensie `ResourceType` voor het rapporteren van de kosten van virtuele Azure-machines in uw abonnement voor alle regio's.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.ClassicCompute/virtualMachines", 
                "Microsoft.Compute/virtualMachines"
        ] 
    }
}
```

### <a name="get-all-database-usage"></a>Gebruik van databases ophalen

Gebruik de dimensie `ResourceType` voor het rapporteren van de kosten van Azure SQL Database in uw abonnement voor alle regio's.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.Sql/servers"
        ] 
    }
}
```

### <a name="report-on-specific-instances"></a>Rapporteren van specifieke instanties

Met de dimensie `Resource` kunt u kosten voor specifieke resources rapporteren.

```json
"filter": {
    "dimensions": {
        "name": "Resource",
        "operator": "In",
        "values": [
            "subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroup}/providers/Microsoft.ClassicCompute/virtualMachines/{ResourceName}"
        ]
    }
}
```

### <a name="changing-timeframes"></a>Tijdsperioden wijzigen

Stel `timeframe` in op `Custom` om een tijdsperiode in te stellen buiten de ingebouwde opties voor week tot heden en maand tot heden.

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>Volgende stappen
- [Aan de slag gaan met Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
