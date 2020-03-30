---
title: Windows Server/System Center VMM 2012 R2 upgraden naar Windows Server 2016-Azure-siteherstel
description: Meer informatie over het instellen van noodherstel voor Azure voor Azure Stack VM's met de Azure Site Recovery-service.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 12/03/2018
ms.author: rajanaki
ms.openlocfilehash: 1d94935db542a0e64754ab8769996fe906f88b46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954409"
---
# <a name="upgrade-windows-server-serversystem-center-2012-r2-vmm-to-windows-servervmm-2016"></a>Windows Server Server/System Center 2012 R2 VMM upgraden naar Windows Server/VMM 2016 

In dit artikel ziet u hoe u Windows Server 2012 R2-hosts & SCVMM 2012 R2 upgraden die zijn geconfigureerd met Azure Site Recovery, naar Windows Server 2016 & SCVMM 2016

Site Recovery draagt bij aan uw business continuity en disaster recovery (BCDR) strategie. De service zorgt ervoor dat uw VM-workloads beschikbaar blijven wanneer er onverwachte storingen optreden.

> [!IMPORTANT]
> Wanneer u Windows Server 2012 R2-hosts upgradet die al zijn geconfigureerd voor replicatie met Azure Site Recovery, moet u de in dit document vermelde stappen volgen. Elk alternatief pad dat is gekozen voor een upgrade kan resulteren in niet-ondersteunde statussen en kan resulteren in een onderbreking van de replicatie of de mogelijkheid om failover uit te voeren.


In dit artikel leert u hoe u de volgende configuraties in uw omgeving upgraden:

> [!div class="checklist"]
> * **Windows Server 2012 R2-hosts die niet worden beheerd door SCVMM** 
> * **Windows Server 2012 R2-hosts die worden beheerd door een zelfstandige SCVMM 2012 R2-server** 
> * **Windows Server 2012 R2-hosts die worden beheerd door zeer beschikbare SCVMM 2012 R2-server**


## <a name="prerequisites--factors-to-consider"></a>Voorwaarden & factoren om rekening mee te houden

Noteer voordat u een upgrade uitvoert: -

- Als u Windows Server 2012 R2-hosts hebt die niet worden beheerd door SCVMM en het is een zelfstandige omgevingsinstelling, is er een onderbreking in de replicatie als u de upgrade probeert uit te voeren.
- Als u "*mijn sleutels niet in Active Directory opslaan onder Gedistribueerd sleutelbeheer*" had geselecteerd tijdens het installeren van SCVMM 2012 R2, worden de upgrades niet voltooid.

- Als u System Center 2012 R2 VMM gebruikt, 

    - Controleer de databasegegevens op VMM: Algemene -> **database-instellingen** ->  **vmm-console****General** -> **Database connection**
    - Controleer de serviceaccounts die worden gebruikt voor de System Center Virtual Machine Manager Agent-service
    - Zorg ervoor dat u een back-up van de VMM-database hebt.
    - Noteer de databasenaam van de betrokken SCVMM-servers. Dit kan door te navigeren naar De**Algemene** -> **Databaseverbinding** **VMM-consoleinstellingen** -> **Settings** -> 
    - Noteer de VMM-id van zowel de primaire 2012R2-primaire als herstelVMM-servers. VMM ID is te vinden in het register "HKLM:\SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Setup".
    - Zorg ervoor dat de nieuwe SCVM's die u aan het cluster toevoegt, dezelfde namen hebben als voorheen. 

- Als u reproduceert tussen twee van uw sites die worden beheerd door SCVM's aan beide zijden, moet u ervoor zorgen dat u eerst uw herstelkant upgradet voordat u de primaire kant upgradet.
  > [!WARNING]
  > Tijdens het upgraden van de SCVMM 2012 R2 selecteert u onder Gedistribueerd sleutelbeheer de optie om **versleutelingssleutels op**te slaan in Active Directory. Kies de instellingen voor het serviceaccount en gedistribueerd sleutelbeheer zorgvuldig. Op basis van uw selectie zijn versleutelde gegevens zoals wachtwoorden in sjablonen mogelijk niet beschikbaar na de upgrade en kunnen ze mogelijk van invloed zijn op replicatie met Azure Site Recovery

