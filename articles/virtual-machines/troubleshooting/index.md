---
layout: LandingPage
description: Informatie over hoe u problemen met implementaties van virtuele machines oplost.
title: Documentatie voor probleemoplossing van Azure Virtual Machines | Microsoft Docs
services: virtual-machines
author: genlin
manager: gwallace
ms.assetid: ''
ms.service: virtual-machines
ms.tgt_pltfrm: na
ms.topic: landing-page
ms.date: 10/3/2018
ms.author: genli
ms.openlocfilehash: ed1873fa8e74b2af5734411dc80aeb9089b61a7e
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080456"
---
# <a name="troubleshooting-azure-virtual-machines"></a>Problemen met virtuele Azure-machines oplossen

## <a name="tools-for-troubleshooting"></a>Hulpprogramma's voor probleemoplossing

- [Seriële console](serial-console-windows.md)
- [Diagnostische gegevens over opstarten](boot-diagnostics.md)
- [Windows-VM: De schijf van het besturingssysteem aan een andere VM koppelen om problemen op te lossen](troubleshoot-recovery-disks-portal-windows.md)
- [Linux-VM: De schijf van het besturingssysteem aan een andere VM koppelen om problemen op te lossen](troubleshoot-recovery-disks-portal-linux.md)

## <a name="cant-connect-to-the-vm"></a>Kan geen verbinding maken met de VM

### <a name="windows"></a>Windows

**Algemene oplossing**

- [RDP opnieuw instellen](reset-rdp.md)
- [Problemen met RDP oplossen](troubleshoot-rdp-connection.md)
- [Gedetailleerde RDP-probleemoplossing](detailed-troubleshoot-rdp.md)

**RDP-fouten**

- [Geen licentieserver](troubleshoot-rdp-no-license-server.md)
- [Een interne fout](Troubleshoot-rdp-internal-error.md)
- [Verificatiefouten](troubleshoot-authentication-error-rdp-vm.md)
- [Specifieke fouten oplossen](troubleshoot-specific-rdp-errors.md)

**Opstartfouten van VM**

* [Opstartfouten van BitLocker](troubleshoot-bitlocker-boot-error.md) 
* [Windows tijdens opstarten: 'Controle van bestandssysteem'](troubleshoot-check-disk-boot-error.md)
* [Fouten met blauw scherm](troubleshoot-common-blue-screen-error.md)
* [Opstarten van VM blijft hangen bij Windows voorbereiden](troubleshoot-vm-boot-configure-update.md)
* [Fout met blauw scherm: 'KRITIEKE SERVICE IS MISLUKT'](troubleshoot-critical-service-failed-boot-error.md)
* [Opstartlusprobleem](troubleshoot-reboot-loop.md)
* [Opstarten van VM blijft hangen tijdens bijwerkfase van Windows](troubleshoot-stuck-updating-boot-error.md)
* [VM wordt opgestart in veilige modus](troubleshoot-rdp-safe-mode.md)

**Overig**
- [VM-wachtwoord voor Windows-VM offline opnieuw instellen](reset-local-password-without-agent.md)
- [NIC opnieuw instellen na onjuiste configuratie](reset-network-interface.md)

### <a name="linux"></a>Linux

- [Problemen met SSH oplossen](troubleshoot-ssh-connection.md)
- [Gedetailleerde SSH-probleemoplossing](detailed-troubleshoot-ssh-connection.md)
- [Veelvoorkomende foutberichten](error-messages.md)
- [VM-wachtwoord voor Linux-VM offline opnieuw instellen](reset-password.md)

## <a name="vm-deployment-issues"></a>Implementatieproblemen van VM

- [Toewijzingsfouten](allocation-failure.md)
- Een virtuele machine opnieuw implementeren
    - [Linux](redeploy-to-new-node-linux.md)
    - [Windows](redeploy-to-new-node-windows.md)
- Problemen met implementaties oplossen
    - [Linux](troubleshoot-deploy-vm-linux.md)
    - [Windows](troubleshoot-deploy-vm-windows.md)
- [Apparaatnamen zijn gewijzigd](troubleshoot-device-names-problems.md)
- [VM-agent voor Windows offline installeren](install-vm-agent-offline.md)
- [Een virtuele machine opnieuw starten of het formaat van een virtuele machine wijzigen](restart-resize-error-troubleshooting.md)

## <a name="vm-performance-issue"></a>Prestatieprobleem van VM
- [Prestatieproblemen met VM's](performance-diagnostics.md)
- Windows
    - [PerfInsights gebruiken](how-to-use-perfinsights.md)
    - [Extensie voor diagnostische gegevens over prestaties](performance-diagnostics-vm-extension.md)
- Linux
    - [PerfInsights gebruiken](how-to-use-perfinsights-linux.md)