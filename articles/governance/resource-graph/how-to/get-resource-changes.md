---
title: Resourcewijzigingen ophalen
description: Meer informatie over hoe u kunt zoeken wanneer een resource is gewijzigd en een lijst krijgt met de eigenschappen die zijn gewijzigd.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 2027f56d44be14895a40550d78a79d9e9dda9d97
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980287"
---
# <a name="get-resource-changes"></a>Resourcewijzigingen ophalen

Resources worden gewijzigd in de loop van dagelijks gebruik, herconfiguratie en zelfs opnieuw implementeren.
Wijzigingen kunnen afkomstig zijn van een individu of een geautomatiseerd proces. De meeste wijziging is standaard, maar soms niet. Met de laatste 14 dagen van de wijzigings geschiedenis kunt u met Azure resource Graph het volgende doen:

- Zien wanneer wijzigingen in een Azure Resource Manager-eigenschap zijn gedetecteerd.
- Zien welke eigenschappen tijdens die gebeurtenis zijn gewijzigd.

Wijzigings detectie en Details zijn waardevol voor de volgende voorbeeld scenario's:

- Tijdens incident beheer om inzicht te krijgen in _mogelijk_ gerelateerde wijzigingen. Query's voor wijzigings gebeurtenissen tijdens een bepaald tijd venster en de wijzigings Details evalueren.
- Een configuratie beheer database, ook wel een CMDB, up-to-date houden. In plaats van alle resources en de volledige eigenschappen sets op een geplande frequentie te vernieuwen, krijgt u alleen de gewijzigde gegevens te zien.
- Meer informatie over welke andere eigenschappen mogelijk zijn gewijzigd wanneer de nalevings status van een resource is gewijzigd. Evaluatie van deze aanvullende eigenschappen kan inzicht bieden in andere eigenschappen die mogelijk moeten worden beheerd via een Azure Policy definitie.

In dit artikel wordt uitgelegd hoe u deze informatie kunt verzamelen via de SDK van resource Graph. Zie de wijzigings [geschiedenis](../../policy/how-to/determine-non-compliance.md#change-history-preview) van Azure Policy of de [wijzigings geschiedenis](../../../azure-monitor/platform/activity-log-view.md#azure-portal)van het Azure-activiteiten logboek om deze informatie te bekijken in de Azure Portal.

> [!NOTE]
> Details wijzigen in resource grafiek zijn voor eigenschappen van Resource Manager. Zie voor het bijhouden van wijzigingen in een virtuele machine de Azure Automation [Wijzigingen bijhouden](../../../automation/automation-change-tracking.md) of de [gast configuratie](../../policy/concepts/guest-configuration.md)van de Azure Policy voor vm's.

> [!IMPORTANT]
> De wijzigings geschiedenis in azure resource Graph bevindt zich in de open bare preview.

## <a name="find-when-changes-were-detected"></a>Zoeken wanneer er wijzigingen zijn gedetecteerd

De eerste stap in het weer geven van wat er is gewijzigd in een resource is het vinden van de wijzigings gebeurtenissen die betrekking hebben op die resource binnen een tijd venster. Deze stap wordt uitgevoerd via het **resourceChanges** rest-eind punt.

Het **resourceChanges** -eind punt vereist twee para meters in de aanvraag tekst:

- **resourceId**: De Azure-resource waarin wordt gezocht naar wijzigingen.
- **interval**: Een eigenschap met _begin_ -en _eind_ datums voor wanneer moet worden gecontroleerd op een wijzigings gebeurtenis met behulp van de **Zulu-tijd zone (Z)** .

Voorbeeld van de aanvraag hoofdtekst:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-03-28T00:00:00.000Z",
        "end": "2019-03-31T00:00:00.000Z"
    }
}
```

Met de bovenstaande aanvraag tekst is de REST API URI voor **resourceChanges** :

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

Het antwoord ziet er ongeveer uit als in dit voor beeld:

```json
{
    "changes": [{
            "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
            "beforeSnapshot": {
                "timestamp": "2019-03-29T01:32:05.993Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-29T01:54:24.42Z"
            }
        },
        {
            "changeId": "9dc352cb-b7c1-4198-9eda-e5e3ed66aec8",
            "beforeSnapshot": {
                "timestamp": "2019-03-28T10:30:19.68Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-28T21:12:31.337Z"
            }
        }
    ]
}
```

Elke gedetecteerde wijzigings gebeurtenis voor de **resourceId** heeft een **changeId** die uniek is voor die bron. Hoewel de **changeId** -teken reeks soms andere eigenschappen kan bevatten, is het alleen gegarandeerd uniek. De wijzigings record bevat de tijden waarop de moment opnamen vóór en na zijn gemaakt.
De wijzigings gebeurtenis is op een bepaald moment in dit venster van tijd opgetreden.

## <a name="see-what-properties-changed"></a>Bekijk welke eigenschappen zijn gewijzigd

Met de **changeId** van het **resourceChanges** -eind punt wordt vervolgens het **resourceChangeDetails** rest-eind punt gebruikt om details van de wijzigings gebeurtenis op te halen.

Het **resourceChangeDetails** -eind punt vereist twee para meters in de aanvraag tekst:

- **resourceId**: De Azure-resource waarin wordt gezocht naar wijzigingen.
- **changeId**: De unieke wijzigings gebeurtenis voor de **resourceId** die is verzameld van **resourceChanges**.

Voorbeeld van de aanvraag hoofdtekst:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

Met de bovenstaande aanvraag tekst is de REST API URI voor **resourceChangeDetails** :

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

Het antwoord ziet er ongeveer uit als in dit voor beeld:

```json
{
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": false,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

**beforeSnapshot** en **afterSnapshot** geven elk het tijdstip waarop de moment opname is gemaakt en de eigenschappen op dat moment. De wijziging is op een bepaald moment tussen deze moment opnamen. In het bovenstaande voor beeld zien we dat de gewijzigde eigenschap is **supportsHttpsTrafficOnly**.

Als u de resultaten wilt vergelijken met een programma, vergelijkt u het **inhouds** gedeelte van elke moment opname om het verschil te bepalen. Als u de volledige moment opname vergelijkt, wordt de **tijds tempel** altijd als een verschil weer gegeven ondanks de verwachte tijd.

## <a name="next-steps"></a>Volgende stappen

- Zie de taal die wordt gebruikt in [Start query's](../samples/starter.md).
- Zie Geavanceerd gebruik in [Geavanceerde query's](../samples/advanced.md).
- Meer informatie over [bronnen verkennen](../concepts/explore-resources.md).