> [!IMPORTANT]
> Raadpleeg de gedetailleerde SCVMM documentatie van [de vereisten](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#requirements-and-limitations)

## <a name="windows-server-2012-r2-hosts-which-arent-managed-by-scvmm"></a>Windows Server 2012 R2-hosts die niet worden beheerd door SCVMM 
De onderstaande lijst met stappen is van toepassing op de gebruikersconfiguratie van [Hyper-V-hosts naar Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-architecture) die is uitgevoerd door deze [zelfstudie](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial) te volgen

> [!WARNING]
> Zoals vermeld in de vereisten, zijn deze stappen alleen van toepassing op een geclusterd omgevingsscenario en niet in een zelfstandige Hyper-V-hostconfiguratie.

1. Volg de stappen om de [upgrade van het rollende cluster](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) uit te voeren. om het upgradeproces voor rollende cluster uit te voeren.
2. Met elke nieuwe Windows Server 2016-host die in het cluster wordt geïntroduceerd, verwijdert u de verwijzing naar een Windows Server 2012 R2-host uit Azure Site Recovery door de volgende stappen te volgen [hier]. Dit moet de host zijn die je hebt gekozen om & uit het cluster te verwijderen.
3. Zodra de opdracht *Update-VMVersion* voor alle virtuele machines is uitgevoerd, zijn de upgrades voltooid. 
4. Gebruik de [hier](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-the-source-environment) genoemde stappen om de nieuwe Windows Server 2016-host te registreren bij Azure Site Recovery. Houd er rekening mee dat de Hyper-V-site al actief is en dat u alleen de nieuwe host in het cluster hoeft te registreren. 
5.  Ga naar Azure portal en controleer de gerepliceerde status in de Recovery Services

## <a name="upgrade-windows-server-2012-r2-hosts-managed-by-stand-alone-scvmm-2012-r2-server"></a>Windows Server 2012 R2-hosts upgraden die worden beheerd door zelfstandige SCVMM 2012 R2-server
Voordat u uw Windows Sever 2012 R2-hosts upgradet, moet u de SCVMM 2012 R2 upgraden naar SCVMM 2016. Volg de onderstaande stappen:-

**Upgrade standalone SCVMM 2012 R2 naar SCVMM 2016**

1.  Verwijder DE ASR-provider door naar het Configuratiescherm te navigeren -> programma's -> programma's en -functies >Microsoft Azure Site Recovery en klik op Verwijderen
2. [Bewaar de SCVMM-database en upgrade het besturingssysteem](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#back-up-and-upgrade-the-operating-system)
3. Selecteer **VMM** > **Verwijderen in** **Programma's toevoegen**. b. Selecteer **Onderdelen verwijderen**en selecteer vervolgens V**MM-beheerserver en VMM-console**. c. Selecteer **Database behouden**in **Databaseopties**. d. Bekijk het overzicht en klik op **Verwijderen**.

4. [VMM 2016 installeren](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#install-vmm-2016)
5. Start SCVMM en controleer de status van elke host onder het tabblad **Stoffen.** Klik op **Vernieuwen** om de meest recente status te krijgen. U moet status zien als "Aandacht nodig". 
17. Installeer de nieuwste [Microsoft Azure Site Recovery Provider](https://aka.ms/downloaddra) op de SCVMM.
16. Installeer de nieuwste [MARS-agent (Microsoft Azure Recovery Service)](https://aka.ms/latestmarsagent) op elke host van het cluster. Vernieuwen om ervoor te zorgen dat SCVMM de hosts kan opvragen.

**Windows Server 2012 R2-hosts upgraden naar Windows Server 2016**

1. Volg de [hier](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) genoemde stappen om het upgradeproces voor rollende cluster uit te voeren. 
2. Nadat u de nieuwe host aan het cluster hebt toegevoegd, vernieuwt u de host van de SCVMM-console om de VMM-agent op deze bijgewerkte host te installeren.
3. *Update-VMVersion* uitvoeren om de VM-versies van de virtuele machines bij te werken. 
4.  Ga naar Azure-portal en controleer de gerepliceerde status van de virtuele machines in de Vault van Recovery Services. 

## <a name="upgrade-windows-server-2012-r2-hosts-are-managed-by-highly-available-scvmm-2012-r2-server"></a>Windows Server 2012 R2-hosts upgraden worden beheerd door een zeer beschikbare SCVMM 2012 R2-server
Voordat u uw Windows Sever 2012 R2-hosts upgradet, moet u de SCVMM 2012 R2 upgraden naar SCVMM 2016. De volgende upgrademodi worden ondersteund tijdens het upgraden van SCVMM 2012 R2-servers die zijn geconfigureerd met Azure Site Recovery - Gemengde modus zonder extra VMM-servers & gemengde modus met extra VMM-servers.

**Upgrade SCVMM 2012 R2 naar SCVMM 2016**

1.  Verwijder DE ASR-provider door naar het Configuratiescherm te navigeren -> programma's -> programma's en -functies >Microsoft Azure Site Recovery en klik op Verwijderen
2. Volg de [hier](https://docs.microsoft.com/system-center/vmm/upgrade-vmm?view=sc-vmm-2016#upgrade-a-standalone-vmm-server) genoemde stappen op basis van de wijze van upgrade die u wilt uitvoeren.
3. Start de SCVMM-console en controleer de status van elke host onder het tabblad **Stoffen.** Klik op **Vernieuwen** om de meest recente status te krijgen. U moet status zien als "Aandacht nodig".
4. Installeer de nieuwste [Microsoft Azure Site Recovery Provider](https://aka.ms/downloaddra) op de SCVMM.
5. Werk de nieuwste [MARS-agent (Microsoft Azure Recovery Service)](https://aka.ms/latestmarsagent) bij op elke host van het cluster. Vernieuwen om ervoor te zorgen dat SC VMM de hosts kan opvragen.


**Windows Server 2012 R2-hosts upgraden naar Windows Server 2016**

1. Volg de [hier](https://docs.microsoft.com/windows-server/failover-clustering/cluster-operating-system-rolling-upgrade#cluster-os-rolling-upgrade-process) genoemde stappen om het upgradeproces voor rollende cluster uit te voeren.
2. Nadat u de nieuwe host aan het cluster hebt toegevoegd, vernieuwt u de host van de SCVMM-console om de VMM-agent op deze bijgewerkte host te installeren.
3. *Update-VMVersion* uitvoeren om de VM-versies van de virtuele machines bij te werken. 
4.  Ga naar Azure-portal en controleer de gerepliceerde status van de virtuele machines in de Vault van Recovery Services. 

## <a name="next-steps"></a>Volgende stappen
Zodra de upgrade van de hosts is uitgevoerd, u een [testfailover](tutorial-dr-drill-azure.md) uitvoeren om de status van uw replicatie en herstelstatus te testen.

