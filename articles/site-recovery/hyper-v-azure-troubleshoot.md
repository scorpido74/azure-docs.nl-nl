---
title: Problemen met herstel van hyperv's met Azure-siteherstel oplossen
description: Beschrijft hoe u problemen met noodherstel problemen met Hyper-V naar Azure-replicatie oplossen met Azure Site Recovery
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: rajanaki
ms.openlocfilehash: 0a3e5c922009353e4ba9ccab12cf70ea2b5992da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961486"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Problemen oplossen met replicatie en failover voor Hyper-V naar Azure

In dit artikel worden veelvoorkomende problemen beschreven die u tegenkomen bij het repliceren van on-premises Hyper-V VM's naar Azure, met behulp van [Azure Site Recovery](site-recovery-overview.md).

## <a name="enable-protection-issues"></a>Beveiligingsproblemen inschakelen

Als u problemen ondervindt wanneer u beveiliging voor Hyper V VM's inschakelt, raadpleegt u de volgende aanbevelingen:

1. Controleer of uw Hyper-V-hosts en VM's aan alle [vereisten en vereisten](hyper-v-azure-support-matrix.md)voldoen.
2. Als Hyper-V-servers zich bevinden in VMM-clouds (System Center Virtual Machine Manager), controleert u of u de [VMM-server](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional)hebt voorbereid.
3. Controleer of de Hyper-V Virtual Machine Management-service wordt uitgevoerd op Hyper-V-hosts.
4. Controleer op problemen die worden weergegeven in de aanmelding Hyper-V-V-VMMS\Admin bij de VM. Dit logboek bevindt zich in **Applications and Services Logs** > **Microsoft** > **Windows**.
5. Controleer op de gast-VM of WMI is ingeschakeld en toegankelijk is.
   - [Meer informatie over](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/) basisWMI-tests.
   - [Problemen oplossen](https://aka.ms/WMiTshooting) Wmi.
   - Problemen met WMI-scripts en -services [oplossen.](https://technet.microsoft.com/library/ff406382.aspx#H22)
6. Zorg ervoor dat de nieuwste versie van Integration Services wordt uitgevoerd op de gast-VM.
    - [Controleer](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) of u de nieuwste versie hebt.
    - [Houden](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) Integration Services up-to-date.
    
## <a name="replication-issues"></a>Replicatieproblemen

Problemen met eerste en doorlopende replicatie kunt u als volgt oplossen:

1. Zorg ervoor dat u de [nieuwste versie](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) van siteherstelservices uitvoert.
2. Controleer of replicatie is onderbroken:
   - Controleer de status vm-status in de hyper-V-managerconsole.
   - Als dit van cruciaal belang is, klikt u met de rechtermuisknop op de vm >**replicatiestatus** **replicatieweergave** > .
   - Als replicatie is onderbroken, klikt u op **Replicatie hervatten**.
3. Controleer of de vereiste services worden uitgevoerd. Als dat niet het zo is, start ze dan opnieuw op.
    - Als u Hyper-V zonder VMM repliceert, controleert u of deze services worden uitgevoerd op de Hyper-V-host:
        - Virtual Machine Management
        - Microsoft Azure Recovery Services Agent
        - Microsoft Azure Site Recovery
        - WMI Provider Host
    - Als u met VMM in de omgeving re-replicart, controleert u of deze services worden uitgevoerd:
        - Controleer op de Hyper-V-host of de Service Virtual Machine Management, de Microsoft Azure Recovery Services Agent en de WMI Provider Host-service worden uitgevoerd.
        - Controleer op de VMM-server of de Virtual Machine Manager-service van het System Center wordt uitgevoerd.
4. Controleer de connectiviteit tussen de Hyper-V-server en Azure. Als u de connectiviteit wilt controleren, opent u Taakbeheer op de Hyper V-host. Klik op het tabblad **Prestaties** op **Resourcemonitor openen**. Controleer op het tabblad **Netwerk** > **Proces met netwerkactiviteit**of cbengine.exe actief grote hoeveelheden (Mbs) aan gegevens verzendt.
5. Controleer of de Hyper-V-hosts verbinding kunnen maken met de URL van de Azure-opslagblob. Als u wilt controleren of de hosts verbinding kunnen maken, selecteert en controleert u **cbengine.exe**. Bekijk **TCP-verbindingen** om de verbinding van de host met de Azure-opslagblob te verifiëren.
6. Controleer prestatieproblemen, zoals hieronder beschreven.
    
### <a name="performance-issues"></a>Prestatieproblemen

Beperkingen van de netwerkbandbreedte kunnen van invloed zijn op replicatie. Problemen als volgt oplossen:

1. [Controleer](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) of er beperkingen zijn voor bandbreedte of beperking in uw omgeving.
2. De [profiler Van implementatieplanner uitvoeren](hyper-v-deployment-planner-run.md).
3. Volg na het uitvoeren van de profiler de [bandbreedte-](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) en [opslagaanbevelingen.](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation)
4. Beperkingen [van gegevensverloop controleren](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits). Als u een hoog gegevensverloop op een vm ziet, gaat u als volgt te werk:
   - Controleer of uw vm is gemarkeerd voor opnieuw synchronisatie.
   - Volg [deze stappen](https://blogs.technet.microsoft.com/virtualization/2014/02/02/hyper-v-replica-debugging-why-are-very-large-log-files-generated/) om de bron van de churn te onderzoeken.
   - Churn kan optreden wanneer de HRL-logboekbestanden meer dan 50% van de beschikbare schijfruimte overschrijden. Als dit het probleem is, u meer opslagruimte inrichten voor alle VM's waarop het probleem zich voordoet.
   - Controleer of replicatie niet is onderbroken. Als dat zo is, blijft het schrijven van de wijzigingen in het hrl-bestand, die kunnen bijdragen aan de toegenomen grootte.
 

## <a name="critical-replication-state-issues"></a>Kritieke replicatiestatusproblemen

1. Als u de replicatiestatus wilt controleren, maakt u verbinding met de on-premises Hyper-V Manager-console, selecteert u de VM en controleert u de status.

    ![Replicatiestatus](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. Klik **op Replicatiestatus weergeven** om de details te bekijken:

    - Als replicatie is onderbroken, klikt u met de rechtermuisknop op de replicatievm > **replicatie** > **hervatten**.
    - Als een VM op een Hyper-V-host die is geconfigureerd in Siteherstel migreert naar een andere Hyper-V-host in hetzelfde cluster of naar een zelfstandige machine, wordt de replicatie voor de VM niet beïnvloed. Controleer gewoon of de nieuwe Hyper-V-host aan alle vereisten voldoet en is geconfigureerd in Site Recovery.

## <a name="app-consistent-snapshot-issues"></a>Problemen met momentopnamen in apps

Een momentopname die consistent is in de app, is een point-in-time momentopname van de toepassingsgegevens in de VM. Vss (Volume Shadow Copy Service) zorgt ervoor dat apps op de VM consistent zijn wanneer de momentopname wordt gemaakt.  In deze sectie worden enkele veelvoorkomende problemen beschreven die u mogelijk ondervindt.

### <a name="vss-failing-inside-the-vm"></a>VSS faalt binnen de VM

1. Controleer of de nieuwste versie van integratieservices is geïnstalleerd en uitgevoerd.  Controleer of er een update beschikbaar is door de volgende opdracht uit te voeren vanuit een verhoogde PowerShell-prompt op de Hyper-V-host: **get-vm | selecteer Naam, status, IntegratieServicesState**.
2. Controleer of VSS-services actief en gezond zijn:
   - Als u de services wilt controleren, meldt u zich aan bij de gast-VM. Open vervolgens een opdrachtprompt voor beheerders en voer de volgende opdrachten uit om te controleren of alle VSS-schrijvers in orde zijn.
       - **Vssadmin lijst schrijvers**
       - **Vssadmin lijst schaduwen**
       - **Aanbieders van vssadmin-lijst**
   - Controleer de uitvoer. Als schrijvers zich in een mislukte staat bevinden, gaat u als volgt te werk:
       - Controleer het toepassingsgebeurtenislogboek op de VM voor VSS-bewerkingsfouten.
   - Probeer deze services opnieuw op te starten die zijn gekoppeld aan de mislukte schrijver:
     - Volumeschaduwkopie
       - Azure-siteherstel VSS-provider
   - Nadat u dit hebt gedaan, wacht u een paar uur om te zien of momentopnamen voor app-consistent met succes worden gegenereerd.
   - Als laatste redmiddel probeer het opnieuw opstarten van de VM. Dit kan services oplossen die niet reageren.
3. Controleer of u geen dynamische schijven in de VM hebt. Dit wordt niet ondersteund voor app-consistente momentopnamen. U Schijfbeheer (diskmgmt.msc) inchecken.

    ![Dynamische schijf](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. Controleer of er geen iSCSI-schijf aan de VM is gekoppeld. Nee, dit wordt niet ondersteund.
5. Controleer of de back-upservice is ingeschakeld. Controleer of deze is ingeschakeld in**Integratieservices** **voor Hyper-V-instellingen.** > 
6. Zorg ervoor dat er geen conflicten zijn met apps die VSS-snapshots maken. Als meerdere apps VSS-snapshots tegelijk proberen te maken, kunnen er conflicten optreden. Als een Back-up-app bijvoorbeeld VSS-snapshots maakt wanneer Siteherstel is gepland door uw replicatiebeleid om een momentopname te maken.   
7. Controleer of de VM een hoge churn rate ondervindt:
    - U de dagelijkse gegevenswijzigingssnelheid voor de vm's van de gast meten met behulp van prestatiemeteritems op Hyper-V-host. Als u de snelheid van de gegevenswijziging wilt meten, schakelt u het volgende tellerin. Verzamel een voorbeeld van deze waarde gedurende 5-15 minuten over de VM-schijven om de VM-churn te krijgen.
        - Categorie: "Hyper-V Virtual Storage Device"
        - Teller: "Schrijf Bytes / Sec"</br>
        - Deze gegevensverloopsnelheid zal toenemen of op een hoog niveau blijven, afhankelijk van hoe druk de VM of de apps zijn.
        - Het gemiddelde gegevensverloop van de bronschijf is 2 MB/s voor standaardopslag voor siteherstel. [Meer informatie](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - Daarnaast u [de schaalbaarheidsdoelen voor opslag verifiëren.](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)
8. Voer de [implementatieplanner uit](hyper-v-deployment-planner-run.md).
9. Bekijk de aanbevelingen voor [netwerk](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) en [opslag](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input).


### <a name="vss-failing-inside-the-hyper-v-host"></a>VSS faalt in de Hyper-V Host

1. Controleer gebeurtenislogboeken op VSS-fouten en -aanbevelingen:
    - Open op de Hyper-V-hostserver het gebeurtenislogboek Hyper-V-beheer in**Admin****Logboeken van Logboeken en Services van** >  **Event Viewer** > **Microsoft** > **Windows** > **Hyper-V.** > 
    - Controleer of er gebeurtenissen zijn die wijzen op momentopnamefouten die consistent zijn in de app.
    - Een typische fout is: "Hyper-V niet te genereren VSS snapshot set voor virtuele machine 'XYZ': De schrijver ervaren een niet-voorbijgaande fout. Als de VSS-service opnieuw wordt opgestart, kunnen problemen worden opgelost als de service niet reageert."

2. Als u VSS-momentopnamen voor de VM wilt genereren, controleert u of Hyper-V Integration Services op de VM zijn geïnstalleerd en of de integratieservice Voor back-up (VSS) is ingeschakeld.
    - Zorg ervoor dat de VSS-service/daemons van Integration Services op de gast worden uitgevoerd en dat ze in een **OK-status** verkeren.
    - U dit controleren vanuit een verhoogde PowerShell-sessie op de Hyper-V-host met opdracht **Get-VMIntegrationService -VMName\<VMName>-Name VSS** U deze informatie ook ophalen door u aan te melden bij de gast-VM. [Meer informatie](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - Controleer of de back-up-/VSS-integratieservices op de VM in gezonde staat worden uitgevoerd. Start deze services opnieuw en de Hyper-V Volume Shadow Copy-aanvragersservice op de Hyper-V-hostserver.

### <a name="common-errors"></a>Algemene fouten

**Foutcode** | **Bericht** | **Details**
--- | --- | ---
**0x800700EA** | "Hyper-V is er niet in geslaagd om VSS-snapshotset voor virtuele machine te genereren: er zijn meer gegevens beschikbaar. (0x800700EA). Het genereren van VSS-momentopnamesets kan mislukken als er een back-upbewerking wordt uitgevoerd.<br/><br/> Replicatiebewerking voor virtuele machine is mislukt: er zijn meer gegevens beschikbaar." | Controleer of uw VM dynamische schijf heeft ingeschakeld. Nee, dit wordt niet ondersteund.
**0x80070032** | "Hyper-V Volume Shadow Copy Requestor kan geen verbinding maken met virtuele machine <./VMname> omdat de versie niet overeenkomt met de versie die hyper-V verwacht | Controleer of de nieuwste Windows-updates zijn geïnstalleerd.<br/><br/> [Upgrade](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) naar de nieuwste versie van Integration Services.



## <a name="collect-replication-logs"></a>Replicatielogboeken verzamelen

Alle Hyper-V-replicatiegebeurtenis wordt aangemeld in het hyper-V-VMMS\Admin-logboek, dat zich bevindt in **Applications and Services Logs** > **Microsoft** > **Windows**. Daarnaast u een analytische log inschakelen voor de Hyper-V Virtual Machine Management Service, als volgt:

1. Maak de analytische en foutopsporingslogboeken zichtbaar in de logboeken. Als u de logboeken beschikbaar wilt maken, klikt u in de logboeken op > **Analytische en foutopsporingslogboeken** **weergeven.** Het analytische logboek wordt weergegeven onder **Hyper-V-VMMS.**
2. Klik **in** het deelvenster Handelingen op **Logboek inschakelen**. 

    ![Logboek inschakelen](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. Nadat deze is ingeschakeld, wordt het weergegeven in **prestatiemeter**, als een **gebeurtenistraceringssessie** onder **gegevensverzamelaarsets**. 
4. Als u de verzamelde informatie wilt bekijken, stopt u de traceringssessie door het logboek uit te schakelen. Sla vervolgens het logboek op en open het opnieuw in Logboeken of gebruik andere hulpmiddelen om het naar behoefte te converteren.


### <a name="event-log-locations"></a>Locaties voor gebeurtenislogboeken

**Gebeurtenislogboek** | **Details** |
--- | ---
**Toepassingen en servicelogboeken/Microsoft/VirtualMachineManager/Server/Admin** (VMM-server) | Logboeken om VMM-problemen op te lossen.
**Toepassingen en servicelogboeken/MicrosoftAzureRecoveryServices/Replicatie** (Hyper-V-host) | Logboeken om problemen met Microsoft Azure Recovery Services Agent op te lossen. 
**Toepassingen en servicelogboeken/Microsoft/Azure Site Recovery/Provider/Operationeel** (Hyper-V-host)| Logboeken om problemen met microsoft Azure Site Recovery Service op te lossen.
**Toepassingen en servicelogboeken/Microsoft/Windows/Hyper-V-VMMS/Admin** (Hyper-V-host) | Logboeken om problemen met het Hyper-V VM-beheer op te lossen.

### <a name="log-collection-for-advanced-troubleshooting"></a>Logboekverzameling voor geavanceerde probleemoplossing

Deze hulpprogramma's kunnen helpen bij geavanceerde probleemoplossing:

-   Voer voor VMM de logboekverzameling siteherstel uit met de [tool Support Diagnostics Platform (SDP).](https://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx)
-   Voor Hyper-V zonder [VMM, download deze tool,](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab)en voer het uit op de Hyper-V host om de logs te verzamelen.

