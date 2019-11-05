---
title: 'Quick Start: spraak herkennen die zijn opgeslagen in de Blob Storage-Speech-Service'
titleSuffix: Azure Cognitive Services
description: NOG TE BEPALEN
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 195c4c94f7ab00a8740c9dd14aad0d0fd0daa9b7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503974"
---
In deze Quick Start gebruikt u de [rest API voor batch transcriptie](../../../batch-transcription.md) om spraak te herkennen die zijn opgeslagen in een [SAS-BLOB](https://aka.ms/ignite2019/speech/placeholder). Nadat u hebt voldaan aan enkele vereisten, is het herkennen van spraak met een REST API slechts enkele stappen nodig:
> [!div class="checklist"]
> * Stuur de JSON-aanvraag naar de speech-service om te beginnen met het transcriberen van spraak.
> * Controleer de status van de transcriptie.
> * Down load de transcriptie-resultaten wanneer u klaar bent.