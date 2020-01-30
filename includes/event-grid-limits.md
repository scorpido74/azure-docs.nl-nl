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
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845959"
---
De volgende limieten gelden voor Azure Event Grid systeem onderwerpen en aangepaste onderwerpen, *niet* voor gebeurtenis domeinen.

| Bron | Limiet |
| --- | --- |
| Aangepaste onderwerpen per Azure-abonnement | 100 |
| Gebeurtenis abonnementen per onderwerp | 500 |
| Publicatie frequentie voor een aangepast onderwerp (ingangs) | 5\.000 gebeurtenissen per seconde per onderwerp |
| Aanvragen publiceren | 250 per seconde |
| Grootte van gebeurtenis | 1 MB (in rekening gebracht als meerdere 64-KB-gebeurtenissen) |

De volgende limieten gelden alleen voor gebeurtenis domeinen.

| Bron | Limiet |
| --- | --- |
| Onderwerpen per gebeurtenis domein | 100.000 |
| Gebeurtenis abonnementen per onderwerp binnen een domein | 500 |
| Gebeurtenis abonnementen voor domein bereik | 50 |
| Publicatie frequentie voor een gebeurtenis domein (ingangs punt) | 5\.000 gebeurtenissen per seconde |
| Aanvragen publiceren | 250 per seconde |
| Gebeurtenis domeinen per Azure-abonnement | 100 |