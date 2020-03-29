---
title: Kan niet op afstand verbinding maken met virtuele azure-machines omdat de NIC is uitgeschakeld | Microsoft Documenten
description: Meer informatie over het oplossen van een probleem waarbij RDP mislukt omdat de NIC is uitgeschakeld in Azure VM| Microsoft Documenten
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/12/2018
ms.author: genli
ms.openlocfilehash: 315974e4995630eb3af055ac0e1c44f7d8dd0737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918237"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Kan extern bureaublad niet naar een vm omdat de netwerkinterface is uitgeschakeld

In dit artikel wordt uitgelegd hoe u een probleem oplossen waarbij u geen Verbinding met Extern bureaublad maken met Azure Windows Virtual Machines (VM's) als de netwerkinterface is uitgeschakeld.


## <a name="symptoms"></a>Symptomen

U geen RDP-verbinding of een ander type verbinding met andere poorten maken met een VM in Azure omdat de netwerkinterface in de VM is uitgeschakeld.

## <a name="solution"></a>Oplossing

Voordat u deze stappen volgt, maakt u een momentopname van de OS-schijf van de betreffende VM als back-up. Zie [Momentopname een schijf voor](../windows/snapshot-copy-managed-disk.md)meer informatie .

Als u de interface voor de virtuele machine wilt inschakelen, gebruikt u Seriële besturingselement of [reset de netwerkinterface](#reset-network-interface) voor de VM.

### <a name="use-serial-control"></a>Seriële besturingselement gebruiken

1. Maak verbinding [met seriële console en open CMD-instantie](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Zie [Netwerkinterface opnieuw instellen](#reset-network-interface)als de seriële console niet is ingeschakeld op uw vm.
2. Controleer de status van de netwerkinterface:

        netsh interface show interface

    Let op de naam van de uitgeschakelde netwerkinterface.

3. Schakel de netwerkinterface in:

        netsh interface set interface name="interface Name" admin=enabled

    Als de interwork-interface bijvoorbeeld 'Ethernet 2' heet, voert u de volgende opdracht uit:

        netsh interface set interface name="Ethernet 2" admin=enabled

4.  Controleer opnieuw de status van de netwerkinterface om ervoor te zorgen dat de netwerkinterface is ingeschakeld.

        netsh interface show interface

    U hoeft de VM op dit moment niet opnieuw op te starten. De VM is weer bereikbaar.

5.  Maak verbinding met de VIRTUELE V.S. en kijk of het probleem is opgelost.

## <a name="reset-network-interface"></a>Netwerkinterface opnieuw instellen

Als u de netwerkinterface opnieuw wilt instellen, wijzigt u het IP-adres naar een ander IP-adres dat beschikbaar is in het subnet. Gebruik hiervoor Azure portal of Azure PowerShell. Zie [Netwerkinterface opnieuw instellen](reset-network-interface.md)voor meer informatie.
