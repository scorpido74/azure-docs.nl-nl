---
title: Migratie van Azure Storage-statistieken | Microsoft Documenten
description: Meer informatie over het migreren van oude statistieken naar nieuwe statistieken die worden beheerd door Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 537369c9466b1083723642ec9e93fcdf25056c5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68855343"
---
# <a name="azure-storage-metrics-migration"></a>Migratie van Azure Storage-statistieken

Azure Storage is afgestemd op de strategie om de monitorervaring in Azure te verenigen en integreert metrische gegevens met het Azure Monitor-platform. In de toekomst eindigt de service van de oude statistieken met een vroege melding op basis van azure-beleid. Als u vertrouwt op oude opslagstatistieken, moet u vóór de einddatum van de service migreren om uw metrische gegevens te behouden.

In dit artikel ziet u hoe u migreren van de oude statistieken naar de nieuwe statistieken.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Meer informatie over oude metrische gegevens die worden beheerd door Azure Storage

Azure Storage verzamelt oude metrische waarden en verzamelt en slaat deze op in $Metric tabellen binnen hetzelfde opslagaccount. U de Azure-portal gebruiken om een bewakingsdiagram in te stellen. U de Azure Storage-SDK's ook gebruiken om de gegevens uit $Metric tabellen te lezen die zijn gebaseerd op het schema. Zie [Storage Analytics](./storage-analytics.md)voor meer informatie.

Oude statistieken bieden alleen capaciteitsstatistieken voor Azure Blob-opslag. Oude statistieken bieden transactiestatistieken voor Blob-opslag, tabelopslag, Azure-bestanden en wachtrijopslag.

Oude statistieken zijn ontworpen in een plat schema. Het ontwerp resulteert in nul metrische waarde wanneer u niet beschikt over de verkeerspatronen die de statistiek activeren. De **servertimeoutfoutwaarde** is bijvoorbeeld ingesteld op 0 in $Metric tabellen, zelfs als u geen time-outfouten van de server ontvangt van het liveverkeer naar een opslagaccount.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Meer informatie over nieuwe metrische gegevens die worden beheerd door Azure Monitor

