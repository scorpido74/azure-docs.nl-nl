---
title: Grootten voor Linux VM in azure
description: Geeft een lijst van de verschillende beschik bare grootten voor virtuele Linux-machines in Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 21351654f01127acb5fe712021ceebb31b020bdc
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484941"
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Grootten voor virtuele Linux-machines in azure

In dit artikel worden de beschik bare grootten en opties voor de virtuele machines van Azure beschreven die u kunt gebruiken om uw Linux-apps en-workloads uit te voeren. Het biedt ook overwegingen bij de implementatie om te weten wanneer u van plan bent deze resources te gebruiken. Dit artikel is ook beschikbaar voor [virtuele Windows-machines](../windows/sizes.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json).

| Type | Grootten | Beschrijving |
|------|-------|-------------|
| [Algemeen doel](../sizes-general.md)   | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, dv2, Av2, DC  | Evenwichtige CPU-geheugen verhouding. Ideaal voor testen en ontwikkelen, kleine tot middel grote data bases en webservers met weinig of gemiddeld verkeer. |
| [Geoptimaliseerde rekenkracht](../sizes-compute.md) | Fsv2 | Hoge CPU-geheugen verhouding. Geschikt voor webservers met gemiddeld verkeer, netwerk apparaten, batch processen en toepassings servers. |
| [Geoptimaliseerd geheugen](../sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Mv2, M, DSv2, dv2 | Hoge geheugen-naar-CPU-verhouding. Ideaal voor relationele database servers, gemiddeld tot grote caches en analyse in het geheugen.                 |
| [Geoptimaliseerde opslag](../sizes-storage.md) | Lsv2 | Hoge schijf doorvoer en IO ideaal voor Big Data, SQL, NoSQL data bases, data warehousing en grote transactionele data bases.  |
| [GPU](../sizes-gpu.md) | NC, NCv2, NCv3, ND, NDv2 (preview), NV, NVv3, NVv4 | Gespecialiseerde virtuele machines gericht op zware grafische rendering en video bewerking, en model training en demijnen (ND) met diep gaande lessen. Beschikbaar met één of meerdere Gpu's. |
| [Krachtig rekenvermogen](../sizes-hpc.md) | HB, HC,  H | Onze snelste en krach tigste virtuele CPU-machines met optionele netwerk interfaces (RDMA) met hoge door voer. |

- Zie [virtual machines prijzen](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux)voor meer informatie over de prijzen van de verschillende grootten. 
- Zie [producten beschikbaar per regio](https://azure.microsoft.com/regions/services/)voor beschik BAARHEID van VM-grootten in azure-regio's.
- Zie [Azure-abonnement en service limieten, quota's en beperkingen](../../azure-subscription-service-limits.md)voor algemene limieten voor virtuele Azure-machines.
- Meer informatie over hoe [Azure Compute units (ACU)](../acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.

## <a name="rest-api"></a>REST-API

Zie het volgende voor informatie over het gebruik van de REST API om te zoeken naar VM-grootten:

- [Beschik bare grootten van virtuele machines weer geven voor het wijzigen van de grootte](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Beschik bare grootten van virtuele machines voor een abonnement weer geven](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Beschik bare grootten van virtuele machines in een beschikbaarheidsset weer geven](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Meer informatie over hoe [Azure Compute units (ACU)](../acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.

## <a name="benchmark-scores"></a>Benchmarkscores

Meer informatie over reken prestaties voor Linux-Vm's met behulp van de [Coopmerking-benchmark scores](compute-benchmark-scores.md).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de verschillende beschik bare VM-grootten:

- [Algemeen doel](../sizes-general.md)
- [Geoptimaliseerde rekenkracht](../sizes-compute.md)
- [Geoptimaliseerd geheugen](../sizes-memory.md)
- [Geoptimaliseerde opslag](../sizes-storage.md)
- [GPU](../sizes-gpu.md)
- [Krachtig rekenvermogen](../sizes-hpc.md)
- Controleer de [vorige generatie](../sizes-previous-gen.md) pagina voor een standaard, Dv1 (D1-4 en D11-14 v1) en A8-A11-serie