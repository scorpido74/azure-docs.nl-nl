---
title: Kan niet extern verbinding maken met Azure Virtual Machines omdat de NIC is uitgeschakeld | Microsoft Docs
description: Meer informatie over het oplossen van een probleem waarbij RDP mislukt omdat de NIC is uitgeschakeld in azure VM | Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77918237"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Kan geen extern bureau blad naar een virtuele machine, omdat de netwerk interface is uitgeschakeld

In dit artikel wordt uitgelegd hoe u een probleem kunt oplossen waarbij u geen Extern bureaublad verbinding kunt maken met Azure Windows Virtual Machines (Vm's) als de netwerk interface is uitgeschakeld.


## <a name="symptoms"></a>Symptomen

U kunt geen RDP-verbinding of een ander type verbinding maken met andere poorten naar een virtuele machine in azure, omdat de netwerk interface in de virtuele machine is uitgeschakeld.

## <a name="solution"></a>Oplossing

Voordat u deze stappen volgt, moet u een moment opname maken van de besturingssysteem schijf van de betrokken VM als back-up. Zie [snap shot a disk](../windows/snapshot-copy-managed-disk.md)(Engelstalig) voor meer informatie.

Als u de interface voor de virtuele machine wilt inschakelen, gebruikt u serieel beheer of de [netwerk interface opnieuw instellen](#reset-network-interface) voor de virtuele machine.

### <a name="use-serial-control"></a>Serieel besturings element gebruiken

1. Verbinding maken met de [seriële console en het Open cmd-exemplaar](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Als de seriële console niet op uw virtuele machine is ingeschakeld, raadpleegt u de [netwerk interface opnieuw instellen](#reset-network-interface).
2. Controleer de status van de netwerk interface:

        netsh interface show interface

    Noteer de naam van de uitgeschakelde netwerk interface.

3. De netwerk interface inschakelen:

        netsh interface set interface name="interface Name" admin=enabled

    Als de interwerk-interface bijvoorbeeld ' Ethernet 2 ' heet, voert u de volgende opdracht uit:

        netsh interface set interface name="Ethernet 2" admin=enabled

4.  Controleer de status van de netwerk interface opnieuw om er zeker van te zijn dat de netwerk interface is ingeschakeld.

        netsh interface show interface

    U hoeft de virtuele machine op dit moment niet opnieuw op te starten. De virtuele machine is terug bereikbaar.

5.  Maak verbinding met de virtuele machine en controleer of het probleem is opgelost.

## <a name="reset-network-interface"></a>Netwerk interface opnieuw instellen

Als u de netwerk interface opnieuw wilt instellen, wijzigt u het IP-adres in een ander IP-adres dat beschikbaar is in het subnet. Gebruik hiervoor Azure Portal of Azure PowerShell. Zie [netwerk interface opnieuw instellen](reset-network-interface.md)voor meer informatie.
