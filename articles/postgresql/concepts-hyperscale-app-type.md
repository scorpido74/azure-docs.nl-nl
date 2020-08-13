---
title: Toepassings type bepalen-grootschalige (Citus)-Azure Database for PostgreSQL
description: Uw toepassing identificeren voor effectief gedistribueerde gegevens modellering
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 0daed8e9bba247ae3d4e320e4a02cc2b1b26dbec
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136945"
---
# <a name="determining-application-type"></a>Toepassings type bepalen

Voor het uitvoeren van efficiënte query's voor een grootschalige (Citus)-Server groep moeten tabellen goed worden gedistribueerd over servers. De aanbevolen distributie is afhankelijk van het type toepassing en de bijbehorende query patronen.

Er zijn brede twee soorten toepassingen die goed werken op grootschalige (Citus). De eerste stap bij het model leren van gegevens is om te identificeren welke van de toepassingen er beter uitziet dan uw toepassing.

## <a name="at-a-glance"></a>In één oogopslag

| Multi tenant-toepassingen                                 | Realtime-toepassingen                                |
|-----------------------------------------------------------|-------------------------------------------------------|
| Soms tien tallen of honderden tabellen in schema          | Klein aantal tabellen                                |
| Query's met betrekking tot één Tenant (bedrijf/archief) per keer | Relatief eenvoudige analyse query's met aggregaties |
| OLTP-workloads voor het leveren van webclients                    | Hoog opname volume van meestal onveranderbare gegevens           |
| OLAP-workloads die worden uitgevoerd op de analytische query's per Tenant   | Vaak gecentreerd rond grote gebeurtenissen in de tabel            |

## <a name="examples-and-characteristics"></a>Voor beelden en kenmerken

**Multi tenant-toepassing**

> Dit zijn meestal SaaS-toepassingen die fungeren als andere bedrijven, accounts of organisaties. De meeste SaaS-toepassingen zijn inherent aan het algemeen. Ze hebben een natuurlijke dimensie voor het distribueren van gegevens tussen knoop punten: alleen Shard op Tenant \_ -id.
>
> Met grootschalige (Citus) kunt u uw data base schalen naar miljoenen tenants zonder dat u uw toepassing opnieuw moet ontwerpen. U kunt de relationele semantiek gebruiken die u nodig hebt, zoals joins, refererende-sleutel beperkingen, trans acties, zuur en consistentie.
>
> -   **Voor beelden**: websites die Store-fronts hosten voor andere bedrijven, zoals een digitale marketing oplossing of een hulp programma voor het automatiseren van de verkoop.
> -   **Kenmerken**: query's met betrekking tot één Tenant in plaats van informatie te koppelen tussen tenants. Dit omvat OLTP-workloads voor het leveren van webclients en OLAP-workloads die een analytische query's per Tenant uitvoeren. Een groot aantal tien tallen of honderden tabellen in uw database schema is ook een indicator voor het gegevens model met meerdere tenants.
>
> Voor het schalen van een multi tenant-app met grootschalige (Citus) zijn ook minimale wijzigingen in de toepassings code vereist. We bieden ondersteuning voor populaire frameworks zoals ruby op rails en django.

**Real-time analyse**

> Toepassingen hebben een enorme parallelle afkomst, waarbij honderden kernen worden gecoördineerd voor snelle resultaten voor numerieke, statistische of opgetelde query's.  Met sharding-en gelijktijdig SQL-query's op meerdere knoop punten kunt u met grootschalige (Citus) realtime query's uitvoeren op miljarden records in een seconde.
>
> Tabellen in realtime analyse gegevens modellen worden doorgaans gedistribueerd door kolommen zoals gebruikers \_ -id, host \_ -id of apparaat \_ -id.
>
> -   **Voor beelden**: Dash boards voor klanten gericht op het gebied van de reactie tijden van een subseconde.
> -   **Kenmerken**: weinig tabellen, die vaak worden gecentreerd rond een grote tabel van apparaat-, site-of gebruikers gebeurtenissen en waarbij een hoog opname volume van meestal onveranderbare gegevens wordt vereist. Relatief eenvoudige (maar computerintensieve) analyse query's met betrekking tot diverse aggregaties en GROEPs-toe.

Als uw situatie in een van de bovenstaande situaties lijkt, is de volgende stap het bepalen van de manier waarop u uw gegevens in de Server groep Shard. De data base-beheerder \' s keuze van distributie kolommen moet overeenkomen met de toegangs patronen van typische query's om prestaties te garanderen.

## <a name="next-steps"></a>Volgende stappen

* [Kies een distributie kolom](concepts-hyperscale-choose-distribution-column.md) voor tabellen in uw toepassing om gegevens efficiënt te distribueren
