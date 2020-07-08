---
title: bestand opnemen
description: bestand opnemen
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 06/03/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 8d23da1c3a45ed12193dfd4c1fc2ede453fc6ac7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84323876"
---
| **Resource** | **Standaardlimiet** | **Maximumlimiet** |
| --- | --- | --- |
| Azure Batch-accounts per regio per abonnement | 1-3 |50 |
| Toegewezen kerngeheugens per Batch-account | 90-900 | Contact opnemen met ondersteuning |
| Kerngeheugens met een lage prioriteit per Batch-account | 10-100 | Contact opnemen met ondersteuning |
| **[Actieve](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** taken en taakplanningen per Batch-account (**voltooide** taken hebben geen limiet) | 100-300 | 1000<sup>1</sup> |
| Pools per Batch-account | 20-100 | 500<sup>1</sup> |

<sup>1</sup>Neem contact op met de ondersteuning van Azure als u een grotere hoeveelheid wilt aanvragen.

> [!IMPORTANT]
> We wijzigen de manier waarop u toegewezen quota aanvraagt en beheert.  Het totale toegewezen Vcpu's is de momenteel afgedwongen waarde, maar binnenkort wordt het toegewezen quotum per VM-serie afgedwongen. Quota met een lage prioriteit worden nog steeds afgedwongen op basis van de totale limiet; deze wordt niet afgedwongen door de VM-serie.

> [!NOTE]
> De standaardlimieten variëren afhankelijk van het type abonnement dat u gebruikt voor het maken van een Batch-account. De weergegeven kerngeheugenquota zijn voor Batch-accounts in de modus Batch-service. [Bekijk de quota in uw Batch-account](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> Om ervoor te zorgen dat onze capaciteit beter kan worden beheerd tijdens de wereldwijde gezondheidspandemie, zijn de standaard kerngeheugenquota voor nieuwe Batch-accounts in sommige regio's en voor sommige typen abonnementen verlaagd. In sommige gevallen zijn de bovenstaande waarden tot nul kerngeheugens verlaagd. Wanneer u een nieuw Batch-account maakt, moet u [uw quotum voor kerngeheugens controleren](../articles/batch/batch-quota-limit.md#view-batch-quotas) en indien nodig [een verhoging van het kerngeheugenquotum aanvragen](../articles/batch/batch-quota-limit.md#increase-a-quota). 
