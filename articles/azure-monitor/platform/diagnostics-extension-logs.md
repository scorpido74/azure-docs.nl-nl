---
title: Gebruik Blob Storage voor IIS en tabel opslag voor gebeurtenissen in Azure Monitor | Microsoft Docs
description: Azure Monitor kunt de logboeken lezen voor Azure-Services die diagnostische gegevens schrijven naar Table-opslag of IIS-logboeken die zijn geschreven naar Blob Storage.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 7edf778dc2429cc1054d1550a513d7461f8e7928
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472555"
---
# <a name="collect-data-from-azure-diagnostics-extension-to-azure-monitor-logs"></a>Gegevens verzamelen van de Azure Diagnostics-extensie naar Azure Monitor-logboeken
Azure Diagnostics-extensie is een [agent in azure monitor](agents-overview.md) die bewakings gegevens verzamelt van het gast besturingssysteem van Azure Compute-resources, waaronder virtuele machines. In dit artikel wordt beschreven hoe u gegevens verzamelt die worden verzameld door de uitbrei ding van de diagnose van Azure Storage naar Azure Monitor-Logboeken.

> [!NOTE]
> De Log Analytics-agent in Azure Monitor is doorgaans de voorkeurs methode voor het verzamelen van gegevens van het gast besturingssysteem in Azure Monitor Logboeken. Zie [overzicht van de Azure monitor agents](agents-overview.md) voor een gedetailleerde vergelijking van de agents.

## <a name="supported-data-types"></a>Ondersteunde gegevens typen
Met de Azure Diagnostics-extensie worden gegevens opgeslagen in een Azure Storage-account. Voor Azure Monitor logboeken voor het verzamelen van deze gegevens moet het zich op de volgende locaties bevinden:

| Logboektype | Resourcetype | Locatie |
| --- | --- | --- |
| IIS-logboeken |Virtuele machines <br> Webrollen <br> Werkrollen |wad-iis-logboekbestanden (Blob Storage) |
| Syslog |Virtuele machines |LinuxsyslogVer2v0 (Table Storage) |
| Operationele gebeurtenissen van de service Fabric |Service Fabric-knooppunten |WADServiceFabricSystemEventTable |
| Service Fabric Reliable Actor-gebeurtenissen |Service Fabric-knooppunten |WADServiceFabricReliableActorEventTable |
| Service Fabric Reliable Services-gebeurtenissen |Service Fabric-knooppunten |WADServiceFabricReliableServiceEventTable |
| Windows-gebeurtenislogboeken |Service Fabric-knooppunten <br> Virtuele machines <br> Webrollen <br> Werkrollen |WADWindowsEventLogsTable (Table Storage) |
| Windows ETW-Logboeken |Service Fabric-knooppunten <br> Virtuele machines <br> Webrollen <br> Werkrollen |WADETWEventTable (Table Storage) |

## <a name="data-types-not-supported"></a>Gegevens typen worden niet ondersteund

- Prestatie gegevens van het gast besturingssysteem
- IIS-logboeken van Azure websites


## <a name="enable-azure-diagnostics-extension"></a>De Azure Diagnostics-extensie inschakelen
Zie de [Windows Azure Diagnostics-extensie (WAD) installeren en configureren,](diagnostics-extension-windows-install.md) of [Gebruik de diagnostische Linux-extensie voor het bewaken van metrische gegevens en logboeken](../../virtual-machines/extensions/diagnostics-linux.md) voor meer informatie over het installeren en configureren van de diagnostische uitbrei ding. Hiermee slaapstandknop u het opslag account op te geven en een verzameling van de gegevens te configureren die u wilt door sturen naar Azure Monitor Logboeken.


## <a name="collect-logs-from-azure-storage"></a>Logboeken van Azure Storage verzamelen
Gebruik de volgende procedure om het verzamelen van gegevens van diagnostische uitbrei dingen van een Azure Storage-account in te scha kelen:

1. Ga in het Azure Portal naar **log Analytics-werk ruimten** en selecteer uw werk ruimte.
1. Klik op **Logboeken voor opslag accounts** in de sectie **werkruimte gegevens bronnen** van het menu.
2. Klik op **toevoegen**.
3. Selecteer het **opslag account** dat de gegevens bevat die u wilt verzamelen.
4. Selecteer het **gegevens type** dat u wilt verzamelen.
5. De waarde voor de bron wordt automatisch ingevuld op basis van het gegevens type.
6. Klik op **OK** om de configuratie op te slaan.
7. Herhaal dit voor aanvullende gegevens typen.

In ongeveer 30 minuten kunt u de gegevens van het opslag account in de werk ruimte Log Analytics bekijken. U ziet alleen de gegevens die worden geschreven naar opslag nadat de configuratie is toegepast. De bestaande gegevens van het opslag account worden niet door de werk ruimte gelezen.

> [!NOTE]
> De portal valideert niet dat de bron bestaat in het opslag account of dat er nieuwe gegevens worden geschreven.



## <a name="next-steps"></a>Volgende stappen

* [Verzamel logboeken en metrische gegevens voor Azure-Services](collect-azure-metrics-logs.md) voor ondersteunde Azure-Services.
* [Schakel oplossingen](../../azure-monitor/insights/solutions.md) in om inzicht te krijgen in de gegevens.
* [Gebruik Zoek query's](../../azure-monitor/log-query/log-query-overview.md) om de gegevens te analyseren.
