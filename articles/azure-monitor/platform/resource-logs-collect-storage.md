---
title: Azure-resource logboeken archiveren in het opslag account | Microsoft Docs
description: Meer informatie over het archiveren van uw Azure-resource logboeken voor lange termijn retentie in een opslag account.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 8a1802f0f24ba5ccad3ec1c45f3baa29dfe6909f
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262555"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Azure-resource logboeken archiveren in een opslag account
[Resource logboeken](resource-logs-overview.md) in azure bieden uitgebreide, frequente gegevens over de interne werking van een Azure-resource. In dit artikel wordt beschreven hoe u bron logboeken verzamelt naar een Azure-opslag account om gegevens te bewaren voor archivering.

## <a name="prerequisites"></a>Vereisten
U moet [een Azure Storage-account maken](../../storage/common/storage-quickstart-create-account.md) als u er nog geen hebt. Het opslag account hoeft zich niet in hetzelfde abonnement te betreden als de resource waarmee logboeken worden verzonden zolang de gebruiker die de instelling configureert de juiste RBAC-toegang heeft tot beide abonnementen.

Gebruik geen bestaand opslag account met andere, niet-bewakings gegevens die erin zijn opgeslagen, zodat u de toegang tot bewakings gegevens beter kunt beheren. Als u ook het [activiteiten logboek](activity-logs-overview.md) archiveert naar een opslag account, kunt u ervoor kiezen om hetzelfde opslag account te gebruiken om alle bewakings gegevens op een centrale locatie te bewaren.

## <a name="create-a-diagnostic-setting"></a>Een diagnostische instelling maken
Bron logboeken worden niet standaard verzameld. U kunt ze in een Azure-opslag account en andere bestemmingen verzamelen door een diagnostische instelling voor een Azure-resource te maken. Zie [Diagnostische instelling maken voor het verzamelen van Logboeken en metrische gegevens in azure](diagnostic-settings.md) voor meer informatie.


## <a name="data-retention"></a>Bewaartijd van gegevens
Het Bewaar beleid definieert het aantal dagen dat de gegevens moeten worden bewaard van elke logboek categorie en metrische gegevens die zijn opgeslagen in een opslag account. Een Bewaar beleid kan een wille keurig aantal dagen zijn tussen 0 en 365. Een Bewaar beleid van 0 geeft aan dat de gebeurtenissen voor die logboek categorie voor onbepaalde tijd worden opgeslagen.

Bewaar beleid wordt per dag toegepast, dus aan het einde van de dag (UTC) worden logboeken van de dag die nu voorbij het Bewaar beleid vallen, verwijderd. Bijvoorbeeld, als u een beleid voor het bewaren van één dag had, worden aan het begin van de dag vandaag nog de logboeken van de dag voor gisteren vernietigd. De verwijderbewerking begint bij middernacht UTC, maar houd er rekening mee dat het kan tot 24 uur duren voor de logboeken worden verwijderd uit uw storage-account. 


## <a name="schema-of-resource-logs-in-storage-account"></a>Schema van bron Logboeken in het opslag account

Zodra u de diagnostische instelling hebt gemaakt, wordt er een opslag container gemaakt in het opslag account zodra er een gebeurtenis optreedt in een van de ingeschakelde logboek categorieën. De blobs in de container gebruiken de volgende naam Conventie:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

De BLOB voor een netwerk beveiligings groep kan bijvoorbeeld een naam hebben die er ongeveer als volgt uitziet:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Elke PT1H.json-blob bevat een JSON-blob van gebeurtenissen die hebben plaatsgevonden binnen het uur dat is opgegeven in de blob-URL (bijvoorbeeld, h=12). Tijdens het huidige uur worden gebeurtenissen toegevoegd aan het bestand PT1H.json wanneer deze zich voordoen. De minuut waarde (m = 00) is altijd 00, omdat de bron logboek gebeurtenissen worden opgesplitst in afzonderlijke blobs per uur.

In het bestand PT1H. json wordt elke gebeurtenis opgeslagen met de volgende indeling. Dit maakt gebruik van een algemeen schema op het hoogste niveau, maar is uniek voor elke Azure-Services, zoals beschreven in het [schema voor bron logboeken](resource-logs-overview.md#resource-logs-schema).

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> Platform-logboeken worden geschreven naar Blob-opslag met behulp van [JSON-lijnen](http://jsonlines.org/), waarbij elke gebeurtenis een regel is en het teken voor nieuwe regel aangeeft dat er een gebeurtenis is. Deze indeling is geïmplementeerd in november 2018. Vóór deze datum zijn logboeken naar Blob Storage geschreven als een JSON-matrix van records, zoals wordt beschreven in [voor bereiding van indeling wijzigen in azure monitor platform logboeken die zijn gearchiveerd in een opslag account](resource-logs-blob-format.md).

## <a name="next-steps"></a>Volgende stappen

* [Down load blobs voor analyse](../../storage/blobs/storage-quickstart-blobs-dotnet.md).
* [Archiveer Azure Active Directory logboeken met Azure monitor](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md).
* [Meer informatie over resource logboeken](../../azure-monitor/platform/resource-logs-overview.md).

