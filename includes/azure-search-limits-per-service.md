---
title: bestand opnemen
description: bestand opnemen
services: search
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 179f525b7b6a7e51889b14b66df6c537ca56bd75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272760"
---
Opslag wordt beperkt door schijfruimte of door een harde limiet voor het *maximum aantal* indexen, documenten of andere bronnen op hoog niveau, afhankelijk van wat het eerst komt. In de volgende tabel worden opslaglimieten opgeslagen. Zie Limieten voor [resource](../articles/search/search-limits-quotas-capacity.md#index-limits)voor maximale limieten voor indexen, documenten en andere objecten.

| Resource | Gratis | Basis<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD<sup>2</sup> | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Servicelevelovereenkomst (SLA)<sup>3</sup>  |Nee |Ja |Ja |Ja |Ja |Ja |Ja |Ja |
| Opslag per partitie |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Partities per service |N.v.t. |1 |12 |12 |12 |3 |12 |12 |
| Partitiegrootte |N.v.t. |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Replica's |N.v.t. |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> Basic heeft één vaste partitie. Op deze laag worden extra zoekeenheden gebruikt voor het toewijzen van meer replica's voor verhoogde queryworkloads.

<sup>2</sup> S3 HD heeft een harde limiet van drie partities, wat lager is dan de partitielimiet voor S3. De partitielimiet is lager doordat S3 HD aanzienlijk meer indexen heeft. Omdat er servicelimieten bestaan voor zowel rekenresources (opslag en verwerking) als inhoud (indexen en documenten), wordt de limiet voor de inhoud het eerst bereikt.

<sup>3</sup> Service level agreements worden aangeboden voor factureerbare diensten op speciale resources. Gratis services en preview-functies hebben geen SLA. Voor factureerbare services worden SLA's van kracht wanneer u voldoende redundantie voor uw service indient. Er zijn twee of meer replica's vereist voor query(lees)SLA's. Er zijn drie of meer replica's nodig voor query- en indexerings-(lees-schrijf)SLA's. Het aantal partities is geen SLA-overweging. 