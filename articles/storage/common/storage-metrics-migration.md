---
title: Migratie van Azure Storage metrische gegevens | Microsoft Docs
description: Meer informatie over het migreren van oude metrische gegevens naar nieuwe metrische gegevens die worden beheerd door Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: b5d8add293a2ba8f14dc2d2fb8ba3b4228f455b0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176223"
---
# <a name="azure-storage-metrics-migration"></a>Migratie van Azure Storage metrieken

Met de strategie voor het samen voegen van de monitor ervaring in azure Azure Storage worden metrische gegevens geïntegreerd met het Azure Monitor platform. In de toekomst wordt de service van de oude metrische gegevens beëindigd met een vroegtijdige melding op basis van Azure Policy. Als u vertrouwt over de metrische gegevens voor de opslag, moet u voorafgaand aan de eind datum van de service migreren om uw metrische informatie te behouden.

In dit artikel wordt beschreven hoe u kunt migreren van de oude metrische gegevens naar de nieuwe metrische gegevens.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Meer informatie over oude metrische gegevens die worden beheerd door Azure Storage

Azure Storage worden oude metrische waarden verzameld en worden deze in $Metric tabellen binnen hetzelfde opslag account geaggregeerd en opgeslagen. U kunt de Azure Portal gebruiken om een bewakings grafiek in te stellen. U kunt ook de Azure Storage Sdk's gebruiken om de gegevens te lezen uit $Metric tabellen die zijn gebaseerd op het schema. Zie [Opslaganalyse](./storage-analytics.md)voor meer informatie.

Oude metrische gegevens bieden alleen metrische gegevens over de capaciteit in Azure Blob-opslag. Oude metrische gegevens geven metrische gegevens over trans acties op Blob-opslag, tabel opslag, Azure Files en wachtrij opslag.

Oude metrische gegevens zijn ontworpen in een plat schema. Het ontwerp resulteert in een metrische waarde van nul wanneer u niet beschikt over de verkeers patronen om de metriek te activeren. De waarde **ServerTimeoutError** is bijvoorbeeld ingesteld op 0 in $metric tabellen, zelfs wanneer u geen server time-outfouten van het live verkeer naar een opslag account ontvangt.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Meer informatie over nieuwe metrische gegevens die worden beheerd door Azure Monitor

Voor nieuwe metrische gegevens van de opslag Azure Storage worden de metrieke waarden verzonden naar de Azure Monitor back-end. Azure Monitor biedt een uniforme bewakings ervaring, met inbegrip van gegevens uit de portal en gegevens opname. Raadpleeg dit [artikel](../../monitoring-and-diagnostics/monitoring-overview-metrics.md)voor meer informatie.

Nieuwe metrische gegevens bieden metrische gegevens over capaciteit en trans acties op blob-, tabel-, bestands-, wachtrij-en Premium-opslag.

Meerdere dimensies is een van de functies die Azure Monitor biedt. Azure Storage stelt het ontwerp in om een nieuw metrische schema te definiëren. Voor ondersteunde dimensies op metrische gegevens vindt u informatie in [Azure Storage metrische gegevens in azure monitor](./storage-metrics-in-azure-monitor.md). Ontwerp met meerdere dimensies biedt kosten efficiëntie voor zowel de band breedte als de capaciteit van het opslaan van metrische gegevens. Als uw verkeer echter geen gerelateerde metrische gegevens heeft geactiveerd, worden de gerelateerde metrieke waarden niet gegenereerd. Als uw verkeer bijvoorbeeld geen time-outfouten van de server heeft geactiveerd, Azure Monitor geen gegevens retour neren wanneer u een query uitvoert op de waarde van metrische **trans acties** met dimensie **ResponseType** gelijk aan **ServerTimeoutError**.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Toewijzing van oude metrische gegevens aan nieuwe metrische gegevens

Als u metrische gegevens programmatisch leest, moet u het nieuwe metrische schema in uw Program ma's aannemen. Raadpleeg de toewijzing die wordt vermeld in de volgende tabel voor meer informatie over de wijzigingen.

**Metrische gegevens over capaciteit**

| Oude metriek | Nieuwe metriek |
| ------------------- | ----------------- |
| **Capaciteit**            | **BlobCapacity** met de dimensie **BlobType** gelijk aan **BlockBlob** of **PageBlob** |
| **ObjectCount**        | **BlobCount** met de dimensie **BlobType** gelijk aan **BlockBlob** of **PageBlob** |
| **ContainerCount**      | **ContainerCount** |

