---
title: NVv4-serie-Azure Virtual Machines
description: Specificaties voor de virtuele machines uit de NVv4-serie.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 0295ed7d44d64fcc1aeb68e1beaa37987b177edb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78273849"
---
# <a name="nvv4-series"></a>NVv4-serie 

De virtuele machines uit de NVv4-serie worden aangedreven door [AMD Radeon instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU'S en AMD EPYC 7V12 (Rome) cpu's. Met de NVv4-serie van Azure worden virtuele machines met gedeeltelijke Gpu's geïntroduceerd. Kies de virtuele machine met de juiste grootte voor GPU-versnelde grafische toepassingen en virtuele Bureau bladen, beginnend bij 1/achtste van een GPU met 2 GiB frame buffer naar een volledige GPU met 16 GiB frame buffer. Virtuele NVv4-machines ondersteunen momenteel alleen Windows-gast besturingssystemen.

<br>

Premium Storage: ondersteund

Premium Storage caching: ondersteund

Livemigratie: niet ondersteund

Updates voor het behouden van geheugen: niet ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | GPU | GPU-geheugen: GiB | Max. aantal gegevensschijven | Max. aantal NIC's |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 |

<sup>1</sup> virtuele machines uit de NVv4-serie beschikken over AMD gelijktijdige multi-threading-technologie

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Ondersteunde besturingssystemen en stuurprogramma’s

Als u gebruik wilt maken van de GPU-mogelijkheden van virtuele machines uit de Azure NVv4-serie die worden uitgevoerd op Windows, moeten de AMD GPU-Stuur Programma's zijn geïnstalleerd.

Voor het hand matig installeren van AMD GPU-Stuur Programma's raadpleegt u de [installatie van de N-serie AMD GPU-stuur programma voor Windows](./windows/n-series-amd-driver-setup.md) voor ondersteunde besturings systemen, stuur Programma's, installatie en verificaties tappen.

## <a name="other-sizes"></a>Andere grootten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.
