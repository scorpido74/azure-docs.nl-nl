---
title: Problemen met VMware vCenter-detectiefouten in Azure Site Recovery oplossen
description: In dit artikel wordt beschreven hoe u vMware vCenter-detectiefouten oplossen in Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mayg
ms.openlocfilehash: f00c7b12accde9df9a5708a2b8b378d70428318d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74091242"
---
# <a name="troubleshoot-vcenter-server-discovery-failures"></a>Problemen met vCenter Server-detectiefouten oplossen

In dit artikel u problemen oplossen die optreden als gevolg van vMware vCenter-detectiefouten.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Niet-numerieke waarden in de eigenschap maxSnapShots

Op versies vóór 9.20 wordt de verbinding verbroken wanneer `snapshot.maxSnapShots` vCenter een niet-numerieke waarde voor de eigenschap eigenschap op een vm ophaalt.

Dit probleem wordt geïdentificeerd door fout-id 95126.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
Ga als ander op zoek naar het probleem:

- Identificeer de VM en stel de waarde in op een numerieke waarde (VM-bewerkingsinstellingen in vCenter).

of

- Upgrade uw configuratieserver naar versie 9.20 of hoger.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Problemen met proxyconfiguratie voor vCenter-connectiviteit

vCenter Discovery eert de standaardproxy-instellingen van het systeem die zijn geconfigureerd door de systeemgebruiker. De DRA-service eert de proxy-instellingen die door de gebruiker worden verstrekt tijdens de installatie van de configuratieserver met behulp van de uniforme installatie-installatie- of OVA-sjabloon. 

In het algemeen wordt de proxy gebruikt om te communiceren met openbare netwerken; zoals communiceren met Azure. Als de proxy is geconfigureerd en vCenter zich in een lokale omgeving bevindt, kan deze niet met DRA communiceren.

De volgende situaties doen zich voor wanneer dit probleem wordt ondervonden:

- VCenter-servervCenter-> \<niet bereikbaar is vanwege de fout: de externe server heeft een fout geretourneerd: (503) Server niet beschikbaar
- De vCenter-server \<vCenter-> niet bereikbaar is vanwege de fout: de externe server heeft een fout geretourneerd: kan geen verbinding maken met de externe server.
- Kan geen verbinding maken met vCenter/ESXi-server.

Ga als ander op zoek naar het probleem:

Download de [PsExec tool](https://aka.ms/PsExec). 

Gebruik het gereedschap PsExec om toegang te krijgen tot de gebruikerscontext van het systeem en te bepalen of het proxyadres is geconfigureerd. U vervolgens vCenter toevoegen aan de bypasslijst met behulp van de volgende procedures.

Voor detectieproxyconfiguratie:

1. Open IE in de context van de systeemgebruiker met de PsExec-tool.
    
    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. Wijzig de proxy-instellingen in Internet Explorer om het vCenter-IP-adres te omzeilen.
3. Start de tmanssvc-service opnieuw.

Voor DRA-proxyconfiguratie:

1. Open een opdrachtprompt en open de map Microsoft Azure Site Recovery Provider.
 
    **cd C:\Program Files\Microsoft Azure Site Recovery Provider**

3. Voer in de opdrachtprompt de volgende opdracht uit.
   
   **DRCONFIGURATOR. EXE /configure /AddBypassUrls [IP-adres/FQDN van vCenter Server op het moment van vCenter toevoegen]**

4. Start de DRA-providerservice opnieuw.

## <a name="next-steps"></a>Volgende stappen

[De configuratieserver voor VMware VM-noodherstel beheren](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
