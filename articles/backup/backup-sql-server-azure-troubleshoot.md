---
title: Problemen met SQL Server database back-up oplossen
description: Informatie over het oplossen van back-ups van SQL Server-data bases die worden uitgevoerd op virtuele machines van Azure met Azure Backup.
ms.topic: troubleshooting
ms.date: 06/18/2019
ms.openlocfilehash: 95f7966fa59f0a1f6f6a3c9c6832cc573f89e05c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172129"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Problemen met SQL Server database back-up oplossen met behulp van Azure Backup

Dit artikel bevat informatie over het oplossen van problemen met SQL Server-data bases die worden uitgevoerd op virtuele machines van Azure.

Zie [over SQL Server back-up in azure vm's](backup-azure-sql-database.md#feature-consideration-and-limitations)voor meer informatie over het back-upproces en de beperkingen.

## <a name="sql-server-permissions"></a>SQL Server machtigingen

Als u de beveiliging voor een SQL Server Data Base op een virtuele machine wilt configureren, moet u de **AzureBackupWindowsWorkload** -extensie op die virtuele machine installeren. Als u de fout **UserErrorSQLNoSysadminMembership**krijgt, betekent dit dat uw SQL Server-exemplaar niet over de vereiste back-upmachtigingen beschikt. Volg de stappen in [set VM permissions](backup-azure-sql-database.md#set-vm-permissions)om deze fout op te lossen.

## <a name="error-messages"></a>Foutberichten

### <a name="backup-type-unsupported"></a>Het back-uptype wordt niet ondersteund

| Severity | Beschrijving | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|---|
| Waarschuwing | De huidige instellingen voor deze data base bieden geen ondersteuning voor bepaalde back-uptypen die aanwezig zijn in het bijbehorende beleid. | <li>Alleen een volledige database back-upbewerking kan worden uitgevoerd op de hoofd database. U kunt geen differentiële back-up of transactie logboek back-up maken. </li> <li>Voor alle data bases in het eenvoudige herstel model is het maken van back-ups van transactie logboeken niet toegestaan.</li> | Wijzig de data base-instellingen zodanig dat alle back-uptypen in het beleid worden ondersteund. Of wijzig het huidige beleid zodat alleen de ondersteunde back-uptypen worden vermeld. Anders worden de niet-ondersteunde back-uptypen overgeslagen tijdens de geplande back-up of mislukt de back-uptaak voor back-ups op aanvraag.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| Deze SQL-database biedt geen ondersteuning voor het gevraagde type back-up. | Deze gebeurtenis treedt op wanneer het herstel model van de data base het aangevraagde back-uptype niet toestaat. De fout kan in de volgende situaties optreden: <br/><ul><li>Voor een Data Base die gebruikmaakt van een eenvoudig herstel model is logboek back-up niet toegestaan.</li><li>Differentiële en logboek back-ups zijn niet toegestaan voor een hoofd database.</li></ul>Zie de documentatie over [SQL Server herstel modellen](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) voor meer informatie. | Als de back-up van het logboek mislukt voor de data base in het eenvoudige herstel model, probeert u een van de volgende opties:<ul><li>Als de data base zich in de modus voor eenvoudig herstel bevindt, schakelt u logboek back-ups uit.</li><li>Gebruik de [SQL Server documentatie](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) om het herstel model van de data base te wijzigen in volledig of bulksgewijs vastgelegd. </li><li> Als u het herstel model niet wilt wijzigen en u een standaard beleid hebt voor het maken van back-ups van meerdere data bases die niet kunnen worden gewijzigd, negeert u de fout. Uw volledige en differentiële back-ups worden per schema uitgevoerd. De back-ups van het logboek worden overgeslagen, wat in dit geval wordt verwacht.</li></ul>Als het een hoofd database is en u differentiële of logboek back-up hebt geconfigureerd, gebruikt u een van de volgende stappen:<ul><li>Gebruik de portal om het schema voor back-upbeleid voor de hoofd database te wijzigen in volledig.</li><li>Als u een standaard beleid hebt voor het maken van back-ups van meerdere data bases die niet kunnen worden gewijzigd, kunt u de fout negeren. De volledige back-up wordt per schema uitgevoerd. Differentiële of logboek back-ups worden niet uitgevoerd, wat in dit geval wordt verwacht.</li></ul> |
| De bewerking is geannuleerd omdat er al een conflicterende bewerking wordt uitgevoerd op dezelfde data base. | Zie het [blog bericht over de beperkingen voor back-ups en herstel](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) die gelijktijdig worden uitgevoerd.| [Gebruik SQL Server Management Studio (SSMS) om de back-uptaken te bewaken](manage-monitor-sql-database-backup.md). Als de conflicterende bewerking is mislukt, start u de bewerking opnieuw.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| SQL-database bestaat niet. | De data base is verwijderd of heeft een andere naam gekregen. | Controleer of de data base per ongeluk is verwijderd of een andere naam heeft gekregen.<br/><br/> Als de data base per ongeluk is verwijderd, kunt u de data base herstellen naar de oorspronkelijke locatie om door te gaan met het maken van back-ups.<br/><br/> Als u de Data Base hebt verwijderd en geen back-ups meer nodig hebt, selecteert u in de Recovery Services kluis back **-up stoppen** met **back-upgegevens behouden** of **back-upgegevens verwijderen**. Zie [back-ups beheren en controleren SQL server data bases](manage-monitor-sql-database-backup.md)voor meer informatie.

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| Logboekketen is onderbroken. | Er wordt een back-up gemaakt van de data base of de virtuele machine via een andere back-upoplossing, waardoor de logboek keten wordt afgekapt.|<ul><li>Controleer of er een andere back-upoplossing of een ander script wordt gebruikt. Als dit het geval is, stopt u de andere back-upoplossing. </li><li>Als de back-up een logboek back-up op aanvraag is, moet u een volledige back-up activeren om een nieuwe logboek keten te starten. Voor geplande logboek back-ups is geen actie vereist omdat de Azure Backup-service automatisch een volledige back-up wordt geactiveerd om dit probleem op te lossen.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| Azure Backup kan geen verbinding maken met het SQL-exemplaar. | Azure Backup kan geen verbinding maken met het SQL Server exemplaar. | Gebruik de aanvullende details in het menu Azure Portal fouten om de hoofd oorzaken te beperken. Raadpleeg [SQL backup Troubleshooting](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) om de fout op te lossen.<br/><ul><li>Als de standaard SQL-instellingen geen externe verbindingen toestaan, wijzigt u de instellingen. Raadpleeg de volgende artikelen voor informatie over het wijzigen van de instellingen:<ul><li>[MSSQLSERVER_-1](/previous-versions/sql/sql-server-2016/bb326495(v=sql.130))</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Als er zich problemen met de aanmelding voordoen, gebruikt u deze koppelingen om ze te herstellen:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| De eerste volledige back-up ontbreekt voor deze gegevens bron. | Volledige back-up ontbreekt voor de data base. Logboeken en differentiële back-ups zijn ouders van een volledige back-up. Zorg er dus voor dat u volledige back-ups maakt voordat u differentiële of logboek back-ups gaat activeren. | Activeer een volledige back-up op aanvraag.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| Kan geen back-up maken omdat het transactie logboek voor de gegevens bron vol is. | De logboek ruimte voor de transactionele data base is vol. | Raadpleeg de [documentatie van SQL Server](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error)om dit probleem op te lossen. |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| Er bestaat al een Data Base met dezelfde naam op de doel locatie | De doel locatie voor terugzetten heeft al een Data Base met dezelfde naam.  | <ul><li>Wijzig de naam van de doel database.</li><li>Of gebruik de optie Overwrite afdwingen op de pagina herstellen.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| Herstellen is mislukt, omdat de database niet offline kan worden gezet. | Wanneer u een herstel bewerking uitvoert, moet de doel database offline worden gezet. Azure Backup kunt deze gegevens niet offline zetten. | Gebruik de aanvullende details in het menu Azure Portal fouten om de hoofd oorzaken te beperken. Raadpleeg de [SQL Server-documentatie](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms) voor meer informatie. |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| Kan het server certificaat met de vinger afdruk niet vinden op het doel. | De hoofd database op het doel exemplaar heeft geen geldige versleutelings vingerafdruk. | Importeer de geldige certificaat vingerafdruk die op het bron exemplaar wordt gebruikt, naar het doel exemplaar. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| De logboekback-up die is gebruikt voor herstel bevat bulksgewijs geregistreerde wijzigingen. Deze kan niet worden gebruikt om op een willekeurig tijdstip te stoppen volgens de SQL-richtlijnen. | Wanneer een Data Base zich in de herstel modus met meerdere logboeken bevindt, kunnen de gegevens tussen een bulksgewijs geregistreerde trans actie en de volgende logboek transactie niet worden hersteld. | Kies een ander tijdstip voor herstel. [Meer informatie](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105)).

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| Er kan niet worden voldaan aan de voorkeur van Backup voor de SQL AlwaysOn-beschikbaarheidsgroep omdat bepaalde knooppunten van de beschikbaarheidsgroep niet zijn geregistreerd. | Knoop punten die zijn vereist voor het uitvoeren van back-ups zijn niet geregistreerd of zijn onbereikbaar. | <ul><li>Zorg ervoor dat alle knoop punten die nodig zijn voor het uitvoeren van back-ups van deze data base, zijn geregistreerd en in orde zijn, en probeer het opnieuw.</li><li>Wijzig de voor keuren van de back-up voor de groep SQL Server AlwaysOn.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| De SQL Server-VM is afgesloten en niet toegankelijk voor Azure Backup service. | De virtuele machine wordt afgesloten. | Controleer of het SQL Server exemplaar wordt uitgevoerd. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| Azure Backup-Service gebruikt de Azure VM-gast agent voor het uitvoeren van back-ups maar de gast agent is niet beschikbaar op de doel server. | De gast agent is niet ingeschakeld of heeft een slechte status. | [Installeer de VM-gast agent](../virtual-machines/extensions/agent-windows.md) hand matig. |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
| De opzet van de automatische beveiliging is verwijderd of is niet geldig. | Wanneer u automatische beveiliging inschakelt voor een SQL Server-exemplaar, **configureert u back-** uptaken voor alle data bases in dat exemplaar. Als u automatische beveiliging uitschakelt terwijl de taken worden uitgevoerd, worden de taken **in uitvoering** met deze fout code geannuleerd. | Schakel automatische beveiliging opnieuw in om alle resterende data bases te beveiligen. |

### <a name="clouddosabsolutelimitreached"></a>CloudDosAbsoluteLimitReached

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
De bewerking is geblokkeerd omdat u de limiet hebt bereikt van het aantal bewerkingen dat binnen 24 uur is toegestaan. | Wanneer u de Maxi maal toegestane limiet hebt bereikt voor een bewerking binnen een periode van 24 uur, wordt deze fout weer geleverd. <br> Bijvoorbeeld: als u de limiet hebt bereikt van het aantal back-uptaken configureren dat per dag kan worden geactiveerd en u een back-up wilt configureren voor een nieuw item, wordt deze fout weer geven. | Normaal gesp roken wordt de bewerking na 24 uur opnieuw geprobeerd om dit probleem op te lossen. Als het probleem zich blijft voordoen, kunt u contact opnemen met micro soft ondersteuning voor hulp.

### <a name="clouddosabsolutelimitreachedwithretry"></a>CloudDosAbsoluteLimitReachedWithRetry

| Foutbericht | Mogelijke oorzaken | Aanbevolen actie |
|---|---|---|
De bewerking is geblokkeerd omdat de kluis de maximum limiet heeft bereikt voor dergelijke bewerkingen die zijn toegestaan in een periode van 24 uur. | Wanneer u de Maxi maal toegestane limiet hebt bereikt voor een bewerking binnen een periode van 24 uur, wordt deze fout weer geleverd. Deze fout treedt meestal op wanneer er op schaal bewerkingen worden uitgevoerd, zoals het wijzigen van beleid of automatische beveiliging. In tegens telling tot in het geval van CloudDosAbsoluteLimitReached is het niet veel wat u kunt doen om deze status op te lossen, Azure Backup service de bewerkingen intern opnieuw probeert uit te voeren voor alle betreffende items.<br> Als er bijvoorbeeld sprake is van een groot aantal gegevens bronnen dat wordt beveiligd met een beleid en u het beleid probeert te wijzigen, worden de beveiligings taken voor elk van de beveiligde items geactiveerd en kan de maximum limiet voor dergelijke bewerkingen per dag worden bereikt.| Azure Backup service wordt deze bewerking na 24 uur automatisch opnieuw uitgevoerd.

## <a name="re-registration-failures"></a>Fouten bij opnieuw registreren

Controleer op een of meer van de volgende symptomen voordat u de bewerking opnieuw registreren start:

* Alle bewerkingen (zoals back-up, herstel en configuratie back-up) mislukken op de virtuele machine met een van de volgende fout codes: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent** , **WorkloadExtensionDidntDequeueMsg**.
* Het gebied voor de **back-upstatus** van het back-upitem kan **niet worden bereikt**. Regel alle andere oorzaken die kunnen resulteren in dezelfde status:

  * Onvoldoende machtigingen voor het uitvoeren van back-upbewerkingen op de VM  
  * De virtuele machine wordt afgesloten, waardoor er geen back-ups kunnen worden gemaakt
  * Netwerkproblemen  

  ![De status is niet bereikbaar bij het opnieuw registreren van een virtuele machine](./media/backup-azure-sql-database/re-register-vm.png)

* In het geval van een AlwaysOn-beschikbaarheids groep zijn de back-ups gestart na het wijzigen van de voor keur van de back-up of na een failover.

Deze symptomen kunnen om een of meer van de volgende redenen optreden:

* Er is een extensie verwijderd uit de portal.
* Een uitbrei ding van het **configuratie scherm** op de virtuele machine is verwijderd onder het **verwijderen of wijzigen van een programma**.
* De virtuele machine is terug in de tijd teruggezet via een in-place schijf herstel bewerking.
* De virtuele machine is afgesloten gedurende een langere periode, waardoor de configuratie van de uitbrei ding is verlopen.
* De virtuele machine is verwijderd en een andere virtuele machine is gemaakt met dezelfde naam en in dezelfde resource groep als de verwijderde VM.
* Een van de knoop punten van de beschikbaarheids groep heeft de volledige back-upconfiguratie niet ontvangen. Dit kan gebeuren wanneer de beschikbaarheids groep is geregistreerd bij de kluis of wanneer er een nieuw knoop punt wordt toegevoegd.

In de voor gaande scenario's wordt u aangeraden een bewerking voor het opnieuw registreren van de virtuele machine te activeren. Zie [hier](https://docs.microsoft.com/azure/backup/backup-azure-sql-automation#enable-backup) voor instructies over het uitvoeren van deze taak in Power shell.

## <a name="size-limit-for-files"></a>Maximale grootte voor bestanden

De totale grootte van de teken reeks van bestanden is niet alleen afhankelijk van het aantal bestanden, maar ook voor de namen en paden. Haal voor elk database bestand de logische bestands naam en het fysieke pad op. U kunt deze SQL-query gebruiken:

```sql
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

Rang schik ze nu in de volgende notatie:

```json
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

Hier volgt een voorbeeld:

```json
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

Als de teken reeks grootte van de inhoud groter is dan 20.000 bytes, worden de database bestanden anders opgeslagen. Tijdens het herstel kunt u het pad naar het doel bestand voor herstellen niet instellen. De bestanden worden teruggezet naar het standaard SQL-pad dat wordt verschaft door SQL Server.

### <a name="override-the-default-target-restore-file-path"></a>Het pad van het standaard doel bestand voor terugzetten overschrijven

U kunt het pad voor het terugzetten van het doel bestand overschrijven tijdens de herstel bewerking door een JSON-bestand met de toewijzing van het database bestand te plaatsen in het pad naar de doel-herstellen. Maak een `database_name.json`-bestand en plaats het op de locatie *C:\Program Files\Azure workload Backup\bin\plugins\SQL*.

De inhoud van het bestand moet de volgende indeling hebben:

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

In de voor gaande inhoud kunt u de logische naam van het database bestand ophalen met behulp van de volgende SQL-query:

```sql
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```

Dit bestand moet worden geplaatst voordat u de herstel bewerking kunt activeren.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure backup voor SQL-vm's](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup)voor meer informatie over Azure Backup voor SQL Server vm's (open bare preview).
