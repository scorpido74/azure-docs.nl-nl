---
title: Azure VM-Gast-OS-firewall is onjuist geconfigureerd | Microsoft Docs
description: ''
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
ms.openlocfilehash: 8f04d943e1db49beed13c183fbd06e401546fc03
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71153895"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>Azure VM-Gast OS firewall is onjuist geconfigureerd

In dit artikel over het oplossen van onjuist geconfigureerde gastbesturingssysteem system-firewall op de virtuele machine in Azure introduceren.

## <a name="symptoms"></a>Symptomen

1.  Het welkomstscherm van de virtuele machine (VM) ziet u dat de virtuele machine volledig geladen is.

2.  Afhankelijk van hoe de Gast-besturingssysteem is geconfigureerd, kunnen er bepaalde of geen van de bereiken van de virtuele machine netwerkverkeer.

## <a name="cause"></a>Oorzaak

Een onjuiste configuratie van de firewall van de Gast-systeem kan sommige of alle soorten netwerkverkeer naar de virtuele machine blokkeren.

## <a name="solution"></a>Oplossing

Voordat u deze stappen hebt uitgevoerd, maakt u een momentopname van de schijf van de betrokken virtuele machine als een back-up. Zie voor meer informatie, [momentopname maken van een schijf](../windows/snapshot-copy-managed-disk.md).

Om dit probleem wilt oplossen, gebruikt u de seriële Console of [herstel de virtuele machine offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline) door het koppelen van de schijf van de virtuele machine aan een virtuele machine voor herstel.

## <a name="online-mitigations"></a>Online oplossingen

Verbinding maken met de [seriële Console en open vervolgens een PowerShell-sessie](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Als de seriële Console is niet ingeschakeld op de virtuele machine, gaat u naar de sectie 'Herstel de virtuele machine Offline' van de volgende Azure-artikel:

 [Een interne fout treedt op wanneer u probeert verbinding maken met een Azure-VM via Extern bureaublad](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)

De volgende regels kunnen worden bewerkt voor de toegang tot de virtuele machine (via RDP) of om een makkelijker troubleshooting ervaring te bieden:

*   Extern bureaublad (TCP-in): Dit is de standaard regel die primaire toegang biedt tot de virtuele machine door RDP in azure toe te staan.

*   Windows Remote Management (HTTP-in): Met deze regel kunt u verbinding maken met de virtuele machine met behulp van Power shell. in azure kunt u met dit soort toegang het script aspect van externe scripts en probleem oplossing gebruiken.

*   Bestands-en printer deling (SMB-in): Deze regel maakt toegang tot netwerk shares mogelijk als optie voor het oplossen van problemen.

*   Bestands-en printer deling (ECHO aanvraag-ICMPv4-in): Met deze regel kunt u de virtuele machine pingen.

U kunt de huidige status van de firewallregel opvragen in de toegang tot de seriële Console-exemplaar.

*   Query's uitvoeren met behulp van de weergegeven naam als een parameter:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(DisplayName.*<FIREWALL RULE NAME>)" -context 9,4 | more
    ```

*   Query's uitvoeren met behulp van de lokale poort die gebruikmaakt van de toepassing:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<APPLICATION PORT>)" -context 9,4 | more
    ```

*   Query's uitvoeren met behulp van het lokale IP-adres die gebruikmaakt van de toepassing:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalIP.*<CUSTOM IP>)" -context 9,4 | more
    ```

*   Als u ziet dat de regel is uitgeschakeld, kunt u deze inschakelen door het uitvoeren van de volgende opdracht uit:

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   Voor het oplossen van problemen, kunt u de firewallprofielen uitschakelen:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Als u dit om in te stellen de firewall correct doet, opnieuw de firewall inschakelen nadat u klaar bent met het oplossen van problemen.

    > [!Note]
    > U hebt geen opnieuw opstarten van de virtuele machine als deze wijziging wilt toepassen.

*   Probeer het opnieuw verbinding maken met de virtuele machine via RDP.

### <a name="offline-mitigations"></a>Offline oplossingen

1.  Als u firewall regels wilt in-of uitschakelen, raadpleegt u [een firewall regel op een Azure VM-gast besturingssysteem in-of uitschakelen](enable-disable-firewall-rule-guest-os.md).

2.  Controleer of u zich in de [Guest OS firewall geblokkeerd binnenkomend verkeer scenario](guest-os-firewall-blocking-inbound-traffic.md).

3.  Als u nog steeds niet zeker weet of de firewall de toegang blokkeert, verwijzen naar [uitschakelen van de Gast OS-Firewall in Azure VM](disable-guest-os-firewall-windows.md), en vervolgens de firewall van de Gast-systeem met behulp van de juiste regels weer inschakelen.

