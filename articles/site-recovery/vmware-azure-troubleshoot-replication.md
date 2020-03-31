---
title: Problemen met replicatie oplossen voor noodherstel van Vm's en fysieke servers voor noodgevallen naar Azure met Azure Site Recovery | Microsoft Documenten
description: In dit artikel vindt u informatie over het oplossen van problemen met problemen met de oplossing van veelvoorkomende replicatieproblemen tijdens het herstel van VMware-vm's en fysieke servers naar Azure met behulp van Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: 7237bb7e0538ba1a9b6333ccb6589efe657a247d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74423963"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Replicatieproblemen voor Vm's en fysieke servers oplossen

In dit artikel worden enkele veelvoorkomende problemen en specifieke fouten beschreven die u tegenkomen wanneer u on-premises Vm's en fysieke servers naar Azure repliceert met [siteherstel.](site-recovery-overview.md)

## <a name="step-1-monitor-process-server-health"></a>Stap 1: De status van de processerver controleren

Siteherstel gebruikt de [processerver](vmware-physical-azure-config-process-server-overview.md#process-server) om gerepliceerde gegevens te ontvangen en te optimaliseren en naar Azure te verzenden.

We raden u aan de status van processervers in portal te controleren, om ervoor te zorgen dat ze zijn verbonden en goed werken, en dat replicatie vordert voor de bronmachines die zijn gekoppeld aan de processerver.

- [Meer informatie over](vmware-physical-azure-monitor-process-server.md) het bewaken van processervers.
- [Best praktijken beoordelen](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Problemen oplossen](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) met de status van de processerver.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>Stap 2: Verbindings- en replicatieproblemen oplossen

Initiële en lopende replicatiefouten worden vaak veroorzaakt door verbindingsproblemen tussen de bronserver en de processerver of tussen de processerver en Azure. 

Als u deze problemen wilt oplossen, [lost u de verbinding en replicatie op.](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication)




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>Stap 3: Problemen met bronmachines oplossen die niet beschikbaar zijn voor replicatie

Wanneer u de bronmachine probeert te selecteren om replicatie in te schakelen met Siterecovery, is de machine mogelijk niet beschikbaar om een van de volgende redenen:

* **Twee virtuele machines met dezelfde instantie UUID:** Als twee virtuele machines onder het vCenter hetzelfde exemplaar UUID hebben, wordt de eerste virtuele machine die door de configuratieserver wordt ontdekt, weergegeven in de Azure-portal. Als u dit probleem wilt oplossen, moet u ervoor zorgen dat geen twee virtuele machines dezelfde instantie UUID hebben. Dit scenario wordt vaak gezien in gevallen waarin een back-up-VM actief wordt en wordt aangemeld bij onze detectierecords. Raadpleeg [Azure Site Recovery VMware-naar-Azure: hoe maak je dubbele of verouderde items](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) op om op te lossen.
* **Onjuiste vCenter-gebruikersreferenties:** zorg ervoor dat u de juiste vCenter-referenties hebt toegevoegd wanneer u de configuratieserver instelt met behulp van de OVF-sjabloon of uniforme installatie. Zie [Referenties wijzigen voor automatische detectie](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery)als u de referenties wilt verifiëren die u tijdens de installatie hebt toegevoegd.
* **vCenter onvoldoende bevoegdheden:** Als de machtigingen voor toegang tot vCenter niet over de vereiste machtigingen beschikken, kan het niet optreden om virtuele machines te ontdekken. Controleer of de machtigingen die in [Een account voorbereiden voor automatische detectie](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) worden toegevoegd aan het vCenter-gebruikersaccount.
* **Azure Site Recovery management servers:** Als de virtuele machine wordt gebruikt als beheerserver onder een of meer van de volgende rollen - Configuration server /scale-out process server / Master target server, dan u de virtuele machine niet kiezen uit portal. Beheerservers kunnen niet worden gerepliceerd.
* **Al beveiligd/mislukt via Azure Site Recovery-services:** Als de virtuele machine al is beveiligd of mislukt via Site Recovery, is de virtuele machine niet beschikbaar om te selecteren voor bescherming in de portal. Zorg ervoor dat de virtuele machine die u zoekt in de portal nog niet is beschermd door een andere gebruiker of onder een ander abonnement.
* **vCenter niet aangesloten:** controleer of vCenter is verbonden. Ga naar Recovery Services-kluis > Site Recovery Infrastructure > Configuration Servers > Klik op de betreffende configuratieserver > een blade rechts van de betreffende servers wordt geopend. Controleer of vCenter is aangesloten. Als het probleem is ingeschakeld en de configuratieserver op de portal [vernieuwt.](vmware-azure-manage-configuration-server.md#refresh-configuration-server) Hierna wordt de virtuele machine op het portaal vermeld.
* **ESXi uitgeschakeld:** Als ESXi-host waaronder de virtuele machine zich bevindt, is uitgeschakeld, wordt de virtuele machine niet vermeld of niet kan worden geselecteerd op de Azure-portal. Als u de ESXi-host aangeeft, [vernieuwt u de configuratieserver](vmware-azure-manage-configuration-server.md#refresh-configuration-server) op de portal. Hierna wordt de virtuele machine op het portaal vermeld.
* **Opnieuw opstarten in behandeling:** Als er een reboot in behandeling is op de virtuele machine, u de machine niet selecteren op azure-portal. Zorg ervoor dat de in behandeling zijnde rebootactiviteiten worden [voltooid, vernieuw de configuratieserver.](vmware-azure-manage-configuration-server.md#refresh-configuration-server) Hierna wordt de virtuele machine op het portaal vermeld.
* **IP niet gevonden:** Als de virtuele machine er geen geldig IP-adres aan heeft gekoppeld, u de machine niet selecteren op Azure-portal. Zorg ervoor dat u een geldig IP-adres aan de virtuele machine toewijst, [vernieuw de configuratieserver.](vmware-azure-manage-configuration-server.md#refresh-configuration-server) Hierna wordt de virtuele machine op het portaal vermeld.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Problemen met beveiligde virtuele machines in het portaal oplossen

Virtuele machines die worden gerepliceerd onder Siteherstel zijn niet beschikbaar in de Azure-portal als er dubbele vermeldingen in het systeem zijn. Voor meer informatie over het verwijderen van verouderde items en het oplossen van het probleem raadpleegt u [Azure Site Recovery VMware-to-Azure: Dubbele of verouderde items opschonen.](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)

## <a name="no-crash-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Geen crash consistent herstelpunt beschikbaar voor de VM in de laatste 'XXX' minuten

Enkele van de meest voorkomende problemen worden hieronder vermeld

### <a name="initial-replication-issues-error-78169"></a>Eerste replicatieproblemen [fout 78169]

Zorg er hierboven voor dat er geen verbindings-, bandbreedte- of tijdsynchronisatiegerelateerde problemen zijn, en zorg ervoor dat:

- Geen anti-virus software blokkeert Azure Site Recovery. Meer [more](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) informatie over mapuitsluitingen die vereist zijn voor Azure Site Recovery.

### <a name="source-machines-with-high-churn-error-78188"></a>Bronmachines met hoge churn [fout 78188]

Mogelijke oorzaken:
- De gegevenswijzigingssnelheid (schrijf bytes per seconde) op de vermelde schijven van de virtuele machine is meer dan de [door Azure Site Recovery ondersteunde limieten](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) voor het replicatiedoelopslagaccounttype.
- Er is een plotselinge piek in de churn rate als gevolg van welke hoge hoeveelheid gegevens in afwachting is voor upload.

Ga als ander op zoek naar het probleem:
- Controleer of het type doelopslagaccount (Standaard of Premium) is ingericht volgens de churn rate-eis aan de bron.
- Als u al aan een door Premium beheerde schijf (asrseeddisktype) wordt gerepliceerd, moet u ervoor zorgen dat de grootte van de schijf de waargenomen churn rate ondersteunt volgens de limieten voor herstel van de site. U de grootte van de asrseeddisk indien nodig vergroten. Volg de onderstaande stappen:
    - Navigeer naar het schijvenblad van de in botsing geslagen gerepliceerde machine en kopieer de naam van de replicaschijf
    - Navigeren naar deze door een replica beheerde schijf
    - Mogelijk ziet u een banner op het overzichtsblad met de tekst dat er een SAS-URL is gegenereerd. Klik op deze banner en annuleer de export. Negeer deze stap als u de banner niet ziet.
    - Zodra de SAS-URL is ingetrokken, gaat u naar configuratieblad van de beheerde schijf en vergroot u de grootte zodat ASR de waargenomen churn rate op de bronschijf ondersteunt
- Als de waargenomen churn tijdelijk is, wacht u een paar uur tot de in behandeling zijnde gegevens uploaden in te halen en herstelpunten te maken.
- Als de schijf niet-kritieke gegevens zoals tijdelijke logboeken, testgegevens enz., overweeg dan om deze gegevens elders te verplaatsen of deze schijf volledig uit te sluiten van replicatie
- Als het probleem blijft bestaan, gebruikt u de [implementatieplanner](site-recovery-deployment-planner.md#overview) siteherstel om replicatie te helpen plannen.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Bronmachines zonder hartslag [fout 78174]

Dit gebeurt wanneer de Azure Site Recovery Mobility-agent op de bronmachine niet communiceert met de configuratieserver (CS).

Als u het probleem wilt oplossen, gebruikt u de volgende stappen om de netwerkverbinding van de bron-vm naar de Config-server te verifiëren:

1. Controleer of de bronmachine wordt uitgevoerd.
2. Meld u aan bij de bronmachine met een account met beheerdersbevoegdheden.
3. Controleer of de volgende services worden uitgevoerd en zo niet de services opnieuw worden gestart:
   - Svagents (InMage Scout VX-agent)
   - InMage Scout-toepassingsservice
4. Controleer op de bronmachine de logboeken op de locatie op foutgegevens:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="process-server-with-no-heartbeat-error-806"></a>Processerver zonder hartslag [fout 806]
Als er geen hartslag van de Process Server (PS) is, controleer dan of:
1. PS VM is up and running
2. Controleer de volgende logboeken op de PS voor foutgegevens:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Hoofddoelserver zonder hartslag [fout 78022]

Dit gebeurt wanneer de Azure Site Recovery Mobility-agent op het masterdoel niet communiceert met de configuratieserver.

Als u het probleem wilt oplossen, gebruikt u de volgende stappen om de servicestatus te verifiëren:

1. Controleer of de mastertargetvm wordt uitgevoerd.
2. Meld u aan bij de mastertarget-vm met een account met beheerdersbevoegdheden.
    - Controleer of de svagents-service wordt uitgevoerd. Als deze wordt uitgevoerd, start u de service opnieuw
    - Controleer de logboeken op de locatie op foutgegevens:
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
3. Als u hoofddoel wilt registreren bij de configuratieserver, navigeert u naar map **%PROGRAMDATA%\ASR\Agent**en voert u het volgende uit op opdrachtprompt:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Fout-id 78144 - Geen app-consistent herstelpunt beschikbaar voor de VM in de laatste 'XXX'-minuten

Er zijn verbeteringen aangebracht in de versies van mobiliteitsagent [9.23](vmware-physical-mobility-service-overview.md#from-923-version-onwards) & [9.27](site-recovery-whats-new.md#update-rollup-39) om het gedrag van VSS-installatiefouten te verwerken. Zorg ervoor dat u op de nieuwste versies voor de beste richtlijnen voor het oplossen van VSS-fouten.

Enkele van de meest voorkomende problemen worden hieronder vermeld

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Oorzaak 1: Bekend probleem in SQL server 2008/2008 R2 
**Hoe op te lossen:** Er is een bekend probleem met SQL server 2008/2008 R2. Raadpleeg dit [KB-artikel Azure Site Recovery Agent of andere niet-component VSS-back-up mislukt voor een server die SQL Server 2008 R2 host](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Oorzaak 2: Azure Site Recovery-taken mislukken op servers die een versie van SQL Server-exemplaren hosten met AUTO_CLOSE DB's 
**Hoe op te lossen:** Verwijzen Kb [artikel](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Oorzaak 3: Bekend probleem in SQL Server 2016 en 2017
**Hoe op te lossen:** Verwijzen Kb [artikel](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component) 


### <a name="more-causes-due-to-vss-related-issues"></a>Meer oorzaken als gevolg van VSS gerelateerde problemen:

Als u verder problemen wilt oplossen, controleert u de bestanden op de bronmachine om de exacte foutcode voor fout te krijgen:
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Hoe u de fouten in het bestand vinden?
Zoeken naar de tekenreeks "vacpError" door het bestand vacp.log in een editor te openen
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

In het bovenstaande voorbeeld **2147754994** is de foutcode die u vertelt over de fout zoals hieronder weergegeven

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS-schrijver is niet geïnstalleerd - Fout 2147221164 

*Hoe op te lossen:* Azure Site Recovery maakt gebruik van Microsoft Volume Shadow copy Service (VSS) om de consistentietag voor toepassingen te genereren. Het installeert een VSS-provider voor de werking ervan om snapshots van de consistentie van apps te maken. Deze VSS-provider is geïnstalleerd als een service. In het geval dat de VSS Provider-service niet is geïnstalleerd, mislukt het maken van de consistentie van de toepassing met de fout-id 0x80040154 "Klasse niet geregistreerd". </br>
Artikel [verwijzen voor het oplossen van problemen met de installatie van VSS-writer's](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures) 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS-schrijver is uitgeschakeld - Fout 2147943458

**Hoe op te lossen:** Azure Site Recovery maakt gebruik van Microsoft Volume Shadow copy Service (VSS) om de consistentietag voor toepassingen te genereren. Het installeert een VSS-provider voor de werking ervan om snapshots van de consistentie van apps te maken. Deze VSS-provider is geïnstalleerd als een service. In het geval dat de VSS Provider-service is uitgeschakeld, mislukt het maken van de consistentievan de toepassing met de fout-id "De opgegeven service is uitgeschakeld en kan niet worden gestart(0x80070422)". </br>

- Als VSS is uitgeschakeld,
    - Controleer of het opstarttype van de VSS-providerservice is ingesteld op **Automatisch**.
    - Start de volgende services opnieuw:
        - VSS-service
        - Azure-siteherstel VSS-provider
        - VDS-service

####  <a name="vss-provider-not_registered---error-2147754756"></a>VSS PROVIDER NOT_REGISTERED - Fout 2147754756

**Hoe op te lossen:** Azure Site Recovery maakt gebruik van Microsoft Volume Shadow copy Service (VSS) om de consistentietag voor toepassingen te genereren. Controleer of de Azure Site Recovery VSS Provider-service is geïnstalleerd of niet. </br>

- Probeer de installatie van de provider opnieuw met de volgende opdrachten:
- Bestaande provider verwijderen: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- Opnieuw installeren: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd
 
Controleer of het opstarttype van de VSS-providerservice is ingesteld op **Automatisch**.
    - Start de volgende services opnieuw:
        - VSS-service
        - Azure-siteherstel VSS-provider
        - VDS-service

## <a name="next-steps"></a>Volgende stappen

Als u meer hulp nodig hebt, plaatst u uw vraag in het [Azure Site Recovery-forum.](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) We hebben een actieve gemeenschap, en een van onze ingenieurs kan u helpen.
