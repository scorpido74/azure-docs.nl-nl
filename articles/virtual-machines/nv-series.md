---
title: NV-serie - Azure Virtual Machines
description: Specificaties voor de NV-serie VM's.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 7fd9fff87564b790915acf01373c078c2012371e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273877"
---
# <a name="nv-series"></a>NV-serie

De virtuele machines uit de NV-serie worden aangedreven door [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU's en NVIDIA GRID-technologie voor desktopversnelde toepassingen en virtuele desktops waar klanten hun gegevens of simulaties kunnen visualiseren. Gebruikers kunnen hun grafisch intensieve workflows op de NV-instanties visualiseren om superieure grafische mogelijkheden te krijgen en bovendien enkele precisieworkloads uit te voeren, zoals codering en rendering. NV-serie VM's worden ook aangedreven door Intel Xeon E5-2690 v3 (Haswell) CPU's.

Elke GPU in NV-exemplaren wordt geleverd met een GRID-licentie. Deze licentie geeft u de flexibiliteit om een NV-exemplaar te gebruiken als een virtueel werkstation voor één gebruiker, of 25 gelijktijdige gebruikers kunnen verbinding maken met de VM voor een virtueel toepassingsscenario.

Premium opslag: niet ondersteund

Premium Storage caching: niet ondersteund

Live migratie: niet ondersteund

Updates voor geheugenbehoud: niet ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | GPU | GPU-geheugen: GiB | Max. aantal gegevensschijven | Max. aantal NIC's | Virtuele werkstations | Virtuele toepassingen |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standard_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standard_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = halve M60-kaart.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Ondersteunde besturingssystemen en stuurprogramma’s

Om te profiteren van de GPU-mogelijkheden van Azure N-serie VM's, moeten NVIDIA GPU-stuurprogramma's worden geïnstalleerd.

De [NVIDIA GPU Driver Extension](./extensions/hpccompute-gpu-windows.md) installeert de juiste NVIDIA CUDA- of GRID-stuurprogramma's op een VM uit de N-serie. Installeer of beheer de extensie met behulp van de Azure-portal of hulpprogramma's zoals Azure PowerShell- of Azure Resource Manager-sjablonen. Bekijk de [NVIDIA GPU Driver Extension-documentatie](./extensions/hpccompute-gpu-windows.md) voor ondersteunde besturingssystemen en implementatiestappen. Zie Azure virtual machine [extensions and features](./extensions/overview.md)voor algemene informatie over VM-extensies.

Als u ervoor kiest nvidia GPU-stuurprogramma's handmatig te installeren, raadpleegt u de opstelling van [GPU-stuurprogramma's uit de N-serie voor Windows](./windows/n-series-driver-setup.md) of [N-serie GPU-stuurprogramma's voor Linux](./linux/n-series-driver-setup.md) voor ondersteunde besturingssystemen, stuurprogramma's, installatie en verificatiestappen.

## <a name="other-sizes"></a>Andere maten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure compute units (ACU)](acu.md) u kunnen helpen bij het vergelijken van rekenprestaties in Azure SKU's.
