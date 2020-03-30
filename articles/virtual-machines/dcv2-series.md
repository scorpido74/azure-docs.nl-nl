---
title: DC-serie - Azure Virtual Machines
description: Specificaties voor de VM's uit de DC-serie.
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: 7834c8a32d4d85fc354bac209e13f19f3b8315fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256926"
---
# <a name="preview-dcsv2-series"></a>Preview: DCsv2-serie


De DCsv2-serie kan helpen de vertrouwelijkheid en integriteit van uw gegevens en code te beschermen terwijl deze in de openbare cloud worden verwerkt. Deze machines worden ondersteund door de nieuwste generatie Intel XEON E-2288G Processor met SGX-technologie. Met de Intel Turbo Boost Technology kunnen deze machines tot 5,0 GHz gaan. DCsv2-serie exemplaren stellen klanten in staat om veilige enclave-gebaseerde applicaties te bouwen om hun code en gegevens te beschermen terwijl deze in gebruik zijn.

Voorbeelden hiervan zijn vertrouwelijke meerpartijengegevens, fraudedetectie, anti-witwaspraktijken, blockchain, analyse van vertrouwelijk gebruik, intelligentieanalyse en vertrouwelijke machine learning.

Premium opslag: ondersteund*

Premium Storage-cache: ondersteund*

Live migratie: niet ondersteund

Updates voor geheugenbehoud: niet ondersteund

*Behalve Standard_DC8_v2



| Grootte             | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer voor schijven met caching en tijdelijke opslag: IOPS / MBps (cachegrootte in GiB) | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Max NIC's / Verwachte netwerkbandbreedte (MBps) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16 (21)                                                            | 1600/24                                   | 2                                            |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                            | 3200/48                                   | 2                                            |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                            | 6400/96                                   | 2                                            |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128 (172)                                                         | 12800/192                                 | 2                                            |

- Vm's uit de DCsv2-serie zijn `Gen2` generatie 2 [VM's](./linux/generation-2.md#creating-a-generation-2-vm) en ondersteunen alleen afbeeldingen.
- Momenteel alleen beschikbaar in het Verenigd Koninkrijk Zuid en Canada Central.
- Vorige generatie vertrouwelijke Compute VM's: [DC-serie](sizes-previous-gen.md)
- Dcsv2 VM's maken met Azure Portal [Create VM - Portal](./linux/quick-create-portal.md)



## <a name="other-sizes"></a>Andere maten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure compute units (ACU)](acu.md) u kunnen helpen bij het vergelijken van rekenprestaties in Azure SKU's.
