---
title: Blob-opslag gebruiken voor IIS en tabelopslag voor gebeurtenissen in Azure Monitor | Microsoft Documenten
description: Azure Monitor kan de logboeken voor Azure-services lezen die diagnostische gegevens schrijven naar tabelopslag of IIS-logboeken die zijn geschreven naar blobopslag.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 44368ab90abd189c6a8a0792494828c87142eb20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672392"
---
# <a name="collect-data-from-azure-diagnostics-extension-to-azure-monitor-logs"></a>Gegevens verzamelen van Azure-diagnostische extensie naar Azure Monitor-logboeken
Azure-diagnostische extensie is een [agent in Azure Monitor](agents-overview.md) die bewakingsgegevens verzamelt van het gastbesturingssysteem van Azure-rekenbronnen, waaronder virtuele machines. In dit artikel wordt beschreven hoe u gegevens verzamelt die zijn verzameld door de diagnostische extensie van Azure Storage naar Azure Monitor-logboeken.

> [!NOTE]
> De loganalytics-agent in Azure Monitor is doorgaans de voorkeursmethode om gegevens van het gastbesturingssysteem te verzamelen in Azure Monitor-logboeken. Zie [Overzicht van de Azure Monitor-agents](agents-overview.md) voor een gedetailleerde vergelijking van de agents.

## <a name="supported-data-types"></a>Ondersteunde gegevenstypen
Azure-diagnostische extensie slaat gegevens op in een Azure Storage-account. Als u Azure Monitor Logs wilt verzamelen, moet deze zich op de volgende locaties bevinden:

| Logboektype | Resourcetype | Locatie |
| --- | --- | --- |
| IIS-logboeken |Virtuele machines <br> Webrollen <br> Werknemersrollen |wad-iis-logfiles (Blob-opslag) |
| Syslog |Virtuele machines |LinuxsyslogVer2v0 (Tabelopslag) |
| Operationele evenementen voor servicefabric |Servicefabricknooppunten |WADServiceFabricSystemEventTable |
| Service Fabric Betrouwbare Actor Evenementen |Servicefabricknooppunten |WADServiceFabricReliableActorEventTable |
| Service Fabric Betrouwbare servicegebeurtenissen |Servicefabricknooppunten |WADServiceFabricReliableServiceEventTable |
| Logboeken voor Windows-gebeurtenissen |Servicefabricknooppunten <br> Virtuele machines <br> Webrollen <br> Werknemersrollen |WADWindowsEventLogsTable (tabelopslag) |
| Windows ETW-logboeken |Servicefabricknooppunten <br> Virtuele machines <br> Webrollen <br> Werknemersrollen |WADETWEventTable (tabelopslag) |

## <a name="data-types-not-supported"></a>Gegevenstypen die niet worden ondersteund

- Prestatiegegevens van het gastbesturingssysteem
- IIS-logboeken van Azure-websites


## <a name="enable-azure-diagnostics-extension"></a>Azure-diagnostische extensie inschakelen
Zie [De diagnostische extensie (WAD) van Windows Azure installeren en configureren](diagnostics-extension-windows-install.md) of Linux Diagnostische extensie gebruiken om statistieken en [logboeken te controleren](../../virtual-machines/extensions/diagnostics-linux.md) voor meer informatie over het installeren en configureren van de diagnostische extensie. Hiermee u het opslagaccount opgeven en de verzameling configureren van de gegevens die u wilt doorsturen naar Azure Monitor-logboeken.


## <a name="collect-logs-from-azure-storage"></a>Logboeken verzamelen van Azure Storage
Gebruik de volgende procedure om het verzamelen van diagnostische extensiegegevens van een Azure Storage-account mogelijk te maken:

1. Ga in de Azure-portal naar **Log Analytics Workspaces** en selecteer uw werkruimte.
1. Klik **op Logboeken voor opslagaccounts** in de sectie **Werkruimtegegevensbronnen** van het menu.
2. Klik **op Toevoegen**.
3. Selecteer het **opslagaccount** dat de gegevens bevat die moeten worden verzameld.
4. Selecteer het **gegevenstype** dat u wilt verzamelen.
5. De waarde voor Bron wordt automatisch ingevuld op basis van het gegevenstype.
6. Klik op **OK** om de configuratie op te slaan.
7. Herhaal dit voor extra gegevenstypen.

In ongeveer 30 minuten u gegevens zien van het opslagaccount in de werkruimte Log Analytics. U ziet alleen gegevens die naar de opslag worden geschreven nadat de configuratie is toegepast. De werkruimte leest de reeds bestaande gegevens van het opslagaccount niet.

> [!NOTE]
> De portal valideert niet of de bron in het opslagaccount bestaat of dat er nieuwe gegevens worden geschreven.



## <a name="next-steps"></a>Volgende stappen

* [Verzamel logboeken en statistieken voor Azure-services](collect-azure-metrics-logs.md) voor ondersteunde Azure-services.
* [Solutions in staat stellen](../../azure-monitor/insights/solutions.md) om inzicht te geven in de gegevens.
* [Gebruik zoekopdrachten](../../azure-monitor/log-query/log-query-overview.md) om de gegevens te analyseren.
