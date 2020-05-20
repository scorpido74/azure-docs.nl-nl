---
title: bestand opnemen
description: bestand opnemen
services: search
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/11/2020
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: fe5c8129434ddb4eec2dd25a3f123f28b4db221b
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682641"
---
Een zoek service wordt beperkt door schijf ruimte of met een vaste limiet voor het maximum aantal indexen of Indexeer functies, afhankelijk van wat het eerste komt. In de volgende tabel worden opslag limieten gedocumenteerd. Zie [limieten per resource](../articles/search/search-limits-quotas-capacity.md#index-limits)voor maximale object limieten.

| Resource | Gratis | Basis<sup>1</sup> | S1 | S2 | S3 | S3 &nbsp; HD | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Service Level Agreement (SLA)<sup>2</sup>  |Nee |Ja |Ja |Ja |Ja |Ja |Ja |Ja |
| Opslag per partitie |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Partities per service |N.v.t. |1 |12 |12 |12 |3 |12 |12 |
| Partitiegrootte |N.v.t. |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Replica's |N.v.t. |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> Basic heeft één vaste partitie. Extra Zoek eenheden kunnen worden gebruikt om replica's voor grotere query volumes toe te voegen.

<sup>2</sup> service overeenkomsten zijn van kracht voor factureer bare Services op toegewezen resources. Gratis services en preview-functies hebben geen SLA. Voor factureer bare Services gelden de Sla's wanneer u voldoende redundantie voor uw service inricht. Er zijn twee of meer replica's vereist voor query-Sla's (lezen). Er zijn drie of meer replica's vereist voor het uitvoeren van query's en indexen (lezen/schrijven). Het aantal partities is geen SLA-overweging. 