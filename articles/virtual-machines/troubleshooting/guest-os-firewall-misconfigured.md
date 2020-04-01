---
title: Azure VM Guest OS-firewall is verkeerd geconfigureerd | Microsoft Documenten
description: Meer informatie over het gebruik van de seriële console of offlinemethode om een verkeerd geconfigureerde firewall voor gastbesturingssysteemop een externe Azure-vm te diagnosticeren en op te lossen.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: e6f42bdf462ac5261f77bc05c62e50500345fe37
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422540"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>Azure VM-gastOS-firewall is verkeerd geconfigureerd

In dit artikel wordt uitgelegd hoe u een verkeerd geconfigureerde firewall voor gastbesturingssysteemop Azure VM oplossen.

## <a name="symptoms"></a>Symptomen

1.  Het welkomstscherm van de virtuele machine (VM) laat zien dat de VM volledig is geladen.

2.  Afhankelijk van hoe het gastbesturingssysteem is geconfigureerd, kan er een of geen netwerkverkeer zijn dat de VM bereikt.

## <a name="cause"></a>Oorzaak

Een verkeerde configuratie van de firewall van het gastsysteem kan sommige of alle soorten netwerkverkeer naar de VM blokkeren.

## <a name="solution"></a>Oplossing

Voordat u deze stappen volgt, maakt u een momentopname van de systeemschijf van de betreffende VM als back-up. Zie [Momentopname een schijf voor](../windows/snapshot-copy-managed-disk.md)meer informatie .

Als u dit probleem wilt oplossen, gebruikt u de seriële console of [herstelt u de VM offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline) door de systeemschijf van de VM aan een herstelvm te koppelen.

## <a name="online-mitigations"></a>Online oplossingen

Maak verbinding met de [seriële console en open vervolgens een PowerShell-exemplaar](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Als de seriële console niet is ingeschakeld op de VM, gaat u naar de sectie 'De VM offline herstellen' van het volgende Azure-artikel:

 [Er treedt een interne fout op wanneer u via Extern bureaublad probeert te verbinden met een Azure-VM](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)

De volgende regels kunnen worden bewerkt om toegang tot de VM (via RDP) in te schakelen of om een eenvoudigere probleemoplossingservaring te bieden:

*   Extern bureaublad (TCP-In): dit is de standaardregel die primaire toegang tot de VM biedt door RDP toe te staan in Azure.

*   Windows Remote Management (HTTP-In): met deze regel u verbinding maken met de VM met PowerShell., In Azure u met dit soort toegang het scripting-aspect van externe scripting en probleemoplossing gebruiken.

*   File and Printer Sharing (SMB-In): Met deze regel kan toegang tot netwerkdelen worden toegestaan als een optie voor het oplossen van problemen.

*   Bestand en printerdelen (Echo Request - ICMPv4-In): met deze regel u de VM pingen.

In de instantie Seriële consoletoegang u de huidige status van de firewallregel opvragen.

*   Query met de weergavenaam als parameter:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(DisplayName.*<FIREWALL RULE NAME>)" -context 9,4 | more
    ```

*   Query met behulp van de lokale poort die de toepassing gebruikt:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<APPLICATION PORT>)" -context 9,4 | more
    ```

*   Query met behulp van het lokale IP-adres dat de toepassing gebruikt:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalIP.*<CUSTOM IP>)" -context 9,4 | more
    ```

*   Als u ziet dat de regel is uitgeschakeld, u deze inschakelen door de volgende opdracht uit te voeren:

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   Voor het oplossen van problemen u de firewallprofielen uitschakelen:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Als u dit doet om de firewall correct in te stellen, schakelt u de firewall opnieuw in nadat u uw probleemoplossing hebt voltooid.

    > [!Note]
    > U hoeft de VM niet opnieuw op te starten om deze wijziging toe te passen.

*   Probeer opnieuw verbinding te maken met de VM via RDP.

### <a name="offline-mitigations"></a>Offline mitigaties

1.  Als u firewallregels wilt in- of uitschakelen, verwijst u naar [Een firewallregel inschakelen of uitschakelen op een Azure VM-gastbesturingssysteem](enable-disable-firewall-rule-guest-os.md).

2.  Controleer of u zich in de [firewall van het gastbesturingssysteem bevindt die het inkomende verkeersscenario blokkeert.](guest-os-firewall-blocking-inbound-traffic.md)

3.  Als u nog steeds twijfelt of de firewall uw toegang blokkeert, raadpleegt u [De gast-OS Firewall uitschakelen in Azure VM](disable-guest-os-firewall-windows.md)en schakelt u de firewall van het gastsysteem opnieuw in met behulp van de juiste regels.

