---
title: Kan geen verbinding maken op afstand naar Azure Virtual Machines omdat de NIC is uitgeschakeld. | Microsoft Docs
description: Meer informatie over het oplossen van problemen waarbij RDP mislukt, omdat de NIC is uitgeschakeld in virtuele Azure-machine | Microsoft Docs
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
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77918237"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Kan geen extern bureaublad met een virtuele machine omdat de netwerkinterface is uitgeschakeld

In dit artikel wordt uitgelegd hoe u een probleem in die u niet een extern bureaublad-verbinding naar Azure Windows Virtual Machines (VM's) als de netwerkinterface is uitgeschakeld op te lossen.


## <a name="symptoms"></a>Symptomen

U kunt een RDP-verbinding of een ander type verbinding met een andere poort aanbrengen in een virtuele machine in Azure omdat de netwerkinterface op de virtuele machine is uitgeschakeld.

## <a name="solution"></a>Oplossing

Voordat u deze stappen hebt uitgevoerd, maakt u een momentopname van de besturingssysteemschijf van de betrokken virtuele machine als een back-up. Zie [snap shot a disk](../windows/snapshot-copy-managed-disk.md)(Engelstalig) voor meer informatie.

Als u de interface voor de virtuele machine wilt inschakelen, gebruikt u serieel beheer of de [netwerk interface opnieuw instellen](#reset-network-interface) voor de virtuele machine.

### <a name="use-serial-control"></a>Seriële besturingselement gebruiken

1. Verbinding maken met de [seriële console en het Open cmd-exemplaar](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Als de seriële console niet op uw virtuele machine is ingeschakeld, raadpleegt u de [netwerk interface opnieuw instellen](#reset-network-interface).
2. Controleer de status van de netwerkinterface:

        netsh interface show interface

    Noteer de naam van de uitgeschakelde netwerkinterface.

3. De netwerkinterface inschakelen:

        netsh interface set interface name="interface Name" admin=enabled

    Bijvoorbeeld, als de interwork-interface is de naam 'Ethernet 2', voer de volgende opdracht:

        netsh interface set interface name="Ethernet 2" admin=enabled

4.  Controleer de status van de netwerkinterface opnieuw om het ervoor te zorgen dat de netwerkinterface is ingeschakeld.

        netsh interface show interface

    U hebt geen op dit moment de virtuele machine opnieuw. De virtuele machine is weer bereikbaar is.

5.  Verbinding maken met de virtuele machine en controleren of het probleem opgelost is.

## <a name="reset-network-interface"></a>De netwerkinterface opnieuw instellen

Als u wilt herstellen van de netwerkinterface, wijzigt u het IP-adres naar een ander IP-adres dat beschikbaar is in het Subnet. U doet dit door Azure portal of Azure PowerShell te gebruiken. Zie [netwerk interface opnieuw instellen](reset-network-interface.md)voor meer informatie.
