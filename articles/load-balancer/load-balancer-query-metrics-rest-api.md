---
title: Statistieken ophalen met de REST API
titleSuffix: Azure Load Balancer
description: Ga in dit artikel aan de slag met de Azure REST API's om status- en gebruiksstatistieken voor Azure Load Balancer te verzamelen.
services: sql-database
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: article
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 760ec8a945ab88b63dde2de75f5354818facf4f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225244"
---
# <a name="get-load-balancer-usage-metrics-using-the-rest-api"></a>Gebruiksstatistieken van Load Balancer met behulp van de REST API

Verzamel het aantal bytes dat door een [standaardloadbalancer](/azure/load-balancer/load-balancer-standard-overview) gedurende een interval van tijd is verwerkt met behulp van de [Azure REST API.](/rest/api/azure/)

Volledige referentiedocumentatie en aanvullende voorbeelden voor de REST-API zijn beschikbaar in de [Azure Monitor REST-referentie.](/rest/api/monitor) 

## <a name="build-the-request"></a>De aanvraag maken

Gebruik de volgende GET-aanvraag om de [Statistiek ByteCount](/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics) te verzamelen bij een standaardloadbalansr. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
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
| resourceGroupName | De naam van de resourcegroep die de resource bevat. U deze waarde verkrijgen via de Azure Resource Manager API, CLI of de portal. |
| loadBalancerName | De naam van de Azure Load Balancer. |
| metrische namen | Door komma's gescheiden lijst met geldige [load balancer-statistieken](/azure/load-balancer/load-balancer-standard-diagnostics). |
| api-versie | De API-versie die u voor de aanvraag moet gebruiken.<br /><br /> Dit document heeft `2018-01-01`betrekking op api-versie , opgenomen in de bovenstaande URL.  |
| Tijdspanne | De tijdspanne van de query. Het is een tekenreeks met `startDateTime_ISO/endDateTime_ISO`de volgende indeling. Deze optionele parameter is ingesteld om een dag aan gegevens in het voorbeeld terug te geven. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Aanvraagbody

Er is geen aanvraaginstantie nodig voor deze operatie.

## <a name="handle-the-response"></a>Het antwoord verwerken

Statuscode 200 wordt geretourneerd wanneer de lijst met metrische waarden wordt geretourneerd. Een volledige lijst met foutcodes is beschikbaar in de [referentiedocumentatie](/rest/api/monitor/metrics/list#errorresponse).

## <a name="example-response"></a>Voorbeeld van een antwoord 

```json
{
    "cost": 0,
    "timespan": "2018-06-05T03:00:00Z/2018-06-07T03:00:00Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/Microsoft.Insights/metrics/ByteCount",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "ByteCount",
                "localizedValue": "Byte Count"
            },
            "unit": "Count",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-06T17:24:00Z",
                            "total": 1067921034.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:25:00Z",
                            "total": 0.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:26:00Z",
                            "total": 3781344.0
                        },
                    ]
                }
            ]
        }
    ],
    "namespace": "Microsoft.Network/loadBalancers",
    "resourceregion": "eastus"
}
```
