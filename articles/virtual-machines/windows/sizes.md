---
title: Windows VM-grootten in azure | Microsoft Docs
description: Geeft een lijst van de verschillende beschik bare grootten voor virtuele Windows-machines in Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: aabf0d30-04eb-4d34-b44a-69f8bfb84f22
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/06/2019
ms.author: jonbeck
ms.openlocfilehash: 0553255cdcdc37aad4e996e2c6cb22d17e336ef3
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721513"
---
# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Grootten voor virtuele Windows-machines in azure

In dit artikel worden de beschik bare grootten en opties voor de virtuele machines van Azure beschreven die u kunt gebruiken om uw Windows-apps en-workloads uit te voeren. Het biedt ook overwegingen bij de implementatie om te weten wanneer u van plan bent deze resources te gebruiken.  Dit artikel is ook beschikbaar voor [virtuele Linux-machines](../linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


| Type                     | Grootten           |    Beschrijving       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Algemeen doel](sizes-general.md)          | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, dv2, Av2, DC  | Evenwichtige CPU-geheugenverhouding. Ideaal voor testen en ontwikkelen, kleine tot middelgrote databases en webservers met weinig of gemiddeld verkeer. |
| [Geoptimaliseerde rekenkracht](sizes-compute.md)        | Fsv2           | Hoge CPU-geheugenverhouding. Goed voor webservers met gemiddeld verkeer, netwerkapparatuur, batchprocessen en toepassingsservers.        |
| [Geoptimaliseerd geheugen](sizes-memory.md)         | Esv3, Ev3, Easv4, Eav4, Mv2, M, DSv2, dv2  | Hoge geheugen-naar-CPU-verhouding. Uiterst geschikt voor relationele-databaseservers, middelgrote tot grote caches en analysefuncties in het geheugen.                 |
| [Geoptimaliseerde opslag](sizes-storage.md)        | Lsv2                | Hoge schijf doorvoer en IO ideaal voor Big Data, SQL, NoSQL data bases, data warehousing en grote transactionele data bases.  |
| [GPU](sizes-gpu.md)            | NC, NCv2, NCv3, ND, NDv2 (preview), NV, NVv3 | Gespecialiseerde virtuele machines gericht op zware grafische rendering en video bewerking, en model training en demijnen (ND) met diep gaande lessen. Beschikbaar met één of meerdere Gpu's.       |
| [Krachtig rekenvermogen](sizes-hpc.md) | HB, HC, H | Onze snelste en krachtigste CPU-virtuele machines met optionele netwerkinterfaces (RDMA) voor hoge doorvoer. |


<br> 

- Zie [virtual machines prijzen](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)voor meer informatie over de prijzen van de verschillende grootten. 
- Zie [Azure-abonnement en service limieten, quota's en beperkingen](../../azure-subscription-service-limits.md)voor algemene limieten voor virtuele Azure-machines.
- Opslagkosten worden afzonderlijk berekend op basis van het aantal pagina's dat in het opslagaccount is gebruikt. [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie.
- Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.


## <a name="rest-api"></a>REST-API

Zie het volgende voor informatie over het gebruik van de REST API om te zoeken naar VM-grootten:

- [Beschik bare grootten van virtuele machines weer geven voor het wijzigen van de grootte](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Beschik bare grootten van virtuele machines voor een abonnement weer geven](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Beschik bare grootten van virtuele machines in een beschikbaarheidsset weer geven](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.

## <a name="benchmark-scores"></a>Benchmarkscores

Meer informatie over reken prestaties voor Windows-Vm's met behulp van de [Coopmerking-benchmark scores](compute-benchmark-scores.md).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de verschillende beschik bare VM-grootten:
- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerde rekenkracht](sizes-compute.md)
- [Geoptimaliseerd geheugen](../virtual-machines-windows-sizes-memory.md)
- [Geoptimaliseerde opslag](../virtual-machines-windows-sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- Controleer de [vorige generatie](sizes-previous-gen.md) pagina voor een standaard, Dv1 (D1-4 en D11-14 v1) en A8-A11-serie




