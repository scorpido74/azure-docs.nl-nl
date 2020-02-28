---
title: Azure-resource logboeken archiveren in het opslag account | Microsoft Docs
description: Meer informatie over het archiveren van uw Azure-resource logboeken voor lange termijn retentie in een opslag account.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 843c179826b2064a1be24d3cee84b398987b4aed
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658968"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Azure-resource logboeken archiveren in een opslag account
[Platform logboeken](platform-logs-overview.md) in azure, inclusief Azure-activiteiten logboek en resource logboeken, bieden gedetailleerde informatie over diagnostische gegevens en controle voor Azure-resources en het Azure-platform waarvan ze afhankelijk zijn.  In dit artikel wordt het verzamelen van platform Logboeken beschreven in een Azure-opslag account om gegevens te bewaren voor archivering.

## <a name="prerequisites"></a>Vereisten
U moet [een Azure Storage-account maken](../../storage/common/storage-account-create.md) als u er nog geen hebt. Het opslag account hoeft zich niet in hetzelfde abonnement te betreden als de resource waarmee logboeken worden verzonden zolang de gebruiker die de instelling configureert de juiste RBAC-toegang heeft tot beide abonnementen.


> [!IMPORTANT]
> Azure Data Lake Storage Gen2-accounts worden momenteel niet ondersteund als doel voor Diagnostische instellingen, zelfs als ze kunnen worden weer gegeven als een geldige optie in de Azure Portal.


Gebruik geen bestaand opslag account met andere, niet-bewakings gegevens die erin zijn opgeslagen, zodat u de toegang tot de gegevens beter kunt beheren. Als u het activiteiten logboek en de resource logboeken Samen archiveert, kunt u ervoor kiezen om hetzelfde opslag account te gebruiken om alle bewakings gegevens op een centrale locatie te bewaren.

## <a name="create-a-diagnostic-setting"></a>Een diagnostische instelling maken
Verzend platform logboeken naar opslag en andere bestemmingen door een diagnostische instelling voor een Azure-resource te maken. Zie [Diagnostische instelling maken voor het verzamelen van Logboeken en metrische gegevens in azure](diagnostic-settings.md) voor meer informatie.


## <a name="collect-data-from-compute-resources"></a>Gegevens verzamelen van Compute-resources
Met Diagnostische instellingen worden bron logboeken verzameld voor Azure-reken resources, zoals elke andere resource, maar niet het gast besturingssysteem of de werk belastingen. Als u deze gegevens wilt verzamelen, moet u de [Windows Azure Diagnostics-agent](diagnostics-extension-overview.md)installeren. 


## <a name="schema-of-platform-logs-in-storage-account"></a>Schema van platform Logboeken in het opslag account

Zodra u de diagnostische instelling hebt gemaakt, wordt er een opslag container gemaakt in het opslag account zodra er een gebeurtenis optreedt in een van de ingeschakelde logboek categorieën. De blobs in de container gebruiken de volgende naam Conventie:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

De BLOB voor een netwerk beveiligings groep kan bijvoorbeeld een naam hebben die er ongeveer als volgt uitziet:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Elke PT1H.json-blob bevat een JSON-blob van gebeurtenissen die hebben plaatsgevonden binnen het uur dat is opgegeven in de blob-URL (bijvoorbeeld, h=12). Tijdens het huidige uur worden gebeurtenissen toegevoegd aan het bestand PT1H.json wanneer deze zich voordoen. De minuut waarde (m = 00) is altijd 00, omdat de bron logboek gebeurtenissen worden opgesplitst in afzonderlijke blobs per uur.

In het bestand PT1H. json wordt elke gebeurtenis opgeslagen met de volgende indeling. Dit maakt gebruik van een algemeen schema op het hoogste niveau, maar dit is uniek voor elke Azure-service, zoals beschreven in het [schema voor bron logboeken](diagnostic-logs-schema.md) en het [schema voor activiteiten logboeken](activity-log-schema.md).

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> Platform-logboeken worden geschreven naar Blob-opslag met behulp van [JSON-lijnen](http://jsonlines.org/), waarbij elke gebeurtenis een regel is en het teken voor nieuwe regel aangeeft dat er een gebeurtenis is. Deze indeling is geïmplementeerd in november 2018. Vóór deze datum zijn logboeken naar Blob Storage geschreven als een JSON-matrix van records, zoals wordt beschreven in [voor bereiding van indeling wijzigen in azure monitor platform logboeken die zijn gearchiveerd in een opslag account](resource-logs-blob-format.md).

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over resource logboeken](platform-logs-overview.md).
* [Maak een diagnostische instelling voor het verzamelen van Logboeken en metrische gegevens in azure](diagnostic-settings.md).
* [Down load blobs voor analyse](../../storage/blobs/storage-quickstart-blobs-dotnet.md).
* [Archiveer Azure Active Directory logboeken met Azure monitor](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md).
