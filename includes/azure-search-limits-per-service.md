---
title: bestand opnemen
description: bestand opnemen
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: b298c87d802314bd865bd6f38c35e4361eb69f3f
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "67176415"
---
Opslag wordt beperkt door schijf ruimte of met een vaste limiet voor het *maximum aantal* indexen, documenten of andere resources op hoog niveau, afhankelijk van wat het eerste komt. In de volgende tabel worden opslag limieten gedocumenteerd. Zie [limieten per resource](../articles/search/search-limits-quotas-capacity.md#index-limits)voor maximale limieten voor indexen, documenten en andere objecten.

| Resource | Free | Basis<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Service Level Agreement (SLA)<sup>3</sup>  |Nee |Ja |Ja |Ja |Ja |Ja |Ja |Ja |
| Opslag per partitie |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Partities per service |N/A |1 |12 |12 |12 |3 |12 |12 |
| Partitiegrootte |N/A |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Replica's |N/A |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> Basic heeft één vaste partitie. Op deze laag worden er extra Zoek eenheden gebruikt voor het toewijzen van meer replica's voor meer query-workloads.

<sup>2</sup> S3 HD heeft een vaste limiet van drie partities, wat lager is dan de partitie limiet voor S3. De partitielimiet is lager doordat S3 HD aanzienlijk meer indexen heeft. Omdat er servicelimieten bestaan voor zowel rekenresources (opslag en verwerking) als inhoud (indexen en documenten), wordt de limiet voor de inhoud het eerst bereikt.

<sup>3</sup> service overeenkomsten worden aangeboden voor factureer bare Services op toegewezen resources. Gratis services en preview-functies hebben geen SLA. Voor factureer bare Services gelden de Sla's wanneer u voldoende redundantie voor uw service inricht. Er zijn twee of meer replica's vereist voor query-Sla's (lezen). Er zijn drie of meer replica's vereist voor het uitvoeren van query's en indexen (lezen/schrijven). Het aantal partities is geen SLA-overweging. 