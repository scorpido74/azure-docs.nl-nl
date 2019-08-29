---
title: Azure Batch Analytics | Microsoft Docs
description: Naslag informatie voor Azure Batch Analytics.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 03cb6231a42e27c474e20f4c6ae91095156bb766
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095521"
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