De volgende metrische gegevens zijn nieuwe aanbiedingen die niet worden ondersteund door de oude metrische gegevens:
* **TableCapacity**
* **TableCount**
* **TableEntityCount**
* **QueueCapacity**
* **QueueCount**
* **QueueMessageCount**
* **FileCapacity**
* **FileCount**
* **FileShareCount**
* **UsedCapacity**

**Metrische gegevens van trans actie**

| Oude metriek | Nieuwe metriek |
| ------------------- | ----------------- |
| **AnonymousAuthorizationError** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **AuthorizationError** en dimensie **verificatie** gelijk aan **anoniem** |
| **AnonymousClientOtherError** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **ClientOtherError** en dimensie **verificatie** gelijk aan **anoniem** |
| **AnonymousClientTimeoutError** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **ClientTimeoutError** en dimensie **verificatie** gelijk aan **anoniem** |
| **AnonymousNetworkError** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **NetworkError** en dimensie **verificatie** gelijk aan **anoniem** |
| **AnonymousServerOtherError** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **ServerOtherError** en dimensie **verificatie** gelijk aan **anoniem** |
| **AnonymousServerTimeoutError** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **ServerTimeoutError** en dimensie **verificatie** gelijk aan **anoniem** |
| **AnonymousSuccess** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **geslaagd** en dimensie **verificatie** gelijk aan **anoniem** |
| **AnonymousThrottlingError** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **ClientThrottlingError** of **ServerBusyError** en dimensie **verificatie** gelijk is aan **anoniem** |
| **AuthorizationError** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **AuthorizationError** |
| **Beschikbaarheid** | **Beschikbaarheid** |
| **AverageE2ELatency** | **SuccessE2ELatency** |
| **AverageServerLatency** | **SuccessServerLatency** |
| **ClientOtherError** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **ClientOtherError** |
| **ClientTimeoutError** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **ClientTimeoutError** |
| **NetworkError** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **NetworkError** |
| **PercentAuthorizationError** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **AuthorizationError** |
| **PercentClientOtherError** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **ClientOtherError** |
| **PercentNetworkError** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **NetworkError** |
| **PercentServerOtherError** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **ServerOtherError** |
| **PercentSuccess** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **geslaagd** |
| **Percentthrottlingerror aan** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **ClientThrottlingError** of **ServerBusyError** |
| **Percenttimeouterror aan** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **ServerTimeoutError** of **ResponseType** gelijk is aan **ClientTimeoutError** |
| **SASAuthorizationError** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **AuthorizationError** en dimensie **verificatie** gelijk aan **SAS** |
| **SASClientOtherError** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **ClientOtherError** en dimensie **verificatie** gelijk aan **SAS** |
| **SASClientTimeoutError** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **ClientTimeoutError** en dimensie **verificatie** gelijk aan **SAS** |
| **SASNetworkError** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **NetworkError** en dimensie **verificatie** gelijk aan **SAS** |
| **SASServerOtherError** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **ServerOtherError** en dimensie **verificatie** gelijk aan **SAS** |
| **SASServerTimeoutError** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **ServerTimeoutError** en dimensie **verificatie** gelijk aan **SAS** |
| **SASSuccess** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **geslaagd** en dimensie **verificatie** gelijk aan **SAS** |
| **SASThrottlingError** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **ClientThrottlingError** of **ServerBusyError** en dimensie **verificatie** gelijk aan **SAS** |
| **ServerOtherError** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **ServerOtherError** |
| **ServerTimeoutError** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **ServerTimeoutError** |
| **Geslaagd** | Trans acties waarbij de dimensie **ResponseType** gelijk is aan **geslaagd** |
| **ThrottlingError** | **Trans acties** waarbij de dimensie **ResponseType** gelijk is aan **ClientThrottlingError** of **ServerBusyError**|
| **TotalBillableRequests** | **Transacties** |
| **TotalEgress** | **Egress** |
| **TotalIngress** | **Inkomend verkeer** |
| **TotalRequests** | **Transacties** |

## <a name="faq"></a>Veelgestelde vragen

### <a name="how-should-i-migrate-existing-alert-rules"></a>Hoe migreer ik bestaande waarschuwings regels?

Als u de regels voor een klassieke waarschuwing hebt gemaakt op basis van de oude metrische gegevens van de opslag, moet u nieuwe waarschuwings regels maken op basis van het nieuwe metrische schema.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>Zijn nieuwe metrische gegevens standaard opgeslagen in hetzelfde opslag account?

Nee. Als u de metrische gegevens wilt archiveren naar een opslag account, gebruikt u de [API voor Azure monitor Diagnostische instellingen](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate).

## <a name="next-steps"></a>Volgende stappen

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Metrische opslag gegevens in Azure Monitor](./storage-metrics-in-azure-monitor.md)
