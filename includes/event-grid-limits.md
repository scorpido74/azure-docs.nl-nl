---
title: bestand opnemen
description: bestand opnemen
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 05/22/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: ee80c04a6365c2cf337c4033a90df8d2993c299d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76845959"
---
De volgende limieten zijn van toepassing op onderwerpen van het Azure Event Grid-systeem en aangepaste onderwerpen, *niet* op gebeurtenisdomeinen.

| Resource | Limiet |
| --- | --- |
| Aangepaste onderwerpen per Azure-abonnement | 100 |
| Gebeurtenisabonnementen per onderwerp | 500 |
| Publicatiesnelheid voor een aangepast onderwerp (binnenvallen) | 5.000 evenementen per seconde per onderwerp |
| Aanvragen publiceren | 250 per seconde |
| Gebeurtenisgrootte | 1 MB (in rekening gebracht als meerdere 64-KB-gebeurtenissen) |

De volgende limieten zijn alleen van toepassing op gebeurtenisdomeinen.

| Resource | Limiet |
| --- | --- |
| Onderwerpen per gebeurtenisdomein | 100.000 |
| Gebeurtenisabonnementen per onderwerp binnen een domein | 500 |
| Abonnementen voor gebeurtenisdomeinbereik | 50 |
| Publicatiesnelheid voor een gebeurtenisdomein (binnenvallen) | 5.000 evenementen per seconde |
| Aanvragen publiceren | 250 per seconde |
| Gebeurtenisdomeinen per Azure-abonnement | 100 |