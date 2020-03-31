---
title: Herstel na noodgevallen van VMware VM's/fysieke servers naar een secundaire site met Azure Site Recovery
description: Meer informatie over het instellen van noodherstel van Vm's voor vmware-v.S. of Windows- en Linux-servers op een secundaire site met Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: 71d230c9fea25edfbf0ca4ea40f15b69779ad060
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256807"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Herstel na noodgeval instellen voor on-premises virtuele VMware-machines of fysieke servers naar een secundaire site

InMage Scout in [Azure Site Recovery](site-recovery-overview.md) biedt realtime replicatie tussen on-premises VMware-sites. InMage Scout is opgenomen in Azure Site Recovery-serviceabonnementen.

## <a name="end-of-support-announcement"></a>Aankondiging van aflopen van ondersteuning

Het Azure Site Recovery-scenario voor replicatie tussen on-premises VMware of fysieke datacenters bereikt het einde van de ondersteuning.

-   Vanaf augustus 2018 kan het scenario niet worden geconfigureerd in de vault van Recovery Services en kan de InMage Scout-software niet meer uit de kluis worden gedownload. Bestaande implementaties worden ondersteund. 
-   Vanaf 31 december 2020 wordt het scenario niet meer ondersteund.
- Bestaande partners kunnen nieuwe klanten aan het scenario aan boord nemen totdat de ondersteuning is beëindigd.

In de loop van 2018 en 2019 worden twee updates uitgebracht: 

-   Update 7: Lost problemen met de netwerkconfiguratie en naleving op en biedt TLS 1.2-ondersteuning.
-   Update 8: Voegt ondersteuning toe voor Linux-besturingssystemen RHEL/CentOS 7.3/7.4/7.5 en voor SUSE 12

Na update 8 worden er geen verdere updates meer uitgebracht. Er zal beperkte hotfix-ondersteuning zijn voor de besturingssystemen die zijn toegevoegd in update 8 en bugfixes op basis van de beste inspanning.

Azure Site Recovery blijft innoveren door VMware- en Hyper-V-klanten een naadloze en best in-class DRaaS-oplossing te bieden met Azure als noodherstelsite. Microsoft raadt bestaande InMage / ASR Scout-klanten aan om het VMware-scenario vmware naar Azure van Azure te gebruiken voor hun bedrijfscontinuïteitsbehoeften. Het VMware-naar Azure-scenario van Azure is een DR-oplossing op bedrijfsniveau voor VMware-toepassingen, die RPO en RTO van minuten biedt, ondersteuning voor replicatie en herstel van multi-VM-toepassingen, naadloze onboarding, uitgebreide bewaking en aanzienlijk TCO-voordeel.

### <a name="scenario-migration"></a>Scenario migratie
Als alternatief raden we aan om noodherstel in te stellen voor on-premises Vm's en fysieke machines door ze te repliceren naar Azure. Doe dit als volgt:

