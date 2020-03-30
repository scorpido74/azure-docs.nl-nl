---
title: Azure Batch Analytics
description: De onderwerpen in Batch Analytics bevatten referentiegegevens voor de gebeurtenissen en waarschuwingen die beschikbaar zijn voor Batch-servicebronnen.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: d9538461dfa48a961f9325566b2adaf265e8daeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025959"
---
# <a name="batch-analytics"></a>Batch-analyse
De onderwerpen in Batch Analytics bevatten referentiegegevens voor de gebeurtenissen en waarschuwingen die beschikbaar zijn voor Batch-servicebronnen.

Zie [Diagnostische logboekregistratie van Azure Batch](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) voor meer informatie over het inschakelen en consumeren van diagnostische logboeken van batch.

## <a name="diagnostic-logs"></a>Diagnostische logboeken

De Azure Batch-service zendt de volgende diagnostische logboekgebeurtenissen uit tijdens de levensduur van bepaalde batchresources.

**Serviceloggebeurtenissen**
* [Poolen maken](batch-pool-create-event.md)
* [Begin van het verwijderen van de groep](batch-pool-delete-start-event.md)
* [Groep verwijderen voltooid](batch-pool-delete-complete-event.md)
* [Beginvan het formaat van de groep](batch-pool-resize-start-event.md)
* [Het formaat van de groep wijzigen voltooid](batch-pool-resize-complete-event.md)
* [Taakstart](batch-task-start-event.md)
* [Taak voltooid](batch-task-complete-event.md)
* [Taak mislukt](batch-task-fail-event.md)