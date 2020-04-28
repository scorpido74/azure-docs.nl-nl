---
title: Azure N-serie AMD GPU-stuurprogramma-installatie voor Windows
description: AMD GPU-stuurprogramma's instellen voor VM's uit de N-serie met Windows Server of Windows in Azure
author: vikancha
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 63114bdf60c1feb2b6cb1092ef78397efdc5b666
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865763"
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

3. Start de VM en download de nieuwste [AMD Cleanup Utility](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe). Verwijder het bestaande stuurprogramma door "amdcleanuputility-x64.exe" uit te voeren. Gebruik geen bestaand opruimprogramma dat bij het vorige stuurprogramma is geïnstalleerd.  

4. Download en installeer het nieuwste stuurprogramma.

5. Start de VM opnieuw op.

## <a name="verify-driver-installation"></a>De installatie van het stuurprogramma controleren

U de installatie van stuurprogramma's verifiëren in Apparaatbeheer. In het volgende voorbeeld wordt een succesvolle configuratie van de Radeon Instinct MI25-kaart op een Azure NVv4 VM weergegeven.
<br />
![GPU-stuurprogramma-eigenschappen](./media/n-series-amd-driver-setup/device-manager.png)

U dxdiag gebruiken om de gpu-weergave-eigenschappen te verifiëren, waaronder het video-RAM-geheugen. In het volgende voorbeeld ziet u een 1/2 partitie van de Radeon Instinct MI25-kaart op een Azure NVv4 VM.
<br />
![GPU-stuurprogramma-eigenschappen](./media/n-series-amd-driver-setup/dxdiag-output.png)

Als u Windows 10 build 1903 of hoger uitvoert, wordt er geen informatie weergegeven op het tabblad 'Weergeven'. Gebruik de optie 'Alle informatie opslaan' onderaan en het uitvoerbestand toont de informatie met betrekking tot AMD MI25 GPU.

![GPU-stuurprogramma-eigenschappen](./media/n-series-amd-driver-setup/dxdiag-details.png)


