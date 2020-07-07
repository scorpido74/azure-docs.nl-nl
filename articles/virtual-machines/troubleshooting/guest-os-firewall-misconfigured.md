---
title: De firewall van het Azure VM-gast besturingssysteem is onjuist geconfigureerd | Microsoft Docs
description: Meer informatie over het gebruik van de seriële console of de offline methode om een onjuist geconfigureerde gast besturingssysteem firewall te diagnosticeren en op te lossen op een externe virtuele machine van Azure.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80422540"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>De firewall van het Azure VM-gast besturingssysteem is onjuist geconfigureerd

In dit artikel vindt u informatie over het oplossen van een onjuist geconfigureerde gast besturingssysteem firewall op Azure VM.

## <a name="symptoms"></a>Symptomen

1.  In het welkomst scherm van de virtuele machine (VM) ziet u dat de VM volledig is geladen.

2.  Afhankelijk van hoe het gast besturingssysteem is geconfigureerd, is er mogelijk een of geen netwerk verkeer dat de virtuele machine bereikt.

## <a name="cause"></a>Oorzaak

Een onjuiste configuratie van de firewall van het gast systeem kan sommige of alle soorten netwerk verkeer naar de virtuele machine blok keren.

## <a name="solution"></a>Oplossing

Voordat u deze stappen volgt, moet u een moment opname maken van de systeem schijf van de betrokken VM als back-up. Zie [snap shot a disk](../windows/snapshot-copy-managed-disk.md)(Engelstalig) voor meer informatie.

U kunt dit probleem oplossen door de seriële console te gebruiken of [de virtuele machine offline te herstellen](troubleshoot-rdp-internal-error.md#repair-the-vm-offline) door de systeem schijf van de virtuele machine aan een herstel-VM te koppelen.

## <a name="online-mitigations"></a>Online beperkende factoren

Maak verbinding met de [seriële console en open vervolgens een Power shell-exemplaar](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Als de seriële console niet is ingeschakeld op de virtuele machine, gaat u naar de sectie ' de virtuele machine offline herstellen ' van het volgende Azure-artikel:

 [Er treedt een interne fout op wanneer u via Extern bureaublad probeert te verbinden met een Azure-VM](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)

De volgende regels kunnen worden bewerkt om toegang tot de virtuele machine (via RDP) in te scha kelen of om een eenvoudiger probleemoplossings ervaring te bieden:

*   Extern bureaublad (TCP-in): dit is de standaard regel die primaire toegang biedt tot de virtuele machine door RDP in azure toe te staan.

*   Windows Remote Management (HTTP-in): met deze regel kunt u verbinding maken met de virtuele machine met behulp van Power shell. in azure kunt u met dit soort toegang gebruikmaken van het script aspect van externe scripts en probleem oplossing.

*   Bestands-en printer deling (SMB-in): met deze regel wordt de toegang tot netwerk shares ingeschakeld als optie voor het oplossen van problemen.

*   Bestands-en printer deling (ECHO aanvraag-ICMPv4-in): met deze regel kunt u de virtuele machine pingen.

In het seriële console toegangs exemplaar kunt u een query uitvoeren op de huidige status van de firewall regel.

*   Query met behulp van de weergave naam als para meter:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(DisplayName.*<FIREWALL RULE NAME>)" -context 9,4 | more
    ```

*   Query's uitvoeren met behulp van de lokale poort die door de toepassing wordt gebruikt:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<APPLICATION PORT>)" -context 9,4 | more
    ```

*   Query met behulp van het lokale IP-adres dat de toepassing gebruikt:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalIP.*<CUSTOM IP>)" -context 9,4 | more
    ```

*   Als u ziet dat de regel is uitgeschakeld, kunt u deze inschakelen door de volgende opdracht uit te voeren:

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   Voor het oplossen van problemen kunt u de Firewall profielen uitschakelen:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Als u dit doet om de firewall op de juiste wijze in te stellen, moet u de firewall opnieuw inschakelen nadat u de probleem oplossing hebt voltooid.

    > [!Note]
    > U hoeft de virtuele machine niet opnieuw op te starten om deze wijziging toe te passen.

*   Probeer opnieuw verbinding te maken met de virtuele machine via RDP.

### <a name="offline-mitigations"></a>Offline oplossingen

1.  Als u firewall regels wilt in-of uitschakelen, raadpleegt u [een firewall regel op een Azure VM-gast besturingssysteem in-of uitschakelen](enable-disable-firewall-rule-guest-os.md).

2.  Controleer of u zich in de firewall van het gast besturingssysteem bevindt [scenario voor binnenkomend verkeer blok keren](guest-os-firewall-blocking-inbound-traffic.md).

3.  Als u nog steeds niet zeker weet of de firewall uw toegang blokkeert, raadpleegt u [de firewall voor het gast besturingssysteem uitschakelen in azure VM](disable-guest-os-firewall-windows.md)en vervolgens de firewall van het gast systeem opnieuw inschakelen met de juiste regels.

