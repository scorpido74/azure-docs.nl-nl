---
title: Grootten voor Linux VM in azure
description: Geeft een lijst van de verschillende beschik bare grootten voor virtuele Linux-machines in Azure.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: eef0304248c7cc9a1bf63e06247f86d607afce46
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86510103"
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Sizes for Linux virtual machines in Azure

In dit artikel worden de beschik bare grootten en opties voor de virtuele machines van Azure beschreven die u kunt gebruiken om uw Linux-apps en-workloads uit te voeren. Het biedt ook overwegingen bij de implementatie om te weten wanneer u van plan bent deze resources te gebruiken. Dit artikel is ook beschikbaar voor [virtuele Windows-machines](../windows/sizes.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json).

| Type | Grootten | Beschrijving |
|------|-------|-------------|
| [Algemeen gebruik](../sizes-general.md)   | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, dv2, Av2, DC, DCv2, Dv4, Dsv4, Ddv4, Ddsv4  | Evenwichtige CPU-geheugen verhouding. Dit is ideaal voor testen en ontwikkelen, voor kleine tot middelgrote databases, en webservers met weinig tot gemiddeld verkeer. |
| [Geoptimaliseerde rekenkracht](../sizes-compute.md) | Fsv2 | Hoge CPU-geheugen verhouding. Geschikt voor webservers met gemiddeld verkeer, netwerk apparaten, batch processen en toepassings servers. |
| [Geoptimaliseerd voor geheugen](../sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Ev4, Esv4, Edv4, Edsv4, Mv2, M, DSv2, dv2 | Hoge geheugen-naar-CPU-verhouding. Zeer geschikt voor relationele databaseservers, middelgrote tot grote caches, en analysefuncties in het geheugen.                 |
| [Geoptimaliseerd voor opslag](../sizes-storage.md) | Lsv2 | Hoge schijf doorvoer en IO ideaal voor Big Data, SQL, NoSQL data bases, data warehousing en grote transactionele data bases.  |
| [GPU](../sizes-gpu.md) | NC, NCv2, NCv3, ND, NDv2 (preview), NV, NVv3, NVv4 | Gespecialiseerde virtuele machines gericht op zware grafische rendering en video bewerking, en model training en demijnen (ND) met diep gaande lessen. Beschikbaar met één of meerdere GPU's. |
| [Krachtig rekenvermogen](../sizes-hpc.md) | HB, HBv2, HC, H | Onze snelste en krach tigste virtuele CPU-machines met optionele netwerk interfaces (RDMA) met hoge door voer. |

- Zie [virtual machines prijzen](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux)voor meer informatie over de prijzen van de verschillende grootten. 
- Zie [producten beschikbaar per regio](https://azure.microsoft.com/regions/services/)voor beschik BAARHEID van VM-grootten in azure-regio's.
- Zie [Azure-abonnement en service limieten, quota's en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md)voor algemene limieten voor virtuele Azure-machines.
- Meer informatie over hoe [Azure Compute units (ACU)](../acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.

## <a name="rest-api"></a>REST-API

Zie het volgende voor informatie over het gebruik van de REST API om te zoeken naar VM-grootten:

- [Beschik bare grootten van virtuele machines weer geven voor het wijzigen van de grootte](/rest/api/compute/virtualmachines/listavailablesizes)
- [Beschik bare grootten van virtuele machines voor een abonnement weer geven](/rest/api/compute/resourceskus/list)
- [Beschik bare grootten van virtuele machines in een beschikbaarheidsset weer geven](/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Meer informatie over hoe [Azure Compute units (ACU)](../acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.

## <a name="benchmark-scores"></a>Benchmarkscores

Meer informatie over reken prestaties voor Linux-Vm's met behulp van de [Coopmerking-benchmark scores](compute-benchmark-scores.md).

## <a name="manage-costs"></a>Kosten beheren

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de verschillende beschik bare VM-grootten:

- [Algemeen gebruik](../sizes-general.md)
- [Geoptimaliseerde rekenkracht](../sizes-compute.md)
- [Geoptimaliseerd voor geheugen](../sizes-memory.md)
- [Geoptimaliseerd voor opslag](../sizes-storage.md)
- [GPU](../sizes-gpu.md)
- [Krachtig rekenvermogen](../sizes-hpc.md)
- Controleer de [vorige generatie](../sizes-previous-gen.md) pagina voor een standaard, Dv1 (D1-4 en D11-14 v1) en A8-A11-serie
