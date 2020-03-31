---
title: bestand opnemen
description: bestand opnemen
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 22bfc3c86605f4c2eed4c022919b3643f394ea95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67080902"
---
| **Resource** | **Standaardlimiet** | **Maximumlimiet** |
| --- | --- | --- |
| Azure Batch-accounts per regio per abonnement | 1-3 |50 |
| Speciale kernen per Batch-account | 90-900 | Contact opnemen met ondersteuning |
| Kernen met lage prioriteit per batchaccount | 10-100 | Contact opnemen met ondersteuning |
| **[Actieve](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** taken en taakroosters per Batch-account **(voltooide** taken hebben geen limiet) | 100-300 | 1.000<sup>1</sup> |
| Pools per Batch-account | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> Standaardlimieten variëren afhankelijk van het type abonnement dat u gebruikt om een Batch-account te maken. De getoonde kernenquota zijn voor Batch-accounts in de batchservicemodus. [Bekijk de quota in uw Batch-account](../articles/batch/batch-quota-limit.md#view-batch-quotas).

<sup>1.</sup> Neem contact op met Azure Support om een verhoging buiten deze limiet aan te vragen.
