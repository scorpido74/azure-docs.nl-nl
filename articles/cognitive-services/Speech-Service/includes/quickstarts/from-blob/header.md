---
title: 'Quick Start: spraak herkennen die zijn opgeslagen in de Blob Storage-Speech-Service'
titleSuffix: Azure Cognitive Services
description: Nader te bepalen
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: ef61139056ad194d89e2a67cb4ac1b1414a3380b
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828922"
---
In deze Quick Start gebruikt u de [rest API voor batch transcriptie](../../../batch-transcription.md) om spraak te herkennen die zijn opgeslagen in een [SAS-BLOB](https://aka.ms/ignite2019/speech/placeholder). Nadat u hebt voldaan aan enkele vereisten, is het herkennen van spraak met een REST API slechts enkele stappen nodig:
> [!div class="checklist"]
> * Stuur de JSON-aanvraag naar de speech-service om te beginnen met het transcriberen van spraak.
> * Controleer de status van de transcriptie.
> * Down load de transcriptie-resultaten wanneer u klaar bent.