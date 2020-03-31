---
title: NVv4-serie - Azure Virtual Machines
description: Specificaties voor de NVv4-serie VM's.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 0295ed7d44d64fcc1aeb68e1beaa37987b177edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273849"
---
# <a name="nvv4-series"></a>NVv4-serie 

De virtuele machines uit de NVv4-serie worden aangedreven door [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU's en AMD EPYC 7V12(Rome) CPU's. Met NVv4-serie introduceert Azure virtuele machines met gedeeltelijke GPU's. Kies de juiste virtuele machine met formaat voor GPU-versnelde grafische toepassingen en virtuele desktops vanaf 1/8 van een GPU met 2 GiB-framebuffer tot een volledige GPU met 16 GiB-framebuffer. NVv4 virtuele machines ondersteunen momenteel alleen Windows gast besturingssysteem.

<br>

Premium opslag: ondersteund

Premium Storage-cache: ondersteund

Live migratie: niet ondersteund

Updates voor geheugenbehoud: niet ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | GPU | GPU-geheugen: GiB | Max. aantal gegevensschijven | Max. aantal NIC's |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 |

<sup>1</sup> NVv4-serie VM's zijn voorzien van AMD Simultane multithreadingtechnologie

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Ondersteunde besturingssystemen en stuurprogramma’s

Om te profiteren van de GPU-mogelijkheden van Azure NVv4-serie VM's met Windows, moeten AMD GPU-stuurprogramma's worden geïnstalleerd.

Zie [N-serie AMD GPU-stuurprogramma-instellingen voor Windows](./windows/n-series-amd-driver-setup.md) voor ondersteunde besturingssystemen, stuurprogramma's, installatie en verificatiestappen om AMD GPU-stuurprogramma's handmatig te installeren.

## <a name="other-sizes"></a>Andere maten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure compute units (ACU)](acu.md) u kunnen helpen bij het vergelijken van rekenprestaties in Azure SKU's.
