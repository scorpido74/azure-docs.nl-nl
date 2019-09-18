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
ms.openlocfilehash: 666868d723b5e040b69762cdb39f472f2f8822c9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057925"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Kan geen extern bureaublad met een virtuele machine omdat de netwerkinterface is uitgeschakeld

In dit artikel wordt uitgelegd hoe u een probleem in die u niet een extern bureaublad-verbinding naar Azure Windows Virtual Machines (VM's) als de netwerkinterface is uitgeschakeld op te lossen.

> [!NOTE]
> Azure heeft twee verschillende implementatiemodellen voor het maken van en werken met resources: [Resource Manager en het klassieke model](../../azure-resource-manager/resource-manager-deployment-model.md). In dit artikel bevat informatie over het Resource Manager-implementatiemodel, dat wordt u aangeraden voor nieuwe implementaties in plaats van het klassieke implementatiemodel.

## <a name="symptoms"></a>Symptomen

U kunt een RDP-verbinding of een ander type verbinding met een andere poort aanbrengen in een virtuele machine in Azure omdat de netwerkinterface op de virtuele machine is uitgeschakeld.

## <a name="solution"></a>Oplossing

Voordat u deze stappen hebt uitgevoerd, maakt u een momentopname van de besturingssysteemschijf van de betrokken virtuele machine als een back-up. Zie voor meer informatie, [momentopname maken van een schijf](../windows/snapshot-copy-managed-disk.md).

Seriële besturingselement gebruiken om in te schakelen de interface voor de virtuele machine, of [opnieuw instellen van de netwerkinterface](##reset-network-interface) voor de virtuele machine.

### <a name="use-serial-control"></a>Seriële besturingselement gebruiken

1. Verbinding maken met [seriële Console- en CMD-instantie openen](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Als de seriële Console is niet ingeschakeld op de virtuele machine, Zie [opnieuw instellen van de netwerkinterface](#reset-network-interface).
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

Als u wilt herstellen van de netwerkinterface, wijzigt u het IP-adres naar een ander IP-adres dat beschikbaar is in het Subnet. U doet dit door Azure portal of Azure PowerShell te gebruiken. Zie voor meer informatie, [opnieuw instellen van de netwerkinterface](reset-network-interface.md).
