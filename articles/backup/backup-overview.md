---
title: Wat is Azure Backup?
description: Biedt een overzicht van de Azure Backup-service en hoe deze bijdraagt aan uw strategie voor bedrijfs continuïteit en herstel na nood gevallen (BCDR).
ms.topic: overview
ms.date: 04/24/2019
ms.custom: mvc
ms.openlocfilehash: 49ef90c874e920ef92dd5206447081a6aa4f7988
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172213"
---
# <a name="what-is-the-azure-backup-service"></a>Wat is de Azure Backup-Service?

De Azure Backup-service maakt back-ups van gegevens naar de Microsoft Azure-cloud. U kunt een back-up maken van on-premises machines en workloads en van virtuele Azure-machines (VM's).

## <a name="why-use-azure-backup"></a>Waarom Azure Backup gebruiken?

Azure Backup biedt deze belangrijke voordelen:

- **On-premises back-up op locatie**: Azure Backup biedt een eenvoudige oplossing voor het maken van back-ups van uw on-premises resources naar de Cloud. Back-ups op korte en lange termijn zonder de noodzaak om complexe on-premises back-upoplossingen te implementeren.
- **Back-ups maken van Azure IaaS vm's**: Azure Backup biedt onafhankelijke en geïsoleerde back-ups om te beschermen tegen onbedoelde vernietiging van originele gegevens. Back-ups worden opgeslagen in een Recovery Services-kluis met ingebouwde beheerde herstelpunten. Configuratie en schaal baarheid zijn eenvoudig, back-ups worden geoptimaliseerd en u kunt zo nodig eenvoudig herstellen.
- **Gemakkelijk schalen**: Azure Backup gebruikt de onderliggende kracht en onbeperkte schaal van de Azure-cloud voor hoge beschikbaarheid, zonder overhead voor onderhoud of bewaking.
- **Onbeperkte gegevens overdracht ophalen**: Azure backup beperkt de hoeveelheid inkomende of uitgaande gegevens die u overdraagt of factureert voor de gegevens die worden overgedragen.
  - Uitgaande gegevens zijn gegevens die tijdens een herstelbewerking worden overgebracht uit een Recovery Services-kluis.
  - Als u met de Azure Import/Exportservice een offline eerste back-up uitvoert voor het importeren van grote hoeveelheden gegevens, zijn er kosten verbonden aan inkomende gegevens.  [Meer informatie](backup-azure-backup-import-export.md).
- Beveilig **uw gegevens**: Azure Backup biedt oplossingen voor het beveiligen van gegevens die onderweg en in rust zijn.
- **App-consistente back-ups ophalen**: een toepassings consistente back-up betekent dat een herstel punt alle vereiste gegevens heeft om de back-up te herstellen. Azure Backup biedt toepassingsconsistente back-ups, om ervoor te zorgen dat er geen aanvullende correcties nodig zijn om de gegevens te herstellen. Herstellen van toepassingsconsistente gegevens verkort de hersteltijd, zodat u snel weer normaal aan het werk kunt.
- **Gegevens over korte en lange termijn behouden**: u kunt Recovery Services kluizen gebruiken voor het bewaren van gegevens op de korte en lange termijn. Azure heeft geen beperkingen voor hoelang gegevens bewaard blijven in een Recovery Services-kluis. U kunt gegevens zo lang bewaren als u wilt. Azure Backup heeft een limiet van 9999 herstelpunten per beveiligd exemplaar.
- **Automatisch opslagbeheer**: voor hybride omgevingen is vaak heterogene opslag vereist, soms on-premises en soms in de cloud. Met Azure Backup zijn er geen kosten voor het gebruik van on-premises opslagapparaten. De back-upopslag wordt automatisch door Azure Backup toegewezen en beheerd en u betaalt naar gebruik. Dat betekent dat u alleen betaalt voor de opslag die u verbruikt. [Lees meer](https://azure.microsoft.com/pricing/details/backup) over prijzen.
- **Meerdere opslagopties**: Azure Backup biedt twee typen replicatie om uw opslag/gegevens maximaal beschikbaar te houden.
  - Met [lokaal redundante opslag LRS](../storage/common/storage-redundancy-lrs.md) worden uw gegevens drie keer gerepliceerd (er worden drie kopieën gemaakt van uw gegevens) in een opslagschaaleenheid in een datacenter. Alle kopieën van de gegevens komen binnen dezelfde regio voor. LRS is een goedkope optie voor het beschermen van uw gegevens tegen lokale hardwarefouten.
  - [Geografisch redundante opslag (GRS)](../storage/common/storage-redundancy-grs.md) is de standaardinstelling en is de replicatieoptie die wordt aanbevolen. Met GRS worden uw gegevens gerepliceerd naar een secundaire regio (honderden kilometers verwijderd van de primaire locatie van de brongegevens). GRS is duurder dan LRS, maar biedt een hoger duurzaamheidsniveau voor uw gegevens, zelfs in geval van een regionale onderbreking.

## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Wat is het verschil tussen Azure Backup en Azure Site Recovery?

Azure Backup en Azure Site Recovery dragen beide bij aan een strategie voor bedrijfscontinuïteit en herstel na noodgevallen (BCDR) in uw bedrijf. BCDR heeft twee algemene doelen:

- Uw bedrijfsgegevens veilig en herstelbaar houden wanneer er zich storingen voordoen.
- Uw apps en workloads in bedrijf te houden tijdens geplande en ongeplande downtime.

Beide services bieden complementaire maar verschillende functionaliteit.

- **Azure site Recovery**: site Recovery biedt een oplossing voor herstel na nood gevallen voor on-premises machines en voor Azure-vm's. U repliceert machines vanaf een primaire naar een secundaire locatie. Wanneer er zich een noodgeval voordoet, voert u een failover naar de secundaire locatie uit en zijn de machines van daaruit toegankelijk. Wanneer alles weer normaal functioneert, voert u een failback uit om ze te herstellen naar de primaire locatie.
- **Azure backup**: de Azure backup-service maakt een back-up van gegevens van on-premises machines en Azure-vm's. Gegevens kunnen op een gedetailleerd niveau worden geback-upt en hersteld, inclusief back-up van bestanden, mappen, de status van het computersysteem en appbewuste gegevensback-up. Azure Backup verwerkt gegevens op een gedetailleerder niveau dan Site Recovery. Als er bijvoorbeeld een presentatie op uw laptop beschadigd raakt, kunt u Azure Backup gebruiken om de presentatie te herstellen. Als u een VM-configuratie en gegevens veilig en toegankelijk wilt houden, kunt u Site Recovery gebruiken.  

Gebruik de tabel om uw behoeften op BCDR-gebied te bepalen.

**Doel** | **Details** | **Vergelijking**
--- | --- | ---
**Gegevensback-up/-retentie** | Back-upgegevens kunnen worden bewaard en opgeslagen voor dagen, maanden of zelfs jaren, indien nodig van een perspectief voor naleving. | Met back-upoplossingen zoals Azure Backup kunt u nauwkeurig gegevens selecteren waarvan u een back-up wilt maken en het back-up- en retentiebeleid nauwkeurig afstemmen.<br/><br/> Site Recovery is niet toegestaan om hetzelfde stem afstemmen.
**Recovery point objective (RPO)** | Acceptabele hoeveelheid gegevens die verloren gaat wanneer een herstelbewerking moet worden uitgevoerd. | Back-ups hebben meer variabele RPO.<br/><br/> Back-ups van VM's hebben doorgaans een RPO van één dag, terwijl de RPO bij databaseback-up soms maar 15 minuten is.<br/><br/> Site Recovery biedt een lage RPO omdat replicatie continu of frequent is, zodat de verschillen tussen het bron- en replica-exemplaar klein zijn.
**Beoogde hersteltijd (RTO)** |De hoeveelheid tijd die nodig is om een terugzet- of herstelbewerking te voltooien. | Vanwege de grotere RPO is de hoeveelheid gegevens die een back-upoplossing moet verwerken doorgaans veel grote, wat resulteert in langere RTO's. Het kan bijvoorbeeld dagen duren om gegevens te herstellen vanaf tapes, afhankelijk van de tijd die nodig is voor het transport van de tape vanaf een externe locatie.

## <a name="what-backup-scenarios-are-supported"></a>Welke back-upscenario's worden ondersteund?

Azure Backup kan een back-up maken van zowel on-premises machines als Azure-VM's.

**Machine** | **Back-upscenario**
--- | ---
**On-premises back-up** |  1) Voer de MARS-agent (Azure Backup Microsoft Azure Recovery Services) uit op on-premises Windows-machines om een back-up te maken van afzonderlijke bestanden en systeemstatus. <br/><br/>2) Maak een back-up van on-premises machines naar een back-upserver (System Center Data Protection Manager (DPM) of Microsoft Azure Backup Server (MABS)) en configureer vervolgens de back-upserver om een back-up te maken van een Azure Backup Recovery Services kluis in Azure.
**Azure VM's** | (1) Schakel het maken van back-ups voor afzonderlijke Azure-VM's in. Wanneer u het maken van back-ups inschakelt, installeert Azure Backup een extensie op de Azure VM-agent die op de virtuele machine wordt uitgevoerd. De agent maakt een back-up van de hele virtuele machine.<br/><br/> 2) Voer de MARS-agent uit op een Azure-VM. Dit is handig als u een back-up wilt maken van afzonderlijke bestanden en mappen op de virtuele machine.<br/><br/>

