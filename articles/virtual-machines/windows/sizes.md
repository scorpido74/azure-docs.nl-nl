---
title: Windows VM-formaten in Azure
description: Geeft een lijst van de verschillende formaten die beschikbaar zijn voor virtuele Windows-machines in Azure.
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
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 398575cc5c3dea96aa644533eb6ce8a262d1981c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164488"
---
# <a name="sizes-for-windows-virtual-machines-in-azure"></a>Grootten voor Windows Virtual Machines in Azure

In dit artikel worden de beschikbare formaten en opties beschreven voor de virtuele Azure-machines die u gebruiken om uw Windows-apps en -workloads uit te voeren. Het biedt ook overwegingen voor implementatie om rekening te houden met wanneer u van plan bent deze resources te gebruiken.  Dit artikel is ook beschikbaar voor [Linux virtuele machines.](../linux/sizes.md)

| Type | Grootten | Beschrijving |
|------|-------|-------------|
| [Algemeen doel](../sizes-general.md) | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC, DCv2 | Gebalanceerde CPU-geheugenverhouding. Dit is ideaal voor testen en ontwikkelen, voor kleine tot middelgrote databases, en webservers met weinig tot gemiddeld verkeer. |
| [Geoptimaliseerde rekenkracht](../sizes-compute.md) | Fsv2 | Hoge CPU-geheugenverhouding. Goed voor medium traffic webservers, netwerkapparaten, batchprocessen en applicatieservers. |
| [Geoptimaliseerd geheugen](../sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Mv2, M, DSv2, Dv2 | Hoge geheugen-cpu-verhouding. Zeer geschikt voor relationele databaseservers, middelgrote tot grote caches, en analysefuncties in het geheugen. |
| [Geoptimaliseerde opslag](../sizes-storage.md)  | Lsv2 Lsv2 | Hoge schijfdoorvoer en IO ideaal voor Big Data, SQL, NoSQL-databases, datawarehousing en grote transactionele databases.  |
| [GPU](../sizes-gpu.md) | NC, NCv2, NCv3, ND, NDv2 (Preview), NV, NVv3, NVv4 | Gespecialiseerde virtuele machines gericht op zware grafische rendering en videobewerking, evenals modeltraining en -gevolgtrekking (ND) met deep learning. Verkrijgbaar met enkele of meerdere GPU's. |
| [Krachtig rekenvermogen](../sizes-hpc.md) | HB, HBv2, HC, H | Onze snelste en krachtigste CPU virtuele machines met optionele high-throughput netwerk interfaces (RDMA). |

- Zie [Virtuele machines prijzen voor](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)informatie over de prijzen van de verschillende formaten.
- Zie [Azure-abonnements- en servicelimieten, quota en beperkingen](../../azure-subscription-service-limits.md)voor algemene limieten voor Azure-VM's.
- Opslagkosten worden afzonderlijk berekend op basis van het aantal pagina's dat in het opslagaccount is gebruikt. Voor meer informatie, [Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/).
- Meer informatie over hoe [Azure compute units (ACU)](../acu.md) u kunnen helpen bij het vergelijken van rekenprestaties in Azure SKU's.

## <a name="rest-api"></a>REST API

Zie het volgende voor informatie over het gebruik van de REST API voor query's voor VM-grootten:

- [Beschikbare virtuele machineformaten weergeven voor het wijzigen van het formaat](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Beschikbare virtuele machineformaten voor een abonnement weergeven](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Beschikbare virtuele machineformaten weergeven in een beschikbaarheidsset](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Meer informatie over hoe [Azure compute units (ACU)](../acu.md) u kunnen helpen bij het vergelijken van rekenprestaties in Azure SKU's.

## <a name="benchmark-scores"></a>Benchmarkscores

Meer informatie over rekenprestaties voor Windows VM's met behulp van de [CoreMark-benchmarkscores](compute-benchmark-scores.md).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de verschillende VM-formaten die beschikbaar zijn:

- [Algemeen doel](../sizes-general.md)
- [Geoptimaliseerde rekenkracht](../sizes-compute.md)
- [Geoptimaliseerd geheugen](../sizes-memory.md)
- [Geoptimaliseerde opslag](../sizes-storage.md)
- [Geoptimaliseerde GPU](../sizes-gpu.md)
- [Krachtig rekenvermogen](../sizes-hpc.md)
- Controleer de pagina van de [vorige generatie](../sizes-previous-gen.md) voor A Standard, Dv1 (D1-4 en D11-14 v1) en A8-A11-serie
