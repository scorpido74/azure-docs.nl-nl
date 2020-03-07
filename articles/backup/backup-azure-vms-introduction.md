---
title: Over Azure VM Backup
description: In dit artikel leest u hoe de Azure Backup-service een back-up maakt van virtuele Azure-machines en hoe u de aanbevolen procedures volgt.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 8ffbf0d0164cbf6f085518d57566b0befde6e124
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363725"
---
# <a name="an-overview-of-azure-vm-backup"></a>Een overzicht van Azure VM backup

In dit artikel wordt beschreven hoe de [Azure backup-service](backup-introduction-to-azure-backup.md) een back-up maakt van Azure virtual machines (vm's).

## <a name="backup-process"></a>Back-upproces

Hier volgt een beschrijving van de manier waarop Azure Backup een back-up voor virtuele Azure-machines uitvoert:

1. Azure Backup start een back-uptaak op basis van het back-upschema dat u opgeeft voor Azure-Vm's die zijn geselecteerd voor back-up.
1. Tijdens de eerste back-up wordt een back-upextensie op de VM geïnstalleerd als de virtuele machine wordt uitgevoerd.
    - Voor virtuele Windows-machines wordt de _VMSnapshot_ -extensie geïnstalleerd.
    - Voor virtuele Linux-machines wordt de _VMSnapshotLinux_ -extensie geïnstalleerd.
1. Voor Windows-Vm's waarop wordt uitgevoerd, worden er back-upcoördinaten met Windows Volume Shadow Copy Service (VSS) gebruikt om een app-consistente moment opname van de virtuele machine te maken.
    - Standaard maakt back-ups volledige VSS-back-ups.
    - Als back-up geen app-consistente moment opname kan maken, wordt een bestands consistente moment opname van de onderliggende opslag gebruikt (omdat er geen schrijf bewerkingen voor de toepassing plaatsvinden terwijl de virtuele machine wordt gestopt).
1. Voor Linux-Vm's neemt Backup een bestands consistente back-up. Voor app-consistente moment opnamen moet u hand matig vooraf/post-scripts aanpassen.
1. Nadat de back-up de moment opname heeft gemaakt, worden de gegevens overgedragen naar de kluis.
    - De back-up wordt geoptimaliseerd door een back-up te maken van elke VM-schijf parallel.
    - Azure Backup leest de blokken op de schijf voor elke schijf waarvan een back-up wordt gemaakt en identificeert en brengt alleen de gegevens blokken die zijn gewijzigd (Delta) sinds de vorige back-up.
    - Momentopname gegevens worden mogelijk niet direct naar de kluis gekopieerd. Het kan enkele uren duren om piek tijden. De totale back-uptijd voor een virtuele machine is minder dan 24 uur voor dagelijks back-upbeleid.
1. Wijzigingen die zijn aangebracht in een Windows-VM nadat Azure Backup is ingeschakeld, zijn:
    - Micro soft C++ Visual 2013 Redistributable (x64)-12.0.40660 is geïnstalleerd in de VM
    - Het opstart type van de Volume Shadow Copy service (VSS) is gewijzigd in automatisch vanuit hand matig
    - IaaSVmProvider Windows-service is toegevoegd

1. Wanneer de gegevens overdracht is voltooid, wordt de moment opname verwijderd en wordt er een herstel punt gemaakt.

![Back-uparchitectuur van Azure virtual machine](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Versleuteling van back-ups van Azure-VM'S

Wanneer u back-ups maakt van virtuele Azure-machines met Azure Backup, worden virtuele machines op rest versleuteld met Storage Service Encryption (SSE). Azure Backup kunt ook een back-up maken van virtuele Azure-machines die zijn versleuteld met behulp van Azure Disk Encryption.

**Versleuteling** | **Details** | **Ondersteuning**
--- | --- | ---
**Azure Disk Encryption** | Azure Disk Encryption versleutelt zowel besturings systeem-als gegevens schijven voor virtuele Azure-machines.<br/><br/> Azure Disk Encryption kan worden geïntegreerd met BitLocker-versleutelings sleutels (BEKs), die worden beveiligd in een sleutel kluis als geheimen. Azure Disk Encryption is ook geïntegreerd met Azure Key Vault Key Encryption Keys (KEKs). | Azure Backup ondersteunt back-ups van beheerde en onbeheerde Azure-Vm's die alleen met BEKs zijn versleuteld, of met BEKs samen met KEKs.<br/><br/> Zowel BEKs als KEKs worden van een back-up gemaakt en versleuteld.<br/><br/> Als er een back-up van KEKs en BEKs wordt gemaakt, kunnen gebruikers met de vereiste machtigingen sleutels en geheimen terug naar de sleutel kluis herstellen als dat nodig is. Deze gebruikers kunnen ook de versleutelde VM herstellen.<br/><br/> Versleutelde sleutels en geheimen kunnen niet worden gelezen door onbevoegde gebruikers of Azure.
**SSE** | Met SSE biedt Azure Storage versleuteling op rest door gegevens automatisch te versleutelen voordat ze worden opgeslagen. Azure Storage worden ook gegevens ontsleuteld voordat ze worden opgehaald. | Azure Backup maakt gebruik van SSE voor op reste versleuteling van virtuele Azure-machines.

Voor beheerde en onbeheerde Azure-Vm's ondersteunt back-ups beide Vm's die zijn versleuteld met alleen BEKs of Vm's die zijn versleuteld met BEKs, samen met KEKs.

De back-up-BEKs (geheimen) en KEKs (sleutels) zijn versleuteld. Ze kunnen alleen worden gelezen en gebruikt wanneer ze terug worden teruggezet naar de sleutel kluis door gemachtigde gebruikers. Niet-geautoriseerde gebruikers of Azure kunnen geen back-upsleutels of geheimen lezen of gebruiken.

Er wordt ook een back-up gemaakt van BEKs. Als de BEKs verloren zijn gegaan, kunnen geautoriseerde gebruikers dus de BEKs herstellen naar de sleutel kluis en de versleutelde Vm's herstellen. Alleen gebruikers met het benodigde machtigings niveau kunnen back-ups maken van versleutelde Vm's, sleutels en geheimen en deze herstellen.

## <a name="snapshot-creation"></a>Moment opname maken

Azure Backup maakt moment opnamen volgens het back-upschema.

- **Windows-vm's:** Voor Windows-Vm's coördineert de back-upservice met VSS voor het maken van een app-consistente moment opname van de VM-schijven.  Azure Backup maakt standaard gebruik van een volledige VSS-back-up (de logboeken van de toepassing worden afgekapt, zoals SQL Server op het moment van de back-up om consistente back-ups op toepassings niveau op te halen).  Als u een SQL Server-Data Base gebruikt in een back-up van Azure VM, kunt u de instelling wijzigen voor het maken van een VSS Copy-back-up (om logboeken te behouden). Raadpleeg [dit artikel](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#troubleshoot-vm-snapshot-issues) voor meer informatie.

- **Virtuele Linux-machines:** Als u app-consistente moment opnamen van virtuele Linux-machines wilt maken, gebruikt u het Linux pre-script en post-script-Framework om uw eigen aangepaste scripts te schrijven om consistentie te garanderen.

  - Azure Backup roept alleen de vooraf/post-scripts aan die door u zijn geschreven.
  - Als de pre-scripts en post-scripts met succes worden uitgevoerd, Azure Backup het herstel punt als toepassings consistent gemarkeerd. Wanneer u echter aangepaste scripts gebruikt, bent u uiteindelijk verantwoordelijk voor de consistentie van de toepassing.
  - Meer [informatie](backup-azure-linux-app-consistent.md) over het configureren van scripts.

### <a name="snapshot-consistency"></a>Consistentie van moment opnamen

In de volgende tabel ziet u de verschillende soorten consistentie van moment opnamen:

**Snapshot** | **Details** | **Gave** | **Overweging**
--- | --- | --- | ---
**Toepassings consistent** | Met app-consistente back-ups worden geheugen inhoud en I/O-bewerkingen in behandeling vastgelegd. App-consistente moment opnamen maken gebruik van een VSS Writer (of pre/post scripts voor Linux) om de consistentie van de app-gegevens te garanderen voordat een back-up wordt uitgevoerd. | Wanneer u een virtuele machine herstelt met een app-consistente moment opname, wordt de VM opgestart. Er zijn geen gegevens beschadiging of-verlies. De apps beginnen met een consistente status. | Windows: alle VSS-schrijvers zijn geslaagd<br/><br/> Linux: vóór/post-scripts zijn geconfigureerd en geslaagd
**Bestands systeem consistent** | Bestandssysteem consistente back-ups bieden consistentie door een moment opname van alle bestanden tegelijk te maken.<br/><br/> | Wanneer u een virtuele machine herstelt met een bestandssysteem consistente moment opname, wordt de VM opgestart. Er zijn geen gegevens beschadiging of-verlies. Apps moeten hun eigen mechanisme ' herstellen ' implementeren om ervoor te zorgen dat de herstelde gegevens consistent zijn. | Windows: sommige VSS-schrijvers zijn mislukt <br/><br/> Linux: standaard (als er vooraf/post-scripts niet zijn geconfigureerd of mislukt)
**Crash-consistent** | Crash-consistente moment opnamen doen zich meestal voor als een Azure-VM wordt afgesloten op het moment van de back-up. Alleen de gegevens die al op de schijf aanwezig zijn op het moment van de back-up worden vastgelegd en er een back-up van wordt gemaakt. | Begint met het opstart proces van de virtuele machine, gevolgd door een schijf controle om beschadigings fouten op te lossen. Gegevens in het geheugen of schrijf bewerkingen die niet naar de schijf zijn overgedragen voordat de crash is verbroken. Apps implementeren hun eigen gegevens verificatie. Een Data Base-app kan bijvoorbeeld het transactie logboek voor verificatie gebruiken. Als het transactie logboek vermeldingen bevat die zich niet in de data base bevinden, worden trans acties teruggedraaid door de data base software totdat de gegevens consistent zijn. | De virtuele machine is afgesloten (status gestopt/toegewezen).

## <a name="backup-and-restore-considerations"></a>Overwegingen voor back-up en herstel

**Overweging** | **Details**
--- | ---
**Schijf** | Back-ups van VM-schijven zijn parallel. Als een virtuele machine bijvoorbeeld vier schijven heeft, probeert de back-upservice tegelijkertijd een back-up te maken van alle vier de schijven. De back-up is incrementeel (alleen gewijzigde gegevens).
**Schema** |  Als u het back-upverkeer wilt verminderen, maakt u op verschillende tijdstippen van de dag een back-up van verschillende Vm's en zorgt u ervoor dat de tijden niet overlappen. Als er op hetzelfde moment back-ups worden gemaakt van Vm's, loopt verkeer storingen.
**Back-ups voorbereiden** | Houd de tijd die nodig is om de back-up voor te bereiden. De voorbereidings tijd omvat het installeren of bijwerken van de back-upextensie en het activeren van een moment opname volgens het back-upschema.
**Gegevensoverdracht** | Bedenk de tijd die nodig is om Azure Backup de incrementele wijzigingen van de vorige back-up te identificeren.<br/><br/> In een incrementele back-up worden de wijzigingen door Azure Backup bepaald door de controlesom van het blok te berekenen. Als een blok wordt gewijzigd, wordt het gemarkeerd voor overdracht naar de kluis. De service analyseert de geïdentificeerde blokken om te proberen de hoeveelheid gegevens die moet worden overgedragen, verder te minimaliseren. Nadat alle gewijzigde blokken zijn geëvalueerd, worden de wijzigingen in de kluis Azure Backup overgedragen.<br/><br/> Er is mogelijk een vertraging tussen het maken van de moment opname en het kopiëren naar de kluis.<br/><br/> Het kan Maxi maal acht uur duren voordat back-ups worden verwerkt. De back-uptijd voor een virtuele machine is minder dan 24 uur voor de dagelijkse back-up.
**Eerste back-up** | Hoewel de totale back-uptijd voor incrementele back-ups minder dan 24 uur is, is dit mogelijk niet het geval voor de eerste back-up. De tijd die nodig is om de eerste back-up te maken, is afhankelijk van de grootte van de gegevens en wanneer de back-up wordt verwerkt.
**Wachtrij herstellen** | Met Azure Backup worden herstel taken van meerdere opslag accounts tegelijk verwerkt en worden herstel aanvragen in een wachtrij geplaatst.
**Kopie terugzetten** | Tijdens het herstel proces worden gegevens van de kluis naar het opslag account gekopieerd.<br/><br/> De totale herstel tijd is afhankelijk van de I/O-bewerkingen per seconde (IOPS) en de door Voer van het opslag account.<br/><br/> Als u de Kopieer tijd wilt beperken, selecteert u een opslag account dat niet is geladen met andere schrijf bewerkingen en lees bewerkingen van de toepassing.

### <a name="backup-performance"></a>Back-upprestaties

Deze algemene scenario's kunnen van invloed zijn op de totale back-uptijd:

- **Een nieuwe schijf toevoegen aan een beveiligde Azure-VM:** Als een virtuele machine incrementele back-up ondergaat en er een nieuwe schijf wordt toegevoegd, neemt de back-uptijd toe. De totale back-uptijd kan langer dan 24 uur duren vanwege de initiële replicatie van de nieuwe schijf, samen met de Delta replicatie van bestaande schijven.
- **Gefragmenteerde schijven:** Back-upbewerkingen zijn sneller wanneer de schijf wijzigingen aaneengesloten zijn. Als de wijzigingen zijn verdeeld over en gefragmenteerd op een schijf, wordt de back-up langzamer.
- **Schijf verloop:** Als beveiligde schijven die een incrementele back-up uitvoeren, een dagelijks verloop hebben van meer dan 200 GB, kan het maken van een back-up lang duren (meer dan acht uur).
- **Back-upversies:** De meest recente versie van de back-up (ook wel bekend als de versie voor direct terugzetten) maakt gebruik van een meer geoptimaliseerd proces dan de controlesom vergelijking voor het identificeren van wijzigingen. Maar als u direct terugzetten gebruikt en een back-upmomentopname hebt verwijderd, worden de back-upswitches naar controlesom vergelijking. In dit geval wordt de back-upbewerking langer dan 24 uur (of mislukken).

## <a name="best-practices"></a>Aanbevolen procedures

Wanneer u back-ups van virtuele machines configureert, kunt u het volgende doen:

- Wijzig de standaard plannings tijden die in een beleid zijn ingesteld. Als de standaard tijd in het beleid bijvoorbeeld 12:00 uur is, verhoogt u de timing met enkele minuten zodat de resources optimaal worden gebruikt.
- Als u virtuele machines herstelt vanuit één kluis, raden we u ten zeerste aan om andere [v2-opslag accounts voor algemeen](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) gebruik te gebruiken om ervoor te zorgen dat het doel-opslag account niet wordt beperkt. Elke virtuele machine moet bijvoorbeeld een ander opslag account hebben. Als er bijvoorbeeld tien Vm's zijn teruggezet, gebruikt u 10 verschillende opslag accounts.
- Voor back-ups van virtuele machines die gebruikmaken van Premium Storage, kunt u met direct terugzetten *50%* beschik bare ruimte toewijzen aan de totale toegewezen opslag ruimte. Dit is **alleen** vereist voor de eerste back-up. De 50% beschik bare ruimte is geen vereiste voor back-ups nadat de eerste back-up is voltooid.
- De limiet voor het aantal schijven per opslag account is relatief ten opzichte van de mate waarin de schijven worden geopend door toepassingen die worden uitgevoerd op een IaaS-VM (Infrastructure as a Service). In het algemeen is het zo dat als 5 tot 10 schijven of meer aanwezig zijn op één opslag account, de belasting kan verdelen door sommige schijven te verplaatsen naar afzonderlijke opslag accounts.

## <a name="backup-costs"></a>Back-upkosten

Voor Azure-Vm's waarvoor een back-up is gemaakt met Azure Backup gelden [Azure backup prijzen](https://azure.microsoft.com/pricing/details/backup/).

De facturering begint pas nadat de eerste geslaagde back-up is voltooid. Op dit moment begint de facturering voor zowel opslag-als beveiligde Vm's. De facturering blijft zo lang dat back-upgegevens voor de virtuele machine worden opgeslagen in een kluis. Als u de beveiliging voor een virtuele machine stopt, maar er in een kluis back-upgegevens voor de VM bestaan, wordt de facturering voortgezet.

Facturering voor een opgegeven virtuele machine stopt alleen als de beveiliging is gestopt en alle back-upgegevens worden verwijderd. Wanneer de beveiliging wordt gestopt en er geen actieve back-uptaken zijn, wordt de grootte van de laatste geslaagde back-up van de virtuele machine de beveiligde instantie grootte die wordt gebruikt voor de maandelijkse factuur.

De berekening van de grootte van beveiligde instanties is gebaseerd op de *werkelijke* grootte van de virtuele machine. De grootte van de virtuele machine is de som van alle gegevens in de virtuele machine, met uitzonde ring van de tijdelijke opslag. Prijzen zijn gebaseerd op de werkelijke gegevens die op de gegevens schijven zijn opgeslagen, niet op de Maxi maal ondersteunde grootte voor elke gegevens schijf die is gekoppeld aan de virtuele machine.

Op dezelfde manier is de factuur voor back-upopslag gebaseerd op de hoeveelheid gegevens die is opgeslagen in Azure Backup, wat de som is van de werkelijke gegevens in elk herstel punt.

Neem bijvoorbeeld een VM met a2-standaard met twee extra gegevens schijven met een maximale grootte van 32 TB. In de volgende tabel ziet u de werkelijke gegevens die op elk van deze schijven zijn opgeslagen:

**Schijf** | **Maximale grootte** | **Werkelijke gegevens aanwezig**
--- | --- | ---
Besturingssysteemschijf | 32 TB | 17 GB
Lokale/tijdelijke schijf | 135 GB | 5 GB (niet inbegrepen voor back-up)
Gegevens schijf 1 | 32 TB| 30 GB
Gegevens schijf 2 | 32 TB | 0 GB

De werkelijke grootte van de virtuele machine in dit geval 17 GB + 30 GB + 0 GB = 47 GB. Deze beveiligde-instantie grootte (47 GB) wordt de basis voor de maandelijkse factuur. Naarmate de hoeveelheid gegevens in de virtuele machine groeit, wordt de grootte van de beveiligde instantie die wordt gebruikt voor het aanpassen van de facturering, gewijzigd.

## <a name="next-steps"></a>Volgende stappen

Bereid nu [voor op de back-up van Azure VM](backup-azure-arm-vms-prepare.md).
