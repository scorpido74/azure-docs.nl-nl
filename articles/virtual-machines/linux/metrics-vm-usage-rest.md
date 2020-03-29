---
title: Azure Virtual Machine-gebruiksgegevens opvragen met de REST API
description: Gebruik de Azure REST API's om gebruiksstatistieken voor een virtuele machine te verzamelen.
author: rloutlaw
ms.service: virtual-machines
ms.subservice: monitoring
ms.custom: REST
ms.topic: article
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: 07e91f3d9fd32f01db91415bfd90746cd1aef403
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944740"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>Gebruiksstatistieken voor virtuele machines met behulp van de REST API

In dit voorbeeld ziet u hoe u het CPU-gebruik voor een [Virtuele Linux-machine](https://docs.microsoft.com/azure/virtual-machines/linux/monitor) ophaalt met behulp van de [Azure REST API.](/rest/api/azure/)

Volledige referentiedocumentatie en aanvullende voorbeelden voor de REST-API zijn beschikbaar in de [Azure Monitor REST-referentie.](/rest/api/monitor) 

## <a name="build-the-request"></a>De aanvraag maken

Gebruik het volgende GET-verzoek om de [CPU-statistiek percentage](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftcomputevirtualmachines) te verzamelen van een virtuele machine

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
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
| resourceGroupName | De naam van de Azure-brongroep die is gekoppeld aan de bron. U deze waarde ophalen via de Azure Resource Manager API, CLI of de portal. |
| vmname | De naam van de Azure Virtual Machine. |
| metrische namen | Door komma's gescheiden lijst met geldige [load balancer-statistieken](/azure/load-balancer/load-balancer-standard-diagnostics). |
| api-versie | De API-versie die u voor de aanvraag moet gebruiken.<br /><br /> Dit document heeft `2018-01-01`betrekking op api-versie , opgenomen in de bovenstaande URL.  |
| Tijdspanne | Tekenreeks met de `startDateTime_ISO/endDateTime_ISO` volgende indeling die het tijdsbereik van de geretourneerde statistieken definieert. Deze optionele parameter is ingesteld om een dag aan gegevens in het voorbeeld terug te geven. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Aanvraagbody

Er is geen aanvraaginstantie nodig voor deze operatie.

## <a name="handle-the-response"></a>Het antwoord verwerken

Statuscode 200 wordt geretourneerd wanneer de lijst met metrische waarden wordt geretourneerd. Een volledige lijst met foutcodes is beschikbaar in de [referentiedocumentatie](/rest/api/monitor/metrics/list#errorresponse).

## <a name="example-response"></a>Voorbeeld van een antwoord 

```json
{
    "cost": 0,
    "timespan": "2018-06-08T23:48:10Z/2018-06-09T00:48:10Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "Percentage CPU",
                "localizedValue": "Percentage CPU"
            },
            "unit": "Percent",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-08T23:48:00Z",
                            "average": 0.44
                        },
                        {
                            "timeStamp": "2018-06-08T23:49:00Z",
                            "average": 0.31
                        },
                        {
                            "timeStamp": "2018-06-08T23:50:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:51:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:52:00Z",
                            "average": 0.285
                        } ]
                } ]
        } ]
}
```
