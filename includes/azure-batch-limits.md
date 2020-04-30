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
| **Resource** | **Standaardlimiet** | **Maximum limiet** |
| --- | --- | --- |
| Azure Batch accounts per regio per abonnement | 1-3 |50 |
| Toegewezen kern geheugens per batch-account | 90-900 | Contact opnemen met ondersteuning |
| Kern geheugens met een lage prioriteit per batch-account | 10-100 | Contact opnemen met ondersteuning |
| **[Actieve](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** taken en taak planningen per batch-account (**voltooide** taken hebben geen limiet) | 100-300 | 1.000<sup>1</sup> |
| Pools per Batch-account | 20-100 | 500<sup>1</sup> |

<sup>1</sup> Neem contact op met de ondersteuning van Azure als u een grotere overschrijding wilt aanvragen.

> [!NOTE]
> De standaard limieten variëren afhankelijk van het type abonnement dat u gebruikt voor het maken van een batch-account. De weer gegeven kernen quota's zijn voor batch-accounts in de batch-service modus. [Bekijk de quota's in uw batch-account](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> Om ervoor te zorgen dat onze capaciteit beter kan worden beheerd tijdens de wereld wijde status Pandemic, zijn de standaard kern quota voor nieuwe batch-accounts in sommige regio's en voor sommige typen abonnementen verlaagd van het bovenstaande bereik van waarden, in sommige gevallen tot nul kernen. Wanneer u een nieuw batch-account maakt, [controleert u het kern quotum](../articles/batch/batch-quota-limit.md#view-batch-quotas) en vraagt u, indien nodig, [een kern quotum toe](../articles/batch/batch-quota-limit.md#increase-a-quota). 
