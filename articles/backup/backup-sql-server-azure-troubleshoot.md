---
title: Problemen met SQL Server-databaseback-up oplossen
description: Probleemoplossingsgegevens voor het maken van back-ups van SQL Server-databases die worden uitgevoerd op Azure VM's met Azure Backup.
ms.topic: troubleshooting
ms.date: 06/18/2019
ms.openlocfilehash: 8d49adb0ab741903ccb2989cfeb4ceaef2e8a38d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408613"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Problemen met SQL Server-databaseback-up oplossen met Azure Backup

In dit artikel vindt u informatie over probleemoplossing voor SQL Server-databases die worden uitgevoerd op virtuele Azure-machines.

Zie [Over SQL Server-back-up in Azure VM's voor](sql-support-matrix.md#feature-consideration-and-limitations)meer informatie over het back-upproces en de beperkingen.

## <a name="sql-server-permissions"></a>SQL Server-machtigingen

Als u beveiliging wilt configureren voor een SQL Server-database op een virtuele machine, moet u de **AzureBackupWindowsWorkload-extensie** op die virtuele machine installeren. Als u de fout **UserErrorSQLNoSysadminMembership**krijgt, betekent dit dat uw SQL Server-instantie niet over de vereiste back-upmachtigingen beschikt. Als u deze fout wilt oplossen, voert u de stappen [in VM-machtigingen instellen](backup-azure-sql-database.md#set-vm-permissions)uit.

## <a name="troubleshoot-discover-and-configure-issues"></a>Problemen met ontdekken en configureren oplossen

Na het maken en configureren van een Vault recovery services, is het ontdekken van databases en het configureren van back-ups een proces in twee stappen.<br>

![sql](./media/backup-azure-sql-database/sql.png)

Als de SQL VM en de bijbehorende exemplaren tijdens de back-upconfiguratie niet zichtbaar zijn in de **Detectie-dB's in VM's** en **Back-up configureren** (zie bovenstaande afbeelding) ervoor zorgen dat:

### <a name="step-1-discovery-dbs-in-vms"></a>Stap 1: Discovery DBs in VM's

- Als de VM niet wordt vermeld in de gedetecteerde VM-lijst en ook niet is geregistreerd voor SQL-back-up in een andere kluis, volgt u de [back-upstappen van Discovery SQL Server.](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#discover-sql-server-databases)

### <a name="step-2-configure-backup"></a>Stap 2: Back-up configureren

- Als de kluis waarin de SQL VM is geregistreerd in dezelfde kluis die wordt gebruikt om de databases te beschermen, volgt u de stappen [Back-up configureren.](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#configure-backup)

Als de SQL VM moet worden geregistreerd in de nieuwe kluis, moet deze worden niet geregistreerd uit de oude kluis.  Voor het uitschrijven van een SQL VM uit de kluis moeten alle beveiligde gegevensbronnen worden gestopt met protecteden en u de back-upgegevens verwijderen. Het verwijderen van back-upgegevens is een destructieve bewerking.  Nadat u alle voorzorgsmaatregelen hebt genomen om de SQL VM uit te schrijven, registreert u deze zelfde VM met een nieuwe kluis en probeert u de back-upbewerking opnieuw.

## <a name="troubleshoot-backup-and-recovery-issues"></a>Problemen met back-ups en herstel problemen oplossen  

Soms kunnen er willekeurige fouten optreden bij back-up- en herstelbewerkingen of kunnen deze bewerkingen vast komen te zitten. Dit kan het gevolg zijn van antivirusprogramma's op uw VM. Als best practice stellen we de volgende stappen voor:

1. Sluit de volgende mappen uit bij het scannen van antivirusprogramma's:

    `C:\Program Files\Azure Workload Backup` `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.WorkloadBackup.Edp.AzureBackupWindowsWorkload`

    Vervang `C:\` de letter van uw *SystemDrive*.

1. Sluit de volgende drie processen die binnen een VM worden uitgevoerd uit antivirusscanning uit:

    - IaasWLPluginSvc.exe
    - IaasWorkloadCoordinaorService.exe
    - TriggerExtensionJob.exe

1. SQL biedt ook een aantal richtlijnen over om te werken met antivirusprogramma's. Zie [dit artikel](https://support.microsoft.com/help/309422/choosing-antivirus-software-for-computers-that-run-sql-server) voor meer informatie.

## <a name="error-messages"></a>Foutberichten

### <a name="backup-type-unsupported"></a>Back-uptype niet ondersteund

| Severity | Beschrijving | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|---|
| Waarschuwing | De huidige instellingen voor deze database ondersteunen bepaalde back-uptypen die aanwezig zijn in het bijbehorende beleid niet. | <li>Alleen een volledige databaseback-upbewerking kan worden uitgevoerd in de hoofddatabase. Noch differentiële back-up, noch transactie log back-up is mogelijk. </li> <li>Elke database in het eenvoudige herstelmodel staat geen back-up van transactielogboeken toe.</li> | Wijzig de database-instellingen zodanig dat alle back-uptypen in het beleid worden ondersteund. Wijzig ook het huidige beleid om alleen de ondersteunde back-uptypen op te nemen. Anders worden de niet-ondersteunde back-uptypen overgeslagen tijdens geplande back-up staak of mislukt de back-uptaak voor on-demand back-up.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| Deze SQL-database biedt geen ondersteuning voor het gevraagde type back-up. | Treedt op wanneer het databaseherstelmodel het gevraagde back-uptype niet toestaat. De fout kan in de volgende situaties optreden: <br/><ul><li>Een database die een eenvoudig herstelmodel gebruikt, staat geen logboekback-up toe.</li><li>Differentiële en logboekback-ups zijn niet toegestaan voor een hoofddatabase.</li></ul>Zie de documentatie [van SQL Server-herstelmodellen](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) voor meer informatie. | Als de logboekbackup mislukt voor de database in het eenvoudige herstelmodel, probeert u een van de volgende opties:<ul><li>Als de database zich in de eenvoudige herstelmodus bevindt, schakelt u logboekback-ups uit.</li><li>Gebruik de [SQL Server-documentatie](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) om het databaseherstelmodel te wijzigen in volledig of bulkgeregistreerd. </li><li> Als u het herstelmodel niet wilt wijzigen en u een standaardbeleid hebt om een back-up te maken van meerdere databases die niet kunnen worden gewijzigd, negeert u de fout. Uw volledige en differentiële back-ups werken per schema. De logboekback-ups worden overgeslagen, wat in dit geval wordt verwacht.</li></ul>Als het een hoofddatabase is en u differentiële of logboekback-up hebt geconfigureerd, gebruikt u een van de volgende stappen:<ul><li>Gebruik de portal om het back-upbeleidsschema voor de hoofddatabase volledig te wijzigen.</li><li>Als u een standaardbeleid hebt om een back-up te maken van meerdere databases die niet kunnen worden gewijzigd, negeert u de fout. Uw volledige back-up werkt per schema. Differentiële of log back-ups zal niet gebeuren, wat wordt verwacht in dit geval.</li></ul> |
| Bewerking geannuleerd omdat een conflicterende bewerking al in dezelfde database werd uitgevoerd. | Bekijk het [blogbericht over back-up- en herstelbeperkingen](https://deep.data.blog/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database/) die gelijktijdig worden uitgevoerd.| [Gebruik SQL Server Management Studio (SSMS) om de back-uptaken te controleren.](manage-monitor-sql-database-backup.md) Nadat de conflicterende bewerking is mislukt, start u de bewerking opnieuw.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| SQL-database bestaat niet. | De database is verwijderd of hernoemd. | Controleer of de database per ongeluk is verwijderd of de naam is gewijzigd.<br/><br/> Als de database per ongeluk is verwijderd, u de back-ups voortzetten om de database naar de oorspronkelijke locatie te herstellen.<br/><br/> Als u de database hebt verwijderd en toekomstige back-ups niet nodig hebt, selecteert u in de kluis Herstelservices de optie **Back-up stoppen** met **Back-upgegevens behouden** of **Back-upgegevens verwijderen.** Zie [Back-upSQL Server-databases beheren en controleren](manage-monitor-sql-database-backup.md)voor meer informatie.

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| Logboekketen is onderbroken. | Er wordt een back-up van de database of de VM gemaakt via een andere back-upoplossing, die de logboekketen afgekapt.|<ul><li>Controleer of er een andere back-upoplossing of script in gebruik is. Als dat het zo is, stopt u de andere back-upoplossing. </li><li>Als de back-up een on-demand logboekback is, activeert u een volledige back-up om een nieuwe logboekketen te starten. Voor geplande logboekbackups is geen actie nodig omdat de Azure Backup-service automatisch een volledige back-up activeert om dit probleem op te lossen.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| Azure Backup kan geen verbinding maken met de SQL-instantie. | Azure Backup kan geen verbinding maken met het SQL Server-exemplaar. | Gebruik de aanvullende details in het azure-portalfoutmenu om de onderliggende oorzaken te beperken. Raadpleeg [SQL-back-upproblemen](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) om de fout op te lossen.<br/><ul><li>Als de standaard SQL-instellingen geen externe verbindingen toestaan, wijzigt u de instellingen. Zie de volgende artikelen voor informatie over het wijzigen van de instellingen:<ul><li>[MSSQLSERVER_-1](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-1-database-engine-error?view=sql-server-ver15)</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Als er aanmeldingsproblemen zijn, gebruikt u deze koppelingen om deze op te lossen:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| De eerste volledige back-up ontbreekt voor deze gegevensbron. | Volledige back-up ontbreekt voor de database. Log- en differentiële back-ups zijn ouders van een volledige back-up, dus zorg ervoor dat u volledige back-ups maakt voordat u differentiële of logboekback-ups activeert. | Activeer een on-demand volledige back-up.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| Kan geen back-up maken omdat het transactielogboek voor de gegevensbron vol is. | De database transactionele logboekruimte is vol. | Als u dit probleem wilt oplossen, raadpleegt u de [SQL Server-documentatie](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| Database met dezelfde naam bestaat al op de doellocatie | De doelherstelbestemming heeft al een database met dezelfde naam.  | <ul><li>Wijzig de naam van de doeldatabase.</li><li>Of gebruik de optie overschrijven van de kracht op de herstelpagina.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| Herstellen is mislukt, omdat de database niet offline kan worden gezet. | Terwijl u een herstel doet, moet de doeldatabase offline worden gehaald. Azure Backup kan deze gegevens niet offline halen. | Gebruik de aanvullende details in het azure-portalfoutmenu om de onderliggende oorzaken te beperken. Raadpleeg de [SQL Server-documentatie](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms) voor meer informatie. |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| Kan het servercertificaat met duimafdruk op het doel niet vinden. | De hoofddatabase op de doelinstantie heeft geen geldige versleutelingsduimafdruk. | Importeer de geldige certificaatduimafdruk die op de broninstantie wordt gebruikt, in de doelinstantie. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| De logboekback-up die is gebruikt voor herstel bevat bulksgewijs geregistreerde wijzigingen. Deze kan niet worden gebruikt om op een willekeurig tijdstip te stoppen volgens de SQL-richtlijnen. | Wanneer een database zich in de herstelmodus voor bulkbestanden bevindt, kunnen de gegevens tussen een bulkgeregistreerde transactie en de volgende logboektransactie niet worden hersteld. | Kies een ander tijdstip voor herstel. [Meer informatie](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server?view=sql-server-ver15).

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| Er kan niet worden voldaan aan de voorkeur van Backup voor de SQL AlwaysOn-beschikbaarheidsgroep omdat bepaalde knooppunten van de beschikbaarheidsgroep niet zijn geregistreerd. | Knooppunten die nodig zijn om back-ups uit te voeren, worden niet geregistreerd of zijn onbereikbaar. | <ul><li>Zorg ervoor dat alle knooppunten die nodig zijn om back-ups van deze database uit te voeren, zijn geregistreerd en in orde en probeer de bewerking opnieuw.</li><li>Wijzig de back-upvoorkeur voor de sql server altijd bij beschikbaarheidsgroep.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| SQL server VM wordt afgesloten en niet toegankelijk voor Azure Backup-service. | De VM is uitgeschakeld. | Controleer of de SQL Server-instantie wordt uitgevoerd. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| Azure Backup-service maakt gebruik van Azure VM-gastagent voor het maken van back-ups, maar gastagent is niet beschikbaar op de doelserver. | De gastagent is niet ingeschakeld of is niet in orde. | [Installeer de VM-gastagent](../virtual-machines/extensions/agent-windows.md) handmatig. |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| Automatische beveiliging Intent is verwijderd of niet meer geldig. | Wanneer u automatische beveiliging inschakelt op een SQL Server-instantie, worden **back-uptaken configureren** voor alle databases in dat exemplaar. Als u automatische beveiliging uitschakelt terwijl de taken worden uitgevoerd, worden de **in-progress-taken** geannuleerd met deze foutcode. | Schakel opnieuw automatische beveiliging in om alle resterende databases te beschermen. |

### <a name="clouddosabsolutelimitreached"></a>CloudDosAbsoluteLimitBereikt

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
De bewerking wordt geblokkeerd omdat u de limiet voor het aantal toegestane bewerkingen in 24 uur hebt bereikt. | Wanneer u de maximaal toelaatbare limiet voor een bewerking in een periode van 24 uur hebt bereikt, komt deze fout. <br> Bijvoorbeeld: als u de limiet hebt bereikt voor het aantal configureback-uptaken dat per dag kan worden geactiveerd en u probeert een back-up te configureren voor een nieuw item, ziet u deze fout. | Als u de bewerking doorgaans na 24 uur opnieuw probeert, wordt dit probleem opgelost. Als het probleem echter blijft bestaan, u contact opnemen met microsoft-ondersteuning voor hulp.

### <a name="clouddosabsolutelimitreachedwithretry"></a>CloudDosAbsoluteLimitReachedRetry

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
De werking wordt geblokkeerd omdat de kluis zijn maximumlimiet heeft bereikt voor dergelijke bewerkingen die in een periode van 24 uur zijn toegestaan. | Wanneer u de maximaal toelaatbare limiet voor een bewerking in een periode van 24 uur hebt bereikt, komt deze fout. Deze fout komt meestal wanneer er op grote schaal bewerkingen zijn, zoals het wijzigen van het beleid of automatische beveiliging. In tegenstelling tot in het geval van CloudDosAbsoluteLimitReached, is er niet veel wat u doen om deze status op te lossen, in feite zal Azure Backup-service de bewerkingen intern opnieuw proberen voor alle items in kwestie.<br> Bijvoorbeeld: als u een groot aantal gegevensbronnen hebt beschermd met een beleid en u probeert dat beleid te wijzigen, wordt dit geactiveerd voor het configureren van beveiligingstaken voor elk van de beveiligde items en kan het soms de maximale limiet bereiken die voor dergelijke bewerkingen per dag is toegestaan.| Azure Backup-service probeert deze bewerking na 24 uur automatisch opnieuw uit.

### <a name="usererrorvminternetconnectivityissue"></a>UserErrorVMInternetConnectivityIssue

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
De VM kan geen contact opnemen met azure backup-service vanwege problemen met de internetverbinding. | De VM heeft uitgaande connectiviteit nodig voor Azure Backup Service, Azure Storage of Azure Active Directory services.| - Als u NSG gebruikt om de connectiviteit te beperken, moet u de AzureBackup-servicetag gebruiken om uitgaande toegang tot Azure Backup toe te staan tot Azure Backup Service, Azure Storage of Azure Active Directory-services. Volg deze [stappen](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#allow-access-using-nsg-tags) om toegang te verlenen.<br>- Zorg ervoor dat DNS Azure-eindpunten oplost.<br>- Controleer of de VM achter een load balancer zit die de toegang tot internet blokkeert. Door openbare IP toe te wijs aan de VM's, zal detectie werken.<br>- Controleer of er geen firewall / antivirus / proxy is die oproepen naar de bovenstaande drie doelservices blokkeert.

## <a name="re-registration-failures"></a>Fouten bij de herregistratie

Controleer op een of meer van de volgende symptomen voordat u de bewerking opnieuw registreren activeert:

* Alle bewerkingen (zoals back-up, herstel en back-up configureren) mislukken op de VM met een van de volgende foutcodes: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**.
* Als het gebied **Back-upstatus** voor het back-upitem **niet bereikbaar**wordt weergegeven, sluit u alle andere oorzaken uit die kunnen leiden tot dezelfde status:

  * Gebrek aan toestemming om back-upgerelateerde bewerkingen uit te voeren op de VM.
  * Het afsluiten van de VM, zodat back-ups niet kunnen plaatsvinden.
  * Netwerkproblemen.

   ![VM opnieuw registreren](./media/backup-azure-sql-database/re-register-vm.png)



* In het geval van een groep Beschikbaarheid always on zijn de back-ups mislukt nadat u de back-upvoorkeur hebt gewijzigd of na een failover.

Deze symptomen kunnen zich voordoen om een of meer van de volgende redenen:

* Een extensie is verwijderd of verwijderd van de portal.
* Er is een extensie verwijderd uit **het Configuratiescherm** op de VM onder **Een programma verwijderen of wijzigen.**
* De VM is hersteld terug in de tijd door middel van in-place schijf te herstellen.
* De VM werd voor een langere periode afgesloten, zodat de extensieconfiguratie ervan is verlopen.
* De VM is verwijderd en een andere vm is gemaakt met dezelfde naam en in dezelfde brongroep als de verwijderde VM.
* Een van de knooppunten voor beschikbaarheidsgroepen heeft niet de volledige back-upconfiguratie ontvangen. Dit kan gebeuren wanneer de beschikbaarheidsgroep is geregistreerd bij de kluis of wanneer een nieuw knooppunt wordt toegevoegd.

In de voorgaande scenario's raden we u aan een bewerking opnieuw te registreren op de VM. Zie [hier](https://docs.microsoft.com/azure/backup/backup-azure-sql-automation#enable-backup) voor instructies over het uitvoeren van deze taak in PowerShell.

## <a name="size-limit-for-files"></a>Groottelimiet voor bestanden

De totale tekenreeksgrootte van bestanden is niet alleen afhankelijk van het aantal bestanden, maar ook van hun namen en paden. Voor elk databasebestand krijgt u de logische bestandsnaam en het fysieke pad. U deze SQL-query gebruiken:

```sql
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

Schik ze nu in de volgende indeling:

```json
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

Hier volgt een voorbeeld:

```json
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

Als de tekenreeksgrootte van de inhoud meer dan 20.000 bytes bedraagt, worden de databasebestanden anders opgeslagen. Tijdens het herstel u het doelbestandspad niet instellen voor herstel. De bestanden worden hersteld naar het standaard SQL-pad dat sql-server biedt.

### <a name="override-the-default-target-restore-file-path"></a>Het standaardbestandspad voor doelherstel overschrijven

U het doelherstelbestandspad tijdens de herstelbewerking overschrijven door een JSON-bestand te plaatsen dat de toewijzing van het databasebestand bevat naar het doelherstelpad. Maak `database_name.json` een bestand en plaats het op de locatie *C:\Program Files\Azure Workload Backup\bin\plugins\SQL*.

De inhoud van het bestand moet in deze indeling zijn:

```json
[
  {
    "Path": "<Restore_Path>",
    "LogicalName": "<LogicalName>",
    "IsDir": "false"
  },
  {
    "Path": "<Restore_Path>",
    "LogicalName": "LogicalName",
    "IsDir": "false"
  },  
]
```

Hier volgt een voorbeeld:

```json
[
  {
   "Path": "F:\\Data\\testdb2_1546408741449456.mdf",
   "LogicalName": "testdb7",
   "IsDir": "false"
  },
  {
    "Path": "F:\\Log\\testdb2_log_1546408741449456.ldf",
    "LogicalName": "testdb7_log",
    "IsDir": "false"
  },  
]
```

In de voorgaande inhoud u de logische naam van het databasebestand krijgen met behulp van de volgende SQL-query:

```sql
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```

Dit bestand moet worden geplaatst voordat u de herstelbewerking activeert.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Backup for SQL VM's voor](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup)meer informatie over Azure Backup for SQL Server VM's (public preview).
