---
title: Kan extern bureaublad niet naar Azure Virtual Machines vanwege statisch IP| Microsoft Documenten
description: Meer informatie over het oplossen van RDP-problemen die worden veroorzaakt door statisch IP in Microsoft Azure.| Microsoft Documenten
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918186"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Kan extern bureaublad niet naar Azure Virtual Machines vanwege statisch IP-adres

In dit artikel wordt een probleem beschreven waarbij u geen extern bureaublad naar Azure Windows Virtual Machines (VM's) gebruiken nadat een statisch IP-adres is geconfigureerd in de VM.


## <a name="symptoms"></a>Symptomen

Wanneer u een RDP-verbinding maakt met een VM in Azure, ontvangt u het volgende foutbericht:

**Extern bureaublad kan om een van de volgende redenen geen verbinding maken met de externe computer:**

1. **Externe toegang tot de server is niet ingeschakeld**

2. **De externe computer is uitgeschakeld**

3. **De externe computer is niet beschikbaar in het netwerk**

**Controleer of de externe computer is ingeschakeld en verbonden is met het netwerk en dat externe toegang is ingeschakeld.**

Wanneer u de schermafbeelding in de [opstartdiagnose](../troubleshooting/boot-diagnostics.md) in de Azure-portal controleert, ziet u de VM-laarzen normaal en wacht u op referenties in het aanmeldingsscherm.

## <a name="cause"></a>Oorzaak

De VM heeft een statisch IP-adres dat is gedefinieerd op de netwerkinterface binnen Windows. Dit IP-adres verschilt van het adres dat is gedefinieerd in de Azure-portal.

## <a name="solution"></a>Oplossing

Voordat u deze stappen volgt, maakt u een momentopname van de OS-schijf van de betreffende VM als back-up. Zie [Momentopname een schijf voor](../windows/snapshot-copy-managed-disk.md)meer informatie .

Als u dit probleem wilt oplossen, gebruikt u Seriële besturingselementen om DHCP in te schakelen of [de netwerkinterface](reset-network-interface.md) voor de vm opnieuw in te schakelen.

### <a name="use-serial-control"></a>Seriële besturingselement gebruiken

1. Maak verbinding [met seriële console en open CMD-instantie](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Zie [Netwerkinterface opnieuw instellen](reset-network-interface.md)als de seriële console niet is ingeschakeld op uw vm.
2. Controleer of de DHCP is uitgeschakeld op de netwerkinterface:

        netsh interface ip show config
3. Als de DHCP is uitgeschakeld, moet u de configuratie van uw netwerkinterface omschakelen om DHCP te gebruiken:

        netsh interface ip set address name="<NIC Name>" source=dhc

    Als de interwork-interface bijvoorbeeld 'Ethernet 2' noemt, voert u de volgende opdracht uit:

        netsh interface ip set address name="Ethernet 2" source=dhc

4. Vraag de IP-configuratie opnieuw op om ervoor te zorgen dat de netwerkinterface nu correct is ingesteld. Het nieuwe IP-adres moet overeenkomen met het adres dat wordt geleverd door de Azure.

        netsh interface ip show config

    U hoeft de VM op dit moment niet opnieuw op te starten. De VM is weer bereikbaar.

Zie [Statische IP-adressen configureren voor een vm](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md)als u het statische IP voor de vm wilt configureren.