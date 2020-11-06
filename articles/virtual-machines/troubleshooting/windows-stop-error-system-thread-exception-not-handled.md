---
title: Windows-Stop fout-uitzonde ring in 0x0000007E-systeem thread niet verwerkt
description: In dit artikel worden de stappen beschreven voor het oplossen van problemen waarbij het gast besturingssysteem een probleem aantreft en de Azure-VM wil starten. In het bericht wordt vermeld dat ' een systeem thread-uitzonde ring niet is afgehandeld '.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/04/2020
ms.author: v-mibufo
ms.openlocfilehash: dd18d69009b9c150c4c12a755e9060cd5dfaccae
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424035"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Windows-Stop fout-uitzonde ring in 0x0000007E-systeem thread niet verwerkt

In dit artikel worden de stappen beschreven voor het oplossen van problemen waarbij het gast besturingssysteem een probleem aantreft en de Azure-VM wil starten. In het bericht wordt vermeld dat ' een systeem thread-uitzonde ring niet is afgehandeld '.

## <a name="symptoms"></a>Symptomen

Wanneer u [Diagnostische gegevens over opstarten](./boot-diagnostics.md) gebruikt om de scherm opname van de virtuele machine weer te geven, ziet u dat er een scherm opname wordt weer gegeven waarin wordt aangegeven dat de computer opnieuw moet worden opgestart met de stop code **systeem thread uitzondering niet verwerkt** of de fout code **0x0000007E**.

![In de scherm opname ziet u Windows vastgelopen tijdens het opstarten met het bericht: ' er is een probleem opgetreden in de PC en de computer moet opnieuw worden opgestart. De computer wordt opnieuw opgestart. " Stop code: ' de systeem THREAD-uitzonde ring is niet verwerkt '](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>Oorzaak

De oorzaak kan niet worden bepaald totdat een geheugen dump bestand is geanalyseerd. Blijf het geheugen dump bestand verzamelen.

## <a name="solution"></a>Oplossing

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
> [Opstart fouten van Azure Virtual Machine oplossen](./boot-error-troubleshoot.md)