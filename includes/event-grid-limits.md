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
ms.openlocfilehash: 3f94481e6a8550479788d92c744327e1dc3b58c4
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "66376923"
---
De volgende limieten gelden voor Azure Event Grid systeem onderwerpen en aangepaste onderwerpen, *niet* voor gebeurtenis domeinen.

| Resource | Limiet |
| --- | --- |
| Aangepaste onderwerpen per Azure-abonnement | 100 |
| Gebeurtenis abonnementen per onderwerp | 500 |
| Publicatie frequentie voor een aangepast onderwerp (ingangs) | 5\.000 gebeurtenissen per seconde per onderwerp |
| Aanvragen publiceren | 250 per seconde |
| Grootte van gebeurtenis | Ondersteuning voor 64 KB in algemene Beschik baarheid (GA). Ondersteuning voor 1 MB is momenteel beschikbaar als preview-versie. |

De volgende limieten gelden alleen voor gebeurtenis domeinen.

| Resource | Limiet |
| --- | --- |
| Onderwerpen per gebeurtenis domein | 1\.000 tijdens de open bare preview |
| Gebeurtenis abonnementen per onderwerp binnen een domein | 50 tijdens de open bare preview |
| Gebeurtenis abonnementen voor domein bereik | 50 tijdens de open bare preview |
| Publicatie frequentie voor een gebeurtenis domein (ingangs punt) | 5\.000 gebeurtenissen per seconde tijdens de open bare preview |
| Aanvragen publiceren | 250 per seconde |