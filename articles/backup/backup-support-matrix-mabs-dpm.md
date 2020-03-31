---
title: MABS & System Center DPM-ondersteuningsmatrix
description: In dit artikel wordt een overzicht van azure backup-ondersteuning samengevat wanneer u Microsoft Azure Backup Server (MABS) of System Center DPM gebruikt om een back-up te maken van on-premises en Azure VM-resources.
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: 6664f7b226b75b364fd1c83f2abc56b5a275eff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582650"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Ondersteuningsmatrix voor back-up met Microsoft Azure Backup Server of System Center DPM

U de [Azure Backup-service](backup-overview.md) gebruiken om een back-up te maken van on-premises machines en workloads en Virtuele Azure-machines (VM's). In dit artikel worden ondersteuningsinstellingen en -beperkingen voor het maken van back-ups van machines samengevat met behulp van Microsoft Azure Backup Server (MABS) of System Center Data Protection Manager (DPM) en Azure Backup.

## <a name="about-dpmmabs"></a>Over DPM/MABS

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) is een bedrijfsoplossing die back-up- en herstelmachines en -gegevens configureert, vergemakkelijkt en beheert. Het maakt deel uit van de [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) suite van producten.

MABS is een serverproduct dat kan worden gebruikt om back-ups te maken van on-premises fysieke servers, VM's en apps die erop worden uitgevoerd.

MABS is gebaseerd op System Center DPM en biedt vergelijkbare functionaliteit met een paar verschillen:

- Er is geen System Center-licentie vereist om MABS uit te voeren.
- Voor zowel MABS als DPM biedt Azure back-upopslag op lange termijn. Bovendien u met DPM back-ups maken van gegevens voor langdurige opslag op tape. MABS biedt deze functionaliteit niet.
- U een back-up maken van een primaire DPM-server met een secundaire DPM-server. De secundaire server beveiligt de database van de primaire server en de gegevensbronreplica's die op de primaire server zijn opgeslagen. Als de primaire server mislukt, kan de secundaire server werkbelastingen blijven beveiligen die door de primaire server worden beveiligd, totdat de primaire server weer beschikbaar is.  MABS biedt deze functionaliteit niet.