1.  Bekijk de snelle vergelijking hieronder. Voordat u on-premises machines repliceren, moet u controleren of ze voldoen aan [de vereisten](./vmware-physical-azure-support-matrix.md#replicated-machines) voor replicatie naar Azure. Als u Vm's van VMware repliceert, raden we u aan [de richtlijnen voor capaciteitsplanning](./site-recovery-plan-capacity-vmware.md)te controleren en het [hulpprogramma Implementatieplanner](./site-recovery-deployment-planner.md) uit te voeren op de vereisten voor identiteitscapaciteit en naleving te controleren.
2.  Nadat u de Implementatieplanner hebt uitgevoerd, u replicatie instellen: o Voor VMware VM's, volg deze zelfstudies om Azure voor te [bereiden,](./tutorial-prepare-azure.md) [uw on-premises VMware-omgeving voor te bereiden](./vmware-azure-tutorial-prepare-on-premises.md)en herstel na noodgevallen in te [stellen.](./vmware-azure-tutorial-prepare-on-premises.md)
o Volg deze [zelfstudie](./physical-azure-disaster-recovery.md)voor fysieke machines.
3.  Nadat machines zijn gerepliceerd naar Azure, u een [noodherstelboor](./site-recovery-test-failover-to-azure.md) uitvoeren om te zien of alles werkt zoals verwacht.

### <a name="quick-comparison"></a>Snelle vergelijking

**Functie** | **Replicatie naar Azure** |**Replicatie tussen VMware-datacenters**
--|--|--
**Vereiste onderdelen** |Mobiliteitsservice op gerepliceerde machines. On-premises configuratieserver, processerver, hoofddoelserver. Tijdelijke processerver in Azure voor failback.|Mobiliteitsservice, processerver, configuratieserver en mastertarget
**Configuratie en orkestratie** |Vault van Recovery Services in de Azure-portal | VContinuum gebruiken 
**Gerepliceerd** |Schijf (Windows en Linux) |Volume-Windows<br> Schijf-Linux
**Gedeelde schijfcluster** |Niet ondersteund|Ondersteund
**Limieten voor gegevensverloop (gemiddeld)** |Gegevens van 10 MB/s per schijf<br> 25 MB/s-gegevens per VM<br> [Meer informatie](./site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) | > gegevens van 10 MB/s per schijf  <br> > 25 MB/s-gegevens per VM
**Monitoring** |Vanuit Azure-portal|Van CX (configuratieserver)
**Ondersteuningsmatrix** | [Klik hier voor meer informatie](./vmware-physical-azure-support-matrix.md)|[Download ASR Scout compatibele matrix](https://aka.ms/asr-scout-cm)


## <a name="prerequisites"></a>Vereisten
Vereisten om deze zelfstudie te voltooien:

- [Bekijk](vmware-physical-secondary-support-matrix.md) de ondersteuningsvereisten voor alle onderdelen.
- Zorg ervoor dat de machines die u wilt repliceren voldoen aan [de gerepliceerde machineondersteuning.](vmware-physical-secondary-support-matrix.md#replicated-vm-support)


## <a name="download-and-install-component-updates"></a>Updates van onderdelen downloaden en installeren

 Bekijk en installeer de nieuwste [updates.](#updates) Updates moeten in de volgende volgorde op servers worden geïnstalleerd:

1. RX-server (indien van toepassing)
2. Configuratieservers
3. Processervers
4. Master Target-servers
5. vContinuum-servers
6. Bronserver (zowel Windows- als Linux-servers)

Installeer de updates als volgt:

> [!NOTE]
>De bestandsupdateversie van alle Scout-componenten is mogelijk niet hetzelfde in het update.zip-bestand. De oudere versie geeft aan dat er geen verandering in de component sinds de vorige update van deze update.

Download het [update](https://aka.ms/asr-scout-update7) .zip-bestand en de configuratiebestanden van de [MySQL- en PHP-upgrade.](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade) Het update.zip-bestand bevat alle basisbinaire bestanden en cumulatieve upgradebinaries van de volgende componenten: 
- InMage_ScoutCloud_RX_8.0.1.0_RHEL6-64_GA_02Mar2015.tar.gz
- RX_8.0.7.0_GA_Update_7_2965621_28Dec18.tar.gz
- InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_CX_TP_8.0.1.0_Windows_GA_26Feb2015_release.exe
- CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe
- InMage_PI_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8.0,70_OL5-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0,7,0_OL5-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0,70_OL6-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL6-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL5-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL5-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0,7,0_RHEL6-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0,7.0_RHEL6-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0,7.0_RHEL7-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0,7,0_SLES10-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP1-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP1-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP2-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP2-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP3-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP3-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP4-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP4-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0,7,0_SLES11-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0,7,0_SLES11-64_GA_04Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP1-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP1-64_GA_04Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP2-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP2-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP3-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP3-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP4-64_GA_03Dec2018_release.tar.gz
  1. Haal de .zip-bestanden eruit.
  2. **RX-server**: Kopieer **RX_8.0.7.0_GA_Update_7_2965621_28Dec18.tar.gz** naar de RX-server en haal deze uit. Voer in de uitgepakte map **/Installeren**.
  3. **Configuratieserver en processerver**: kopieer **CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe** naar de configuratieserver en processerver. Dubbelklik om het uit te voeren.<br>
  4. **Windows Master Target-server:** kopieer **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** naar de server om de unified agent bij te werken. Dubbelklik erop om het uit te voeren. Hetzelfde bestand kan ook worden gebruikt voor verse installatie. Dezelfde unified agent-update is ook van toepassing op de bronserver.
  De update hoeft niet van toepassing op de Master doel voorbereid met **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe** als dit is nieuwe GA installateur met de nieuwste wijzigingen.
  5. **vContinuum-server**: kopieer **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe** naar de server.  Zorg ervoor dat je de vContinuum-wizard hebt gesloten. Dubbelklik op het bestand om het uit te voeren.
  6. **Linux master target server**: Om de unified agent bij te werken, kopieer **je InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz** naar de Linux Master Target-server en haal je deze eruit. Voer in de uitgepakte map **/Installeren**.
  7. **Windows-bronserver**: Kopieer **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** naar de bronserver om de uniforme agent bij te werken. Dubbelklik op het bestand om het uit te voeren. 
  8. **Linux-bronserver:** als u de unified agent wilt bijwerken, kopieert u de bijbehorende versie van het unified agent-bestand naar de Linux-server en haalt u deze uit. Voer in de uitgepakte map **/Installeren**.  Voorbeeld: Kopieer voor RHEL 6.7 64-bits server **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz** naar de server en haal deze eruit. Voer in de uitgepakte map **/Installeren**.
  9. Na het upgraden van configuration server, Process Server en RX server met de bovengenoemde installers, moeten de PHP- en MySQL-bibliotheken handmatig worden geüpgraded met stappen die worden genoemd in sectie 7.4 van de [handleiding voor snelle installatie.](https://aka.ms/asr-scout-quick-install-guide)

## <a name="enable-replication"></a>Replicatie inschakelen

1. Replicatie instellen tussen de bron- en doelVMware-sites.
2. Raadpleeg de volgende documenten voor meer informatie over installatie, beveiliging en herstel:

   * [Opmerkingen vrijgeven](https://aka.ms/asr-scout-release-notes)
   * [Compatibiliteitsmatrix](https://aka.ms/asr-scout-cm)
   * [Gebruikershandleiding](https://aka.ms/asr-scout-user-guide)
   * [RX-gebruikershandleiding](https://aka.ms/asr-scout-rx-user-guide)
   * [Handleiding voor snelle installatie](https://aka.ms/asr-scout-quick-install-guide)
   * [MySQL- en PHP-bibliotheken upgraden](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade)

## <a name="updates"></a>Updates

### <a name="site-recovery-scout-801-update-7"></a>Site Recovery Scout 8.0.1 Update 7 
Bijgewerkt: 31 december 2018 Download [Scout update 7](https://aka.ms/asr-scout-update7).
Scout Update 7 is een volledige installateur die kan worden gebruikt voor verse installatie en om bestaande agents / MT die op eerdere updates (van Update 1 naar Update 6) te upgraden. Het bevat alle fixes van update 1 tot update 6 plus de nieuwe fixes en verbeteringen hieronder beschreven.
 
#### <a name="new-features"></a>Nieuwe functies
* PCI-naleving
* ONDERSTEUNING voor TLS v1.2

#### <a name="bug-and-security-fixes"></a>Bug- en beveiligingsoplossingen
* Opgelost: Windows Cluster/Standalone Machines hebben een onjuiste IP-configuratie bij herstel/DR-Drill.
* Opgelost: Soms is de schijfbewerking toevoegen mislukt voor v2V-cluster.
* Opgelost: vContinuum Wizard komt vast te zitten tijdens herstelfase als het mastertarget Windows Server 2016 is
* Opgelost: MySQL-beveiligingsproblemen worden beperkt door MySQL te upgraden naar versie 5.7.23

#### <a name="manual-upgrade-for-php-and-mysql-on-csps-and-rx"></a>Handmatige upgrade voor PHP en MySQL op CS, PS en RX
Het PHP-scriptingplatform moet worden geüpgraded naar versie 7.2.10 op configuration server, process server en RX Server.
Het MySQL-databasebeheersysteem moet worden geüpgraded naar versie 5.7.23 op configuration server, process server en RX Server.
Volg de handmatige stappen in de [handleiding voor snelle installatie](https://aka.ms/asr-scout-quick-install-guide) om PHP- en MySQL-versies te upgraden.

### <a name="site-recovery-scout-801-update-6"></a>Site Recovery Scout 8.0.1 Update 6 
Bijgewerkt: 12 oktober 2017

Download [Scout update 6](https://aka.ms/asr-scout-update6).

Scout Update 6 is een cumulatieve update. Het bevat alle fixes van Update 1 tot Update 5 plus de nieuwe fixes en verbeteringen hieronder beschreven. 

#### <a name="new-platform-support"></a>Nieuwe platformondersteuning
* Ondersteuning is toegevoegd voor Bron Windows Server 2016
* Ondersteuning is toegevoegd voor het volgen van Linux-besturingssystemen:
    - Red Hat Enterprise Linux (RHEL) 6.9
    - CentOS 6.9
    - Oracle Linux 5.11
    - Oracle Linux 6.8
* Ondersteuning is toegevoegd voor VMware Center 6.5

Installeer de updates als volgt:

> [!NOTE]
>De bestandsupdateversie van alle Scout-componenten is mogelijk niet hetzelfde in het update.zip-bestand. De oudere versie geeft aan dat er geen verandering in de component sinds de vorige update van deze update.

Download [update](https://aka.ms/asr-scout-update6) het update.zip-bestand. Het bestand bevat de volgende onderdelen: 
- RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
- CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
- UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
- UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
- vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
- UA update4 bits voor RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_\<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  1. Haal de .zip-bestanden eruit.
  2. **RX-server**: Kopieer **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** naar de RX-server en haal deze uit. Voer in de uitgepakte map **/Installeren**.
  3. **Configuratieserver en processerver**: kopieer **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** naar de configuratieserver en processerver. Dubbelklik om het uit te voeren.<br>
  4. **Windows Master Target-server:** kopieer **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** naar de server om de unified agent bij te werken. Dubbelklik erop om het uit te voeren. Dezelfde unified agent-update is ook van toepassing op de bronserver. Als de bron niet is bijgewerkt naar Update 4, moet u de uniforme agent bijwerken.
  De update hoeft niet van toepassing op de Master doel voorbereid met **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** als dit is nieuwe GA installateur met de nieuwste wijzigingen.
  5. **vContinuum-server**: Kopieer **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** naar de server.  Zorg ervoor dat je de vContinuum-wizard hebt gesloten. Dubbelklik op het bestand om het uit te voeren.
  De update hoeft niet van toepassing op de Master Target voorbereid met **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** als dit is nieuwe GA installateur met de nieuwste wijzigingen.
  6. **Linux master target server**: Om de unified agent bij te werken, kopieer **je UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** naar de hoofddoelserver en haal deze eruit. Voer in de uitgepakte map **/Installeren**.
  7. **Windows-bronserver:** kopieer **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** naar de bronserver om de uniforme agent bij te werken. Dubbelklik op het bestand om het uit te voeren. 
  U hoeft de update 5-agent niet op de bronserver te installeren als deze al is bijgewerkt naar Update 4 of als bronagent is geïnstalleerd met de nieuwste basisinstaller **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
  8. **Linux-bronserver:** als u de unified agent wilt bijwerken, kopieert u de bijbehorende versie van het unified agent-bestand naar de Linux-server en haalt u deze uit. Voer in de uitgepakte map **/Installeren**.  Voorbeeld: Kopieer voor RHEL 6.7 64-bits server **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** naar de server en haal deze eruit. Voer in de uitgepakte map **/Installeren**.


> [!NOTE]
> * Het installatieprogramma Van Base Unified Agent (UA) voor Windows is vernieuwd om Windows Server 2016 te ondersteunen. De nieuwe installateur **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** is verpakt met de basis Scout GA pakket (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Hetzelfde installatieprogramma wordt gebruikt voor alle ondersteunde Windows-versies. 
> * Base Windows vContinuum & Master Target-installatieprogramma is vernieuwd om Windows Server 2016 te ondersteunen. De nieuwe installateur **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** is verpakt met de basis Scout GA pakket (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Hetzelfde installatieprogramma wordt gebruikt voor de implementatie van Windows 2016 Master Target en Windows 2012R2 Master Target.
> * Windows server 2016 op de fysieke server wordt niet ondersteund door ASR Scout. Het ondersteunt alleen Windows Server 2016 VMware VM. 
>

#### <a name="bug-fixes-and-enhancements"></a>Bugfixes en verbeteringen
- Failback bescherming mislukt voor Linux VM met lijst van schijven worden gerepliceerd is leeg aan het einde van config.

### <a name="site-recovery-scout-801-update-5"></a>Site Recovery Scout 8.0.1 Update 5
Scout Update 5 is een cumulatieve update. Het bevat alle fixes van update 1 tot update 4, en de nieuwe fixes hieronder beschreven.
- Fixes van Site Recovery Scout Update 4 naar Update 5 zijn specifiek voor de master target en vContinuum componenten.
- Als bronservers, het hoofddoel, de configuratie, het proces en de RX-servers al update 4 uitvoeren, past u deze alleen toe op de hoofddoelserver. 

#### <a name="new-platform-support"></a>Nieuwe platformondersteuning
* SUSE Linux Enterprise Server 11 Service Pack 4(SP4)
* SLES 11 SP4 64 bit **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** is verpakt met het basis Scout GA-pakket (**InMage_Scout_Standard_8.0.1 GA.zip**). Download het GA-pakket van de portal, zoals beschreven in het maken van een kluis.


#### <a name="bug-fixes-and-enhancements"></a>Bugfixes en verbeteringen

* Oplossingen voor een grotere betrouwbaarheid van Windows-clusterondersteuning:
    * Opgelost- Sommige van de P2V MSCS-clusterschijven worden RAW na herstel.
    * Fixed- P2V MSCS cluster recovery fails due to an disk order mismatch.
    * Fixed- De MSCS-clusterbewerking om schijven toe te voegen mislukt met een fout in schijfgrootte.
    * Opgelost- De gereedheidscontrole voor het MSCS-cluster met RDM LUNS-toewijzing mislukt in grootteverificatie.
    * Fixed- Single node cluster protection fails because of a SCSI mismatch issue. 
    * Fixed- Re-protection of the P2V Windows cluster server fails if target cluster disks are present. 
    
* Opgelost: Als de geselecteerde hoofddoelserver zich tijdens failback-beveiliging niet op dezelfde ESXi-server bevindt als de beveiligde bronmachine (tijdens forward protection), neemt vContinuum de verkeerde hoofddoelserver op tijdens failback-herstel en het herstel bewerking mislukt.

> [!NOTE]
> * De P2V-clusterfixes zijn alleen van toepassing op fysieke MSCS-clusters die onlangs zijn beveiligd met Site Recovery Scout Update 5. Als u de clustercorrecties wilt installeren op beveiligde P2V MSCS-clusters met oudere updates, volgt u de upgradestappen die worden vermeld in sectie 12 van de releasenotities voor [siteherstelscout.](https://aka.ms/asr-scout-release-notes)
> * als op het moment van herbeveiliging dezelfde set schijven actief is op elk van de clusterknooppunten als wanneer ze in eerste instantie werden beschermd, kan de bescherming van een fysiek MSCS-cluster alleen bestaande doelschijven opnieuw gebruiken. Als dit niet het zo is, gebruik dan de handmatige stappen in sectie 12 van [Site Recovery Scout Release Notes](https://aka.ms/asr-scout-release-notes)om de doelzijdeschijven naar het juiste datastore-pad te verplaatsen, voor hergebruik tijdens herbescherming. Als u het MSCS-cluster opnieuw beschermt in de P2V-modus zonder de upgradestappen te volgen, wordt een nieuwe schijf gemaakt op de doel-ESXi-server. U moet de oude schijven handmatig verwijderen uit het gegevensarchief.
> * Wanneer een bron SLES11 of SLES11 (met een servicepack) server op een elegante manier opnieuw wordt opgestart, markeer dan handmatig de replicatieparen van de **hoofdschijf** voor hersynchronisatie. Er is geen melding in de CX-interface.Als u de hoofdschijf niet markeert voor opnieuw synchronisatie, ziet u mogelijk problemen met de integriteit van gegevens.


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 Update 4
Scout Update 4 is een cumulatieve update. Het bevat alle fixes van update 1 tot update 3, en de nieuwe fixes hieronder beschreven.

#### <a name="new-platform-support"></a>Nieuwe platformondersteuning

* Ondersteuning is toegevoegd voor vCenter/vSphere 6.0, 6.1 en 6.2
* Ondersteuning is toegevoegd voor deze Linux-besturingssystemen:
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1 en 7.2
  * CentOS 7.0, 7.1 en 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64-bits **InMage_UA_8,0,1,0_RHEL7-64_GA_06Oct2016_release.tar.gz** is verpakt met het basis-Scout GA-pakket **InMage_Scout_Standard_8.0.1 GA.zip**. Download het Scout GA-pakket van de portal zoals beschreven in het maken van een kluis.

#### <a name="bug-fixes-and-enhancements"></a>Bugfixes en verbeteringen

* Verbeterde shutdown handling voor de volgende Linux besturingssystemen en klonen, om ongewenste resynchronisatie problemen te voorkomen:
    * Red Hat Enterprise Linux (RHEL) 6.x
    * Oracle Linux (OL) 6.x
* Voor Linux zijn alle machtigingen voor toegang tot mappen in de uniformagentinstallatiemap nu alleen beperkt tot de lokale gebruiker.
* Op Windows, een oplossing voor een time-out probleem dat is opgetreden bij het uitgeven van gemeenschappelijke gedistribueerde consistentie bladwijzers, op zwaar geladen gedistribueerde toepassingen zoals SQL Server en Share Point clusters.
* Een loggerelateerde fix in het installatieprogramma voor de configuratieserverbasis.
* Een download link naar VMware vCLI 6.0 werd toegevoegd aan de Windows master target base installer.
* Er zijn extra controles en logboeken toegevoegd voor wijzigingen in de netwerkconfiguratie tijdens failover- en disaster recovery-oefeningen.
* Een oplossing voor een probleem waardoor bewaargegevens niet aan de configuratieserver zijn gemeld.  
* Voor fysieke clusters is een oplossing voor een probleem waardoor volumeresizing is mislukt in de wizard vContinuum, wanneer het bronvolume wordt kleineren.
* Een oplossing voor een probleem met clusterbeveiliging dat is mislukt met fouten: 'Kan de schijfhandtekening niet vinden', wanneer de clusterschijf een PRDM-schijf is.
* Een oplossing voor een crash van een cxps-transportserver, veroorzaakt door een uitzondering buiten het bereik.
* Servernaam en IP-adreskolommen zijn nu opnieuw te maken op de pagina **Push-installatie** van de wizard vContinuum.
* RX API-verbeteringen:
  * De vijf nieuwste beschikbare gemeenschappelijke consistentiepunten zijn nu beschikbaar (alleen gegarandeerde tags).
  * Voor alle beveiligde apparaten worden capaciteit en details van de vrije ruimte weergegeven.
  * Scout driver status op de bronserver is beschikbaar.

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** base pakket heeft:
>     * Een bijgewerkt installatieprogramma voor het basisbestand van de configuratieserver **(InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
>     * Een Windows master target base installer **(InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe).**
>     * Gebruik voor alle nieuwe installaties de nieuwe configuratieserver en Windows master target GA-bits.
> * Update 4 kan direct worden toegepast op 8.0.1 GA.
> * De configuratieserver en RX-updates kunnen niet worden teruggedraaid nadat ze zijn toegepast.


### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 Update 3

Alle updates voor siteherstel zijn cumulatief. Update 3 bevat alle fixes van Update 1 en Update 2. Update 3 kan direct worden toegepast op 8.0.1 GA. De configuratieserver en RX-updates kunnen niet worden teruggedraaid nadat ze zijn toegepast.

#### <a name="bug-fixes-and-enhancements"></a>Bugfixes en verbeteringen
Update 3 lost de volgende problemen op:

* De configuratieserver en RX worden niet geregistreerd in de kluis wanneer ze achter de proxy staan.
* Het aantal uren waarin de doelstelling herstelpunt (RPO) niet is bereikt, wordt niet bijgewerkt in het gezondheidsrapport.
* De configuratieserver synchroniseert niet met RX wanneer de ESX-hardwaredetails of netwerkdetails utf-8-tekens bevatten.
* Windows Server 2008 R2-domeincontrollers starten niet na herstel.
* Offline synchronisatie werkt niet zoals verwacht.
* Na vm-failover verloopt replicatiepaar lange tijd niet meer in de configuratieserverconsole. Gebruikers kunnen de failback- of hervattingsbewerkingen niet voltooien.
* Algemene momentopnamebewerkingen door de consistentietaak zijn geoptimaliseerd om de loskoppeling van toepassingen, zoals SQL Server-clients, te verminderen.
* De prestaties van de Consistency Tool (VACP.exe) zijn verbeterd. Het geheugengebruik dat nodig is voor het maken van momentopnamen op Windows is verminderd.
* De pushinstall-service loopt vast wanneer het wachtwoord groter is dan 16 tekens.
* vContinuum controleert en vraagt niet om nieuwe vCenter-referenties, wanneer referenties worden gewijzigd.
* Op Linux downloadt de master target cache manager (cachemgr) geen bestanden van de processerver. Dit resulteert in replicatie paar throttling.
* Wanneer de schijfvolgorde van het fysieke failovercluster (MSCS) niet op alle knooppunten hetzelfde is, is replicatie niet ingesteld voor sommige clustervolumes. Het cluster moet opnieuw worden beschermd om van deze oplossing te kunnen profiteren.  
* SMTP-functionaliteit werkt niet zoals verwacht, nadat RX is geüpgraded van Scout 7.1 naar Scout 8.0.1.
* Meer statistieken zijn toegevoegd in het logboek voor de terugdraaibewerking, om de tijd te volgen die nodig is om het te voltooien.
* Ondersteuning is toegevoegd voor Linux-besturingssystemen op de bronserver:
  * Red Hat Enterprise Linux (RHEL) 6 update 7
  * CentOS 6 update 7
* De configuratieserver en RX-consoles tonen nu meldingen voor het paar, dat in bitmapmodus gaat.
* De volgende beveiligingsoplossingen zijn toegevoegd in RX:
    * Autorisatie bypass via parameter manipulatie: Beperkte toegang tot niet-toepasselijke gebruikers.
    * Cross-site aanvraag vervalsing: De pagina-token concept werd geïmplementeerd, en het genereert willekeurig voor elke pagina. Dit betekent dat er slechts één aanmeldingsexemplaar is voor dezelfde gebruiker en dat het vernieuwen van de pagina niet werkt. In plaats daarvan wordt het omgeleid naar het dashboard.
    * Schadelijke bestandsupload: Bestanden zijn beperkt tot specifieke extensies: z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb , sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xls, xml en zip.
    * Permanente cross-site scripting: invoervalidaties zijn toegevoegd.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 Update 2 (Update 03Dec15)

Oplossingen in update 2 zijn:

* **Configuratieserver**: Problemen waardoor de 31-daagse gratis metingsfunctie niet kon werken zoals verwacht, toen de configuratieserver is geregistreerd bij de kluis Azure Site Recovery.
* **Unified agent**: Oplossing voor een probleem in update 1 waardoor de update niet is geïnstalleerd op de hoofddoelserver, tijdens de upgrade van versie 8.0 naar 8.0.1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Update 1 azure-siteherstelscout 8.0.1
Update 1 bevat de volgende bugfixes en nieuwe functies:

* 31 dagen gratis bescherming per serverexemplaar. Dit stelt u in staat om functionaliteit te testen of een proof-of-concept op te zetten.
* Alle bewerkingen op de server, inclusief failover en failback, zijn de eerste 31 dagen gratis. De tijd begint wanneer een server voor het eerst is beveiligd met Site Recovery Scout. Vanaf de 32e dag wordt elke beveiligde server in rekening gebracht tegen het standaardinstantietarief voor siteherstelbeveiliging op een site die eigendom is van de klant.
* Op elk moment is het aantal beveiligde servers dat momenteel in rekening wordt gebracht beschikbaar op het **dashboard** in de kluis.
* Ondersteuning is toegevoegd voor vSphere Command-Line Interface (vCLI) 5.5 Update 2.
* Ondersteuning is toegevoegd voor deze Linux-besturingssystemen op de bronserver:
    * RHEL 6 Update 6
    * RHEL 5 Update 11
    * CentOS 6 Update 6
    * CentOS 5-update 11
* Bugfixes om de volgende problemen op te lossen:
  * Vault-registratie mislukt voor de configuratieserver of RX-server.
  * Clustervolumes worden niet weergegeven zoals verwacht wanneer geclusterde VM's opnieuw worden beschermd terwijl ze worden hervat.
  * Failback mislukt wanneer de hoofddoelserver wordt gehost op een andere ESXi-server dan de on-premises productie-VM's.
  * Machtigingen voor configuratiebestanden worden gewijzigd wanneer u een upgrade uitvoert naar 8.0.1. Deze wijziging is van invloed op de bescherming en bewerkingen.
  * De hersynchronisatiedrempel wordt niet afgedwongen zoals verwacht, waardoor inconsistent replicatiegedrag ontstaat.
  * De RPO-instellingen worden niet correct weergegeven in de configuratieserverconsole. De niet-gecomprimeerde gegevenswaarde geeft ten onrechte de gecomprimeerde waarde weer.
  * De bewerking Verwijderen wordt niet verwijderd zoals verwacht in de wizard vContinuum en replicatie wordt niet verwijderd van de configuratieserverconsole.
  * In de wizard vContinuum wordt de schijf automatisch niet geselecteerd wanneer u op **Details** klikt in de schijfweergave, tijdens de beveiliging van MSCS VM's.
  * In het physical-to-virtual (P2V)-scenario worden vereiste HP-services (zoals CIMnotify en CqMgHost) niet verplaatst naar handmatig in VM-herstel. Dit probleem resulteert in extra opstarttijd.
  * Linux VM-beveiliging mislukt wanneer er meer dan 26 schijven op de hoofddoelserver staan.

