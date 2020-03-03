---
title: Setup voor Azure N-serie AMD GPU-stuur programma voor Windows
description: AMD GPU-Stuur Programma's instellen voor virtuele machines uit de N-serie met Windows Server of Windows in azure
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
ms.openlocfilehash: 6dc8c54b9d138ab62e086cca59cd5b4801fa6130
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228342"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>AMD GPU-Stuur Programma's installeren op Vm's met N-serie waarop Windows wordt uitgevoerd

Als u gebruik wilt maken van de GPU-mogelijkheden van de nieuwe virtuele machines uit de Azure NVv4-serie met Windows, moeten de AMD GPU-Stuur Programma's zijn geïnstalleerd. De uitbrei ding van het AMD-stuur programma is in de komende weken beschikbaar. Dit artikel bevat ondersteunde besturings systemen, stuur Programma's en hand matige installatie-en verificatie stappen.

Zie [GPU Windows VM-grootten](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor basis specificaties, opslag capaciteit en schijf Details.



## <a name="supported-operating-systems-and-drivers"></a>Ondersteunde besturingssystemen en stuurprogramma’s

| OS | Stuurprogramma |
| -------- |------------- |
| Windows 10 EVD-build 1903 <br/><br/>Windows 10-build 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. q 1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (. exe) |


## <a name="driver-installation"></a>Installatie van Stuur Programma's

1. Verbind door Extern bureaublad naar elke VM van de NVv4-serie.

2. Als u een NVv4 preview-klant bent, stopt u de virtuele machine en wacht u totdat deze is verplaatst naar de status gestopt (toewijzing ongedaan gemaakt).

3. Start de virtuele machine en verwijder vervolgens het voor beeld van het stuur programma door ' amdcleanuputility-x64. exe ' uit te voeren in de map '. ..\AMDCleanUninstallUtility '. Het exacte pad varieert, afhankelijk van de locatie van de vorige stuur programma-installatie bestanden.  

4. Down load en installeer het meest recente stuur programma.

## <a name="verify-driver-installation"></a>Installatie van stuur programma verifiëren

U kunt de installatie van Stuur Programma's controleren in Apparaatbeheer. In het volgende voor beeld ziet u een geslaagde configuratie van de Radeon instinct MI25-kaart op een Azure NVv4-VM.
<br />
eigenschappen](./media/n-series-amd-driver-setup/device-manager.png) van ![GPU-stuur programma

U kunt Dxdiag gebruiken om de eigenschappen van de GPU-weer gave te controleren, inclusief de video-RAM. In het volgende voor beeld ziet u een 1/achtste partitie van de Radeon instinct MI25-kaart op een Azure NVv4-VM.
<br />
eigenschappen](./media/n-series-amd-driver-setup/dxdiag.png) van ![GPU-stuur programma
