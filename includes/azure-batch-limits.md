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
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "67080902"
---
| **Resource** | **Standaardlimiet** | **Maximumlimiet** |
| --- | --- | --- |
| Azure Batch accounts per regio per abonnement | 1-3 |50 |
| Toegewezen kern geheugens per batch-account | 90-900 | Neem contact op met ondersteuning |
| Kern geheugens met een lage prioriteit per batch-account | 10-100 | Neem contact op met ondersteuning |
| **[Actieve](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** taken en taak planningen per batch-account (**voltooide** taken hebben geen limiet) | 100-300 | 1\.000<sup>1</sup> |
| Pools per Batch-account | 20-100 | 500<sup>1</sup> |

> [!NOTE]
> De standaard limieten variëren afhankelijk van het type abonnement dat u gebruikt voor het maken van een batch-account. De weer gegeven kernen quota's zijn voor batch-accounts in de batch-service modus. [Bekijk de quota's in uw batch-account](../articles/batch/batch-quota-limit.md#view-batch-quotas).

<sup>1</sup> Neem contact op met de ondersteuning van Azure als u een grotere overschrijding wilt aanvragen.