U downloadt MABS vanuit het [Microsoft Download Center.](https://www.microsoft.com/download/details.aspx?id=57520) Het kan on-premises of op een Azure VM worden uitgevoerd.

DPM en MABS ondersteunen een back-up van een breed scala aan apps en besturingssystemen voor servers en computers. Ze bieden meerdere back-upscenario's:

- U een back-up maken op machineniveau met systeemstatus of bare-metal back-up.
- U een back-up maken van specifieke volumes, shares, mappen en bestanden.
- U een back-up maken van specifieke apps met behulp van geoptimaliseerde app-bewuste instellingen.

## <a name="dpmmabs-backup"></a>DPM/MABS-back-up

Back-up met DPM/MABS en Azure Backup werkt als volgt:

1. DPM/MABS-beveiligingsmiddel wordt geïnstalleerd op elke machine waarvan een back-up wordt gemaakt.
1. Er wordt een back-up van machines en apps op dpm/MABS.
1. De Mars-agent (Microsoft Azure Recovery Services) is geïnstalleerd op de DPM-server/MABS.
1. De MARS-agent maakt een back-up van de DPM/MABS-schijven naar een vault voor back-upherstelservices in Azure met Azure Backup.

Voor meer informatie:

- [Meer informatie](backup-architecture.md#architecture-back-up-to-dpmmabs) over MABS-architectuur.
- [Bekijk wat er wordt ondersteund](backup-support-matrix-mars-agent.md) voor de MARS-agent.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

**Scenario** | **Agent** | **Locatie**
--- | --- | ---
**Back-up smaken van on-premises machines/workloads** | DPM/MABS-beveiligingsmiddel draait op de machines die u wilt back-upsmaken.<br/><br/> De MARS-agent op de DPM/MABS-server.<br/> De minimale versie van de Microsoft Azure Recovery Services-agent of Azure Backup-agent die nodig is om deze functie in te schakelen, is 2.0.8719.0.  | DPM/MABS moet on-premises worden uitgevoerd.

## <a name="supported-deployments"></a>Ondersteunde implementaties

DPM/MABS kan worden geïmplementeerd zoals samengevat in de volgende tabel.

**Implementatie** | **Ondersteuning** | **Details**
--- | --- | ---
**On-premises ingezet** | Fysieke server<br/><br/>Hyper-V VM<br/><br/> VMware VM | Als DPM/MABS is geïnstalleerd als vmware-vm, maakt dit alleen een back-up van VMware VM's en workloads die op die VM's worden uitgevoerd.
**Geïmplementeerd als een Azure Stack VM** | Alleen MABS | DPM kan niet worden gebruikt om back-ups te maken van Azure Stack VM's.
**Geïmplementeerd als Een Azure VM** | Beschermt Azure VM's en workloads die worden uitgevoerd op deze VM's | DPM/MABS die in Azure wordt uitgevoerd, kan geen back-upmaken van on-premises machines.

## <a name="supported-mabs-and-dpm-operating-systems"></a>Ondersteunde MABS- en DPM-besturingssystemen

Azure Backup kan een back-up maken van DPM/MABS-exemplaren waarop een van de volgende besturingssystemen wordt uitgevoerd. Besturingssystemen moeten de nieuwste servicepacks en -updates uitvoeren.

**Scenario** | **DPM/MABS**
--- | ---
**MABS op een Azure VM** |  Windows 2016 Datacenter.<br/><br/> Windows 2019 Datacenter.<br/><br/> We raden je aan om te beginnen met een afbeelding van de marktplaats.<br/><br/> Minimaal Standard_A4_v2 met vier cores en 8 GB RAM.
**DPM op een Azure VM** | System Center 2012 R2 met update 3 of hoger.<br/><br/> Windows-besturingssysteem zoals [vereist door System Center](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).<br/><br/> We raden je aan om te beginnen met een afbeelding van de marktplaats.<br/><br/> Minimaal Standard_A4_v2 met vier cores en 8 GB RAM.
**MABS on-premises** |  MABS v3 en hoger: Windows Server 2016 of Windows Server 2019
**DPM on-premises** | Fysieke server/Hyper-V VM: System Center 2012 SP1 of hoger.<br/><br/> VMware VM: System Center 2012 R2 met Update 5 of hoger.

>[!NOTE]
>Het installeren van Azure Backup Server wordt niet ondersteund op Windows Server Core of Microsoft Hyper-V Server.

## <a name="management-support"></a>Ondersteuning voor beheer

**Probleem** | **Details**
--- | ---
**Installeren** | Installeer DPM/MABS op een machine voor één gebruik.<br/><br/> Installeer DPM/MABS niet op een domeincontroller, op een machine met de functie-installatie Van Application Server, op een machine waarop Microsoft Exchange Server of System Center Operations Manager wordt uitgevoerd, of op een clusterknooppunt.<br/><br/> [Bekijk alle DPM-systeemvereisten](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).
**Domain** | DPM/MABS moet worden samengevoegd tot een domein. Installeer eerst en sluit je vervolgens aan bij DPM/MABS in een domein. DPM/MABS verplaatsen naar een nieuw domein na implementatie wordt niet ondersteund.
**Opslag** | Moderne back-upopslag (MBS) wordt ondersteund vanaf DPM 2016/MABS v2 en hoger. Het is niet beschikbaar voor MABS v1.
**MABS-upgrade** | U MABS v3 direct installeren of upgraden naar MABS v3 van MABS v2. [Meer informatie](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**Bewegende MABS** | Het verplaatsen van MABS naar een nieuwe server met behoud van de opslag wordt ondersteund als u MBS gebruikt.<br/><br/> De server moet dezelfde naam hebben als het origineel. U de naam niet wijzigen als u dezelfde opslaggroep wilt behouden en dezelfde MABS-database gebruiken om gegevensherstelpunten op te slaan.<br/><br/> U hebt een back-up van de MABS-database nodig, omdat u deze moet herstellen.

## <a name="mabs-support-on-azure-stack"></a>MABS-ondersteuning op Azure Stack

U MABS implementeren op een Azure Stack VM, zodat u back-ups van Azure Stack VM's en workloads vanaf één locatie beheren.

**Component** | **Details**
--- | ---
**MABS op Azure Stack VM** | Tenminste maat A2. We raden u aan te beginnen met een Windows Server 2012 R2- of Windows Server 2016-afbeelding van de Azure Marketplace.<br/><br/> Installeer niets anders op de MABS VM.
**MABS-opslag** | Gebruik een apart opslagaccount voor de MABS VM. De MARS-agent die op MABS draait, heeft tijdelijke opslag nodig voor een cachelocatie en om gegevens die vanuit de cloud zijn hersteld, te bewaren.
**MABS-opslaggroep** | De grootte van de MABS-opslaggroep wordt bepaald door het aantal en de grootte van schijven die zijn gekoppeld aan de MABS VM. Elke AZURE Stack VM-grootte heeft een maximum aantal schijven. A2 is bijvoorbeeld vier schijven.
**MABS retentie** | Bewaar geen back-upgegevens op de lokale MABS-schijven langer dan vijf dagen.
**MABS opschalen** | Als u uw implementatie wilt opschalen, u de grootte van de MABS VM vergroten. U bijvoorbeeld overschakelen van A- naar D-serie.<br/><br/> U er ook voor zorgen dat u regelmatig gegevens met back-upnaar Azure ontlaadt. Indien nodig u extra MABS-servers implementeren.
**.NET Framework over MABS** | De MABS VM heeft .NET Framework 3.3 SP1 of later erop geïnstalleerd.
**MABS-domein** | De MABS VM moet worden samengevoegd met een domein. Een domeingebruiker met beheerdersbevoegdheden moet MABS op de VM installeren.
**Back-up van Azure Stack VM-gegevens** | U een back-up maken van bestanden, mappen en apps.
**Ondersteunde back-up** | Deze besturingssystemen worden ondersteund voor VM's waarvan u een back-up wilt maken:<br/><br/> Windows Server halfjaarlijks kanaal (datacenter, onderneming, standaard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**SQL Server-ondersteuning voor Azure Stack VM's** | Een back-up van SQL Server 2016, SQL Server 2014, SQL Server 2012 SP1.<br/><br/> Een back-up maken en een database herstellen.
**SharePoint-ondersteuning voor Azure Stack VM's** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Een back-up maken en een farm, database, front-end en webserver herstellen.
**Netwerkvereisten voor back-upVM's** | Alle VM's in Azure Stack-workloads moeten tot hetzelfde virtuele netwerk behoren en tot hetzelfde abonnement behoren.

## <a name="dpmmabs-networking-support"></a>Ondersteuning voor DPM/MABS-netwerken

### <a name="url-access"></a>URL-toegang

De DPM-server/MABS heeft toegang nodig tot deze URL's:

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="azure-expressroute-support"></a>Azure ExpressRoute-ondersteuning

U een back-up maken van uw gegevens via Azure ExpressRoute met openbare peering (beschikbaar voor oude circuits) en Microsoft-peering. Back-up via private peering wordt niet ondersteund.

Met openbare peering: zorg voor toegang tot de volgende domeinen/adressen:

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

Selecteer met Microsoft-peering de volgende services/regio's en relevante communitywaarden:

* Azure Active Directory (12076:5060)
* Microsoft Azure-regio (afhankelijk van de locatie van uw vault voor Herstelservices)
* Azure Storage (afhankelijk van de locatie van uw vault van Herstelservices)

Zie voor meer informatie de [routeringsvereisten expressroute.](https://docs.microsoft.com/azure/expressroute/expressroute-routing)

>[!NOTE]
>Public Peering is afgeschaft voor nieuwe circuits.

### <a name="dpmmabs-connectivity-to-azure-backup"></a>DPM/MABS-connectiviteit met Azure Backup

Connectiviteit met de Azure Backup-service is vereist om back-ups goed te laten functioneren en het Azure-abonnement moet actief zijn. In de volgende tabel ziet u het gedrag als deze twee dingen niet voorkomen.

**MABS naar Azure** | **Abonnement** | **Back-up en herstellen**
--- | --- | ---
Verbonden | Actief | Maak een back-up naar de DPM/MABS-schijf.<br/><br/> Back-ups maken van Azure.<br/><br/> Herstellen vanaf schijf.<br/><br/> Herstellen vanuit Azure.
Verbonden | Verlopen/gedeprovisioneerd | Geen back-up naar schijf of Azure.<br/><br/> Als het abonnement is verlopen, u herstellen vanaf schijf of Azure.<br/><br/> Als het abonnement buiten gebruik is gesteld, u niet herstellen vanaf schijf of Azure. De Azure-herstelpunten worden verwijderd.
Meer dan 15 dagen geen connectiviteit | Actief | Geen back-up naar schijf of Azure.<br/><br/> U herstellen vanaf schijf of Azure.
Meer dan 15 dagen geen connectiviteit | Verlopen/gedeprovisioneerd | Geen back-up naar schijf of Azure.<br/><br/> Als het abonnement is verlopen, u herstellen vanaf schijf of Azure.<br/><br/> Als het abonnement buiten gebruik is gesteld, u niet herstellen vanaf schijf of Azure. De Azure-herstelpunten worden verwijderd.

## <a name="dpmmabs-storage-support"></a>Ondersteuning voor DPM/MABS-opslag

Gegevens waarvan een back-up wordt gemaakt naar DPM/MABS, worden opgeslagen in lokale schijfopslag.

**Opslag** | **Details**
--- | ---
**Mbs** | Moderne back-upopslag (MBS) wordt ondersteund vanaf DPM 2016/MABS v2 en hoger. Het is niet beschikbaar voor MABS v1.
**MABS-opslag op Azure VM** | Gegevens worden opgeslagen op Azure-schijven die zijn gekoppeld aan de DPM/MABS VM en die worden beheerd in DPM/MABS. Het aantal schijven dat kan worden gebruikt voor DPM/MABS-opslaggroep wordt beperkt door de grootte van de VM.<br/><br/> A2 VM: 4 schijven; A3 VM: 8 schijven; A4 VM: 16 schijven, met een maximale grootte van 1 TB voor elke schijf. Dit bepaalt de totale back-upopslaggroep die beschikbaar is.<br/><br/> De hoeveelheid gegevens die u maken, is afhankelijk van het aantal en de grootte van de aangesloten schijven.
**MABS-gegevensbewaring op Azure VM** | We raden u aan gegevens één dag op de DPM/MABS Azure-schijf te bewaren en een back-up te maken van DPM/MABS naar de kluis voor langere bewaring. U zo een grotere hoeveelheid gegevens beschermen door deze te ontladen naar Azure Backup.

### <a name="modern-backup-storage-mbs"></a>Moderne back-upopslag (MBS)

Vanaf DPM 2016/MABS v2 (uitgevoerd op Windows Server 2016) en hoger u profiteren van moderne back-upopslag (MBS).

- MBS-back-ups worden opgeslagen op een ReFS-schijf (Resilient File System).
- MBS maakt gebruik van ReFS-blokklonen voor een snellere back-up en efficiënter gebruik van opslagruimte.
- Wanneer u volumes toevoegt aan de lokale DPM/MABS-opslaggroep, configureert u deze met stationsletters. U vervolgens workloadopslag configureren op verschillende volumes.
- Wanneer u beveiligingsgroepen maakt om een back-up van gegevens te maken naar DPM/MABS, selecteert u het station dat u wilt gebruiken. U bijvoorbeeld back-ups opslaan voor SQL- of andere hoge IOPS-workloads op een krachtige schijf en workloads opslaan waarvan minder vaak een back-up wordt gemaakt op een schijf met lagere prestaties.

## <a name="supported-backups-to-mabs"></a>Ondersteunde back-ups naar MABS

Raadpleeg de [Azure Backup Server Protection Matrix](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix#protection-support-matrix)voor informatie over de verschillende servers en workloads die u beveiligen met Azure Backup Server.

## <a name="supported-backups-to-dpm"></a>Ondersteunde back-ups naar DPM

Voor informatie over de verschillende servers en workloads die u beschermen met Data Protection Manager, raadpleegt u het artikel [Wat kan DPM back-up?](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019).

- Geclusterde workloads waareen back-up van maakt door DPM/MABS moeten zich in hetzelfde domein bevinden als DPM/MABS of in een onderliggend/vertrouwd domein.
- U NTLM/certificaatverificatie gebruiken om back-ups te maken van gegevens in niet-vertrouwde domeinen of werkgroepen.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie](backup-architecture.md#architecture-back-up-to-dpmmabs) over MABS-architectuur.
- [Bekijk](backup-support-matrix-mars-agent.md) wat er wordt ondersteund voor de MARS-agent.
- Stel een MABS-server [in.](backup-azure-microsoft-azure-backup.md)
- [DPM instellen](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180).
