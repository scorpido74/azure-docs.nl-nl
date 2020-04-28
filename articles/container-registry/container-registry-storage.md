---
title: Container installatie kopie opslag
description: Meer informatie over hoe uw docker-container installatie kopieën worden opgeslagen in Azure Container Registry, met inbegrip van beveiliging, redundantie en capaciteit.
ms.topic: article
ms.date: 03/21/2018
ms.openlocfilehash: f66c3dd95edfe5035c46857cb6f9aa59d8a6a0e1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74456203"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Opslag van container installatie kopieën in Azure Container Registry

Elke [basis-, standaard-en Premium](container-registry-skus.md) Azure container Registry-voor delen van geavanceerde Azure-opslag functies zoals versleuteling op rest voor afbeeldings gegevens beveiliging en geo-redundantie voor de beveiliging van de afbeeldings gegevens. In de volgende secties worden de functies en limieten beschreven van installatie kopie opslag in Azure Container Registry (ACR).

## <a name="encryption-at-rest"></a>Versleuteling-at-rest

Alle container installatie kopieën in het REGI ster worden op rest versleuteld. Azure versleutelt automatisch een afbeelding voordat deze wordt opgeslagen en ontsleutelt deze op het moment dat u of uw toepassingen en services de installatie kopie ophalen.

## <a name="geo-redundant-storage"></a>Geografisch redundante opslag

Azure gebruikt een geografisch redundant opslag schema voor de beveiliging tegen verlies van uw container installatie kopieën. Azure Container Registry repliceert uw container installatie kopieën automatisch naar meerdere geografisch ongeoorloofde data centers, waardoor er geen verlies kan optreden bij een regionale opslag fout.

## <a name="geo-replication"></a>Geo-replicatie

Overweeg het gebruik van de functie voor [geo-replicatie](container-registry-geo-replication.md) van Premium-registers voor scenario's die nog meer zekerheid voor hoge Beschik baarheid vereisen. Geo-replicatie biedt beveiliging tegen verlies van toegang tot uw REGI ster in het geval van een *totale* regionale fout, niet alleen een opslag fout. Geo-replicatie biedt ook andere voor delen, zoals de opslag van installatie kopieën van het netwerk, voor snellere pushes en pull-bewerkingen in gedistribueerde ontwikkel-of implementatie scenario's.

## <a name="image-limits"></a>Limieten voor afbeeldingen

In de volgende tabel worden de container installatie kopie en opslag limieten voor Azure-container registers beschreven.

| Resource | Limiet |
| -------- | :---- |
| Opslagplaatsen | Geen limiet |
| Installatiekopieën | Geen limiet |
| Lagen | Geen limiet |
| Tags | Geen limiet|
| Storage | 5 TB |

Zeer grote aantallen opslag plaatsen en tags kunnen de prestaties van uw REGI ster beïnvloeden. Verwijder regel matig ongebruikte opslag plaatsen, tags en installatie kopieën als onderdeel van de onderhouds routine voor het REGI ster. Verwijderde register resources zoals opslag plaatsen, afbeeldingen en tags *kunnen niet* worden hersteld na verwijdering. Zie [container installatie kopieën verwijderen in azure container Registry](container-registry-delete.md)voor meer informatie over het verwijderen van register resources.

## <a name="storage-cost"></a>Opslagkosten

Zie [Azure container Registry prijzen][pricing]voor volledige informatie over prijzen.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure container Registry sku's](container-registry-skus.md)voor meer informatie over de verschillende Azure container Registry Sku's (Basic, Standard en Premium).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
