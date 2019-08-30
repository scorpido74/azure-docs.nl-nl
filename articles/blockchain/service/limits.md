---
title: Limieten voor Azure Block Chain
description: Overzicht van de service-en functionele limieten in azure Block Chain Service
services: azure-blockchain
keywords: Block Chain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: aeed84f19da3843d043eafef9d7444661901c53e
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147134"
---
# <a name="limits-in-azure-blockchain-service"></a>Limieten in azure Block Chain Service

De Azure Block Chain-service heeft service-en functionele limieten, zoals het aantal knoop punten dat een lid kan hebben, consortium beperkingen en opslag bedragen.

## <a name="pricing-tier"></a>Prijscategorie

De maximum limieten voor trans acties en validatie knooppunten zijn afhankelijk van of u de Azure Block Chain-Service inricht op basis van de prijs categorie Basic of Standard.

| Prijscategorie | Maximum aantal transactie knooppunten | Maxi maal aantal validatie knooppunten |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

Het wijzigen van de prijs categorie tussen basis en standaard nadat het maken van een lid is niet ondersteund.

## <a name="storage-capacity"></a>Opslagcapaciteit

De maximale hoeveelheid opslag die per knoop punt voor grootboek gegevens en logboeken kan worden gebruikt, is 1,8 terabyte.

Het verminderen van de omvang van het groot boek en de logboek opslag wordt niet ondersteund.

## <a name="consortium-limits"></a>Consortium limieten

* De **namen van consortiums en leden moeten uniek zijn** van andere consortium-en lidnamen in de Azure Block Chain-service.

* **De namen van leden en consortiums kunnen niet worden gewijzigd**

* **Alle leden in een consortium moeten dezelfde prijs categorie hebben**

* **Alle leden die deel uitmaken van een consortium, moeten zich in dezelfde regio bevinden**

    Het eerste lid dat is gemaakt in een consortium, bepaalt de regio. Uitgenodigde leden van het consortium moeten zich in dezelfde regio bevinden als het eerste lid. Als u alle leden beperkt tot dezelfde regio, zorgt u ervoor dat netwerk consensus geen negatieve gevolgen heeft.

* **Een consortium moet ten minste één beheerder hebben**

    Als er slechts één beheerder in een consortium is, kunnen ze zich niet van het consortium verwijderen of hun lid verwijderen totdat een andere beheerder wordt toegevoegd of bevorderd in het consortium.

* **Leden die uit het consortium zijn verwijderd, kunnen niet opnieuw worden toegevoegd**

    Ze moeten daarom opnieuw worden uitgenodigd om lid te worden van het consortium en een nieuw lid te maken. De bestaande leden bron worden niet verwijderd om historische trans acties te behouden.

* **Alle leden van een consortium moeten dezelfde grootboek versie gebruiken**

    Zie [patches, updates en versies](ledger-versions.md)voor meer informatie over de patches, updates en grootboek versies die beschikbaar zijn in de Azure Block Chain-service.

## <a name="next-steps"></a>Volgende stappen

* [Patches, updates en versies](ledger-versions.md)
