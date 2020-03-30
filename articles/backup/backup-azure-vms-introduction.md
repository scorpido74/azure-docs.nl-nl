---
title: Over Azure VM Backup
description: Lees in dit artikel hoe de Azure Backup-service een back-up maakt van Azure Virtual-machines en hoe u aanbevolen procedures volgen.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: f4b36f57362607a13c09896cd7109596aba0a852
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415970"
---
# <a name="an-overview-of-azure-vm-backup"></a>Een overzicht van Azure VM-back-up

In dit artikel wordt beschreven hoe de [Azure Backup-service](backup-introduction-to-azure-backup.md) een back-up maakt van virtuele Azure-machines (VM's).

Azure Backup biedt onafhankelijke en geïsoleerde back-ups om te voorkomen dat onbedoelde vernietiging van de gegevens op uw VM's. Back-ups worden opgeslagen in een vault van Recovery Services met ingebouwd beheer van herstelpunten. Configuratie en schaling zijn eenvoudig, back-ups zijn geoptimaliseerd en u eenvoudig herstellen als dat nodig is.

Als onderdeel van het back-upproces wordt een [momentopname gemaakt](#snapshot-creation)en worden de gegevens overgebracht naar de kluis Van Recovery Services zonder dat dit gevolgen heeft voor de productieworkloads. De momentopname biedt verschillende niveaus van consistentie, zoals [hier](#snapshot-consistency)beschreven.

Azure Backup heeft ook gespecialiseerde aanbiedingen voor databaseworkloads zoals [SQL Server](backup-azure-sql-database.md) en [SAP HANA](sap-hana-db-about.md) die workload-aware zijn, 15 minuten RPO (doelstelling herstelpunt) bieden en back-up en herstel van afzonderlijke databases mogelijk maken.

## <a name="backup-process"></a>Back-upproces

Zo voltooit Azure Backup een back-up voor Azure VM's:

1. Voor Azure VM's die zijn geselecteerd voor back-up, start Azure Backup een back-uptaak volgens het back-upschema dat u opgeeft.
1. Tijdens de eerste back-up wordt een back-upextensie op de VM geïnstalleerd als de VM wordt uitgevoerd.
    - Voor Windows VM's is de [VMSnapshot-extensie](https://docs.microsoft.com/azure/virtual-machines/extensions/vmsnapshot-windows) geïnstalleerd.
    - Voor Linux VM's is de [VMSnapshotLinux extensie](https://docs.microsoft.com/azure/virtual-machines/extensions/vmsnapshot-linux) geïnstalleerd.
1. Voor Windows VM's die worden uitgevoerd, coördineert Back-up met Windows Volume Shadow Copy Service (VSS) om een app-consistente momentopname van de VM te maken.
    - Back-up maakt standaard volledige VSS-back-ups.
    - Als back-up geen momentopname voor een app-consistent kan maken, wordt er een momentopname van de onderliggende opslag voor het bestand gemaakt (omdat er geen toepassingsschrijft plaatsvindt terwijl de vm wordt gestopt).
1. Voor Linux VM's neemt Backup een bestandsconsistente back-up. Voor app-consistente momentopnamen moet u vooraf/post-scripts handmatig aanpassen.
1. Nadat Backup de momentopname heeft gemaakt, worden de gegevens naar de kluis overgebracht.
    - De back-up wordt geoptimaliseerd door een back-up te maken van elke VM-schijf.
    - Voor elke schijf waarvan een back-up wordt gemaakt, leest Azure Backup de blokken op de schijf en identificeert en draagt alleen de gegevensblokken over die zijn gewijzigd (de delta) sinds de vorige back-up.
    - Momentopnamegegevens worden mogelijk niet onmiddellijk naar de kluis gekopieerd. Het kan enkele uren duren op piekmomenten. De totale back-uptijd voor een VM is minder dan 24 uur voor het dagelijkse back-upbeleid.
1. Wijzigingen die zijn aangebracht in een Windows-vm nadat Azure Backup is ingeschakeld, zijn:
    - Microsoft Visual C++ 2013 Redistributable(x64) - 12.0.40660 is geïnstalleerd in de VM
    - Opstarttype van De dienst van de Schaduwkopie van het Volume (VSS) veranderd in automatisch van handboek
    - IaaSVmProvider Windows-service wordt toegevoegd

1. Wanneer de gegevensoverdracht is voltooid, wordt de momentopname verwijderd en wordt een herstelpunt gemaakt.

![Azure-back-uparchitectuur voor virtuele machines](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Versleuteling van Azure VM-back-ups

Wanneer u een back-up maakt van Azure VM's met Azure Backup, worden VM's in rust versleuteld met Storage Service Encryption (SSE). Azure Backup kan ook een back-up maken van Azure VM's die zijn versleuteld met Azure Disk Encryption.

**Codering** | **Details** | **Ondersteuning**
--- | --- | ---
**Azure Disk Encryption** | Azure Disk Encryption versleutelt zowel besturingssysteem- als gegevensschijven voor Azure VM's.<br/><br/> Azure Disk Encryption integreert met BitLocker-encryptiesleutels (BEKs), die in een sleutelkluis als geheimen worden beschermd. Azure Disk Encryption integreert ook met Azure Key Vault key encryption keys (KEKs). | Azure Backup ondersteunt back-ups van beheerde en onbeheerde Azure VM's die zijn versleuteld met alleen BEK's of met BEKs samen met KEKs.<br/><br/> Zowel BEKs als KEKs worden geback-upt en versleuteld.<br/><br/> Omdat er een back-up van KEKs en BEK's wordt opgenomen, kunnen gebruikers met de benodigde machtigingen sleutels en geheimen indien nodig terugzetten naar de sleutelkluis. Deze gebruikers kunnen ook de versleutelde VM herstellen.<br/><br/> Versleutelde sleutels en geheimen kunnen niet worden gelezen door onbevoegde gebruikers of door Azure.
**Sse** | Met SSE biedt Azure Storage versleuteling in rust door gegevens automatisch te versleutelen voordat deze worden opgeslagen. Azure Storage decodeert ook gegevens voordat deze worden opgehaald. | Azure Backup maakt gebruik van SSE voor at-rest-encryptie van Azure VM's.

Voor beheerde en onbeheerde Azure VM's ondersteunt Backup zowel VM's die zijn versleuteld met alleen BEK's als VM's die zijn versleuteld met BEK's samen met KEKs.

De back-up BEKs (geheimen) en KEKs (sleutels) zijn versleuteld. Ze kunnen alleen worden gelezen en gebruikt wanneer ze door geautoriseerde gebruikers worden teruggezet naar de sleutelkluis. Onbevoegde gebruikers of Azure kunnen geen back-upsleutels of geheimen lezen of gebruiken.

BeKs zijn ook een back-up. Dus, als de BEKs verloren gaan, kunnen geautoriseerde gebruikers de BEKs herstellen naar de sleutelkluis en de versleutelde VM's herstellen. Alleen gebruikers met het benodigde niveau van machtigingen kunnen een back-up maken en versleutelde VM's of sleutels en geheimen herstellen.

## <a name="snapshot-creation"></a>Maken van momentopnamen

Azure Backup maakt snapshots volgens het back-upschema.

- **Windows VM's:** Voor Windows VM's coördineert de back-upservice met VSS om een app-consistente momentopname van de VM-schijven te maken.  Azure Backup neemt standaard een volledige VSS-back-up (hiermee worden de logboeken van toepassingen zoals SQL Server op het moment van back-up afgekapt om consistente back-up op toepassingsniveau te krijgen).  Als u een SQL Server-database gebruikt op Azure VM-back-up, u de instelling wijzigen om een VSS Copy-back-up te maken (om logboeken te behouden). Raadpleeg [dit artikel](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#troubleshoot-vm-snapshot-issues) voor meer informatie.

- **Linux VM's:** Als u app-consistente snapshots van Linux-VM's wilt maken, gebruikt u het Linux-voor-script- en post-script-framework om uw eigen aangepaste scripts te schrijven om consistentie te garanderen.

  - Azure Backup roept alleen de pre/post scripts aan die door u zijn geschreven.
  - Als de prescripts en postscripts succesvol worden uitgevoerd, markeert Azure Backup het herstelpunt als toepassingsconsistent. Wanneer u echter aangepaste scripts gebruikt, bent u uiteindelijk verantwoordelijk voor de consistentie van de toepassing.
  - [Meer informatie](backup-azure-linux-app-consistent.md) over het configureren van scripts.

## <a name="snapshot-consistency"></a>Consistentie van momentopnamen

In de volgende tabel worden de verschillende typen momentopnameconsistentie uitgelegd:

**Momentopname** | **Details** | **Herstel** | **Overweging**
--- | --- | --- | ---
**Toepassingsconsistent** | App-consistente back-ups vastleggen geheugeninhoud en in afwachting van I/O-bewerkingen. App-consistente momentopnamen gebruiken een VSS-schrijver (of pre/post-scripts voor Linux) om de consistentie van de app-gegevens te garanderen voordat er een back-up plaatsvindt. | Wanneer u een vm herstelt met een momentopname die consistent is voor de app, wordt de VM opgestart. Er is geen gegevenscorruptie of verlies. De apps starten in een consistente staat. | Windows: Alle VSS-schrijvers zijn erin geslaagd<br/><br/> Linux: Pre/post scripts zijn geconfigureerd en geslaagd
**Consistent bestandssysteem** | Consistente back-ups van het bestandssysteem bieden consistentie door een momentopname van alle bestanden tegelijkertijd te maken.<br/><br/> | Wanneer u een vm herstelt met een consistente momentopname van het bestandssysteem, wordt de VM opgestart. Er is geen gegevenscorruptie of verlies. Apps moeten hun eigen "fix-up"-mechanisme implementeren om ervoor te zorgen dat herstelde gegevens consistent zijn. | Windows: Sommige VSS-schrijvers zijn mislukt <br/><br/> Linux: Standaard (als pre/post-scripts niet zijn geconfigureerd of mislukt)
**Crash-consistent** | Momentopnamen voor crashconsistent worden doorgaans uitgevoerd als een Azure VM wordt afgesloten op het moment van back-up. Alleen de gegevens die al op de schijf aanwezig zijn op het moment van back-up worden vastgelegd en een back-up gemaakt. | Begint met het VM-opstartproces, gevolgd door een schijfcontrole om beschadigingsfouten op te lossen. Alle gegevens in het geheugen of schrijfbewerkingen die niet naar de schijf zijn overgebracht voordat de crash verloren gaat. Apps implementeren hun eigen gegevensverificatie. Een database-app kan bijvoorbeeld het transactielogboek gebruiken voor verificatie. Als het transactielogboek items bevat die zich niet in de database bevinden, worden transacties in de database teruggeschoven totdat de gegevens consistent zijn. | VM is in shutdown (gestopt / deallocated) staat.

## <a name="backup-and-restore-considerations"></a>Overwegingen voor back-ups en herstel

**Overweging** | **Details**
--- | ---
**Schijf** | Back-up van VM-schijven is parallel. Als een VM bijvoorbeeld vier schijven heeft, probeert de back-upservice van alle vier de schijven parallel te maken. Back-up is incrementeel (alleen gewijzigde gegevens).
**Planning** |  Als u back-upverkeer wilt verminderen, maakt u op verschillende tijdstippen van de dag een back-up van verschillende VM's en controleert u of de tijden elkaar niet overlappen. Het back-ups maken van VM's zorgt tegelijkertijd voor files.
**Back-ups voorbereiden** | Houd rekening met de tijd die nodig is om de back-up voor te bereiden. De voorbereidingstijd omvat het installeren of bijwerken van de back-upextensie en het activeren van een momentopname volgens het back-upschema.
**Gegevensoverdracht** | Houd rekening met de tijd die Azure Backup nodig heeft om de incrementele wijzigingen van de vorige back-up te identificeren.<br/><br/> In een incrementele back-up bepaalt Azure Backup de wijzigingen door de checksum van het blok te berekenen. Als een blok wordt gewijzigd, is het gemarkeerd voor overdracht naar de kluis. De service analyseert de geïdentificeerde blokken om te proberen de hoeveelheid gegevens die moet worden overgedragen verder te minimaliseren. Na evaluatie van alle gewijzigde blokken, brengt Azure Backup de wijzigingen over naar de kluis.<br/><br/> Er kan een vertraging tussen het nemen van de momentopname en het kopiëren naar kluis.<br/><br/> Op piekmomenten kan het tot acht uur duren voordat back-ups worden verwerkt. De back-uptijd voor een VM is minder dan 24 uur voor de dagelijkse back-up.
**Eerste back-up** | Hoewel de totale back-uptijd voor incrementele back-ups minder dan 24 uur bedraagt, is dat mogelijk niet het geval voor de eerste back-up. De tijd die nodig is voor de eerste back-up is afhankelijk van de grootte van de gegevens en wanneer de back-up wordt verwerkt.
**Wachtrij herstellen** | Azure Backup-processen herstellen taken van meerdere opslagaccounts tegelijk en het zet herstelaanvragen in een wachtrij.
**Kopie herstellen** | Tijdens het herstelproces worden gegevens gekopieerd van de kluis naar het opslagaccount.<br/><br/> De totale hersteltijd is afhankelijk van de I/O-bewerkingen per seconde (IOPS) en de doorvoer van het opslagaccount.<br/><br/> Als u de kopieertijd wilt verkorten, selecteert u een opslagaccount dat niet is geladen met andere toepassingsschrijf- en leesbewerkingen.

### <a name="backup-performance"></a>Back-upprestaties

Deze veelvoorkomende scenario's kunnen van invloed zijn op de totale back-uptijd:

- **Een nieuwe schijf toevoegen aan een beveiligde Azure-vm:** Als een VM een incrementele back-up ondergaat en een nieuwe schijf wordt toegevoegd, wordt de back-uptijd verhoogd. De totale back-uptijd kan langer dan 24 uur duren vanwege de eerste replicatie van de nieuwe schijf, samen met deltareplicatie van bestaande schijven.
- **Gefragmenteerde schijven:** Back-upbewerkingen zijn sneller wanneer schijfwijzigingen aaneengesloten zijn. Als wijzigingen worden verspreid en gefragmenteerd over een schijf, zal de back-up trager zijn.
- **Schijfverloop:** Als beveiligde schijven die incrementele back-up ondergaan een dagelijkse churn van meer dan 200 GB hebben, kan het lang duren voordat back-ups (meer dan acht uur) zijn voltooid.
- **Back-upversies:** De nieuwste versie van Back-up (bekend als de Instant Restore-versie) maakt gebruik van een meer geoptimaliseerd proces dan checksum vergelijking voor het identificeren van wijzigingen. Maar als u Instant Restore gebruikt en een back-upmomentopname hebt verwijderd, schakelt de back-up over naar de vergelijking van checksum. In dit geval zal de back-upbewerking meer dan 24 uur duren (of mislukken).

## <a name="best-practices"></a>Aanbevolen procedures

Wanneer u VM-back-ups configureert, raden we u aan deze praktijken te volgen:

- Wijzig de standaardplanningstijden die zijn ingesteld in een beleid. Als de standaardtijd in het beleid bijvoorbeeld 12:00 uur is, verhoogt u de timing met enkele minuten, zodat resources optimaal worden gebruikt.
- Als u VM's uit één kluis herstelt, raden we u ten zeerste aan verschillende [v2-opslagaccounts voor algemene doeleinden](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) te gebruiken om ervoor te zorgen dat het doelopslagaccount niet wordt beperkt. Elke virtuele machine moet bijvoorbeeld een ander opslagaccount hebben. Als er bijvoorbeeld 10 VM's zijn hersteld, gebruikt u 10 verschillende opslagaccounts.
- Voor back-ups van VM's die premium opslag gebruiken, raden we u aan *om 50%* vrije ruimte toe te wijzen aan de totale toegewezen opslagruimte, die **alleen** nodig is voor de eerste back-up. De 50% vrije ruimte is geen vereiste voor back-ups nadat de eerste back-up is voltooid
- De limiet voor het aantal schijven per opslagaccount is relatief ten opzichte van hoe zwaar de schijven worden benaderd door toepassingen die worden uitgevoerd op een VM (Infrastructure as A Service). Als algemene praktijk, als 5 tot 10 schijven of meer aanwezig zijn op een enkele opslagaccount, de balans van de belasting door het verplaatsen van een aantal schijven naar afzonderlijke opslagaccounts.

## <a name="backup-costs"></a>Back-upkosten

Azure VM's die zijn gemaakt met Azure Backup zijn onderworpen aan [Azure Backup-prijzen.](https://azure.microsoft.com/pricing/details/backup/)

Facturering begint pas als de eerste succesvolle back-up is voltooid. Op dit moment begint de facturering voor zowel opslag- als beveiligde VM's. Facturering gaat door zolang back-upgegevens voor de virtuele machine in een kluis worden opgeslagen. Als u de beveiliging van een virtuele machine stopt, maar er back-upgegevens voor de virtuele machine in een kluis bestaan, wordt de facturering voortgezet.

Facturering voor een opgegeven VM stopt alleen als de beveiliging is gestopt en alle back-upgegevens worden verwijderd. Wanneer de beveiliging stopt en er geen actieve back-uptaken zijn, wordt de grootte van de laatste succesvolle VM-back-up de beveiligde instantiegrootte die wordt gebruikt voor de maandelijkse factuur.

De berekening van de grootte van beveiligde instantie is gebaseerd op de *werkelijke* grootte van de vm. De grootte van de VM is de som van alle gegevens in de VM, met uitzondering van de tijdelijke opslag. De prijzen zijn gebaseerd op de werkelijke gegevens die op de gegevensschijven zijn opgeslagen, niet op de maximale ondersteunde grootte voor elke gegevensschijf die aan de vm is gekoppeld.

Op dezelfde manier is de back-upopslagfactuur gebaseerd op de hoeveelheid gegevens die is opgeslagen in Azure Backup, de som van de werkelijke gegevens in elk herstelpunt.

Neem bijvoorbeeld een VM van A2-standaardformaat met twee extra gegevensschijven met een maximale grootte van 32 TB per stuk. In de volgende tabel worden de werkelijke gegevens weergegeven die op elk van deze schijven zijn opgeslagen:

**Schijf** | **Maximale grootte** | **Werkelijke gegevens aanwezig**
--- | --- | ---
Besturingssysteemschijf | 32 TB | 17 GB
Lokale/tijdelijke schijf | 135 GB | 5 GB (niet inbegrepen voor back-up)
Gegevensschijf 1 | 32 TB| 30 GB
Gegevensschijf 2 | 32 TB | 0 GB

De werkelijke grootte van de VM is in dit geval 17 GB + 30 GB + 0 GB = 47 GB. Deze grootte van beveiligde instantie (47 GB) wordt de basis voor de maandelijkse factuur. Naarmate de hoeveelheid gegevens in de virtuele machine toeneemt, wordt de grootte van de beveiligde instantie die wordt gebruikt voor factureringswijzigingen, afgestemd.

## <a name="next-steps"></a>Volgende stappen

Bereid [u nu voor op Azure VM-back-up.](backup-azure-arm-vms-prepare.md)