## <a name="why-use-a-backup-server"></a>Waarom een back-upserver gebruiken?

De voor delen van het maken van back-ups van computers en apps naar MABS/DPM-opslag en het maken van een back-up van DPM/MABS-opslag naar een kluis zijn als volgt:

- Een back-up naar MABS/DPM biedt app-aware back-ups die zijn geoptimaliseerd voor veelgebruikte apps zoals SQL Server, Exchange en SharePoint, naast back-ups van bestanden/mappen/volumes en back-ups van de machinestatus (bare-metal, systeemstatus).
- Voor on-premises machines hoeft u de MARS-agent niet te installeren op elke machine waarvan u een back-up wilt maken. Op elke computer wordt de DPM-MABS-beveiligings agent uitgevoerd en de MARS-agent wordt alleen uitgevoerd op de MABS/DPM.
- U hebt meer flexibiliteit en gedetailleerde planningsopties voor het uitvoeren van back-ups.
- U kunt één console gebruiken voor het beheren van back-ups van meerdere machines die u verzamelt in beveiligingsgroepen. Dit is handig wanneer apps worden getierd op meerdere machines en u een back-up wilt maken.

Lees meer [hoe back-up werkt](backup-architecture.md#architecture-back-up-to-dpmmabs) wanneer u een back-server gebruikt, en over de [ondersteuningsvereisten](backup-support-matrix-mabs-dpm.md) voor back-upservers.

## <a name="what-can-i-back-up"></a>Waarvan kan ik een back-up maken?

**Machine** | **Back-upmethode** | **Een back-up maken**
--- | --- | ---
**On-premises Windows-VM's** | MARS-agent uitvoeren | Back-up van bestanden, mappen, systeemstatus.<br/><br/> Linux-machines worden niet ondersteund.
**On-premises machines** | Back-up naar DPM/MABS | Back-up van alles dat wordt beveiligd door [DPM](backup-support-matrix-mabs-dpm.md#supported-backups-to-dpm) of [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs), met inbegrip van bestanden/mappen/shares/volumes en app-specifieke gegevens.
**Azure VM's** | Back-upextensie voor Azure VM-agent uitvoeren | Back-up van de hele virtuele machine
**Azure VM's** | MARS-agent uitvoeren | Back-up van bestanden, mappen, systeemstatus.<br/><br/> Linux-machines worden niet ondersteund.
**Azure VM's** | Back-up naar MABS/DPM die wordt uitgevoerd in Azure | Back-up van alles dat wordt beveiligd door [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs) of [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807), met inbegrip van bestanden/mappen/shares/volumes en app-specifieke gegevens.

## <a name="what-backup-agents-do-i-need"></a>Welke back-upagents heb ik nodig?

**Scenario** | **Agent**
--- | ---
**Back-ups maken van Azure-VM's** | Geen agent nodig. Azure VM-extensie voor back-up is geïnstalleerd op de virtuele machine van Azure wanneer u de eerste Azure-VM-back-up uitvoert.<br/><br/> Ondersteunt Windows en Linux.
**Back-up van on-premises Windows-machines** | Download, installeer en voer de MARS-agent rechtstreeks uit op de machine.
**Back-ups van virtuele Azure-machines maken met de MARS-agent** | Download, installeer en voer de MARS-agent rechtstreeks uit op de machine. De MARS-agent kan naast de back-upextensie worden uitgevoerd.
**Back-up van on-premises machines en Azure-VM's naar DPM/MABS** | De DPM- of MABS-beveiligingsagent wordt uitgevoerd op de machines die u wilt beveiligen. De MARS-agent wordt uitgevoerd op de DPM-server/MABS waarvan u een back-up wilt maken naar Azure.

## <a name="which-backup-agent-should-i-use"></a>Welke back-upagent moet ik gebruiken?

**Een back-up maken** | **Oplossing** | **Beperking**
--- | --- | ---
**Ik wil een back-up maken van een hele Azure VM** | Schakel back-up in voor de virtuele machine. De back-upextensie wordt automatisch geconfigureerd op de Windows- of Linux-Azure-VM. | Er wordt een back-up gemaakt van de hele virtuele machine <br/><br/> Voor virtuele Windows-machines is de back-up app-consistent. Voor Linux is de back-up bestandsconsistent. Als u app-bewust voor Linux-Vm's nodig hebt, moet u dit configureren met aangepaste scripts.
**Ik wil een back-up van bepaalde bestanden/mappen maken op Azure VM** | Implementeer de MARS-agent op de virtuele machine.
**Ik wil een back-up maken van on-premises Windows-machines** | Installeer de MARS-agent op de machine. | U kunt een back-up van bestanden, mappen en de systeemstatus maken naar Azure. Back-ups zijn niet app-bewust.
**Ik wil een directe back-up maken van on-premises Linux-machines** | U moet DPM of MABS implementeren om een back-up te maken naar Azure. | Het maken van een back-up van de Linux-host wordt niet ondersteund. u kunt alleen een back-up maken van een Linux-gast machine die wordt gehost op Hyper-V
**Ik wil een back-up maken van apps die on-premises worden uitgevoerd** | Voor app-bewuste back-ups moeten computers worden beveiligd door DPM of MABS.
**Ik wil gedetailleerde en flexibele back-up- en herstelinstellingen voor virtuele Azure-machines** | Bescherm Azure-VM's met MABS/DPM in Azure voor extra flexibiliteit voor back-upplanning en volledige flexibiliteit voor het beschermen en herstellen van bestanden, mappen, volumes, apps en systeemstatus.

## <a name="backup-and-retention"></a>Back-up en retentie

Azure Backup heeft een limiet van 9999 herstelpunten, ook wel back-ups of momentopnamen genoemd, *per beveiligd exemplaar*.

- Een beveiligd exemplaar is een computer, een server (fysiek of virtueel) of een werkbelasting die is geconfigureerd voor het opslaan van back-ups van gegevens in Azure. Een exemplaar is beveiligd zodra er een back-up van de gegevens is opgeslagen.
- De back-up van de gegevens biedt beveiliging. Als de brongegevens verloren gaan of beschadigd raken, kunt u met de back-up de brongegevens herstellen.

In de volgende tabel wordt de maximale back-upfrequentie voor elk onderdeel weergegeven. De configuratie van uw back-upbeleid bepaalt hoe snel u de herstelpunten verbruikt. Als u bijvoorbeeld elke dag een herstel punt maakt, kunt u de herstel punten voor 27 jaar bewaren voordat u de service uitvoert. Als u een maandelijks herstel punt neemt, kunt u de herstel punten voor 833 jaar bewaren voordat u de service uitvoert. De back-upservice heeft geen verval tijd ingesteld voor een herstel punt.

|  | Azure Backup-agent (MARS)| System Center DPM | Azure Backup-server | Back-up van virtuele machines van Azure IaaS |
| --- | --- | --- | --- | --- |
| Back-upfrequentie<br/> (naar de Recovery Services-kluis) |Drie back-ups per dag |Twee back-ups per dag |Twee back-ups per dag |Eén back-up per dag |
| Back-upfrequentie<br/> (naar schijf) |Niet van toepassing |Om de 15 minuten voor SQL Server<br/><br/> Elk uur voor andere workloads |Om de 15 minuten voor SQL Server<br/><br/> Elk uur voor andere workloads |Niet van toepassing |
| Bewaaropties |Dagelijks, wekelijks, maandelijks, jaarlijks |Dagelijks, wekelijks, maandelijks, jaarlijks |Dagelijks, wekelijks, maandelijks, jaarlijks |Dagelijks, wekelijks, maandelijks, jaarlijks |
| Maximumaantal herstelpunten per beveiligd exemplaar |9999|9999|9999|9999|
| Maximale bewaarperiode |Afhankelijk van back-upfrequentie |Afhankelijk van back-upfrequentie |Afhankelijk van back-upfrequentie |Afhankelijk van back-upfrequentie |
| Herstelpunten op lokale schijf |Niet van toepassing | 64 voor bestands servers<br/><br/> 448 voor toepassingsservers | 64 voor bestands servers<br/><br/> 448 voor toepassingsservers |Niet van toepassing |
| Herstelpunten op tape |Niet van toepassing |Onbeperkt |Niet van toepassing |Niet van toepassing |

## <a name="how-does-azure-backup-work-with-encryption"></a>Hoe werkt Azure Backup met versleuteling?

**Versleuteling** | **On-premises back-ups maken** | **Back-ups maken van Azure-VM's** | **Back-up van SQL op virtuele machines van Azure**
--- | --- | --- | ---
Versleuteling 'at rest'<br/> (Versleuteling van gegevens waarbij het persistent wordt gemaakt/opgeslagen) | Door de klant opgegeven wachtwoordzin wordt gebruikt voor het versleutelen van gegevens | Azure [Storage service Encryption (SSE)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) wordt gebruikt voor het versleutelen van gegevens die zijn opgeslagen in de kluis.<br/><br/> Met back-up worden gegevens automatisch versleuteld voordat ze worden opgeslagen. Azure Storage worden gegevens ontsleuteld voordat ze worden opgehaald. Het gebruik van door de klant beheerde sleutels voor SSE wordt momenteel niet ondersteund.<br/><br/> U kunt back-ups maken van virtuele machines die gebruikmaken van [Azure Disk Encryption (ADE)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) om besturings systeem-en gegevens schijven te versleutelen. Azure Backup ondersteunt Vm's die zijn versleuteld met alleen BEK en met BEK en [Kek](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/). Bekijk de [beperkingen](backup-azure-vms-encryption.md#encryption-support). | Azure Backup ondersteunt back-ups van SQL Server-data bases of-servers waarop [TDe](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) is ingeschakeld. Backup ondersteunt TDE met sleutels die worden beheerd door Azure, of met door de klant beheerde sleutels (BYOK).<br/><br/> Backup voert geen SQL-versleuteling uit als onderdeel van het back-upproces.
Versleuteling in transit<br/> (Versleuteling van gegevens die van de ene locatie naar een andere worden verplaatst) | Gegevens worden versleuteld met behulp van AES256 en verzonden naar de kluis in azure via HTTPS | Binnen Azure worden gegevens tussen Azure Storage en de kluis beveiligd door HTTPS. Deze gegevens blijven op het Azure-backbone-netwerk.<br/><br/> Voor bestands herstel beveiligt iSCSI de gegevens die worden verzonden tussen de kluis en de virtuele Azure-machine. Beveiligde tunneling beschermt het iSCSI-kanaal. | Binnen Azure worden gegevens tussen Azure Storage en de kluis beveiligd door HTTPS.<br/><br/> Bestands herstel is niet relevant voor SQL.

## <a name="next-steps"></a>Volgende stappen

- [Bekijk](backup-architecture.md) de architectuur en componenten voor verschillende back-upscenario's.
- [Controleer](backup-support-matrix.md) de ondersteunings vereisten en beperkingen voor back-ups en voor [Azure VM-back-ups](backup-support-matrix-iaas.md).

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png
