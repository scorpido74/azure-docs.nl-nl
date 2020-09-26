---
title: Windows-Stop fout status systeem proces beëindigd
description: Dit artikel bevat stappen voor het oplossen van problemen waarbij het besturings systeem de Stop-fout 0xC000021A tegen komt, waarmee wordt voor komen dat een virtuele machine van Azure wordt opgestart.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 037d0858-4611-4707-bd46-cc8085d011ed
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: v-mibufo
ms.openlocfilehash: b07033f96402edc24edd51de57661603e57472bc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91344818"
---
# <a name="windows-stop-error---0xc000021a-status-system-process-terminated"></a>Windows-Stop fout: 0xC000021A status systeem proces beëindigd

Dit artikel bevat stappen voor het oplossen van problemen waarbij het besturings systeem (OS) de Stop-fout 0xC000021A tegen komt, waardoor een Azure virtual machine (VM) niet kan worden opgestart.

## <a name="symptom"></a>Symptoom

Wanneer u [Diagnostische gegevens over opstarten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) gebruikt om de scherm opname van de virtuele machine weer te geven, wordt in de scherm opname het bericht weer gegeven dat het besturings systeem een fout heeft aangetroffen tijdens het opstarten, met het volgende bericht:

**Er is een probleem opgetreden op de PC en opnieuw moet worden opgestart. Er worden alleen fout gegevens verzameld en vervolgens kunt u de computer opnieuw opstarten. (# #% voltooid) Als u meer wilt weten, kunt u later online zoeken naar deze fout: 0xC000021a**.

  ![In afbeelding 1 wordt de fout code #0xC000021A weer gegeven met het bericht ' er is een probleem opgetreden in de PC en de computer moet opnieuw worden opgestart. Er worden alleen fout gegevens verzameld en vervolgens kunt u de computer opnieuw opstarten.](./media/windows-stop-error-system-process-terminated/1-pc-problem-restart.png)

## <a name="cause"></a>Oorzaak

Fout 0xC000021A betekent dat **STATUS_SYSTEM_PROCESS_TERMINATED**.

Deze fout treedt op wanneer een kritiek proces, zoals WinLogon (winlogon.exe) of het client server run-time Subsystem (csrss.exe) mislukt. Zodra de kernel detecteert dat een van deze services is gestopt, wordt de fout **Stop 0xc000021a** gegenereerd. Deze fout kan verschillende oorzaken hebben, zoals:

- Er zijn niet-overeenkomende systeem bestanden geïnstalleerd.
- Een installatie van een service pack of KB-update is mislukt.
- Bij een back-upprogramma dat wordt gebruikt voor het herstellen van een harde schijf, zijn de bestanden die in gebruik waren mogelijk niet goed teruggezet.
- Er is een incompatibel programma van derden geïnstalleerd.

## <a name="solution"></a>Oplossing

### <a name="collect-the-memory-dump-file"></a>Het geheugen dump bestand verzamelen

Om dit probleem op te lossen moet de crash dump worden geanalyseerd. Verzamel het geheugen dump bestand voor de crash en neem contact op met de ondersteuning. Voer de volgende stappen uit om het dump bestand te verzamelen:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>De besturingssysteem schijf koppelen aan een nieuwe herstel-VM

1.  Gebruik stap 1-3 van de [VM-reparatie opdrachten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) om een herstel-VM voor te bereiden.
2.  Maak met behulp van **verbinding met extern bureaublad**verbinding met de herstel-VM.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Het dump bestand zoeken en een ondersteunings ticket verzenden

1.  Ga op de virtuele machine herstellen naar de map Windows in de gekoppelde besturingssysteem schijf. Als de stuur programma-letter die is toegewezen aan de gekoppelde besturingssysteem schijf F, gaat u naar F:\Windows.
2.  Zoek het bestand Memory. dmp en [Verzend een ondersteunings ticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) met het geheugen dump bestand.
3.  Als u problemen ondervindt bij het vinden van het bestand Memory. dmp, wilt u in plaats daarvan mogelijk [niet-maskeer bare interrupt-aanroepen (NMI) gebruiken in de seriële console](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) . U kunt de handleiding volgen om [een crashdumpbestand te genereren met behulp van NMI-aanroepen](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump).

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg voor meer informatie over het oplossen van problemen [veelvoorkomende opstart fouten](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-error-troubleshoot) of het [oplossen van problemen met een Windows-VM door de besturingssysteem schijf te koppelen aan een herstel-VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-windows). U moet ook vertrouwd zijn met [het gebruik van diagnostische gegevens over opstarten voor het oplossen van problemen met een virtuele machine](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics).
- Zie [Azure Resource Manager Overview](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)voor meer informatie over het gebruik van Resource Manager.
- Als u geen verbinding kunt maken met uw virtuele machine, raadpleegt u [problemen met RDP-verbindingen met een virtuele machine van Azure oplossen](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection).
