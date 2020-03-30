---
title: Opstartfouten in Azure Linux Virtual Machines oplossen | Microsoft Documenten
description: In dit artikel u worden gekoppeld aan artikelen om opstartfouten in Azure Linux Virtual Machines op te lossen.
services: virtual-machines-linux
documentationCenter: ''
author: vilibert
manager: spogge
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2019
ms.author: vilibert
ms.openlocfilehash: 37cb201751f72918838efe5837aa0e357d483f24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74408740"
---
# <a name="troubleshoot-azure-linux-virtual-machines-boot-errors"></a>Problemen met azure Linux Virtual Machines opstartfouten oplossen

In dit artikel worden de veelvoorkomende opstartfouten weergegeven die u ontvangen wanneer u een virtuele Linux-machine (VM) start in Microsoft Azure. Zie de artikelen in de sectie **Opstartfouten en -oplossingen** voor meer informatie over de fouten.

## <a name="boot-errors-and-solutions"></a>Opstartfouten en -oplossingen

* [GRUB redding](troubleshoot-vm-boot-error.md)

## <a name="next-steps"></a>Volgende stappen

- [VM-seriële console](serial-console-linux.md)

Een Linux-vm oplossen door de OS-schijf aan een herstel-vm te koppelen met Azure:

- [Azure VM-reparatie](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

 Disk Swap - dit kan worden geautomatiseerd met behulp van een van beide:
- [Power Shell-herstelscripts](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
- [bash Herstelscripts](https://github.com/sribs/azure-support-scripts)

- [CLI](troubleshoot-recovery-disks-linux.md)
- [Azure-portal](troubleshoot-recovery-disks-portal-linux.md)


## <a name="disk-swap-video"></a>Disk Swap Video:

Als u geen toegang hebt tot GRUB bekijk [deze](https://youtu.be/m5t0GZ5oGAc) video en zie, hoe u gemakkelijk automatiseren van de disk swap procedure om uw VM te herstellen

## <a name="unofficial-solution"></a>Onofficiële oplossing

Herstel van een VM kan ook worden geprobeerd met het niet-ondersteunde BETA-script [ALAR](https://github.com/malachma/azure-auto-recover)