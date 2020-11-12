---
title: De VM reageert niet tijdens het wachten op de lokale sessie beheerder
description: In dit artikel worden de stappen beschreven voor het oplossen van problemen waarbij het gast besturingssysteem vastloopt op het moment dat de lokale sessie beheerder de verwerking voltooit tijdens het opstarten van een virtuele machine van Azure.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/22/2020
ms.author: v-mibufo
ms.openlocfilehash: f2f0177b5fe8bb97773d297319f6c9196d8178d2
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536231"
---
# <a name="vm-is-unresponsive-while-waiting-for-the-local-session-manager"></a>De VM reageert niet tijdens het wachten op de lokale sessie beheerder

In dit artikel worden de stappen beschreven voor het oplossen van problemen waarbij het gast besturingssysteem niet wacht totdat de lokale sessie beheerder de verwerking voltooit tijdens het opstarten van een virtuele Azure-machine (VM).

## <a name="symptoms"></a>Symptomen

Wanneer u [Diagnostische gegevens over opstarten](./boot-diagnostics.md) gebruikt om de scherm opname van de virtuele machine weer te geven, ziet u dat de scherm opname een prompt met het volgende bericht bevat: ' *wacht op de lokale sessie beheerder* '

![In de scherm afbeelding ziet u het gast besturingssysteem dat is vastgelopen in het bericht ' wacht op de lokale sessie beheerder ' in Windows Server 2012 R2.](media/vm-unresponsive-wait-local-session-manager/vm-unresponsive-wait-local-session-manager-1.png)

## <a name="cause"></a>Oorzaak

Er kunnen meerdere redenen zijn waarom een virtuele machine niet op de lokale sessie beheerder wacht. Als er wordt gewacht tot de lokale sessie beheerder een persistent probleem is, moet u een geheugen dump voor analyse verzamelen.

## <a name="solution"></a>Oplossing

In sommige gevallen wordt het probleem opgelost door simpelweg te wachten totdat het proces is voltooid. Als uw virtuele machine niet reageert en gedurende een uur op het wacht scherm blijft, moet u een geheugen dump verzamelen en contact opnemen met micro soft ondersteuning.

### <a name="collect-the-memory-dump-file"></a>Het geheugen dump bestand verzamelen

Als u dit probleem wilt oplossen, moet u eerst het geheugen dump bestand voor de crash verzamelen en vervolgens contact opnemen met de ondersteuning met het geheugen dump bestand. Voer de volgende stappen uit om het dump bestand te verzamelen:

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>De besturingssysteem schijf koppelen aan een nieuwe herstel-VM

1. Volg [de stappen 1-3 van de VM-reparatie opdrachten](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) om een herstel-VM voor te bereiden.
2. Maak verbinding met de herstel-VM met behulp van Verbinding met extern bureaublad.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Het dump bestand zoeken en een ondersteunings ticket verzenden

1. Ga op de reparatie-VM naar de map Windows in de gekoppelde besturingssysteem schijf. Als de stuur programma-letter die is toegewezen aan de gekoppelde besturingssysteem schijf, wordt aangeduid als *F* , moet u naar gaan `F:\Windows` .
2. Zoek het bestand **Memory. dmp** en [Verzend een ondersteunings ticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) met het geheugen dump bestand.
3. Als u problemen ondervindt met het vinden van het bestand **Memory. dmp** , volgt u de hand leiding voor het [genereren van een crash dump bestand met behulp van niet-maskeer bare interrupt (NMI)-aanroepen](/windows/client-management/generate-kernel-or-complete-crash-dump).

Zie voor meer informatie over NMI-aanroepen de [aanroep van niet-maskeer bare interrupt (NMI) in](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) de gebruikers handleiding voor de seriële console.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Opstart fouten van Azure Virtual Machine oplossen](boot-error-troubleshoot.md)