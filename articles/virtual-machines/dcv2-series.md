---
title: DC-serie-Azure Virtual Machines
description: Specificaties voor de Vm's van de DC-serie.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: 41b08a921b4cc71275c216e86e89e49b59fc950a
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163740"
---
# <a name="preview-dcv2-series"></a>Voor beeld: DCv2-serie


De DCv2-serie kan u helpen de vertrouwelijkheid en integriteit van uw gegevens en code te beschermen tijdens de verwerking in de open bare Cloud. Deze machines worden ondersteund door de nieuwste generatie Intel XEON E-2288G-processor met SGX-technologie. Met de Intel Turbo Boost-technologie kunnen deze machines tot wel 5.0 GHz gaan. Met DCv2-serie-instanties kunnen klanten veilige, op enclave gebaseerde toepassingen bouwen om hun code en gegevens te beschermen terwijl ze worden gebruikt.

Voor beelden van use cases zijn onder meer het vertrouwelijk delen van gegevens, het opsporen van fraude, het witwassen van anti geld, het block Chain, het gebruik van analyse, de intelligentie en de vertrouwelijke machine learning.

Premium Storage: ondersteund *

Premium Storage caching: ondersteund *

Livemigratie: niet ondersteund

Updates voor het behouden van geheugen: niet ondersteund

\* Met uitzonde ring van Standard_DC8_v2



| Grootte             | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer voor schijven met caching en tijdelijke opslag: IOPS / MBps (cachegrootte in GiB) | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (MBps) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16 (21)                                                            | 1600/24                                   | 2                                            |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                            | 3200/48                                   | 2                                            |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                            | 6400/96                                   | 2                                            |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128 (172)                                                         | 12800/192                                 | 2                                            |

- Vm's uit de DCv2-serie zijn [twee vm's van de tweede generatie](./linux/generation-2.md#creating-a-generation-2-vm) en ondersteunen alleen `Gen2` installatie kopieën.




## <a name="other-sizes"></a>Andere grootten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.
