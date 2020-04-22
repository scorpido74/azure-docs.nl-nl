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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81737957"
---
| **Resource** | **Standaardlimiet** | **Maximumlimiet** |
| --- | --- | --- |
| Azure Batch-accounts per regio per abonnement | 1-3 |50 |
| Speciale kernen per Batch-account | 90-900 | Contact opnemen met ondersteuning |
| Kernen met lage prioriteit per batchaccount | 10-100 | Contact opnemen met ondersteuning |
| **[Actieve](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** taken en taakroosters per Batch-account **(voltooide** taken hebben geen limiet) | 100-300 | 1.000<sup>1</sup> |
| Pools per Batch-account | 20-100 | 500<sup>1</sup> |

<sup>1.</sup> Neem contact op met Azure Support om een verhoging buiten deze limiet aan te vragen.

> [!NOTE]
> Standaardlimieten variëren afhankelijk van het type abonnement dat u gebruikt om een Batch-account te maken. De getoonde kernenquota zijn voor Batch-accounts in de batchservicemodus. [Bekijk de quota in uw Batch-account](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> Om ons te helpen de capaciteit beter te beheren tijdens de wereldwijde gezondheidspandemie, zijn de standaardkernquota voor nieuwe Batch-accounts in sommige regio's en voor sommige soorten abonnementen teruggebracht van het bovenstaande bereik van waarden, in sommige gevallen tot nul cores. Wanneer u een nieuw batchaccount maakt, [controleert u uw kernquotum](../articles/batch/batch-quota-limit.md#view-batch-quotas) en vraagt u indien nodig [een kernquotumverhoging](../articles/batch/batch-quota-limit.md#increase-a-quota)aan. 
