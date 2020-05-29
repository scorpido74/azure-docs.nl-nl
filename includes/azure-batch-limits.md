---
title: bestand opnemen
description: bestand opnemen
services: batch
author: LauraBrenner
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: a0d74aa38dffdd41fbe617066391ef271a507349
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81737957"
---
| **Resource** | **Standaardlimiet** | **Maximumlimiet** |
| --- | --- | --- |
| Azure Batch-accounts per regio per abonnement | 1-3 |50 |
| Toegewezen kerngeheugens per Batch-account | 90-900 | Contact opnemen met ondersteuning |
| Kerngeheugens met een lage prioriteit per Batch-account | 10-100 | Contact opnemen met ondersteuning |
| **[Actieve](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** taken en taakplanningen per Batch-account (**voltooide** taken hebben geen limiet) | 100-300 | 1000<sup>1</sup> |
| Pools per Batch-account | 20-100 | 500<sup>1</sup> |

<sup>1</sup>Neem contact op met de ondersteuning van Azure als u een grotere hoeveelheid wilt aanvragen.

> [!NOTE]
> De standaardlimieten variëren afhankelijk van het type abonnement dat u gebruikt voor het maken van een Batch-account. De weergegeven kerngeheugenquota zijn voor Batch-accounts in de modus Batch-service. [Bekijk de quota in uw Batch-account](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> Om ervoor te zorgen dat onze capaciteit beter kan worden beheerd tijdens de wereldwijde gezondheidspandemie, zijn de standaard kerngeheugenquota voor nieuwe Batch-accounts in sommige regio's en voor sommige typen abonnementen verlaagd. In sommige gevallen zijn de bovenstaande waarden tot nul kerngeheugens verlaagd. Wanneer u een nieuw Batch-account maakt, moet u [uw quotum voor kerngeheugens controleren](../articles/batch/batch-quota-limit.md#view-batch-quotas) en indien nodig [een verhoging van het kerngeheugenquotum aanvragen](../articles/batch/batch-quota-limit.md#increase-a-quota). 
