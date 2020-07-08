---
title: Opstart fouten in azure Linux Virtual Machines oplossen | Microsoft Docs
description: In dit artikel vindt u een koppeling naar artikelen om opstart fouten in azure Linux Virtual Machines op te lossen.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74408740"
---
# <a name="troubleshoot-azure-linux-virtual-machines-boot-errors"></a>Opstart fouten van Azure Linux Virtual Machines oplossen

In dit artikel vindt u een lijst met veelvoorkomende opstart fouten die kunnen optreden wanneer u een virtuele Linux-machine (VM) start in Microsoft Azure. Zie de artikelen in de sectie **opstart fouten en oplossingen** voor meer informatie over de fouten.

## <a name="boot-errors-and-solutions"></a>Opstart fouten en oplossingen

* [GRUB-herstel](troubleshoot-vm-boot-error.md)

## <a name="next-steps"></a>Volgende stappen

- [VM-seriële console](serial-console-linux.md)

Problemen met een virtuele Linux-machine oplossen door de besturingssysteem schijf te koppelen aan een herstel-VM met behulp van Azure:

- [Herstel van virtuele Azure-machines](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

 Schijf swap: dit kan worden geautomatiseerd met behulp van:
- [Power shell-herstel scripts](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
- [bash-herstel scripts](https://github.com/sribs/azure-support-scripts)

- [CLI](troubleshoot-recovery-disks-linux.md)
- [Azure-portal](troubleshoot-recovery-disks-portal-linux.md)


## <a name="disk-swap-video"></a>Video over het wisselen van schijven:

Als u geen toegang hebt tot GRUB [deze](https://youtu.be/m5t0GZ5oGAc) video te bekijken en te zien, kunt u eenvoudig de schijf swap-procedure automatiseren om uw VM te herstellen

## <a name="unofficial-solution"></a>Niet-officiële oplossing

Herstel van een virtuele machine kan ook worden uitgevoerd met het niet-ondersteunde bèta script [ALAR](https://github.com/malachma/azure-auto-recover)