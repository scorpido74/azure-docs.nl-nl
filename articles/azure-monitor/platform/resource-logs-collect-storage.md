---
title: Azure-bronlogboeken archiveren naar opslagaccount | Microsoft Documenten
description: Meer informatie over het archiveren van uw Azure-bronlogboeken voor langdurige bewaring in een opslagaccount.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 787640ef08ee91220f42065af155c372247f0136
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804602"
---
# <a name="archive-azure-resource-logs-to-storage-account"></a>Azure-bronlogboeken archiveren naar opslagaccount
[Platformlogboeken](platform-logs-overview.md) in Azure, inclusief Azure Activity log en resource logs, bieden gedetailleerde diagnostische en controle-informatie voor Azure-resources en het Azure-platform waarvan ze afhankelijk zijn.  In dit artikel worden platformlogboeken verzameld voor een Azure-opslagaccount om gegevens te bewaren voor archivering.

## <a name="prerequisites"></a>Vereisten
U moet [een Azure-opslagaccount maken](../../storage/common/storage-account-create.md) als u er nog geen hebt. Het opslagaccount hoeft niet in hetzelfde abonnement te zitten als de logboeken voor het verzenden van resources, zolang de gebruiker die de instelling configureert, de juiste RBAC-toegang tot beide abonnementen heeft.

> [!IMPORTANT]
> Als u de gegevens naar onveranderlijke opslag wilt verzenden, stelt u het onveranderlijke beleid in voor het opslagaccount zoals beschreven in [Set en beheert u het onveranderlijkheidsbeleid voor Blob-opslag](../../storage/blobs/storage-blob-immutability-policies-manage.md). U moet alle stappen in dit artikel volgen, inclusief het inschakelen van beveiligde toevoegende blobs.

> [!IMPORTANT]
> Azure Data Lake Storage Gen2-accounts worden momenteel niet ondersteund als bestemming voor diagnostische instellingen, ook al worden ze mogelijk vermeld als een geldige optie in de Azure-portal.


U mag geen bestaand opslagaccount gebruiken met andere, niet-bewakingsgegevens die erin zijn opgeslagen, zodat u de toegang tot de gegevens beter beheren. Als u het logboek en de bronlogboeken echter samenarchiveert, u ervoor kiezen om hetzelfde opslagaccount te gebruiken om alle bewakingsgegevens op een centrale locatie te bewaren.

## <a name="create-a-diagnostic-setting"></a>Een diagnostische instelling maken
Stuur platformlogboeken naar opslag en andere bestemmingen door een diagnostische instelling voor een Azure-bron te maken. Zie [Diagnostische instelling maken om logboeken en statistieken in Azure](diagnostic-settings.md) te verzamelen voor meer informatie.


## <a name="collect-data-from-compute-resources"></a>Gegevens verzamelen uit rekenbronnen
Diagnostische instellingen verzamelen resourcelogs voor Azure-compute resources, net als elke andere resource, maar niet voor hun gastbesturingssysteem of workloads. Als u deze gegevens wilt verzamelen, installeert u de [Windows Azure Diagnostics-agent](diagnostics-extension-overview.md). 


## <a name="schema-of-platform-logs-in-storage-account"></a>Schema van platformlogboeken in opslagaccount

Zodra u de diagnostische instelling hebt gemaakt, wordt er een opslagcontainer gemaakt in het opslagaccount zodra een gebeurtenis plaatsvindt in een van de ingeschakelde logboekcategorieën. De blobs in de container gebruiken de volgende naamgevingsconventie:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

De blob voor een netwerkbeveiligingsgroep kan bijvoorbeeld een naam hebben die vergelijkbaar is met de volgende:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Elke PT1H.json-blob bevat een JSON-blob van gebeurtenissen die hebben plaatsgevonden binnen het uur dat is opgegeven in de blob-URL (bijvoorbeeld, h=12). Tijdens het huidige uur worden gebeurtenissen toegevoegd aan het bestand PT1H.json wanneer deze zich voordoen. De minuutwaarde (m=00) is altijd 00, omdat gebeurtenisgebeurtenissen voor resourcelogboeken worden opgesplitst in afzonderlijke blobs per uur.

In het PT1H.json-bestand wordt elke gebeurtenis opgeslagen met de volgende indeling. Dit maakt gebruik van een algemeen schema op het hoogste niveau, maar is uniek voor elke Azure-services zoals beschreven in [het schema voor bronlogboeken](diagnostic-logs-schema.md) en [het schema van de activiteitenlogboeken](activity-log-schema.md).

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> Platformlogboeken worden naar blobopslag geschreven met [JSON-regels,](http://jsonlines.org/)waarbij elke gebeurtenis een regel is en het teken newline een nieuwe gebeurtenis aangeeft. Dit formaat werd geïmplementeerd in november 2018. Voorafgaand aan deze datum zijn logboeken naar blobopslag geschreven als een json-array met records zoals beschreven in [Voorbereiden op indelingswijziging in Azure Monitor-platformlogboeken die zijn gearchiveerd naar een opslagaccount.](resource-logs-blob-format.md)

## <a name="next-steps"></a>Volgende stappen

* [Lees meer over resourcelogs](platform-logs-overview.md).
* [Diagnostische instelling maken om logboeken en statistieken in Azure te verzamelen.](diagnostic-settings.md)
* [Blobs downloaden voor analyse](../../storage/blobs/storage-quickstart-blobs-dotnet.md).
* [Azure Active Directory-logboeken archiveren met Azure Monitor](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md).
