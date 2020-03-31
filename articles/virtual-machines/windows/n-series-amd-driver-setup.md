---
title: Azure N-serie AMD GPU-stuurprogramma-installatie voor Windows
description: AMD GPU-stuurprogramma's instellen voor VM's uit de N-serie met Windows Server of Windows in Azure
services: virtual-machines-windows
author: vikancha
manager: jkabat
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 883dbc95ee77d03aee4c3231c6ab8c03f9f7f6e4
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387832"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Installeer AMD GPU-stuurprogramma's op VM's uit de N-serie met Windows

Om te profiteren van de GPU-mogelijkheden van de nieuwe Azure NVv4-serie VM's met Windows, moeten AMD GPU-stuurprogramma's worden geïnstalleerd. De AMD driver extensie zal beschikbaar zijn in de komende weken. In dit artikel worden ondersteunde besturingssystemen, stuurprogramma's en handmatige installatie- en verificatiestappen weergegeven.

Zie [GPU Windows VM-formaten](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor basisspecificaties, opslagcapaciteiten en schijfdetails.



## <a name="supported-operating-systems-and-drivers"></a>Ondersteunde besturingssystemen en stuurprogramma’s

| OS | Stuurprogramma |
| -------- |------------- |
| Windows 10 EVD - Build 1903 <br/><br/>Windows 10 - Build 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20.Q1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (.exe) |


## <a name="driver-installation"></a>Installatie van stuurprogramma's

1. Maak verbinding via Extern bureaublad met elke VM uit de NVv4-serie.

2. Als u een NVv4 preview-klant bent, stopt u de VM en wacht tot deze wordt verplaatst naar de status Gestopt (Deallocated).

3. Start de VM en download de nieuwste [AMD Cleanup Utility](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe). Verwijder het bestaande stuurprogramma door "amdcleanuputility-x64.exe" uit te voeren. Gebruik geen exisitng cleanup utility dat is geïnstalleerd met de vorige driver.  

4. Download en installeer het nieuwste stuurprogramma.

5. Start de VM opnieuw op.

## <a name="verify-driver-installation"></a>De installatie van het stuurprogramma controleren

U de installatie van stuurprogramma's verifiëren in Apparaatbeheer. In het volgende voorbeeld wordt een succesvolle configuratie van de Radeon Instinct MI25-kaart op een Azure NVv4 VM weergegeven.
<br />
![GPU-stuurprogramma-eigenschappen](./media/n-series-amd-driver-setup/device-manager.png)

U dxdiag gebruiken om de gpu-weergave-eigenschappen te verifiëren, waaronder het video-RAM-geheugen. In het volgende voorbeeld wordt een 1/8e partitie van de Radeon Instinct MI25-kaart op een Azure NVv4 VM weergegeven.
<br />
![GPU-stuurprogramma-eigenschappen](./media/n-series-amd-driver-setup/dxdiag.png)
