---
title: Setup voor Azure N-serie AMD GPU-stuur programma voor Windows
description: AMD GPU-Stuur Programma's instellen voor virtuele machines uit de N-serie met Windows Server of Windows in azure
author: vikancha
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 745ec7ebf792fe1165022516be4c83fb9e864cc9
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83799884"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>AMD GPU-Stuur Programma's installeren op Vm's met N-serie waarop Windows wordt uitgevoerd

Als u gebruik wilt maken van de GPU-mogelijkheden van de nieuwe virtuele machines uit de Azure NVv4-serie met Windows, moeten de AMD GPU-Stuur Programma's zijn geïnstalleerd. Met de [uitbrei ding van het AMD GPU-stuur programma](../extensions/hpccompute-amd-gpu-windows.md) worden AMD GPU-Stuur Programma's geïnstalleerd op een NVv4-serie. De uitbrei ding installeren of beheren met de Azure Portal of hulpprogram ma's, zoals Azure PowerShell of Azure Resource Manager sjablonen. Zie de [documentatie over de uitbrei ding van de AMD GPU-stuur programma](../extensions/hpccompute-amd-gpu-windows.md) voor ondersteunde besturings systemen en implementaties tappen.

Als u ervoor kiest om de AMD GPU-Stuur Programma's hand matig te installeren, worden in dit artikel ondersteunde besturings systemen, stuur Programma's en installatie-en verificatie stappen beschreven.

Alleen GPU-Stuur Programma's die door micro soft zijn gepubliceerd, worden ondersteund op NVv4 Vm's. Installeer geen GPU-Stuur Programma's vanuit een andere bron.

Zie [GPU Windows VM-grootten](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor basis specificaties, opslag capaciteit en schijf Details.



## <a name="supported-operating-systems-and-drivers"></a>Ondersteunde besturingssystemen en stuurprogramma’s

| Besturingssysteem | Stuurprogramma |
| -------- |------------- |
| Windows 10 EVD-build 1903 <br/><br/>Windows 10-build 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. q 1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (. exe) |


## <a name="driver-installation"></a>Installatie van Stuur Programma's

1. Verbind door Extern bureaublad naar elke VM van de NVv4-serie.

2. Down load en installeer het meest recente stuur programma.

3. Start de VM opnieuw op.

## <a name="verify-driver-installation"></a>Installatie van stuur programma verifiëren

U kunt de installatie van Stuur Programma's controleren in Apparaatbeheer. In het volgende voor beeld ziet u een geslaagde configuratie van de Radeon instinct MI25-kaart op een Azure NVv4-VM.
<br />
![Eigenschappen van GPU-stuur programma](./media/n-series-amd-driver-setup/device-manager.png)

U kunt Dxdiag gebruiken om de eigenschappen van de GPU-weer gave te controleren, inclusief de video-RAM. In het volgende voor beeld ziet u een 1/2-partitie van de Radeon instinct MI25-kaart op een Azure NVv4-VM.
<br />
![Eigenschappen van GPU-stuur programma](./media/n-series-amd-driver-setup/dxdiag-output.png)

Als u Windows 10 build 1903 of hoger gebruikt, wordt in Dxdiag geen informatie weer gegeven op het tabblad weer geven. Gebruik de optie ' alle gegevens opslaan ' onderaan en in het uitvoer bestand worden de gegevens weer gegeven die betrekking hebben op AMD MI25 GPU.

![Eigenschappen van GPU-stuur programma](./media/n-series-amd-driver-setup/dxdiag-details.png)


