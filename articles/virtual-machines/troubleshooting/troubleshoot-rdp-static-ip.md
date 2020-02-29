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
ms.openlocfilehash: 92ad33fbc759605ae901c3bcf09283c8e0b1c4b5
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77918186"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Kan geen extern bureaublad naar Azure Virtual Machines vanwege statisch IP-adres

In dit artikel beschrijft een probleem waarbij u kunt geen extern bureaublad naar Azure Windows Virtual Machines (VM's) nadat een statisch IP-adres is geconfigureerd in de virtuele machine.


## <a name="symptoms"></a>Symptomen

Wanneer u een RDP-verbinding met een virtuele machine in Azure maakt, ontvangt u de volgende strekking weergegeven:

**Extern bureaublad kunt geen verbinding met de externe computer maken om een van de volgende redenen:**

1. **Externe toegang tot de server is niet ingeschakeld**

2. **De externe computer is uitgeschakeld**

3. **De externe computer is niet beschikbaar op het netwerk**

**Zorg ervoor dat de externe computer is ingeschakeld en is verbonden met het netwerk en dat externe toegang is ingeschakeld.**

Wanneer u de scherm opname van de [Diagnostische gegevens over opstarten](../troubleshooting/boot-diagnostics.md) in de Azure Portal controleert, ziet u dat de VM normaal wordt opgestart en wacht op referenties in het aanmeldings scherm.

## <a name="cause"></a>Oorzaak

De virtuele machine heeft een statisch IP-adres dat gedefinieerd in de netwerkinterface in Windows. Dit IP-adres verschilt van het adres dat gedefinieerd in de Azure-portal.

## <a name="solution"></a>Oplossing

Voordat u deze stappen hebt uitgevoerd, maakt u een momentopname van de besturingssysteemschijf van de betrokken virtuele machine als een back-up. Zie [snap shot a disk](../windows/snapshot-copy-managed-disk.md)(Engelstalig) voor meer informatie.

U kunt dit probleem oplossen door met behulp van seriële controle DHCP in te scha kelen of de [netwerk interface](reset-network-interface.md) voor de virtuele machine opnieuw in te stellen.

### <a name="use-serial-control"></a>Seriële besturingselement gebruiken

1. Verbinding maken met de [seriële console en het Open cmd-exemplaar](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Als de seriële console niet op uw virtuele machine is ingeschakeld, raadpleegt u de [netwerk interface opnieuw instellen](reset-network-interface.md).
2. Controleer als de DHCP is uitgeschakeld op de netwerkinterface:

        netsh interface ip show config
3. Als de DHCP is uitgeschakeld, kunt u de configuratie van de netwerkinterface op het gebruik van DHCP ongedaan maken:

        netsh interface ip set address name="<NIC Name>" source=dhc

    Bijvoorbeeld, als de interwork interface namen 'Ethernet 2', voer de volgende opdracht:

        netsh interface ip set address name="Ethernet 2" source=dhc

4. Query uitvoeren op de IP-configuratie opnieuw om het ervoor te zorgen dat de netwerkinterface is nu correct ingesteld. Het nieuwe IP-adres moet overeenkomen met de versie die wordt geleverd door Azure.

        netsh interface ip show config

    U hebt geen op dit moment de virtuele machine opnieuw. De virtuele machine is weer bereikbaar is.

Als u hierna het statische IP-adres voor de virtuele machine wilt configureren, raadpleegt u [statische IP-adressen configureren voor een virtuele machine](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md).