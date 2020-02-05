---
title: Azure Batch Analytics
description: De onderwerpen in batch Analytics bevatten referentie-informatie voor de gebeurtenissen en waarschuwingen die beschikbaar zijn voor batch-service resources.
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
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025959"
---
# <a name="batch-analytics"></a>Batch-analyse
De onderwerpen in batch Analytics bevatten referentie-informatie voor de gebeurtenissen en waarschuwingen die beschikbaar zijn voor batch-service resources.

Zie [Azure batch diagnostische logboek registratie](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) voor meer informatie over het inschakelen en gebruiken van batch diagnose Logboeken.

## <a name="diagnostic-logs"></a>Diagnostische logboeken

De Azure Batch-service verzendt de volgende diagnostische logboek gebeurtenissen tijdens de levens duur van bepaalde batch-resources.

**Gebeurtenissen van de service-logboek**
* [Groep maken](batch-pool-create-event.md)
* [Pool verwijderen starten](batch-pool-delete-start-event.md)
* [Pool verwijderen voltooid](batch-pool-delete-complete-event.md)
* [Pool resize start](batch-pool-resize-start-event.md)
* [Formaat van pool wijzigen voltooid](batch-pool-resize-complete-event.md)
* [Taak starten](batch-task-start-event.md)
* [De taak is voltooid](batch-task-complete-event.md)
* [Taak is mislukt](batch-task-fail-event.md)