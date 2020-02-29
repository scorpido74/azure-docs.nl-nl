---
title: NVv4-serie-Azure Virtual Machines
description: Specificaties voor de virtuele machines uit de NVv4-serie.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: ccafe6f317ff50684922fd4409beb42214c18858
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "78161853"
---
# <a name="nvv4-series-preview"></a>NVv4-serie (preview-versie)

De virtuele machines uit de NVv4-serie worden aangedreven door [AMD Radeon instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU'S en AMD EPYC 7V12 (Rome) cpu's. Met de NVv4-serie van Azure worden virtuele machines met gedeeltelijke Gpu's geïntroduceerd. Kies de virtuele machine met de juiste grootte voor GPU-versnelde grafische toepassingen en virtuele Bureau bladen, beginnend bij 1/achtste van een GPU met 2 GiB frame buffer naar een volledige GPU met 16 GiB frame buffer. Virtuele NVv4-machines ondersteunen momenteel alleen Windows-gast besturingssystemen.

[Meld u aan en krijg tijdens de preview-periode toegang tot deze machines](https://aka.ms/nvv4signup).
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

Als u gebruik wilt maken van de GPU-mogelijkheden van virtuele machines uit de Azure N-serie die worden uitgevoerd op Windows, moeten de NVIDIA-of AMD GPU-Stuur Programma's zijn geïnstalleerd.

Met de [uitbrei ding NVIDIA GPU-stuur programma](./extensions/hpccompute-gpu-windows.md) worden de juiste NVIDIA CUDA-of grid-Stuur Programma's geïnstalleerd op een Windows N-serie-VM. De uitbrei ding installeren of beheren met de Azure Portal of hulpprogram ma's, zoals Azure PowerShell of Azure Resource Manager sjablonen. Zie de [documentatie over NVIDIA GPU-Stuur Programma's](./extensions/hpccompute-gpu-windows.md) voor ondersteunde besturings systemen en implementaties tappen. Zie [extensies en functies van virtuele Azure-machines](/.extensions/overview.md)voor algemene informatie over VM-extensies.

Als u ervoor kiest om de NVIDIA GPU-Stuur Programma's hand matig te installeren, raadpleegt u de [installatie van het stuur programma voor de N-Series GPU voor Windows](./windows/n-series-driver-setup.md) voor ondersteunde besturings systemen, stuur Programma's, installatie en verificaties tappen.

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
