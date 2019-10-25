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
ms.openlocfilehash: 890095718833c90a6764d0799d2081b393b1d0bd
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887758"
---
De volgende limieten gelden voor Azure Event Grid systeem onderwerpen en aangepaste onderwerpen, *niet* voor gebeurtenis domeinen.

| Bron | Limiet |
| --- | --- |
| Aangepaste onderwerpen per Azure-abonnement | 100 |
| Gebeurtenis abonnementen per onderwerp | 500 |
| Publicatie frequentie voor een aangepast onderwerp (ingangs) | 5\.000 gebeurtenissen per seconde per onderwerp |
| Aanvragen publiceren | 250 per seconde |
| Grootte van gebeurtenis | Ondersteuning voor 64 KB in algemene Beschik baarheid (GA). Ondersteuning voor 1 MB is momenteel beschikbaar als preview-versie. |

De volgende limieten gelden alleen voor gebeurtenis domeinen.

| Bron | Limiet |
| --- | --- |
| Onderwerpen per gebeurtenis domein | 100.000 |
| Gebeurtenis abonnementen per onderwerp binnen een domein | 500 |
| Gebeurtenis abonnementen voor domein bereik | 50 |
| Publicatie frequentie voor een gebeurtenis domein (ingangs punt) | 5\.000 gebeurtenissen per seconde |
| Aanvragen publiceren | 250 per seconde |
| Gebeurtenis domeinen per Azure-abonnement | 100 |