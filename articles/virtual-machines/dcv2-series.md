---
title: DC-serie-Azure Virtual Machines
description: Specificaties voor de Vm's van de DC-serie.
author: susaxen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: jushiman
ms.openlocfilehash: c5074a81625f38cf94d5acd9751414bad4a9c3e8
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292470"
---
# <a name="dcsv2-series"></a>DCsv2-serie


De DCsv2-serie kan u helpen de vertrouwelijkheid en integriteit van uw gegevens en code te beschermen tijdens de verwerking in de open bare Cloud. Deze machines worden ondersteund door de nieuwste generatie Intel XEON E-2288G-processor met SGX-technologie. Met de Intel Turbo Boost-technologie kunnen deze machines tot wel 5.0 GHz gaan. Met DCsv2-serie-instanties kunnen klanten veilige, op enclave gebaseerde toepassingen bouwen om hun code en gegevens te beschermen terwijl ze worden gebruikt.

Voor beelden van use cases zijn: vertrouwelijk delen van gegevens, fraude detectie, anti-Money witwassen, Block Chain, analyse van vertrouwelijk gebruik, intelligentie analyse en vertrouwelijke machine learning.

Premium Storage: ondersteund *

Premium Storage caching: ondersteund *

Livemigratie: niet ondersteund

Updates voor het behouden van geheugen: niet ondersteund

* Met uitzonde ring van Standard_DC8_v2



| Grootte             | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer voor schijven met caching en tijdelijke opslag: IOPS / MBps (cachegrootte in GiB) | Maximum aantal Nic's/verwachte netwerk bandbreedte (MBps) | EPC-geheugen (MiB) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|----------------------------------------------|---------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16                                                                                               | 2   | 28                                      |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32                                                                                               | 2  | 56                                          |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64                                                                                               | 2  | 112                                          |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128                                                                                         | 2   | 168                                         |

- Vm's uit de DCsv2-serie zijn [twee vm's van de tweede generatie](./linux/generation-2.md#creating-a-generation-2-vm) en ondersteunen alleen `Gen2` installatie kopieën.
- Momenteel beschikbaar in de regio's die [hier](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)worden weer gegeven.
- Vorige generatie van vertrouwelijk Compute-Vm's: [gelijkstroom reeks](sizes-previous-gen.md#preview-dc-series)
- DCsv2 Vm's maken met behulp van de [Azure Portal](./linux/quick-create-portal.md) of [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)



## <a name="other-sizes-and-information"></a>Andere grootten en informatie

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

Prijs calculator: [prijs calculator](https://azure.microsoft.com/pricing/calculator/)

Meer informatie over schijven typen: [schijf typen](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types#ultra-ssd-preview/)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.
