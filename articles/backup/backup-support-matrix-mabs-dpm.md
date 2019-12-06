---
title: Ondersteunings matrix voor MABS & System Center DPM
description: Dit artikel bevat een overzicht van Azure Backup ondersteuning wanneer u Microsoft Azure Backup Server (MABS) of System Center DPM gebruikt om back-ups te maken van on-premises en Azure VM-resources.
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: f9ee31525f2ee5a19aebe0a9258dff3ecfdcbb92
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74841164"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Ondersteunings matrix voor back-up met Microsoft Azure Backup Server of System Center DPM

U kunt de [Azure backup-service](backup-overview.md) gebruiken voor het maken van een back-up van on-premises machines en werk belastingen, en virtuele machines van Azure (vm's). In dit artikel vindt u een overzicht van de ondersteunings instellingen en beperkingen voor het maken van back-ups van machines met behulp van Microsoft Azure Backup Server (MABS) of System Center Data Protection Manager (DPM) en Azure Backup.

## <a name="about-dpmmabs"></a>Over DPM-MABS

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) is een bedrijfs oplossing waarmee back-ups en herstel van bedrijfs computers en-gegevens worden geconfigureerd, vergemakkelijkt en beheerd. Het maakt deel uit van de [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) -product suite.

MABS is een server product dat kan worden gebruikt voor het maken van een back-up van on-premises fysieke servers, Vm's en apps die hierop worden uitgevoerd.

MABS is gebaseerd op System Center DPM en biedt vergelijk bare functionaliteit met enkele verschillen:

- Er is geen System Center-licentie vereist om MABS uit te voeren.
- Voor zowel MABS als DPM biedt Azure lange termijn back-upopslag. Daarnaast kunt u met DPM back-ups maken van gegevens voor lange termijn opslag op tape. MABS biedt deze functionaliteit niet.
- U kunt een back-up maken van een primaire DPM-server met een secundaire DPM-server. De secundaire server beveiligt de database van de primaire server en de gegevensbronreplica's die op de primaire server zijn opgeslagen. Als de primaire server mislukt, kan de secundaire server werkbelastingen blijven beveiligen die door de primaire server worden beveiligd, totdat de primaire server weer beschikbaar is.  MABS biedt deze functionaliteit niet.

