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
ms.openlocfilehash: 5dccdb6c357635e78b076b1560bf6c0c62c03753
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77135141"
---
## <a name="supported-distributions-and-drivers"></a>Ondersteunde distributies en stuurprogramma 's

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA drivers

NVIDIA CUDA drivers voor NC, NCv2, NCv3, ND en NDv2-serie VM's (optioneel voor NV-serie) worden alleen ondersteund op de Linux-distributies in de volgende tabel. CUDA-stuurprogrammagegevens zijn actueel op het moment van publicatie. Ga voor de nieuwste CUDA-stuurprogramma's naar de [NVIDIA-website.](https://developer.nvidia.com/cuda-zone) Zorg ervoor dat u de nieuwste CUDA-stuurprogramma's voor uw distributie installeert of upgradet. 

> [!TIP]
> Als alternatief voor handmatige CUDA-stuurprogramma-installatie op een Linux-vm, u een Azure [Data Science Virtual Machine-afbeelding](../articles/machine-learning/data-science-virtual-machine/overview.md) implementeren. De DSVM-edities voor Ubuntu 16.04 LTS of CentOS 7.4 pre-install NVIDIA CUDA drivers, de CUDA Deep Neural Network Library en andere tools.

| Distributie | Stuurprogramma |
| --- | -- | 
| Ubuntu 16.04 LTS, 18.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3, 7.4, 7.5, 7.6<br/><br/> Op CentOS gebaseerde 7.3, 7.4, 7.5, 7.6, CentOS-gebaseerde 7.4 HPC | NVIDIA CUDA 10.1, driver branch R418 |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID drivers

Microsoft herverdeelt NVIDIA GRID driver installers voor NV en NVv3-serie VM's die worden gebruikt als virtuele werkstations of voor virtuele toepassingen. Installeer alleen deze GRID-stuurprogramma's op Azure NV VM's, alleen op de besturingssystemen die in de volgende tabel worden vermeld. Deze stuurprogramma's omvatten licenties voor GRID Virtual GPU-software in Azure. U hoeft geen NVIDIA vGPU-softwarelicentieserver in te stellen.

| Distributie | Stuurprogramma |
| --- | -- |
|Ubuntu 18.04 LTS<br/><br/>Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.0 tot 7.6<br/><br/>Op CentOS gebaseerde 7,0 tot 7,6<br/><br/>SUSE Linux Enterprise Server 12 SP2 | NVIDIA GRID 10.1, driver branch R440|

> [!WARNING] 
> Installatie van software van derden in Red Hat-producten kan invloed hebben op de ondersteuningsvoorwaarden van Red Hat. Zie het [Knowledge Base-artikel over Red Hat](https://access.redhat.com/articles/1067).
>