Voor nieuwe opslagstatistieken zendt Azure Storage de metrische gegevens uit naar de back-end van Azure Monitor. Azure Monitor biedt een uniforme bewakingservaring, inclusief gegevens uit de portal en gegevensopname. Voor meer informatie u verwijzen naar dit [artikel](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Nieuwe statistieken bieden capaciteitsstatistieken en transactiestatistieken in Blob, Tabel, Bestand, Wachtrij en premium opslag.

Multi-dimension is een van de functies die Azure Monitor biedt. Azure Storage neemt het ontwerp over bij het definiëren van een nieuw metriek schema. Voor ondersteunde dimensies op statistieken vindt u details in [Azure Storage-statistieken in Azure Monitor.](./storage-metrics-in-azure-monitor.md) Multi-dimension design biedt kostenefficiëntie op zowel bandbreedte van inname en capaciteit van het opslaan van statistieken. Als uw verkeer geen gerelateerde statistieken heeft geactiveerd, worden de gerelateerde metrische gegevens dus niet gegenereerd. Als uw verkeer bijvoorbeeld geen time-outfouten van de server heeft veroorzaakt, retourneert Azure Monitor geen gegevens wanneer u de waarde van metrische **transacties** met dimensie **ResponseType** opvraagt dat gelijk is aan **ServerTimeoutError**.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>Toewijzing van oude metrische gegevens aan nieuwe metrische gegevens

Als u metrische gegevens programmatisch leest, moet u het nieuwe metrische schema in uw programma's gebruiken. Als u de wijzigingen beter wilt begrijpen, u verwijzen naar de toewijzing in de volgende tabel:

**Capaciteitsstatistieken**

| Oude statistiek | Nieuwe statistiek |
| ------------------- | ----------------- |
| **Capaciteit**            | **BlobCapaciteit** met de dimensie **BlobType** gelijk aan **BlockBlob** of **PageBlob** |
| **Objectaantal**        | **BlobCount** met de dimensie **BlobType** gelijk aan **BlockBlob** of **PageBlob** |
| **ContainerCount ContainerCount**      | **ContainerCount ContainerCount** |

De volgende statistieken zijn nieuwe aanbiedingen die de oude statistieken niet ondersteunen:
* **Tabelcapaciteit**
* **Tabeltelling**
* **Tabelentiteittelling**
* **Wachtrijcapaciteit**
* **Aantal wachtrijen**
* **Aantal queuemessage's**
* **Bestandscapaciteit**
* **FileCount**
* **FileShareCount**
* **Gebruikte capaciteit**

**Transactiestatistieken**

| Oude statistiek | Nieuwe statistiek |
| ------------------- | ----------------- |
| **AnoniemAutorisatieFout** | Transacties met de dimensie **ResponseType** gelijk aan **AutorisatieFout** en **dimensieverificatie** gelijk aan **Anoniem** |
| **AnoniemeclientOtherError** | Transacties met de dimensie **ResponseType** gelijk aan **ClientOtherError** en dimension **Authentication** gelijk aan **Anoniem** |
| **AnoniemeClientTime-outError** | Transacties met de dimensie **ResponseType** gelijk aan **ClientTimeoutError** en dimension **Authentication** gelijk aan **Anoniem** |
| **AnoniemNetwerkFout** | Transacties met de dimensie **ResponseType** gelijk aan **NetworkError** en dimension **Authentication** gelijk aan **Anoniem** |
| **AnoniemeServerOtherD** | Transacties met de dimensie **ResponseType** gelijk aan **ServerOtherError** en **dimensieverificatie** gelijk aan **Anoniem** |
| **AnoniemeServerTimeoutFout** | Transacties met de dimensie **ResponseType** gelijk aan **ServerTimeoutError** en dimension **Authentication** gelijk aan **Anoniem** |
| **AnoniemSucces** | Transacties met de dimensie **ResponseType** gelijk aan **succes** en **dimensieverificatie** gelijk aan **Anoniem** |
| **AnoniemeThrottlingError** | Transacties met de dimensie **ResponseType** gelijk aan **ClientThrottlingError** of **ServerBusyError** en dimension **Authentication** gelijk aan **Anoniem** |
| **AutorisatieFout** | Transacties met de dimensie **ResponseType** gelijk aan **AutorisatieFout** |
| **Beschikbaarheid** | **Beschikbaarheid** |
| **AverageE2ELatency** | **SuccesE2ELatentie** |
| **AverageServerLatency** | **SuccessServerLatentie** |
| **ClientOtherError** | Transacties met de dimensie **ResponseType** gelijk aan **ClientOtherError** |
| **ClientTimeoutFout** | Transacties met de dimensie **ResponseType** gelijk aan **ClientTimeoutError** |
| **Netwerkfout** | Transacties met de dimensie **ResponseType** gelijk aan **NetworkError** |
| **PercentAuthorizationError PercentAuthorizationError** | Transacties met de dimensie **ResponseType** gelijk aan **AutorisatieFout** |
| **PercentclientOtherError** | Transacties met de dimensie **ResponseType** gelijk aan **ClientOtherError** |
| **PercentNetworkError PercentNetworkError** | Transacties met de dimensie **ResponseType** gelijk aan **NetworkError** |
| **PercentserverOtherError** | Transacties met de dimensie **ResponseType** gelijk aan **ServerOtherError** |
| **ProcentSucces** | Transacties met de dimensie **ResponseType** gelijk aan **succes** |
| **PercentisseringFout** | Transacties met de dimensie **ResponseType** gelijk aan **ClientThrottlingError** of **ServerBusyError** |
| **PercentTimeoutError PercentTimeoutError** | Transacties met de dimensie **ResponseType** gelijk aan **ServerTimeoutError** of **ResponseType** gelijk aan **ClientTimeoutError** |
| **SASAuthorizationFout** | Transacties met de dimensie **ResponseType** gelijk aan **AutorisatieFout** en **dimensieverificatie** gelijk aan **SAS** |
| **SASClientOtherError** | Transacties met de dimensie **ResponseType** gelijk aan **ClientOtherError** en dimension **Authentication** gelijk aan **SAS** |
| **SASClientTimeoutError** | Transacties met de dimensie **ResponseType** gelijk aan **ClientTimeoutError** en dimension **Authentication** gelijk aan **SAS** |
| **SASNetworkError (SASNetworkError)** | Transacties met de dimensie **ResponseType** gelijk aan **NetworkError** en dimension **Authentication** gelijk aan **SAS** |
| **SASServerOtherD** | Transacties met de dimensie **ResponseType** gelijk aan **ServerOtherError** en dimension **authentication** gelijk aan **SAS** |
| **SASServerTimeoutD** | Transacties met de dimensie **ResponseType** gelijk aan **ServerTimeoutError** en dimension **Authentication** gelijk aan **SAS** |
| **SASSucces** | Transacties met de dimensie **ResponseType** gelijk aan **succes** en **dimensieauthenticatie** gelijk aan **SAS** |
| **SASThrottlingError (SASThrottlingError)** | Transacties met de dimensie **ResponseType** gelijk aan **ClientThrottlingError** of **ServerBusyError** en dimension **Authentication** gelijk aan **SAS** |
| **ServerOtherD** | Transacties met de dimensie **ResponseType** gelijk aan **ServerOtherError** |
| **ServerTimeoutD** | Transacties met de dimensie **ResponseType** gelijk aan **ServerTimeoutError** |
| **Geslaagd** | Transacties met de dimensie **ResponseType** gelijk aan **succes** |
| **ThrottlingError** | **Transacties** met de dimensie **ResponseType** gelijk aan **ClientThrottlingError** of **ServerBusyError**|
| **TotalBillableRequests TotalBillableRequests TotalBillableRequests TotalBill** | **Transacties** |
| **TotalEgress** | **Egress** |
| **TotalIngress** | **Ingress** |
| **TotalRequests** | **Transacties** |

## <a name="faq"></a>Veelgestelde vragen

### <a name="how-should-i-migrate-existing-alert-rules"></a>Hoe moet ik bestaande waarschuwingsregels migreren?

Als u klassieke waarschuwingsregels hebt gemaakt op basis van oude opslagstatistieken, moet u nieuwe waarschuwingsregels maken op basis van het nieuwe metrische schema.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>Worden nieuwe metrische gegevens standaard opgeslagen in hetzelfde opslagaccount?

Nee. Als u de metrische gegevens wilt archiveren naar een opslagaccount, gebruikt u de [API voor diagnostische instellingen voor Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate).

## <a name="next-steps"></a>Volgende stappen

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Opslagstatistieken in Azure Monitor](./storage-metrics-in-azure-monitor.md)
