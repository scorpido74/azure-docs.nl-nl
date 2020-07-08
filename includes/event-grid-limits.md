---
title: bestand opnemen
description: bestand opnemen
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 05/18/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7e7a0424e4454639211c6494aab0700e75269361
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83721450"
---
De volgende limieten gelden voor Azure Event Grid systeem onderwerpen en aangepaste onderwerpen, *niet* voor gebeurtenis domeinen.

| Resource | Limiet |
| --- | --- |
| Aangepaste onderwerpen per Azure-abonnement | 100 |
| Gebeurtenis abonnementen per onderwerp | 500 |
| Publicatie frequentie voor een aangepast onderwerp (ingangs) | 5.000 gebeurtenissen per seconde per onderwerp |
| Grootte van gebeurtenis | 1 MB. Bewerkingen worden in rekening gebracht in stappen van 64 KB. Dit betekent dat gebeurtenissen van meer dan 64 KB bewerkingen in rekening brengen, alsof ze meerdere gebeurtenissen waren. Bijvoorbeeld, een gebeurtenis die 130 KB zou moeten doen, alsof het drie afzonderlijke gebeurtenissen waren.  |
| Onderwerpen per gebeurtenis domein | 100.000 |
| Gebeurtenis abonnementen per onderwerp binnen een domein | 500 |
| Gebeurtenis abonnementen voor domein bereik | 50 |
| Publicatie frequentie voor een gebeurtenis domein (ingangs punt) | 5.000 gebeurtenissen per seconde |
| Gebeurtenis domeinen per Azure-abonnement | 100 |
| Verbindingen van privé-eind punten per onderwerp of domein | 64 | 
| IP-firewall regels per onderwerp of domein | 16 | 