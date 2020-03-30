---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 40e5a1bf940e46aed566a1e3fa6dcb4e6b2d9230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77135143"
---
## <a name="supported-operating-systems-and-drivers"></a>Ondersteunde besturingssystemen en stuurprogramma’s

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla (CUDA) bestuurders

NVIDIA Tesla (CUDA) drivers voor NC, NCv2, NCv3, ND en NDv2-serie VM's (optioneel voor NV-serie) worden alleen ondersteund op de besturingssystemen die in de volgende tabel worden vermeld. Driver download links zijn actueel op het moment van publicatie. Ga naar de [NVIDIA](https://www.nvidia.com/)-website voor de nieuwste stuurprogramma's.

> [!TIP]
> Als alternatief voor handmatige CUDA-stuurprogramma-installatie op een Windows Server VM, u een Azure [Data Science Virtual Machine-afbeelding](../articles/machine-learning/data-science-virtual-machine/overview.md) implementeren. De DSVM-edities voor Windows Server 2016 installeren NVIDIA CUDA-stuurprogramma's, de CUDA Deep Neural Network Library en andere hulpprogramma's.


| OS | Stuurprogramma |
| -------- |------------- |
| Windows Server 2016 | [398,75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [398,75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID drivers

Microsoft herverdeelt NVIDIA GRID driver installers voor NV en NVv3-serie VM's die worden gebruikt als virtuele werkstations of voor virtuele toepassingen. Installeer alleen deze GRID-stuurprogramma's op Azure NV-serie VM's, alleen op de besturingssystemen die in de volgende tabel worden vermeld. Deze stuurprogramma's omvatten licenties voor GRID Virtual GPU-software in Azure. U hoeft geen NVIDIA vGPU-softwarelicentieserver in te stellen.

Houd er rekening mee dat de Nvidia-extensie altijd de nieuwste driver zal installeren. We bieden hier links naar de vorige versie voor klanten die afhankelijk zijn van een oudere versie.

Voor Windows Server 2019, Windows Server 2016 en Windows 10(tot build 1909):
- [GRID 10.1 (442.06)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe)
- [GRID 10.0 (441.66)](https://download.microsoft.com/download/2/a/3/2a316e62-3be9-4ddb-ae8e-c04b6df6e22d/441.66_grid_win10_server2016_server2019_64bit_international.exe) (.exe) 

Voor Windows Server 2012 R2, Windows Server 2008 R2, Windows 8 en Windows 7: 
- [GRID 10.1 (442.06)](https://go.microsoft.com/fwlink/?linkid=874184) (.exe)
- [GRID 10.0 (441.66)](https://download.microsoft.com/download/d/8/0/d80091f8-0d55-47c2-958a-bacd136f432a/441.66_grid_win7_win8_server2008R2_server2012R2_64bit_international.exe) (.exe)  
