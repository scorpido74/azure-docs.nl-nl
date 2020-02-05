---
title: Azure VMware Solutions (AVS)-overzicht van knoop punten
description: Meer informatie over AVS-knoop punten en-concepten.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 08d8fc3e6f1f2f83cf3c4fee3fdafb0bd07e336c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024922"
---
# <a name="avs-nodes-overview"></a>Overzicht van AVS-knoop punten

Knoop punten zijn de bouw stenen van een Privécloud. Een knoop punt is:

* Een toegewezen bare-metal Compute-host waarop een VMware ESXi Hyper Visor is geïnstalleerd  
* Een reken eenheid die u kunt aanschaffen of reserveren voor het maken van AVS-persoonlijke Clouds
* Beschikbaar voor aankoop of reserve ring in een regio waar de AVS-service beschikbaar is

U maakt een automatische AVS-Cloud op basis van de aangeschafte knoop punten. Voor het maken van een AVS-Privécloud hebt u Mini maal drie knoop punten van dezelfde SKU nodig. Voeg extra knoop punten toe om een nieuwe AVS-Cloud uit te breiden. U kunt knoop punten toevoegen aan een bestaand cluster of een nieuw cluster maken door de knoop punten in de Azure Portal te kopen en ze te koppelen aan de AVS-service. Alle aangeschafte knoop punten zijn zichtbaar onder de AVS-service. 

## <a name="provisioned-nodes"></a>Ingerichte knoop punten

Ingerichte knoop punten bieden een betalen naar gebruik-capaciteit. Met het inrichten van knoop punten kunt u uw VMware-cluster op aanvraag snel schalen. U kunt indien nodig knoop punten toevoegen of een ingericht knoop punt verwijderen om uw VMware-cluster omlaag te schalen. Ingerichte knoop punten worden maandelijks gefactureerd en worden in rekening gebracht voor het abonnement waar ze zijn ingericht.

* Als u betaalt voor uw Azure-abonnement per credit card, wordt de kaart onmiddellijk gefactureerd.
* Als u per factuur factureert, worden de kosten op uw volgende factuur weer gegeven.

## <a name="vmware-solution-by-avs-nodes-sku"></a>VMware-oplossing op AVS-knoop punten SKU

De volgende typen knoop punten zijn beschikbaar voor inrichting of reserve ring.

| SKU           | CS28-knoop punt                 | CS36-knoop punt                 | CS36m-knoop punt                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Regio        | US - oost, US - west            | US - oost, US - west            | Europa - west                 |
| CPU           | 2x 2,2 GHz, 28 kernen (56 HT) | 2x 2,3 GHz, 36 kern geheugens (72 HT) | 2x 2,3 GHz, 36 kern geheugens (72 HT) |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Cacheschijf    | 1,6-TB NVMe                 | 3,2-TB NVMe                 | 3,2-TB NVMe                 |
| Capaciteits schijf | 5,625 TB onbewerkt                | 11,25 TB onbewerkt                | 15,36 TB onbewerkt                |
| Opslag type  | Alle Flash                   | Alle Flash                   | Alle Flash                   |

## <a name="limits"></a>Beperkingen

De volgende limieten voor knoop punten zijn van toepassing op de AVS-persoonlijke Clouds.

| Bron | Limiet |
|----------|-------|
| Minimum aantal knoop punten voor het maken van een AVS-Privécloud | 3 |
| Maximum aantal knoop punten in een cluster op een Privécloud-Cloud | 16 |
| Maximum aantal knoop punten in een Privécloud in de Cloud | 64 |
| Minimum aantal knoop punten op een nieuw cluster | 3 |

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [aanschaffen van knoop punten](create-nodes.md)
* Informatie over [persoonlijke Clouds van AVS](cloudsimple-private-cloud.md)
* Meer informatie over het [inrichten van knoop punten](create-nodes.md)
* Meer informatie over [persoonlijke Clouds](cloudsimple-private-cloud.md)
