---
title: VM-grootten kiezen voor Pools
description: Kiezen uit de beschik bare VM-grootten voor reken knooppunten in Azure Batch Pools
ms.topic: how-to
ms.date: 09/12/2019
ms.custom: seodec18
ms.openlocfilehash: 2e0d403f405d58c0d7488ac6d0c306be2f2d79ea
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779155"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Een VM-grootte voor reken knooppunten in een Azure Batch groep kiezen

Wanneer u een knooppunt grootte voor een Azure Batch groep selecteert, kunt u kiezen uit vrijwel alle VM-grootten die beschikbaar zijn in Azure. Azure biedt tal van grootten voor Linux-en Windows-Vm's voor verschillende werk belastingen.

Er zijn enkele uitzonde ringen en beperkingen voor het kiezen van een VM-grootte:

* Sommige VM-reeksen of VM-grootten worden niet ondersteund in batch.
* Sommige VM-grootten zijn beperkt en moeten specifiek worden ingeschakeld voordat ze kunnen worden toegewezen.

## <a name="supported-vm-series-and-sizes"></a>Ondersteunde VM-reeksen en-groottes

### <a name="pools-in-virtual-machine-configuration"></a>Groepen in de configuratie van de virtuele machine

Batch-Pools in de virtuele-machine configuratie ondersteunen bijna alle VM-grootten ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). Raadpleeg de volgende tabel voor meer informatie over ondersteunde grootten en beperkingen.

| VM-reeks  | Ondersteunde grootten |
|------------|---------|
| Basic A | Alle grootten *behalve* Basic_A0 (a0) |
| A | Alle grootten *behalve* Standard_A0 |
| Av2 | Alle grootten |
| B | Geen |
| DC | Geen |
| Dv2, DSv2 | Alle grootten |
| Dv3, Dsv3 | Alle grootten |
| Dav4, Dasv4 | Geen-nog niet beschikbaar |
| Ev3, Esv3 | Alle grootten, met uitzonde ring van E64is_v3 en E64i_v3 |
| Eav4, Easv4 | Geen-nog niet beschikbaar |
| F, FS | Alle grootten |
| Fsv2 | Alle grootten |
| G, GS | Alle grootten |
| H | Alle grootten |
| HB<sup>1</sup> | Alle grootten |
| HBv2<sup>1</sup> | Alle grootten |
| HC<sup>1</sup> | Alle grootten |
| Ls | Alle grootten |
| Lsv2 | Geen-nog niet beschikbaar |
| M<sup>1</sup> | Alle grootten, met uitzonde ring van M64, M64m, M128, M128m |
| Mv2 | Geen-nog niet beschikbaar |
| NC | Alle grootten |
| NCv2<sup>1</sup> | Alle grootten |
| NCv3<sup>1</sup> | Alle grootten |
| ND<sup>1</sup> | Alle grootten |
| NDv2<sup>1</sup> | Geen-nog niet beschikbaar |
| NV | Alle grootten |
| NVv3<sup>1</sup> | Alle grootten |
| NVv4 | Geen |
| SAP HANA | Geen |

<sup>1</sup> deze VM-grootten kunnen worden toegewezen in batch-Pools in de configuratie van virtuele machines, maar u moet een nieuw batch-account maken en een specifieke [quota verhoging](batch-quota-limit.md#increase-a-quota)aanvragen. Deze beperking wordt verwijderd zodra vCPU quotum per VM-serie volledig wordt ondersteund voor batch-accounts.

### <a name="pools-in-cloud-service-configuration"></a>Groepen in de Cloud service configuratie

Batch-Pools in de Cloud service configuratie ondersteunen alle [VM-grootten voor Cloud Services](../cloud-services/cloud-services-sizes-specs.md) **, met uitzonde ring** van de volgende opties:

| VM-reeks  | Niet-ondersteunde grootten |
|------------|-------------------|
| A-serie   | Extra klein       |
| Av2-serie | Standard_A1_v2, Standard_A2_v2 Standard_A2m_v2 |

## <a name="size-considerations"></a>Overwegingen over grootte

* **Toepassings vereisten** : Houd rekening met de kenmerken en vereisten van de toepassing die u op de knoop punten uitvoert. Houd ook rekening met het feit of bijvoorbeeld de toepassing meerdere threads heeft en hoeveel geheugen deze gebruikt. Zo kunt u gemakkelijker de meest geschikte en voordeligste knooppuntgrootte bepalen. Voor [mpi-werk belastingen](batch-mpi.md) of CUDA-toepassingen met meerdere instanties kunt u respectievelijk gespecialiseerde VM-grootten met [HPC](../virtual-machines/linux/sizes-hpc.md) of [GPU-](../virtual-machines/linux/sizes-gpu.md) capaciteit overwegen. (Zie [met RDMA compatibele of GPU-compatibele instanties in batch-Pools gebruiken](batch-pool-compute-intensive-sizes.md).)

* **Taken per knoop punt** : het is gebruikelijk om een knooppunt grootte te selecteren, ervan uitgaande dat er één taak tegelijk op een knoop punt wordt uitgevoerd. Het kan echter handig zijn om meerdere taken (en dus meerdere toepassings exemplaren) parallel op reken knooppunten [uit te voeren](batch-parallel-node-tasks.md) tijdens de taak uitvoering. In dit geval is het gebruikelijk om een multicore-knooppunt grootte te kiezen die geschikt is voor de verhoogde vraag van parallelle taak uitvoering.

* **Laad niveaus voor verschillende taken** : alle knoop punten in een groep hebben dezelfde grootte. Als u toepassingen met verschillende systeemvereisten en/of workloadniveaus wilt uitvoeren, moet u afzonderlijke pools maken.

* **Beschik baarheid van regio** : een VM-serie of-grootte is mogelijk niet beschikbaar in de regio's waar u uw batch-accounts maakt. Zie [producten beschikbaar per regio](https://azure.microsoft.com/regions/services/)om te controleren of er een grootte beschikbaar is.

* **Quota's** : de [kernen Quota's](batch-quota-limit.md#resource-quotas) in uw batch-account kunnen het aantal knoop punten van een bepaalde grootte beperken die u aan een batch-pool kunt toevoegen. Raadpleeg [dit artikel](batch-quota-limit.md#increase-a-quota)als u een quota verhoging wilt aanvragen. 

* **Groeps configuratie** : in het algemeen hebt u meer opties voor VM-grootte wanneer u een groep in de virtuele-machine configuratie maakt, vergeleken met de configuratie van de Cloud service.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [werk stroom van de batch-service en primaire resources](batch-service-workflow-features.md) , zoals Pools, knoop punten, taken en taken.
* Zie voor meer informatie over het gebruik van Compute-intensieve VM-grootten [RDMA-compatibele of GPU-compatibele instanties in batch-Pools gebruiken](batch-pool-compute-intensive-sizes.md).
