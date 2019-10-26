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
ms.openlocfilehash: 661849d2d5c42db70d366e48e4138008bc18b720
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72902156"
---
## <a name="supported-operating-systems-and-drivers"></a>Ondersteunde besturingssystemen en stuurprogramma’s

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla-Stuur programma's (CUDA)

NVIDIA Tesla (CUDA)-Stuur Programma's voor NC-, NCv2-, NCv3-, ND-en NDv2-serie-Vm's (optioneel voor NV-serie) worden alleen ondersteund op de besturings systemen die in de volgende tabel worden vermeld. Koppelingen voor het downloaden van Stuur Programma's zijn actueel op het moment van publicatie. Ga naar de [NVIDIA](https://www.nvidia.com/)-website voor de nieuwste stuurprogramma's.

> [!TIP]
> Als alternatief voor het hand matig installeren van CUDA-Stuur Programma's op een virtuele machine met Windows Server kunt u een Azure [Data Science virtual machine](../articles/machine-learning/data-science-virtual-machine/overview.md) -installatie kopie implementeren. Met de DSVM-edities voor Windows Server 2016 worden NVIDIA CUDA-Stuur Programma's, de CUDA diep Neural-netwerk bibliotheek en andere hulpprogram ma's vooraf geïnstalleerd.


| Besturingssysteem | Stuurprogramma |
| -------- |------------- |
| Windows Server 2016 | [398,75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (. exe) |
| Windows Server 2012 R2 | [398,75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (. exe) |

### <a name="nvidia-grid-drivers"></a>NVIDIA-raster Stuur Programma's

Micro soft distribueert installatie Programma's voor NVIDIA-raster stations voor NV-en NVv3-Vm's die worden gebruikt als virtuele werk stations of voor virtuele toepassingen. Installeer alleen deze raster Stuur Programma's op virtuele machines uit de Azure NV-serie, alleen voor de besturings systemen die in de volgende tabel worden vermeld. Deze Stuur Programma's omvatten licenties voor raster-Virtual GPU-software in Azure. U hoeft geen NVIDIA vGPU-software licentie server in te stellen.

Houd er rekening mee dat de NVIDIA-extensie altijd het latst-stuur programma installeert. We bieden hier koppelingen naar de vorige versie voor klanten, die afhankelijk zijn van een oudere versie.

| Besturingssysteem | Stuurprogramma |
| -------- |------------- |
| Windows Server 2019<br/><br/>Windows Server 2016<br/><br/>Windows 10 | [Raster 9,1 (431,79)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe) <br/><br/> [Raster 9,0 (431,02)](https://download.microsoft.com/download/8/C/C/8CC88D54-EB07-44D3-8FA9-B797B173ED04/431.02_grid_win10_server2016_server2019_64bit_international.exe) (. exe)  |
| Windows Server 2012 R2<br/><br/>Windows Server 2008 R2<br/><br/>Windows 8<br/><br/>Windows 7 | [Raster 9,1 (431,79)](https://go.microsoft.com/fwlink/?linkid=874184) (. exe)<br/><br/> [Raster 9,0 (431,02)](https://download.microsoft.com/download/B/D/6/BD6D1C34-E41F-4654-B5AB-F4DA9C08AA64/431.02_grid_win7_win8_server2008R2_server2012R2_64bit_international.exe) (. exe)  |
