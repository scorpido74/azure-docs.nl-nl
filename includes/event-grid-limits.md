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
ms.openlocfilehash: 220759e8ed31c091887bd55f8d12aa4cc03a065f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590278"
---
De volgende limieten gelden voor Azure Event Grid systeem onderwerpen en aangepaste onderwerpen, *niet* voor gebeurtenis domeinen.

| Resource | Limiet |
| --- | --- |
| Aangepaste onderwerpen per Azure-abonnement | 100 |
| Gebeurtenis abonnementen per onderwerp | 500 |
| Publicatie frequentie voor een aangepast onderwerp (ingangs) | 5.000 gebeurtenissen per seconde per onderwerp |
| Aanvragen publiceren | 250 per seconde |
| Grootte van gebeurtenis | 1 MB. Bewerkingen worden in rekening gebracht in stappen van 64 KB. Dit betekent dat gebeurtenissen van meer dan 64 KB bewerkingen in rekening brengen, alsof ze meerdere gebeurtenissen waren. Bijvoorbeeld, een gebeurtenis die 130 KB zou moeten doen, alsof het drie afzonderlijke gebeurtenissen waren.  |
| Onderwerpen per gebeurtenis domein | 100.000 |
| Gebeurtenis abonnementen per onderwerp binnen een domein | 500 |
| Gebeurtenis abonnementen voor domein bereik | 50 |
| Publicatie frequentie voor een gebeurtenis domein (ingangs punt) | 5.000 gebeurtenissen per seconde |
| Aanvragen publiceren voor een gebeurtenis domein | 250 per seconde |
| Gebeurtenis domeinen per Azure-abonnement | 100 |
| Verbindingen van privé-eind punten per onderwerp of domein | 64 | 
| IP-firewall regels per onderwerp of domein | 16 | 