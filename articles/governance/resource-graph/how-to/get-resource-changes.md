---
title: Resourcewijzigingen ophalen
description: Begrijp hoe u zoeken wanneer een resource is gewijzigd, krijg een lijst met de eigenschappen die zijn gewijzigd en evalueer de diffs.
ms.date: 10/09/2019
ms.topic: how-to
ms.openlocfilehash: 9504ac77fc4a3b03434912cc65284e2001df6e03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873026"
---
# <a name="get-resource-changes"></a>Resourcewijzigingen ophalen

Resources worden gewijzigd in de loop van dagelijks gebruik, herconfiguratie en zelfs herschikking.
Verandering kan afkomstig zijn van een individu of van een geautomatiseerd proces. De meeste verandering is door het ontwerp, maar soms is het niet. Met azure resource graph u met Azure Resource Graph de laatste 14 dagen van de wijzigingsgeschiedenis:

- Zoeken wanneer wijzigingen zijn gedetecteerd op een eigenschap Azure Resource Manager
- Zie Details voor eigenschapswijziging voor elke resourcewijziging
- Bekijk een volledige vergelijking van de resource voor en na de gedetecteerde wijziging

Wijzigingsdetectie en details zijn waardevol voor de volgende voorbeeldscenario's:

- Tijdens incidentmanagement om _mogelijk_ gerelateerde veranderingen te begrijpen. Vraag naar wijzigingsgebeurtenissen tijdens een specifiek tijdsvenster en evalueer de wijzigingsdetails.
- Een configuratiebeheerdatabase, bekend als cmdb, up-to-date houden. In plaats van alle resources en hun volledige eigenschapsets op een geplande frequentie te vernieuwen, krijgt u alleen wat er is gewijzigd.
- Inzicht in welke andere eigenschappen mogelijk zijn gewijzigd toen een resource de nalevingsstatus veranderde. Evaluatie van deze extra eigenschappen kan inzicht bieden in andere eigenschappen die mogelijk moeten worden beheerd via een Azure Policy-definitie.

