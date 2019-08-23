---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 0ed7fbf5e2b4f7f9b554f718d88c62e7a266f4ad
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69903896"
---
## <a name="supported-distributions-and-drivers"></a>Ondersteunde distributies en stuurprogramma 's

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA-Stuur Programma's

NVIDIA CUDA-Stuur Programma's voor NC-, NCv2-, NCv3-, ND-en NDv2-serie-Vm's (optioneel voor NV-serie) worden alleen ondersteund in de Linux-distributies die in de volgende tabel worden vermeld. De gegevens van het CUDA-stuur programma zijn actueel op het moment van publicatie. Bezoek de [NVIDIA](https://developer.nvidia.com/cuda-zone) -website voor de meest recente CUDA-Stuur Programma's. Zorg ervoor dat u de meest recente CUDA-Stuur Programma's voor uw distributie installeert of bijwerkt. 

> [!TIP]
> Als alternatief voor het hand matig installeren van CUDA-Stuur Programma's op een virtuele Linux-machine, kunt u een Azure [Data Science virtual machine](../articles/machine-learning/data-science-virtual-machine/overview.md) -installatie kopie implementeren. De DSVM-edities voor Ubuntu 16,04 LTS of CentOS 7,4 vooraf: Installeer NVIDIA CUDA-Stuur Programma's, de CUDA diepe Neural-netwerk bibliotheek en andere hulpprogram ma's.

| Distributie | Stuurprogramma |
| --- | -- | 
| Ubuntu 16,04 LTS, 18,04 LTS<br/><br/> Red Hat Enterprise Linux 7,3, 7,4, 7,5, 7,6<br/><br/> Op CentOS gebaseerde 7,3, 7,4, 7,5, 7,6, op CentOS 7,4 gebaseerde HPC | NVIDIA CUDA 10,1, driver Branch R418 |

### <a name="nvidia-grid-drivers"></a>NVIDIA-raster Stuur Programma's

Micro soft distribueert installatie Programma's voor NVIDIA-raster stations voor NV-en NVv3-Vm's die worden gebruikt als virtuele werk stations of voor virtuele toepassingen. Installeer alleen deze raster Stuur Programma's op Azure NV-Vm's, alleen voor de besturings systemen die in de volgende tabel worden vermeld. Deze Stuur Programma's omvatten licenties voor raster-Virtual GPU-software in Azure. U hoeft geen NVIDIA vGPU-software licentie server in te stellen.

| Distributie | Stuurprogramma |
| --- | -- |
|Ubuntu 18,04 LTS<br/><br/>Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7,0 tot 7,6<br/><br/>CentOS op basis van 7,0 tot 7,6<br/><br/>SUSE Linux Enterprise Server 12 SP2 | NVIDIA-raster 9,0, R430 van het stuur programma|

> [!WARNING] 
> Installatie van software van derden in Red Hat-producten kan invloed hebben op de ondersteuningsvoorwaarden van Red Hat. Zie het [Knowledge Base-artikel over Red Hat](https://access.redhat.com/articles/1067).
>
