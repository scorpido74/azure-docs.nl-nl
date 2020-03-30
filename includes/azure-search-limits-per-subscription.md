---
title: bestand opnemen
description: bestand opnemen
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: ee430241173a6c19e2a32e176f28411631d9cb19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272900"
---
U meerdere services maken binnen een abonnement. Elk kan worden ingericht op een specifieke laag. U wordt alleen beperkt door het aantal services dat op elke laag is toegestaan. U bijvoorbeeld maximaal 12 services maken op de basislaag en nog eens 12 services op de S1-laag binnen hetzelfde abonnement. Zie [Een SKU of laag kiezen voor Azure Cognitive Search](../articles/search/search-sku-tier.md)voor meer informatie over lagen.

Maximale servicelimieten kunnen op verzoek worden verhoogd. Neem contact op met Azure Support als u meer services binnen hetzelfde abonnement nodig hebt.

| Resource            | Gratis<sup>1</sup> | Basic | S1  | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Maximale diensten    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Maximale schaal in zoekeenheden (SU)<sup>2</sup> |N.v.t. |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> Gratis is gebaseerd op gedeelde, niet toegewijde, middelen. Scale-up wordt niet ondersteund op gedeelde resources.

<sup>2</sup> Zoekeenheden zijn factureringseenheden, toegewezen als *een replica* of een *partitie.* U hebt beide resources nodig voor opslag-, indexerings- en querybewerkingen. Zie [Resourceniveaus schalen voor query- en indexworkloads voor](../articles/search/search-capacity-planning.md)meer informatie over SU-berekeningen. 