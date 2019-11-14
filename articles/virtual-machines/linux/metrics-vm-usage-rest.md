---
title: Azure virtual machine-gebruiks gegevens ophalen met behulp van de REST API
description: Gebruik de Azure REST Api's voor het verzamelen van metrische gegevens over gebruik voor een virtuele machine.
services: virtual-machines
author: rloutlaw
ms.reviewer: routlaw
manager: gwallace
ms.service: load-balancer
ms.custom: REST
ms.topic: article
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: 523b81e53f2b0622b237993dbd88fb9492079c86
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035812"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>Metrische gegevens over het gebruik van virtuele machines ophalen met behulp van de REST API

In dit voor beeld ziet u hoe u het CPU-gebruik voor een [virtuele Linux-machine](https://docs.microsoft.com/azure/virtual-machines/linux/monitor) ophaalt met behulp van de [Azure-rest API](/rest/api/azure/).

Volledige referentie documentatie en aanvullende voor beelden voor de REST API zijn beschikbaar in de [Azure monitor rest-referentie](/rest/api/monitor). 

## <a name="build-the-request"></a>De aanvraag maken

Gebruik de volgende GET-aanvraag voor het verzamelen van het [percentage CPU-metriek](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftcomputevirtualmachines) van een virtuele machine

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Aanvraagheaders

De volgende headers zijn vereist: 

|Aanvraagheader|Beschrijving|  
|--------------------|-----------------|  
|*Content-Type:*|Vereist. Ingesteld op `application/json`.|  
|*Authorization:*|Vereist. Ingesteld op een geldig `Bearer` [toegangstoken](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>URI-parameters

| Naam | Beschrijving |
| :--- | :---------- |
| subscriptionId | De abonnements-ID waarmee een Azure-abonnement wordt geïdentificeerd. Als u meerdere abonnementen hebt, raadpleegt u [werken met meerdere abonnementen](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | De naam van de Azure-resource groep die is gekoppeld aan de resource. U kunt deze waarde ophalen uit de Azure Resource Manager-API, CLI of de portal. |
| vmname | De naam van de virtuele machine van Azure. |
| metricnames | Een door komma's gescheiden lijst met geldige [Load Balancer metrische gegevens](/azure/load-balancer/load-balancer-standard-diagnostics). |
| api-version | De API-versie die voor de aanvraag moet worden gebruikt.<br /><br /> In dit document worden de API-versie `2018-01-01`beschreven, die is opgenomen in de bovenstaande URL.  |
| duur | Teken reeks met de volgende indeling `startDateTime_ISO/endDateTime_ISO` die het tijds bereik van de geretourneerde metrische gegevens definieert. Deze optionele para meter is zo ingesteld dat de gegevens van een dag in het voor beeld worden geretourneerd. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Aanvraagbody

Er is geen aanvraag tekst nodig voor deze bewerking.

## <a name="handle-the-response"></a>Het antwoord verwerken

De status code 200 wordt geretourneerd wanneer de lijst met metrische waarden is geretourneerd. In de [referentie documentatie](/rest/api/monitor/metrics/list#errorresponse)vindt u een volledige lijst met fout codes.

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