U kunt MABS downloaden van het [micro soft Download centrum](https://www.microsoft.com/download/details.aspx?id=57520). Het kan on-premises of op een virtuele machine van Azure worden uitgevoerd.

DPM en MABS bieden ondersteuning voor het maken van back-ups van een groot aantal verschillende apps en server-en client besturingssystemen. Ze bieden meerdere back-upscenario's:

- U kunt een back-up op machine niveau maken met de systeem status of bare metal-back-up.
- U kunt back-ups maken van specifieke volumes, shares, mappen en bestanden.
- U kunt back-ups van specifieke apps maken met behulp van geoptimaliseerde app-instellingen.

## <a name="dpmmabs-backup"></a>DPM/MABS-back-up

Back-up met DPM-MABS en Azure Backup werkt als volgt:

1. De DPM/MABS-beveiligings agent wordt geïnstalleerd op elke computer waarvan een back-up wordt gemaakt.
1. Er wordt een back-up gemaakt van machines en apps naar lokale opslag op DPM-MABS.
1. De Microsoft Azure Recovery Services-agent (MARS) is geïnstalleerd op de DPM-server-MABS.
1. De MARS-agent maakt een back-up van de DPM-MABS-schijven met een back-upRecovery Services kluis in azure met behulp van Azure Backup.

Voor meer informatie:

- Meer [informatie](backup-architecture.md#architecture-back-up-to-dpmmabs) over MABS-architectuur.
- [Controleer wat wordt ondersteund](backup-support-matrix-mars-agent.md) voor de Mars-agent.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

**Scenario** | **Agent** | **Locatie**
--- | --- | ---
**Back-ups maken van on-premises machines/werk belastingen** | De DPM/MABS Protection-agent wordt uitgevoerd op de computers waarvan u een back-up wilt maken.<br/><br/> De MARS-agent op de DPM-MABS-server.<br/> De minimale versie van de Microsoft Azure Recovery Services agent, of Azure Backup Agent, die vereist is om deze functie in te scha kelen, is 2.0.8719.0.  | DPM-MABS moeten on-premises worden uitgevoerd.

## <a name="supported-deployments"></a>Ondersteunde implementaties

DPM/MABS kan worden geïmplementeerd, zoals in de volgende tabel wordt samenvatten.

**Implementatie** | **Ondersteuning** | **Details**
--- | --- | ---
**On-premises geïmplementeerd** | Fysieke server<br/><br/>Hyper-V VM<br/><br/> VMware-VM | Als DPM/MABS is geïnstalleerd als een VMware-VM, wordt alleen een back-up gemaakt van virtuele VMware-machines en workloads die worden uitgevoerd op deze Vm's.
**Geïmplementeerd als een Azure Stack VM** | Alleen MABS | DPM kan niet worden gebruikt om back-ups te maken van Azure Stack Vm's.
**Geïmplementeerd als een virtuele machine van Azure** | Beveiligt Azure-Vm's en-workloads die worden uitgevoerd op deze Vm's | DPM-MABS die in Azure worden uitgevoerd, kunnen geen back-ups maken van on-premises machines.

## <a name="supported-mabs-and-dpm-operating-systems"></a>Ondersteunde MABS-en DPM-besturings systemen

Azure Backup kunt een back-up maken van DPM-MABS-exemplaren waarop een van de volgende besturings systemen wordt uitgevoerd. In besturings systemen moeten de nieuwste service packs en updates worden uitgevoerd.

**Scenario** | **DPM-MABS**
--- | ---
**MABS op een Azure VM** | Windows Server 2012 R2.<br/><br/> Windows 2016 Datacenter.<br/><br/> Windows 2019 Datacenter.<br/><br/> U wordt aangeraden te beginnen met een installatie kopie van de Marketplace.<br/><br/> Mini maal a2 standaard met twee kernen en 3,5 GB aan RAM-geheugen.
**DPM op een virtuele Azure-machine** | System Center 2012 R2 met update 3 of hoger.<br/><br/> Het Windows-besturings systeem zoals [vereist door System Center](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).<br/><br/> U wordt aangeraden te beginnen met een installatie kopie van de Marketplace.<br/><br/> Mini maal a2 standaard met twee kernen en 3,5 GB aan RAM-geheugen.
**On-premises MABS** | Ondersteunde 64-bits besturings systemen:<br/><br/> MABS v3 en hoger: Windows Server 2019 (Standard, Data Center, Essentials). <br/><br/> MABS v2 en hoger: Windows Server 2016 (Standard, Data Center, Essentials).<br/><br/> Alle MABS-versies: Windows Server 2012 R2.<br/><br/>Alle MABS-versies: Windows Storage Server 2012 R2.
**On-premises DPM** | Fysieke server/Hyper-V-VM: System Center 2012 SP1 of hoger.<br/><br/> VMware VM: System Center 2012 R2 met Update 5 of hoger.

## <a name="management-support"></a>Beheer ondersteuning

**Probleem** | **Details**
--- | ---
**Installatie** | Installeer DPM-MABS op een computer met één doel.<br/><br/> Installeer DPM/MABS niet op een domein controller, op een computer met de installatie van de functie toepassings server op een computer waarop micro soft Exchange Server of System Center Operations Manager, of op een cluster knooppunt.<br/><br/> [Controleer alle DPM-systeem vereisten](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).
**Domein** | DPM/MABS moet lid zijn van een domein. Installeer eerst en voeg vervolgens DPM-MABS toe aan een domein. Het verplaatsen van DPM-MABS naar een nieuw domein na implementatie wordt niet ondersteund.
**Storage** | Moderne back-upopslag (MBS) wordt ondersteund vanuit DPM 2016/MABS v2 en hoger. Het is niet beschikbaar voor MABS v1.
**MABS-upgrade** | U kunt MABS v3 rechtstreeks installeren of een upgrade uitvoeren naar MABS v3 vanuit MABS v2. [Meer informatie](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**MABS verplaatsen** | Het is niet mogelijk om MABS naar een nieuwe server te verplaatsen terwijl de opslag wordt behouden als u MBS gebruikt.<br/><br/> De server moet dezelfde naam hebben als de oorspronkelijke. U kunt de naam niet wijzigen als u dezelfde opslag groep wilt bewaren en dezelfde MABS-Data Base wilt gebruiken om gegevens herstel punten op te slaan.<br/><br/> U hebt een back-up van de MABS-data base nodig, omdat u deze moet herstellen.

## <a name="mabs-support-on-azure-stack"></a>MABS-ondersteuning voor Azure Stack

U kunt MABS implementeren op een Azure Stack virtuele machine, zodat u een back-up van Azure Stack Vm's en workloads vanaf één locatie kunt beheren.

**Onderdeel** | **Details**
--- | ---
**MABS op Azure Stack VM** | Ten minste a2. U kunt het beste beginnen met een installatie kopie van Windows Server 2012 R2 of Windows Server 2016 vanuit Azure Marketplace.<br/><br/> Installeer niets anders op de MABS-VM.
**MABS-opslag** | Gebruik een afzonderlijk opslag account voor de MABS-VM. De MARS-agent die wordt uitgevoerd op MABS, heeft tijdelijke opslag nodig voor een cache locatie en voor het bewaren van gegevens die zijn hersteld vanuit de Cloud.
**Opslag groep MABS** | De grootte van de MABS-opslag groep wordt bepaald door het aantal en de grootte van de schijven die aan de MABS-VM zijn gekoppeld. Voor elke Azure Stack VM-grootte geldt een maximum aantal schijven. A2 is bijvoorbeeld vier schijven.
**MABS-retentie** | Bewaar niet langer dan vijf dagen back-ups van gegevens op de lokale MABS-schijven.
**MABS omhoog schalen** | Als u de implementatie wilt uitbreiden, kunt u de grootte van de MABS-VM verg Roten. U kunt bijvoorbeeld wijzigen van een naar D-serie.<br/><br/> U kunt er ook voor zorgen dat u regel matig gegevens offloadt met back-up naar Azure. Indien nodig kunt u aanvullende MABS-servers implementeren.
**.NET Framework op MABS** | Voor de MABS-VM moet .NET Framework 3,3 SP1 of hoger zijn geïnstalleerd.
**Domein MABS** | De MABS-VM moet lid zijn van een domein. Een domein gebruiker met beheerders bevoegdheden moet MABS installeren op de virtuele machine.
**Back-up van VM-gegevens Azure Stack** | U kunt back-ups maken van bestanden, mappen en apps.
**Ondersteunde back-up** | Deze besturings systemen worden ondersteund voor virtuele machines waarvan u een back-up wilt maken:<br/><br/> Windows Server Semi-Annual-kanaal (Data Center, Enter prise, Standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**SQL Server ondersteuning voor Azure Stack Vm's** | Maak een back-up van SQL Server 2016 SQL Server 2014 SQL Server 2012 SP1.<br/><br/> Back-ups maken en herstellen van een Data Base.
**Share point-ondersteuning voor Azure Stack Vm's** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Back-ups maken en herstellen van een farm, Data Base, front-end en webserver.
**Netwerk vereisten voor het maken van back-ups van Vm's** | Alle virtuele machines in Azure Stack workload moeten deel uitmaken van hetzelfde virtuele netwerk en behoren tot hetzelfde abonnement.

## <a name="dpmmabs-networking-support"></a>DPM/MABS-netwerk ondersteuning

### <a name="url-access"></a>URL-toegang

De DPM-server-MABS moet toegang hebben tot deze Url's:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="dpmmabs-connectivity-to-azure-backup"></a>DPM/MABS-verbinding met Azure Backup

De verbinding met de Azure Backup-service is vereist voor een juiste werking van back-ups en het Azure-abonnement moet actief zijn. In de volgende tabel ziet u het gedrag als deze twee dingen zich niet voordoen.

**MABS naar Azure** | **Abonnement** | **Back-up maken/herstellen**
--- | --- | ---
Verbonden | Actief | Maak een back-up naar de DPM-MABS-schijf.<br/><br/> Maak een back-up naar Azure.<br/><br/> Herstellen vanaf schijf.<br/><br/> Herstellen vanuit Azure.
Verbonden | Verlopen/niet-ingericht | Geen back-up naar schijf of Azure.<br/><br/> Als het abonnement is verlopen, kunt u het herstellen vanaf schijf of Azure.<br/><br/> Als het abonnement uit bedrijf is genomen, kunt u niet meer herstellen vanaf schijf of Azure. De Azure-herstel punten worden verwijderd.
Geen connectiviteit langer dan 15 dagen | Actief | Geen back-up naar schijf of Azure.<br/><br/> U kunt herstellen vanaf schijf of Azure.
Geen connectiviteit langer dan 15 dagen | Verlopen/niet-ingericht | Geen back-up naar schijf of Azure.<br/><br/> Als het abonnement is verlopen, kunt u het herstellen vanaf schijf of Azure.<br/><br/> Als het abonnement uit bedrijf is genomen, kunt u niet meer herstellen vanaf schijf of Azure. De Azure-herstel punten worden verwijderd.

## <a name="dpmmabs-storage-support"></a>Ondersteuning voor DPM/MABS-opslag

Gegevens waarvan een back-up is gemaakt naar DPM/MABS, worden opgeslagen op de lokale schijf opslag.

**Storage** | **Details**
--- | ---
**MBS** | Moderne back-upopslag (MBS) wordt ondersteund vanuit DPM 2016/MABS v2 en hoger. Het is niet beschikbaar voor MABS v1.
**MABS-opslag op Azure VM** | Gegevens worden opgeslagen op Azure-schijven die zijn gekoppeld aan de DPM/MABS-VM en die worden beheerd in DPM/MABS. Het aantal schijven dat kan worden gebruikt voor de DPM/MABS-opslag groep wordt beperkt door de grootte van de virtuele machine.<br/><br/> A2 VM: 4 schijven; A3 VM: 8 schijven; A4 VM: 16 schijven, met een maximale grootte van 1 TB voor elke schijf. Hiermee bepaalt u de totale beschik bare back-upopslaggroep.<br/><br/> De hoeveelheid gegevens waarvan u een back-up kunt maken, is afhankelijk van het aantal en de grootte van de gekoppelde schijven.
**MABS voor het bewaren van gegevens op Azure VM** | We raden u aan gegevens voor één dag op de DPM-MABS Azure-schijf te bewaren en een back-up te maken van DPM/MABS naar de kluis voor een langere Bewaar periode. Zo kunt u een grotere hoeveelheid gegevens beveiligen door deze naar Azure Backup te offloaden.

### <a name="modern-backup-storage-mbs"></a>Moderne back-upopslag (MB)

Vanuit DPM 2016/MABS v2 (dat wordt uitgevoerd op Windows Server 2016) en hoger, kunt u profiteren van moderne back-upopslag (MBS).

- MBS-back-ups worden opgeslagen op een ReFS-schijf (robuust bestands systeem).
- MBS maakt gebruik van ReFS-blok kloon voor snellere back-ups en efficiënter gebruik van opslag ruimte.
- Wanneer u volumes aan de lokale DPM/MABS-opslag groep toevoegt, configureert u deze met stationsletters. U kunt vervolgens de werkbelasting opslag configureren op verschillende volumes.
- Wanneer u beveiligings groepen maakt om een back-up te maken van gegevens naar DPM/MABS, selecteert u het station dat u wilt gebruiken. U kunt bijvoorbeeld back-ups opslaan voor SQL of andere workloads met een hoge IOPS op een hoogwaardige schijf en werk belastingen opslaan waarvan minder vaak een back-up wordt gemaakt op een lager prestatie station.

## <a name="supported-backups-to-mabs"></a>Ondersteunde back-ups naar MABS

Raadpleeg de [Azure Backup Server Protection-matrix](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix#protection-support-matrix)voor informatie over de verschillende servers en workloads die u kunt beveiligen met Azure backup server.

## <a name="supported-backups-to-dpm"></a>Ondersteunde back-ups naar DPM

Voor informatie over de verschillende servers en workloads die u kunt beveiligen met Data Protection Manager raadpleegt u het artikel [waarvan u een back-up kunt maken van DPM?](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019).

- Het maken van een back-up van geclusterde werk belastingen door DPM/MABS moet zich in hetzelfde domein als DPM/MABS of in een onderliggend/vertrouwd domein bevallen.
- U kunt NTLM/certificaat verificatie gebruiken om een back-up te maken van gegevens in niet-vertrouwde domeinen of werk groepen.

## <a name="next-steps"></a>Volgende stappen

- Meer [informatie](backup-architecture.md#architecture-back-up-to-dpmmabs) over MABS-architectuur.
- [Controleer](backup-support-matrix-mars-agent.md) wat wordt ondersteund voor de Mars-agent.
- [Stel](backup-azure-microsoft-azure-backup.md) een MABS-server in.
- [DPM instellen](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180).
