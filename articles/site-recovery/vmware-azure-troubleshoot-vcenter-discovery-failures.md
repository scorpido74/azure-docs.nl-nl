---
title: Problemen met failback naar on-premises oplossen tijdens een nood herstel van de VMware-VM naar Azure met Azure Site Recovery
description: In dit artikel worden manieren beschreven voor het oplossen van problemen met failback en opnieuw beveiligen tijdens de nood herstel van een VMware-VM naar Azure met Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mayg
ms.openlocfilehash: e9213637f45a4761af60de9dfac7add6324f6b96
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053854"
---
# <a name="troubleshoot-vcenter-discovery-failures"></a>vCenter-detectiefouten oplossen

Dit artikel helpt u bij het oplossen van problemen die zich voordoen als gevolg van fouten in VMware vCenter-detectie.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Niet-numerieke waarden in de eigenschap maxSnapShots

Op eerdere versies dan 9,20 wordt de verbinding met vCenter verbroken wanneer een niet-numerieke waarde voor de eigenschap `snapshot.maxSnapShots` eigenschap op een virtuele machine wordt opgehaald.

Dit probleem wordt geïdentificeerd aan de hand van fout-ID 95126.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
Om het probleem op te lossen:

- Identificeer de virtuele machine en stel de waarde in op een numerieke waarde (VM-instellingen bewerken in vCenter).

Of

- Voer een upgrade uit van uw configuratie server naar versie 9,20 of hoger.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Problemen met de proxy configuratie voor de vCenter-connectiviteit

vCenter-detectie voldoet aan de systeem standaard proxy-instellingen die zijn geconfigureerd door de systeem gebruiker. De DRA-service verzorgt de proxy-instellingen van de gebruiker tijdens de installatie van de configuratie server met behulp van het installatie programma voor installatie of eicellen van de Unified Setup. 

In het algemeen wordt de proxy gebruikt om te communiceren met open bare netwerken. zoals communiceren met Azure. Als de proxy is geconfigureerd en vCenter zich in een lokale omgeving bevindt, kan deze niet communiceren met DRA.

De volgende situaties treden op wanneer dit probleem zich voordoet:

- De vCenter-Server \<vCenter-> is niet bereikbaar vanwege de volgende fout: de externe server heeft een fout geretourneerd: (503) server niet beschikbaar
- De vCenter-Server \<vCenter-> is niet bereikbaar vanwege de volgende fout: de externe server heeft een fout geretourneerd: kan geen verbinding maken met de externe server.
- Kan geen verbinding maken met de vCenter/ESXi-server.

Om het probleem op te lossen:

Down load het [PsExec-hulp programma](https://aka.ms/PsExec). 

Gebruik het hulp programma PsExec om toegang te krijgen tot de context van het systeem gebruikers en te bepalen of het proxy adres is geconfigureerd. U kunt vervolgens met behulp van de volgende procedures vCenter toevoegen aan de lijst overs Laan.

Voor detectie proxy configuratie:

1. Open IE in de context van het systeem gebruikers met het hulp programma PsExec.
    
    PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe"

2. Wijzig de proxy-instellingen in Internet Explorer om het vCenter-IP-adres te omzeilen.
3. Start de tmanssvc-service opnieuw.

Voor DRA-proxy configuratie:

1. Open een opdracht prompt en open de map Microsoft Azure Site Recovery provider.
 
    **CD C:\Program Files\Microsoft Azure Site Recovery provider**

3. Voer de volgende opdracht uit vanaf de opdracht prompt.
   
   **DRCONFIGURATOR. EXE/configure/AddBypassUrls [IP-adres/FQDN van vCenter Server beschikbaar op het moment van vCenter toevoegen]**

4. Start de DRA-Provider service opnieuw.

## <a name="next-steps"></a>Volgende stappen

[De configuratie server voor herstel na nood gevallen voor VMware VM beheren](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
