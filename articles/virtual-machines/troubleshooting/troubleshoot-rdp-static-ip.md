---
title: Kan geen extern bureaublad naar Azure Virtual Machines vanwege statisch IP-adres | Microsoft Docs
description: Meer informatie over het RDP-probleem dat wordt veroorzaakt door een statische IP-adres in Microsoft Azure op te lossen. | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/08/2018
ms.author: genli
ms.openlocfilehash: d1cb34f1666db0e213a41fa8d2e684ba148b7e61
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088446"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Kan geen extern bureaublad naar Azure Virtual Machines vanwege statisch IP-adres

In dit artikel beschrijft een probleem waarbij u kunt geen extern bureaublad naar Azure Windows Virtual Machines (VM's) nadat een statisch IP-adres is geconfigureerd in de virtuele machine.

> [!NOTE]
> Azure heeft twee verschillende implementatiemodellen voor het maken van en werken met resources: [Resource Manager en het klassieke model](../../azure-resource-manager/resource-manager-deployment-model.md). In dit artikel bevat informatie over het Resource Manager-implementatiemodel, dat wordt u aangeraden voor nieuwe implementaties in plaats van het klassieke implementatiemodel.

## <a name="symptoms"></a>Symptomen

Wanneer u een RDP-verbinding met een virtuele machine in Azure maakt, ontvangt u de volgende strekking weergegeven:

**Extern bureaublad kan geen verbinding maken met de externe computer voor een van de volgende redenen:**

1. **Externe toegang tot de server is niet ingeschakeld**

2. **De externe Computer is uitgeschakeld**

3. **De externe computer is niet beschikbaar op het netwerk**

**Zorg ervoor dat de externe computer is ingeschakeld en verbonden met het netwerk, en dat externe toegang is ingeschakeld.**

Wanneer u de schermafbeelding controleren in de [diagnostische gegevens over opstarten](../troubleshooting/boot-diagnostics.md) in Azure portal, ziet u de virtuele machine normaal worden opgestart en wacht tot de referenties in het aanmeldingsscherm.

## <a name="cause"></a>Oorzaak

De virtuele machine heeft een statisch IP-adres dat gedefinieerd in de netwerkinterface in Windows. Dit IP-adres verschilt van het adres dat gedefinieerd in de Azure-portal.

## <a name="solution"></a>Oplossing

Voordat u deze stappen hebt uitgevoerd, maakt u een momentopname van de besturingssysteemschijf van de betrokken virtuele machine als een back-up. Zie voor meer informatie, [momentopname maken van een schijf](../windows/snapshot-copy-managed-disk.md).

U lost dit probleem, kunt u seriële besturingselement gebruiken om DHCP of [opnieuw instellen van de netwerkinterface](reset-network-interface.md) voor de virtuele machine.

### <a name="use-serial-control"></a>Seriële besturingselement gebruiken

1. Verbinding maken met [seriële Console- en CMD-instantie openen](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Als de seriële Console is niet ingeschakeld op de virtuele machine, Zie [opnieuw instellen van de netwerkinterface](reset-network-interface.md).
2. Controleer als de DHCP is uitgeschakeld op de netwerkinterface:

        netsh interface ip show config
3. Als de DHCP is uitgeschakeld, kunt u de configuratie van de netwerkinterface op het gebruik van DHCP ongedaan maken:

        netsh interface ip set address name="<NIC Name>" source=dhc

    Bijvoorbeeld, als de interwork interface namen 'Ethernet 2', voer de volgende opdracht:

        netsh interface ip set address name="Ethernet 2" source=dhc

4. Query uitvoeren op de IP-configuratie opnieuw om het ervoor te zorgen dat de netwerkinterface is nu correct ingesteld. Het nieuwe IP-adres moet overeenkomen met de versie die wordt geleverd door Azure.

        netsh interface ip show config

    U hebt geen op dit moment de virtuele machine opnieuw. De virtuele machine is weer bereikbaar is.

Hierna als u configureren van het statische IP-adres voor de virtuele machine wilt, Zie [statische IP-adressen configureren voor een virtuele machine](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md).