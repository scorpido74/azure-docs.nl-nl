---
title: VM-grootten kiezen voor Pools-Azure Batch | Microsoft Docs
description: Kiezen uit de beschik bare VM-grootten voor reken knooppunten in Azure Batch Pools
services: batch
documentationcenter: ''
author: ju-shim
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2019
ms.author: jushiman
ms.custom: seodec18
ms.openlocfilehash: 34ab09f7d8d47804992b8ef6864bfea60d1c9b4d
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76026605"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Een VM-grootte voor reken knooppunten in een Azure Batch groep kiezen

Wanneer u een knooppunt grootte voor een Azure Batch groep selecteert, kunt u kiezen uit vrijwel alle VM-grootten die beschikbaar zijn in Azure. Azure biedt tal van grootten voor Linux-en Windows-Vm's voor verschillende werk belastingen.

Er zijn enkele uitzonde ringen en beperkingen voor het kiezen van een VM-grootte:

* Sommige VM-reeksen of VM-grootten worden niet ondersteund in batch.
* Sommige VM-grootten zijn beperkt en moeten specifiek worden ingeschakeld voordat ze kunnen worden toegewezen.

## <a name="supported-vm-series-and-sizes"></a>Ondersteunde VM-reeksen en-groottes

### <a name="pools-in-virtual-machine-configuration"></a>Groepen in de configuratie van de virtuele machine

Batch-Pools in de virtuele-machine configuratie ondersteunen bijna alle VM-grootten ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). Raadpleeg de volgende tabel voor meer informatie over ondersteunde grootten en beperkingen.

Alle promotie-of preview-VM-grootten die niet worden vermeld, zijn niet gegarandeerd voor ondersteuning.

| VM-serie  | Ondersteunde grootten | Groeps toewijzings modus Batch-account<sup>1</sup> |
|------------|---------|-----------------|
| Basic A-serie | Alle grootten *behalve* Basic_A0 (a0) | Alle |
| A-serie | Alle grootten *behalve* Standard_A0 | Alle |
| Av2-serie | Alle grootten | Alle |
| B-serie | Geen | Niet beschikbaar |
| DC-serie | Geen | Niet beschikbaar |
| Dv2, DSv2-serie | Alle grootten | Alle |
| Dv3, Dsv3-serie | Alle grootten | Alle |
| Ev3, Esv3-serie | Alle grootten | Alle |
| Fsv2-reeks | Alle grootten | Alle |
| H-serie | Alle grootten | Alle |
| HB-serie<sup>2</sup> | Alle grootten | Alle |
| HC-serie<sup>2</sup> | Alle grootten | Alle |
| Ls-serie | Alle grootten | Alle |
| Lsv2-serie | Geen | Niet beschikbaar |
| M-serie | Standard_M64ms (alleen met lage prioriteit), Standard_M128s (alleen lage prioriteit) | Alle |
| Mv2-serie | Geen | Niet beschikbaar |
| NC-serie | Alle grootten | Alle |
| NCv2-serie<sup>2</sup> | Alle grootten | Alle |
| NCv3-serie<sup>2</sup> | Alle grootten | Alle |
| ND-serie<sup>2</sup> | Alle grootten | Alle |
| NDv2-serie | Alle grootten | Modus Gebruikersabonnement |
| NV-serie | Alle grootten | Alle |
| NVv3-serie | Geen | Niet beschikbaar |
| SAP HANA | Geen | Niet beschikbaar |

<sup>1</sup> sommige nieuwere VM-reeksen worden in eerste instantie gedeeltelijk ondersteund. Deze VM-serie kan worden toegewezen door batch-accounts waarvoor de **groeps toewijzings modus** is ingesteld op **gebruikers abonnement**. Zie [batch-accounts beheren](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode) voor meer informatie over de configuratie van een batch-account. Zie [quota's en limieten](batch-quota-limit.md) voor meer informatie over het aanvragen van een quotum voor deze gedeeltelijk ondersteunde VM-serie voor batch-accounts voor **gebruikers abonnementen** .  

<sup>2</sup> deze VM-grootten kunnen worden toegewezen in batch-Pools in de configuratie van virtuele machines, maar u moet een specifieke [quota verhoging](batch-quota-limit.md#increase-a-quota)aanvragen.

### <a name="pools-in-cloud-service-configuration"></a>Groepen in de Cloud service configuratie

Batch-Pools in de Cloud service configuratie ondersteunen alle [VM-grootten voor Cloud Services](../cloud-services/cloud-services-sizes-specs.md) **, met uitzonde ring** van de volgende opties:

| VM-serie  | Niet-ondersteunde grootten |
|------------|-------------------|
| A-serie   | Extra klein       |
| Av2-serie | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Overwegingen over grootte

* **Toepassings vereisten** : Houd rekening met de kenmerken en vereisten van de toepassing die u op de knoop punten uitvoert. Houd ook rekening met het feit of bijvoorbeeld de toepassing meerdere threads heeft en hoeveel geheugen deze gebruikt. Zo kunt u gemakkelijker de meest geschikte en voordeligste knooppuntgrootte bepalen. Voor [mpi-werk belastingen](batch-mpi.md) of CUDA-toepassingen met meerdere instanties kunt u respectievelijk gespecialiseerde VM-grootten met [HPC](../virtual-machines/linux/sizes-hpc.md) of [GPU-](../virtual-machines/linux/sizes-gpu.md) capaciteit overwegen. (Zie [met RDMA compatibele of GPU-compatibele instanties in batch-Pools gebruiken](batch-pool-compute-intensive-sizes.md).)

* **Taken per knoop punt** : het is gebruikelijk om een knooppunt grootte te selecteren, ervan uitgaande dat er één taak tegelijk op een knoop punt wordt uitgevoerd. Het kan echter handig zijn om meerdere taken (en dus meerdere toepassings exemplaren) parallel op reken knooppunten [uit te voeren](batch-parallel-node-tasks.md) tijdens de taak uitvoering. In dit geval is het gebruikelijk om een multicore-knooppunt grootte te kiezen die geschikt is voor de verhoogde vraag van parallelle taak uitvoering.

* **Laad niveaus voor verschillende taken** : alle knoop punten in een groep hebben dezelfde grootte. Als u toepassingen met verschillende systeemvereisten en/of workloadniveaus wilt uitvoeren, moet u afzonderlijke pools maken.

* **Beschik baarheid van regio** : een VM-serie of-grootte is mogelijk niet beschikbaar in de regio's waar u uw batch-accounts maakt. Zie [producten beschikbaar per regio](https://azure.microsoft.com/regions/services/)om te controleren of er een grootte beschikbaar is.

* **Quota's** : de [kernen Quota's](batch-quota-limit.md#resource-quotas) in uw batch-account kunnen het aantal knoop punten van een bepaalde grootte beperken die u aan een batch-pool kunt toevoegen. Raadpleeg [dit artikel](batch-quota-limit.md#increase-a-quota)als u een quota verhoging wilt aanvragen. 

* **Groeps configuratie** : in het algemeen hebt u meer opties voor VM-grootte wanneer u een groep in de virtuele-machine configuratie maakt, vergeleken met de configuratie van de Cloud service.

## <a name="next-steps"></a>Volgende stappen

* Zie [grootschalige parallelle reken oplossingen ontwikkelen met batch](batch-api-basics.md)voor een uitgebreid overzicht van batch.
* Zie voor meer informatie over het gebruik van Compute-intensieve VM-grootten [RDMA-compatibele of GPU-compatibele instanties in batch-Pools gebruiken](batch-pool-compute-intensive-sizes.md).
