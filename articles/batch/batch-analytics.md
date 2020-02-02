---
title: Azure Batch Analytics
description: De onderwerpen in batch Analytics bevatten referentie-informatie voor de gebeurtenissen en waarschuwingen die beschikbaar zijn voor batch-service resources.
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: jushiman
ms.openlocfilehash: 5f5063ff2b285a0613d8c7bf367007997afcb778
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935263"
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