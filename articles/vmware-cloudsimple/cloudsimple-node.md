---
title: Overzicht van Azure VMware-oplossing per CloudSimple-knoop punten
description: Meer informatie over CloudSimple-knoop punten en-concepten.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 729b832c068dcd401fa6e9d2f4af9193d3859a83
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71845551"
---
# <a name="cloudsimple-nodes-overview"></a>Overzicht van CloudSimple-knoop punten

Knoop punten zijn de bouw stenen van een Privécloud. Een knoop punt is:

* Een toegewezen bare-metal Compute-host waarop een VMware ESXi Hyper Visor is geïnstalleerd  
* Een reken eenheid die u kunt aanschaffen of reserveren voor het maken van persoonlijke Clouds
* Beschikbaar voor aankoop of reserve ring in een regio waar de CloudSimple-service beschikbaar is

U kunt een Privécloud maken op basis van de aangeschafte knoop punten. Als u een Privécloud wilt maken, hebt u Mini maal drie knoop punten van dezelfde SKU nodig. Als u een Privécloud wilt uitbreiden, voegt u extra knoop punten toe.  U kunt knoop punten toevoegen aan een bestaand cluster of een nieuw cluster maken door de knoop punten in de Azure Portal te kopen en ze te koppelen aan de CloudSimple-service.  Alle aangeschafte knoop punten zijn zichtbaar in de CloudSimple-service.  

## <a name="purchased-nodes"></a>Aangeschafte knoop punten

Aangeschafte knoop punten bieden een betalen naar gebruik-capaciteit. Met het aanschaffen van knoop punten kunt u uw VMware-cluster op aanvraag snel schalen. U kunt indien nodig knoop punten toevoegen of een aangeschaft knoop punt verwijderen om uw VMware-cluster te schalen. Aangeschafte knoop punten worden maandelijks gefactureerd en worden in rekening gebracht op het moment dat ze worden gekocht.

* Als u betaalt voor uw Azure-abonnement per credit card, wordt de kaart onmiddellijk gefactureerd.
* Als u per factuur factureert, worden de kosten op uw volgende factuur weer gegeven.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>VMware-oplossing op CloudSimple knoop punten SKU

De volgende typen knoop punten zijn beschikbaar voor aankoop of reserve ring.

| SKU           | CS28-knoop punt                 | CS36-knoop punt                 | CS36m-knoop punt                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Regio        | US - oost, US - west            | US - oost, US - west            | Europa -west                 |
| CPU           | 2x 2,2 GHz, 28 kernen (56 HT) | 2x 2,3 GHz, 36 kern geheugens (72 HT) | 2x 2,3 GHz, 36 kern geheugens (72 HT) |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Cacheschijf    | 1,6-TB NVMe                 | 3,2-TB NVMe                 | 3,2-TB NVMe                 |
| Capaciteits schijf | 5,625 TB onbewerkt                | 11,25 TB onbewerkt                | 15,36 TB onbewerkt                |
| Opslagtype  | Alle Flash                   | Alle Flash                   | Alle Flash                   |

## <a name="limits"></a>Limieten

De volgende limieten voor knoop punten zijn van toepassing op persoonlijke Clouds.

| Resource | Limiet |
|----------|-------|
| Minimum aantal knoop punten voor het maken van een Privécloud | 3 |
| Maximum aantal knoop punten in een cluster in een Privécloud | 16 |
| Maximum aantal knoop punten in een Privécloud | 64 |
| Minimum aantal knoop punten op een nieuw cluster | 3 |

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [aanschaffen van knoop punten](create-nodes.md)
* Meer informatie over [persoonlijke Clouds](cloudsimple-private-cloud.md)
