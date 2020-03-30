---
title: Azure VMware Solution by CloudSimple - Overzicht knooppunten
description: Meer informatie over CloudSimple-knooppunten en -concepten.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65afe26a98a53b00b72a1ea2b49799db2049b727
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024922"
---
# <a name="cloudsimple-nodes-overview"></a>Overzicht van CloudSimple-knooppunten

Knooppunten zijn de bouwstenen van een Private Cloud. Een knooppunt is:

* Een speciale bare metal compute host waar een VMware ESXi hypervisor is geïnstalleerd  
* Een rekeneenheid die u inrichten of reserveren om Private Clouds te maken
* Beschikbaar voor inrichten of reserveren in een regio waar de CloudSimple-service beschikbaar is

U maakt een Private Cloud op basis van de ingerichte knooppunten. Als u een Private Cloud wilt maken, hebt u minimaal drie knooppunten van dezelfde SKU nodig. Als u een private cloud wilt uitbreiden, voegt u extra knooppunten toe.  U knooppunten toevoegen aan een bestaand cluster of een nieuw cluster maken door knooppunten in de Azure-portal in te richten en deze te koppelen aan de CloudSimple-service.  Alle ingerichte knooppunten zijn zichtbaar onder de CloudSimple-service.  

## <a name="provisioned-nodes"></a>Ingerichte knooppunten

Ingerichte knooppunten bieden pay-as-you-go-capaciteit. Met het inrichten van knooppunten u uw VMware-cluster snel op aanvraag schalen. U knooppunten toevoegen als dat nodig is of een ingerichte node verwijderen om uw VMware-cluster af te schalen. Ingerichte knooppunten worden maandelijks gefactureerd en in rekening gebracht bij het abonnement waar ze zijn ingericht.

* Als u met een creditcard voor uw Azure-abonnement betaalt, wordt de kaart onmiddellijk in rekening gebracht.
* Als u per factuur wordt gefactureerd, worden de kosten weergegeven op uw volgende factuur.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>VMware-oplossing door CloudSimple-knooppunten SKU

De volgende typen knooppunten zijn beschikbaar voor inrichten of reserveren.

| SKU           | CS28 - Knooppunt                 | CS36 - Knooppunt                 | CS36m - Knooppunt                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Regio        | Oost-VS, West-VS            | Oost-VS, West-VS            | Europa -west                 |
| CPU           | 2x2,2 GHz, 28 cores (56 HT) | 2x2,3 GHz, 36 cores (72 HT) | 2x2,3 GHz, 36 cores (72 HT) |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Cacheschijf    | 1,6 TB NVMe                 | 3,2 TB NVMe                 | 3,2 TB NVMe                 |
| Capaciteitsschijf | 5.625 TB Raw                | 11,25 TB Raw                | 15,36 TB Raw                |
| Opslagtype  | Alle Flash                   | Alle Flash                   | Alle Flash                   |

## <a name="limits"></a>Limieten

De volgende knooppuntlimieten zijn van toepassing op Privéclouds.

| Resource | Limiet |
|----------|-------|
| Minimum aantal knooppunten om een private cloud te maken | 3 |
| Maximaal aantal knooppunten in een cluster op een private cloud | 16 |
| Maximaal aantal knooppunten in een private cloud | 64 |
| Minimumaantal knooppunten op een nieuw cluster | 3 |

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [inrichten van knooppunten](create-nodes.md)
* Meer informatie over [privéwolken](cloudsimple-private-cloud.md)
