---
title: VM-grootten van Azure Linux-versnelde compute
description: Geeft een lijst van de verschillende geoptimaliseerde GPU-grootten die beschikbaar zijn voor virtuele Linux-machines in Azure. Bevat informatie over het aantal Vcpu's, gegevens schijven en Nic's en de opslag doorvoer en netwerk bandbreedte voor grootten in deze serie.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/11/2019
ms.author: jonbeck
ms.openlocfilehash: 983e85dc0ebefb27804c0f3a794360def6050ba9
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034908"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Grootte van door GPU geoptimaliseerde virtuele machines

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-distributions-and-drivers"></a>Ondersteunde distributies en stuurprogramma 's

Als u gebruik wilt maken van de GPU-mogelijkheden van virtuele machines uit de Azure N-serie waarop Linux wordt uitgevoerd, moeten de NVIDIA GPU-Stuur Programma's zijn geïnstalleerd. Met de [uitbrei ding NVIDIA GPU-stuur programma](../extensions/hpccompute-gpu-linux.md) worden de juiste NVIDIA-CUDA of raster Stuur Programma's geïnstalleerd op een virtuele machine uit de N-serie. De uitbrei ding installeren of beheren met de Azure Portal of hulpprogram ma's, zoals de Azure CLI-of Azure Resource Manager-sjablonen. Zie de [documentatie voor NVIDIA GPU-Stuur Programma's](../extensions/hpccompute-gpu-linux.md) voor ondersteunde distributies en implementaties tappen. Zie [extensies en functies van virtuele Azure-machines](../extensions/overview.md)voor algemene informatie over VM-extensies.

Als u ervoor kiest om de NVIDIA GPU-Stuur Programma's hand matig te installeren, raadpleegt u [het stuur programma voor de installatie van de N-Series GPU voor Linux](n-series-driver-setup.md) voor ondersteunde distributies, stuur Programma's en installatie-en verificatie stappen.


[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* U mag geen X-server of andere systemen installeren die gebruikmaken van het `Nouveau`-stuur programma op Ubuntu NC-Vm's. Voordat u NVIDIA GPU-Stuur Programma's installeert, moet u het `Nouveau`-stuur programma uitschakelen.  

## <a name="other-sizes"></a>Andere grootten
- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerde rekenkracht](sizes-compute.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen
Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.