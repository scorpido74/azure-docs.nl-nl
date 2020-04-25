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
ms.openlocfilehash: 5992726893b722b0aa46c976a0167793f5ee6bb4
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82132246"
---
De volgende limieten gelden voor Azure Event Grid systeem onderwerpen en aangepaste onderwerpen, *niet* voor gebeurtenis domeinen.

| Resource | Limiet |
| --- | --- |
| Aangepaste onderwerpen per Azure-abonnement | 100 |
| Gebeurtenis abonnementen per onderwerp | 500 |
| Publicatie frequentie voor een aangepast onderwerp (ingangs) | 5.000 gebeurtenissen per seconde per onderwerp |
| Aanvragen publiceren | 250 per seconde |
| Grootte van gebeurtenis | 1 MB. Bewerkingen worden in rekening gebracht in stappen van 64 KB. Dit betekent dat gebeurtenissen van meer dan 64 KB bewerkingen in rekening brengen, alsof ze meerdere gebeurtenissen waren. Bijvoorbeeld, een gebeurtenis die 130 KB zou moeten doen, alsof het drie afzonderlijke gebeurtenissen waren.  |

De volgende limieten gelden alleen voor gebeurtenis domeinen.

| Resource | Limiet |
| --- | --- |
| Onderwerpen per gebeurtenis domein | 100.000 |
| Gebeurtenis abonnementen per onderwerp binnen een domein | 500 |
| Gebeurtenis abonnementen voor domein bereik | 50 |
| Publicatie frequentie voor een gebeurtenis domein (ingangs punt) | 5.000 gebeurtenissen per seconde |
| Aanvragen publiceren | 250 per seconde |
| Gebeurtenis domeinen per Azure-abonnement | 100 |