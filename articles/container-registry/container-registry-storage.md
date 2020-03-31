---
title: Opslag van containerafbeeldingen
description: Details over hoe uw Docker-containerafbeeldingen worden opgeslagen in Azure Container Registry, inclusief beveiliging, redundantie en capaciteit.
ms.topic: article
ms.date: 03/21/2018
ms.openlocfilehash: f66c3dd95edfe5035c46857cb6f9aa59d8a6a0e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456203"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Opslag van containerafbeeldingen in Azure Container Registry

Elk [Basis-, Standaard- en Premium](container-registry-skus.md) Azure-containerregister profiteert van geavanceerde Azure-opslagfuncties, zoals versleuteling-rust voor beeldgegevensbeveiliging en georedundantie voor beeldgegevensbeveiliging. In de volgende secties worden zowel de functies als de limieten van beeldopslag in Azure Container Registry (ACR) beschreven.

## <a name="encryption-at-rest"></a>Encryptie-at-rest

Alle containerafbeeldingen in uw register worden in rust versleuteld. Azure versleutelt automatisch een afbeelding voordat deze wordt opgeslagen en decodeert deze on-the-fly wanneer u of uw toepassingen en services de afbeelding oprollen.

## <a name="geo-redundant-storage"></a>Geografisch redundante opslag

Azure maakt gebruik van een georedundant opslagschema om te voorkomen dat uw containerafbeeldingen worden verloren. Azure Container Registry repliceert uw containerafbeeldingen automatisch naar meerdere geografisch ver afgelegen datacenters, waardoor het verlies ervan in het geval van een regionale opslagfout wordt voorkomen.

## <a name="geo-replication"></a>Geo-replicatie

Voor scenario's die nog meer betrouwbaarheid met hoge beschikbaarheid vereisen, u overwegen de [georeplicatiefunctie van Premium-registers](container-registry-geo-replication.md) te gebruiken. Geo-replicatie helpt ervoor te zorgen dat u geen toegang meer hebt tot uw register in het geval van een *totale* regionale storing, niet alleen een opslagfout. Geo-replicatie biedt ook andere voordelen, zoals netwerkclose-image-opslag voor snellere pushes en trekt gedistribueerde ontwikkelings- of implementatiescenario's in.

## <a name="image-limits"></a>Afbeeldingslimieten

In de volgende tabel worden de containerafbeeldings- en opslaglimieten beschreven die gelden voor Azure-containerregisters.

| Resource | Limiet |
| -------- | :---- |
| Opslagplaatsen | Geen limiet |
| Installatiekopieën | Geen limiet |
| Lagen | Geen limiet |
| Tags | Geen limiet|
| Storage | 5 TB |

Zeer hoge aantallen repositories en tags kunnen de prestaties van uw register beïnvloeden. Verwijder regelmatig ongebruikte opslagplaatsen, tags en afbeeldingen als onderdeel van uw registeronderhoudsroutine. Verwijderde registerbronnen zoals opslagplaatsen, afbeeldingen en tags *kunnen niet* worden hersteld na verwijdering. Zie [Containerafbeeldingen verwijderen in Azure Container Registry voor](container-registry-delete.md)meer informatie over het verwijderen van registerresources.

## <a name="storage-cost"></a>Opslagkosten

Zie [Azure Container Registry-prijzen][pricing]voor meer informatie over prijzen.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Container Registry SKU's](container-registry-skus.md)voor meer informatie over de verschillende Azure Container Registry SKU's (Basic, Standard, Premium).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