In dit artikel ziet u hoe u deze informatie verzamelen via de SDK van Resource Graph. Zie de [wijzigingsgeschiedenis van](../../policy/how-to/determine-non-compliance.md#change-history-preview) Azure Policy of Azure Activity Log [Change](../../../azure-monitor/platform/activity-log-view.md#azure-portal)om deze informatie in de Azure-portal te bekijken.
Zie Analyse van [toepassingswijzigingen (voorbeeld) gebruiken](../../../azure-monitor/app/change-analysis.md) in Azure Monitor voor meer informatie over wijzigingen in uw toepassingen vanaf de infrastructuurlaag tot de implementatie van toepassingen.

> [!NOTE]
> Details wijzigen in ResourceGraph zijn voor eigenschappen van ResourceManager. Zie De [bijhouden van wijzigingen](../../../automation/automation-change-tracking.md) in een virtuele machine van Azure Automation of de gastconfiguratie van Azure Policy [voor VM's voor](../../policy/concepts/guest-configuration.md)het bijhouden van wijzigingen in een virtuele machine.

> [!IMPORTANT]
> De geschiedenis van Azure Resource is gewijzigd in Openbare preview.

## <a name="find-detected-change-events-and-view-change-details"></a>Gedetecteerde wijzigingsgebeurtenissen zoeken en wijzigingsdetails weergeven

De eerste stap om te zien wat er op een resource is gewijzigd, is om de wijzigingsgebeurtenissen met betrekking tot die resource binnen een tijdsvenster te vinden. Elke wijzigingsgebeurtenis bevat ook details over wat er op de resource is gewijzigd. Deze stap wordt uitgevoerd via het REST-eindpunt **resourceChanges.**

Het eindpunt **resourceChanges** accepteert de volgende parameters in de aanvraaginstantie:

- **resourceId** \[\]vereist: de Azure-bron om te zoeken naar wijzigingen op.
- **interval** \[\]vereist : Een eigenschap met _begin-_ en _einddatums_ voor wanneer u moet controleren op een wijzigingsgebeurtenis met behulp van de **Zulu-tijdzone (Z).**
- **eigenschappenophalen (optioneel):** een booleaanse eigenschap die instelt als het antwoordobject eigenschapswijzigingen bevat.

Voorbeeldaanvraaginstantie:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-09-28T00:00:00.000Z",
        "end": "2019-09-29T00:00:00.000Z"
    },
    "fetchPropertyChanges": true
}
```

Met de bovenstaande aanvraaginstantie is de REST API URI voor **resourceChanges:**

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

Het antwoord lijkt op dit voorbeeld:

```json
{
    "changes": [
        {
            "changeId": "{\"beforeId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"beforeTime\":\"2019-09-28T00:45:35.012Z\",\"afterId\":\"6178968e-981e-4dac-ac37-340ee73eb577\",\"afterTime\":\"2019-09-28T00:52:53.371Z\"}",
            "beforeSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "afterSnapshot": {
                "snapshotId": "6178968e-981e-4dac-ac37-340ee73eb577",
                "timestamp": "2019-09-28T00:52:53.371Z"
            },
            "changeType": "Create"
        },
        {
            "changeId": "{\"beforeId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"beforeTime\":\"2019-09-28T00:43:38.366Z\",\"afterId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"afterTime\":\"2019-09-28T00:45:35.012Z\"}",
            "beforeSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "afterSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "changeType": "Delete"
        },
        {
            "changeId": "{\"beforeId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"beforeTime\":\"2019-09-28T00:43:15.518Z\",\"afterId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"afterTime\":\"2019-09-28T00:43:38.366Z\"}",
            "beforeSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "afterSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "propertyChanges": [
                {
                    "propertyName": "tags.org",
                    "afterValue": "compute",
                    "changeCategory": "User",
                    "changeType": "Insert"
                },
                {
                    "propertyName": "tags.team",
                    "afterValue": "ARG",
                    "changeCategory": "User",
                    "changeType": "Insert"
                }
            ],
            "changeType": "Update"
        },
        {
            "changeId": "{\"beforeId\":\"19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268\",\"beforeTime\":\"2019-09-28T00:42:46.839Z\",\"afterId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"afterTime\":\"2019-09-28T00:43:15.518Z\"}",
            "beforeSnapshot": {
                "snapshotId": "19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268",
                "timestamp": "2019-09-28T00:42:46.839Z"
            },
            "afterSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "propertyChanges": [{
                "propertyName": "tags.cgtest",
                "afterValue": "hello",
                "changeCategory": "User",
                "changeType": "Insert"
            }],
            "changeType": "Update"
        }
    ]
}
```

Elke gedetecteerde wijzigingsgebeurtenis voor de **resourceId** heeft de volgende eigenschappen:

- **changeId** - Deze waarde is uniek voor die bron. Hoewel de **changeId-tekenreeks** soms andere eigenschappen kan bevatten, is het alleen gegarandeerd uniek.
- **beforeSnapshot** - Bevat de **snapshotId** en **timestamp** van de resourcemomentopname die is gemaakt voordat een wijziging werd gedetecteerd.
- **afterSnapshot** - Bevat de **snapshotId** en **de tijdstempel** van de resourcemomentopname die is gemaakt nadat een wijziging is gedetecteerd.
- **changeType** - Beschrijft het type wijziging dat is gedetecteerd voor de gehele wijzigingsrecord tussen de **beforeSnapshot** en **afterSnapshot**. Waarden zijn: _Maken,_ _Bijwerken_en _Verwijderen_. De **eigenschapChanges** property array is only included when **changeType** is _Update_.
- **eigenschappenWijzigingen** - Deze array met eigenschappen bevat alle resourceeigenschappen die zijn bijgewerkt tussen de **beforeSnapshot** en de **afterSnapshot:**
  - **eigenschapNaam** - De naam van de resourceeigenschap die is gewijzigd.
  - **changeCategory** - Beschrijft wat de wijziging heeft aangebracht. Waarden zijn: _Systeem_ en _Gebruiker_.
  - **changeType** - Beschrijft het type wijziging dat is gedetecteerd voor de eigenschap afzonderlijke resource.
    Waarden zijn: _Invoegen,_ _Bijwerken,_ _Verwijderen_.
  - **beforeValue** - De waarde van de resourceeigenschap in de **beforeSnapshot**. Wordt niet weergegeven wanneer **changeType** is _invoegen._
  - **nawaarde** - De waarde van de eigenschap resource in de **afterSnapshot**. Wordt niet weergegeven wanneer **changeType** wordt _verwijderd._

## <a name="compare-resource-changes"></a>Resourcewijzigingen vergelijken

Met de **changeId** van het **resourceChanges-eindpunt** wordt het **resourceChangeDetails** REST-eindpunt vervolgens gebruikt om de voor- en namomentopnamen van de bron die is gewijzigd, te krijgen.

Het **resourceChangeDetails-eindpunt** vereist twee parameters in de aanvraaginstantie:

- **resourceId:** de Azure-bron om wijzigingen op te vergelijken.
- **changeId:** de unieke wijzigingsgebeurtenis voor de **resourceId** die is verzameld uit **resourceChanges**.

Voorbeeldaanvraaginstantie:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"afterTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

Met de bovenstaande aanvraaginstantie is de REST API URI voor **resourceChangeDetails:**

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

Het antwoord lijkt op dit voorbeeld:

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

**beforeSnapshot** en **afterSnapshot** geven elk de tijd waarop de momentopname is gemaakt en de eigenschappen op dat moment. De verandering gebeurde op een bepaald punt tussen deze snapshots. Als we naar het bovenstaande voorbeeld kijken, kunnen we zien dat de eigenschap die is **gewijzigdhttpsFileOnly is ondersteund.**

Als u de resultaten wilt vergelijken, gebruikt u de eigenschap **Changes** in **resourceChanges** of evalueert u het **inhoudsgedeelte** van elke momentopname in **resourceChangeDetails** om het verschil te bepalen. Als u de momentopnamen vergelijkt, wordt de **tijdstempel** altijd weergegeven als een verschil, ondanks dat deze wordt verwacht.

## <a name="next-steps"></a>Volgende stappen

- Zie de taal die wordt gebruikt in [Starter-query's](../samples/starter.md).
- Zie geavanceerde toepassingen in [Geavanceerde query's](../samples/advanced.md).
- Meer informatie over het [verkennen van bronnen.](../concepts/explore-resources.md)