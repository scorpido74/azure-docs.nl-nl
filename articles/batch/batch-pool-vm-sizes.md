---
title: Vm-formaten kiezen voor groepen - Azure Batch | Microsoft Documenten
description: Kiezen uit de beschikbare VM-formaten voor rekenknooppunten in Azure Batch-groepen
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: c18190ec5e5d079d51630a976681717a78a46e00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087045"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Een VM-grootte kiezen voor rekenknooppunten in een Azure Batch-groep

Wanneer u een knooppuntgrootte selecteert voor een Azure Batch-groep, u kiezen uit bijna alle VM-formaten die beschikbaar zijn in Azure. Azure biedt een reeks formaten voor Linux- en Windows-VM's voor verschillende workloads.

Er zijn een paar uitzonderingen en beperkingen voor het kiezen van een VM-grootte:

* Sommige VM-reeksen of VM-formaten worden niet ondersteund in Batch.
* Sommige VM-formaten zijn beperkt en moeten specifiek worden ingeschakeld voordat ze kunnen worden toegewezen.

## <a name="supported-vm-series-and-sizes"></a>Ondersteunde VM-reeksen en -formaten

### <a name="pools-in-virtual-machine-configuration"></a>Pools in virtual machine-configuratie

Batchpools in de Virtual Machine-configuratie ondersteunen bijna alle VM-formaten[(Linux,](../virtual-machines/linux/sizes.md) [Windows).](../virtual-machines/windows/sizes.md) Zie de volgende tabel voor meer informatie over ondersteunde groottes en beperkingen.

| VM-reeks  | Ondersteunde groottes |
|------------|---------|
| Basic A | Alle *maten, behalve* Basic_A0 (A0) |
| A | Alle *maten, behalve* Standard_A0 |
| Av2 | Alle maten |
| B | Geen |
| DC | Geen |
| Dv2, DSv2 | Alle maten |
| Dv3, Dsv3 | Alle maten |
| Dav4, Dasv4 | None |
| Ev3, Esv3 | Alle maten, met uitzondering van E64is_v3 en E64i_v3 |
| Eav4, Easv4 | None |
| F, Fs | Alle maten |
| Fsv2 | Alle maten |
| G, Gs | Alle maten |
| H | Alle maten |
| HB<sup>1</sup> | Alle maten |
| HBv2<sup>1</sup> | Alle maten |
| HC<sup>1</sup> | Alle maten |
| Ls | Alle maten |
| Lsv2 Lsv2 | None |
| M<sup>1</sup> | Alle maten, behalve M64, M64m, M128, M128m |
| Mv2 Mv2 | None |
| NC | Alle maten |
| NCv2<sup>1</sup> | Alle maten |
| NCv3<sup>1</sup> | Alle maten |
| ND<sup>1</sup> | Alle maten |
| NDv2<sup>1</sup> | None |
| NV | Alle maten |
| NVv3<sup>1</sup> | Alle maten |
| NVv4 (NVv4) | Geen |
| SAP HANA | Geen |

<sup>1</sup> Deze VM-formaten kunnen worden toegewezen in batchgroepen in de configuratie van virtuele machines, maar u moet een nieuw batchaccount maken en een specifieke [quotumverhoging](batch-quota-limit.md#increase-a-quota)aanvragen. Deze beperking wordt verwijderd zodra het VCPU-quotum per VM-reeks volledig wordt ondersteund voor Batch-accounts.

### <a name="pools-in-cloud-service-configuration"></a>Pools in Cloud Service-configuratie

Batchgroepen in de Cloud [Service-configuratie](../cloud-services/cloud-services-sizes-specs.md) ondersteunen alle VM-formaten voor Cloud **Services, behalve** voor het volgende:

| VM-reeks  | Niet-ondersteunde formaten |
|------------|-------------------|
| A-serie   | Extra klein       |
| Av2-serie | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Overwegingen over grootte

* **Toepassingsvereisten** - Houd rekening met de kenmerken en vereisten van de toepassing die u op de knooppunten uitvoert. Houd ook rekening met het feit of bijvoorbeeld de toepassing meerdere threads heeft en hoeveel geheugen deze gebruikt. Zo kunt u gemakkelijker de meest geschikte en voordeligste knooppuntgrootte bepalen. Voor [MPI-workloads](batch-mpi.md) met meerdere instanties of CUDA-toepassingen moet u overwegen om respectievelijk gespecialiseerde [VM-formaten](../virtual-machines/linux/sizes-hpc.md) met [HPC](../virtual-machines/linux/sizes-gpu.md) of GPU te gebruiken. (Zie [Exemplaren met RDMA-geschikt of GPU gebruiken in batchgroepen](batch-pool-compute-intensive-sizes.md).)

* **Taken per knooppunt** - Het is typisch om een knooppuntgrootte te selecteren, ervan uitgaande dat één taak tegelijk op een knooppunt wordt uitgevoerd. Het kan echter voordelig zijn om meerdere taken (en dus meerdere toepassingsinstanties) parallel op compute-knooppunten te laten [uitvoeren](batch-parallel-node-tasks.md) tijdens de uitvoering van taken. In dit geval is het gebruikelijk om een multicore-knooppuntgrootte te kiezen om tegemoet te komen aan de toegenomen vraag naar parallelle taakuitvoering.

* **Belastingsniveaus voor verschillende taken** - Alle knooppunten in een groep zijn even groot. Als u toepassingen met verschillende systeemvereisten en/of workloadniveaus wilt uitvoeren, moet u afzonderlijke pools maken.

* **Beschikbaarheid van regio 's** : een VM-serie of -grootte is mogelijk niet beschikbaar in de regio's waar u batchaccounts maakt. Zie [Producten die beschikbaar](https://azure.microsoft.com/regions/services/)zijn per regio om te controleren of er een grootte beschikbaar is.

* **Quota** : de [kernenquota](batch-quota-limit.md#resource-quotas) in uw batchaccount kunnen het aantal knooppunten van een bepaalde grootte beperken dat u aan een batchgroep toevoegen. Zie [dit artikel](batch-quota-limit.md#increase-a-quota)om een quotumverhoging aan te vragen. 

* **Configuratie van de groep** - Over het algemeen hebt u meer opties voor vm-grootte wanneer u een groep maakt in de configuratie van de virtuele machine in vergelijking met de cloudserviceconfiguratie.

## <a name="next-steps"></a>Volgende stappen

* Zie [Grootschalige parallelle rekenoplossingen ontwikkelen met Batch](batch-api-basics.md)voor een diepgaand overzicht van Batch.
* Zie [Rdma-geschikte of GPU-geschikte exemplaren gebruiken in batchgroepen](batch-pool-compute-intensive-sizes.md)voor informatie over het gebruik van computerintensieve VM-formaten.
