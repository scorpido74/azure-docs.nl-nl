---
title: Overzicht van Azure-resource logboeken | Microsoft Docs
description: Meer informatie over de ondersteunde services en het gebeurtenis schema voor Azure-resource Logboeken.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 10/22/2019
ms.openlocfilehash: b953f9b5e5fd8c853746caad3047986786bd1317
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989612"
---
# <a name="azure-resource-logs-overview"></a>Overzicht van Azure-resource logboeken
Azure-resource logboeken zijn [platform logboeken](platform-logs-overview.md) die worden verzonden door Azure-resources die hun interne bewerking beschrijven. Alle bron logboeken delen een gemeen schappelijk schema op het hoogste niveau met de flexibiliteit voor elke service om unieke eigenschappen voor hun eigen gebeurtenissen te verzenden.

> [!NOTE]
> Bron logboeken zijn voorheen bekend als Diagnostische logboeken.

## <a name="collecting-resource-logs"></a>Bron logboeken verzamelen
Resource logboeken worden automatisch gegenereerd door ondersteunde Azure-resources, maar ze worden niet verzameld tenzij u ze configureert met een [Diagnostische instelling](diagnostic-settings.md). Maak een diagnostische instelling voor elke Azure-resource om de logboeken door te sturen naar de volgende bestemmingen:

| Bestemming | Scenario |
|:---|:---|:---|
| [Log Analytics-werkruimte](resource-logs-collect-workspace.md) | Analyseer de logboeken met andere bewakings gegevens en gebruik Azure Monitor functies, zoals logboek query's en logboek waarschuwingen. |
| [Azure Storage](resource-logs-collect-storage.md) | Archiveer de logboeken voor controle of back-up. |
| [Event hub](resource-logs-stream-event-hubs.md) | De logboeken streamen naar logboek registraties en telemetrie-systemen van derden.  |

## <a name="compute-resources"></a>Reken resources
Bron logboeken verschillen van Logboeken op het niveau van het gast besturingssysteem in azure Compute-resources. Voor reken resources is een agent vereist voor het verzamelen van Logboeken en metrische gegevens uit hun gast besturingssysteem, met inbegrip van een gebeurtenis logboek, syslog en prestatie meter items. Gebruik de [Diagnostische uitbrei ding](agents-overview.md#azure-diagnostic-extension) voor het routeren van logboek gegevens van virtuele machines van Azure en de [log Analytics-agent](agents-overview.md#log-analytics-agent) voor het verzamelen van Logboeken en metrieken van virtuele machines in azure, in andere Clouds en on-premises in een log Analytics-werk ruimte. Zie [bronnen van bewakings gegevens voor Azure monitor](data-sources.md) voor meer informatie.

## <a name="resource-logs-schema"></a>Schema voor bron logboeken
Zie [resource logboek schema](diagnostic-logs-schema.md)voor meer informatie over het schema en de categorieën van het resource logboek. 

## <a name="next-steps"></a>Volgende stappen

* Meer [informatie over andere Azure-platform logboeken](platform-logs-overview.md) die u kunt gebruiken om Azure te bewaken.
