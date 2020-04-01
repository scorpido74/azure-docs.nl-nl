---
title: Automatische, georedundante back-ups
description: SQL Database maakt automatisch om de paar minuten een lokale databaseback-up en maakt gebruik van georedundante opslag voor azure-leestoegang voor georedundante redundante opslag voor georedundantie.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 12/13/2019
ms.openlocfilehash: 9ac6927df63d51830a58773e32ad0968920c0867
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061763"
---
# <a name="automated-backups"></a>Automatische back-ups

Azure SQL Database maakt automatisch de databaseback-ups die worden bewaard voor de duur van de geconfigureerde bewaarperiode. Het maakt gebruik van Azure [read-access geo-redundante opslag (RA-GRS)](../storage/common/storage-redundancy.md) om ervoor te zorgen dat de back-ups worden bewaard, zelfs als het datacenter niet beschikbaar is.

Databaseback-ups zijn een essentieel onderdeel van elke bedrijfscontinuïteits- en disaster recovery-strategie, omdat ze uw gegevens beschermen tegen onbedoelde beschadiging of verwijdering. Als uw beveiligingsregels vereisen dat uw back-ups voor een langere tijd beschikbaar zijn (tot 10 jaar), u langetermijnretentie configureren op [singleton-databases](sql-database-long-term-retention.md) en elastische databasegroepen.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Wat is een SQL Database back-up?

SQL Database maakt elke week gebruik van SQL Server-technologie om [volledige back-ups](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) te maken, elke 12 uur [differentiële back-ups](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) en om de 5 tot 10 minuten [back-ups van transactielogboeken.](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) De back-ups worden opgeslagen in [RA-GRS-opslagblobs](../storage/common/storage-redundancy.md) die worden gerepliceerd naar een [gekoppeld datacenter](../best-practices-availability-paired-regions.md) voor bescherming tegen een datacenterstoring. Wanneer u een database herstelt, bepaalt de service welke volledige, differentiële en transactielogboekback-ups moeten worden hersteld.

U kunt deze back-ups gebruiken om:

- **Een bestaande database herstellen naar een tijdstip in het verleden** binnen de bewaarperiode met behulp van de Azure-portal, Azure PowerShell, Azure CLI of de REST API. Voor afzonderlijke databases en elastische databasegroepen wordt met deze bewerking een nieuwe database gemaakt op dezelfde server als de oorspronkelijke database. In beheerde instantie kan deze bewerking een kopie van de database of dezelfde of een andere beheerde instantie onder hetzelfde abonnement maken.
- **Een verwijderde database herstellen naar het moment van verwijdering** of op elk moment binnen de bewaartermijn. De verwijderde database kan alleen worden hersteld op dezelfde logische server of beheerde instantie waar de oorspronkelijke database is gemaakt.
- **Een database herstellen naar een andere geografische regio**. Geo-restore stelt u in staat om te herstellen van een geografische ramp wanneer u geen toegang hebt tot uw server en database. Het creëert een nieuwe database op elke bestaande server, waar ook ter wereld.
- **Een database herstellen vanuit een specifieke langetermijnback-up** in één database of elastische databasegroep, als de database is geconfigureerd met een Lange Termijn-bewaarbeleid (LTR). Met LTR u een oude versie van de database herstellen met behulp van [de Azure-portal](sql-database-long-term-backup-retention-configure.md#using-azure-portal) of [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) om te voldoen aan een nalevingsverzoek of om een oude versie van de toepassing uit te voeren. Zie [Langetermijnretentie](sql-database-long-term-retention.md) voor meer informatie.

Zie Database herstellen [van back-ups](sql-database-recovery-using-backups.md)als u een herstel wilt uitvoeren.

> [!NOTE]
> In Azure Storage verwijst de term *replicatie* naar het kopiëren van bestanden van de ene locatie naar de andere. SQL *Server-databasereplicatie* verwijst naar het synchroniseren van meerdere secundaire databases met een primaire database.

U een aantal van deze bewerkingen uitproberen met behulp van de volgende voorbeelden:

| | Azure Portal | Azure PowerShell |
|---|---|---|
| Back-upretentie wijzigen | [Individuele database](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [Beheerd exemplaar](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [Individuele database](sql-database-automated-backups.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[Beheerd exemplaar](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Back-upretentie op lange termijn wijzigen | [Individuele database](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Beheerde instantie - N/A  | [Individuele database](sql-database-long-term-backup-retention-configure.md)<br/>Beheerde instantie - N/A  |
| Een database herstellen vanaf een moment | [Individuele database](sql-database-recovery-using-backups.md#point-in-time-restore) | [Individuele database](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Beheerd exemplaar](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Een verwijderde database herstellen | [Individuele database](sql-database-recovery-using-backups.md) | [Individuele database](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Beheerd exemplaar](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Een database herstellen vanuit Azure Blob-opslag | Enkele database - N/A <br/>Beheerde instantie - N/A  | Enkele database - N/A <br/>[Beheerd exemplaar](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>Back-upfrequentie

### <a name="point-in-time-restore"></a>Terugzetten naar eerder tijdstip

SQL Database ondersteunt selfservice voor point-in-time restore (PITR) door automatisch volledige back-ups, differentiële back-ups en transactielogboekback-ups te maken. Er worden wekelijks volledige databaseback-ups gemaakt en er worden over het algemeen elke 12 uur back-ups van verschillende databaseback-ups gemaakt. Transactielogboekback-ups worden over het algemeen elke 5 tot 10 minuten gemaakt. De frequentie van de back-ups van het transactielogboek is gebaseerd op de rekengrootte en de hoeveelheid databaseactiviteit. 

De eerste volledige back-up wordt direct na het maken van een database gepland. Deze back-up is meestal binnen 30 minuten voltooid, maar het kan langer duren wanneer de database groot is. De initiële back-up kan bijvoorbeeld langer duren op een herstelde database of een databasekopie. Na de eerste volledige back-up worden alle verdere back-ups automatisch op de achtergrond gepland en beheerd. De exacte timing van alle databaseback-ups wordt bepaald door de SQL Database-service, omdat deze de algehele werkbelasting van het systeem evenredig verdeelt. U kunt de back-uptaken niet wijzigen of uitschakelen.

PITR-back-ups zijn beveiligd met georedundante opslag. Zie [Redundantie in Azure Storage](../storage/common/storage-redundancy.md) voor meer informatie.

Zie [Point-in-time restore voor](sql-database-recovery-using-backups.md#point-in-time-restore)meer informatie over PITR.

### <a name="long-term-retention"></a>Lange bewaartermijn

Voor afzonderlijke en gepoolde databases u LTR (lange termijn retentie) van volledige back-ups tot 10 jaar configureren in Azure Blob-opslag. Als u ltr-beleid inschakelt, worden de wekelijkse volledige back-ups automatisch gekopieerd naar een andere RA-GRS-opslagcontainer. Om aan verschillende nalevingsvereisten te voldoen, u verschillende bewaartermijnen selecteren voor wekelijkse, maandelijkse en/of jaarlijkse back-ups. Het opslagverbruik is afhankelijk van de geselecteerde frequentie van back-ups en de bewaarperiode of -perioden. U de [LTR-prijscalculator](https://azure.microsoft.com/pricing/calculator/?service=sql-database) gebruiken om de kosten van LTR-opslag te schatten.

Net als PITR-back-ups worden LTR-back-ups beveiligd met georedundante opslag. Zie [Redundantie in Azure Storage](../storage/common/storage-redundancy.md) voor meer informatie.

Zie Voor meer informatie over LTR voor het [behoud van back-ups op lange termijn](sql-database-long-term-retention.md).

## <a name="backup-storage-consumption"></a>Back-upopslagverbruik

Voor afzonderlijke databases wordt deze vergelijking gebruikt om het totale gebruik van back-upopslag te berekenen:

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

Voor elastische databasegroepen wordt de totale grootte van de back-upopslag samengevoegd op poolniveau en wordt deze als volgt berekend:

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`

Back-ups die vóór de bewaarperiode plaatsvinden, worden automatisch verwijderd op basis van hun tijdstempel. Omdat differentiële back-ups en logboekback-ups een eerdere volledige back-up nodig hebben om nuttig te zijn, worden ze samen in wekelijkse brokken verwijderd.

Azure SQL Database berekent uw totale back-upopslag in bewaarbehoud als een cumulatieve waarde. Elk uur wordt deze waarde gerapporteerd aan de Azure-factureringspijplijn, die verantwoordelijk is voor het aggregeren van dit uurgebruik om uw verbruik aan het einde van elke maand te berekenen. Nadat de database is verwijderd, neemt het verbruik af naarmate back-ups ouder worden. Nadat back-ups ouder zijn geworden dan de bewaarperiode, stopt de facturering.

   > [!IMPORTANT]
   > Back-ups van een database worden bewaard voor de opgegeven bewaarperiode, zelfs als de database is verwijderd. Hoewel het laten vallen en opnieuw maken van een database vaak kan besparen op opslag- en rekenkosten, kan dit de kosten voor back-upopslag verhogen omdat Microsoft een back-up behoudt voor de opgegeven bewaarperiode (die minimaal 7 dagen is) voor elke verloren database, elke keer dat deze wordt verwijderd.

### <a name="monitor-consumption"></a>Verbruik controleren

Elk type back-up (volledig, differentieel en logboek) wordt gerapporteerd op het databasecontroleblad als een afzonderlijke statistiek. In het volgende diagram ziet u hoe u het verbruik van back-upopslag voor één database controleren. Deze functie is momenteel niet beschikbaar voor beheerde exemplaren.

![Het verbruik van databaseback-ups controleren in de Azure-portal](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Back-upopslagverbruik verfijnen

Overtollig e-upopslagverbruik is afhankelijk van de werkbelasting en de grootte van de afzonderlijke databases. Overweeg enkele van de volgende tuningtechnieken om het verbruik van back-upopslag te verminderen:

* Verlaag de [bewaartermijn voor back-ups](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) tot het minimum dat mogelijk is voor uw behoeften.
* Vermijd het doen van grote schrijfbewerkingen, zoals index reconstructies, vaker dan je nodig hebt.
* Voor grote gegevensbelastingsbewerkingen u [geclusterde kolomarchiefindexen](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)gebruiken, het aantal niet-geclusterde indexen verminderen en bulkbelastingbewerkingen overwegen met een aantal rijen rond 1 miljoen.
* In de servicelaag voor algemene doeleinden is de ingerichte gegevensopslag minder duur dan de prijs van de overtollige back-upopslag. Als u voortdurend hoge kosten voor extra back-upopslag hebt, u overwegen de gegevensopslag te verhogen om te besparen op de back-upopslag.
* Gebruik TempDB in plaats van permanente tabellen in uw ETL-logica voor het opslaan van tijdelijke resultaten. (Alleen van toepassing op beheerde instantie.)
* Overweeg om TDE-versleuteling uit te schakelen voor databases die geen gevoelige gegevens bevatten (bijvoorbeeld databases ontwikkelen of testen). Back-ups voor niet-versleutelde databases worden meestal gecomprimeerd met een hogere compressieverhouding.

> [!IMPORTANT]
> Voor analytische datamart \ data warehouse workloads raden we u ten zeerste aan [geclusterde kolomarchiefindexen](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)te gebruiken, het aantal niet-geclusterde indexen te verminderen en bulkbelastingbewerkingen met een aantal rijen rond 1 miljoen te overwegen om het overtollige opslagverbruik te verminderen.

## <a name="storage-costs"></a>Opslagkosten

De prijs voor opslag is afhankelijk van of u het DTU-model of het vCore-model gebruikt.

### <a name="dtu-model"></a>DTU-model

Er zijn geen extra kosten verbonden aan back-upopslag voor databases en elastische databasegroepen als u het DTU-model gebruikt.

### <a name="vcore-model"></a>vCore-model

Voor afzonderlijke databases wordt een minimum back-upopslagbedrag dat gelijk is aan 100 procent van de databasegrootte zonder extra kosten verstrekt. Voor elastische databasegroepen en beheerde exemplaren wordt een minimumback-upopslagbedrag verstrekt dat gelijk is aan 100 procent van de toegewezen gegevensopslag voor respectievelijk de groep of instantiegrootte, zonder extra kosten. Voor aanvullend verbruik van back-upopslag worden GB/maand berekend. Dit extra verbruik is afhankelijk van de werkbelasting en grootte van de afzonderlijke databases.

Azure SQL Database berekent uw totale back-upopslag in bewaarbehoud als een cumulatieve waarde. Elk uur wordt deze waarde gerapporteerd aan de Azure-factureringspijplijn, die verantwoordelijk is voor het aggregeren van dit uurgebruik om uw verbruik aan het einde van elke maand te krijgen. Nadat de database is verwijderd, vermindert Microsoft het verbruik naarmate de back-ups ouder worden. Nadat back-ups ouder zijn geworden dan de bewaarperiode, stopt de facturering. Omdat alle logboekback-ups en differentiële back-ups voor de volledige bewaarperiode worden bewaard, hebben sterk gewijzigde databases hogere back-upkosten.

Stel dat een database 744 GB back-upopslag heeft verzameld en dat dit bedrag gedurende een hele maand constant blijft. Als u dit cumulatieve opslagverbruik wilt omzetten in uurgebruik, deelt u dit door 744,0 (31 dagen per maand * 24 uur per dag). Sql Database meldt dus dat de database elk uur 1 GB PITR-back-up verbruikt. Azure-facturering verzamelt dit verbruik en geeft een gebruik van 744 GB voor de hele maand weer. De kosten worden gebaseerd op het tarief $/GB/maand in uw regio.

Nu, een complexer voorbeeld. Stel dat de database heeft de retentie verhoogd tot 14 dagen in het midden van de maand. Stel dat deze toename (hypothetisch) resulteert in een verdubbeling van de totale back-upopslag tot 1.488 GB. SQL Database zou 1 GB aan gebruik rapporteren voor uren 1 tot en met 372. Het zou het gebruik als 2 GB voor uren 373 tot en met 744 melden. Dit gebruik zou worden samengevoegd tot een definitieve factuur van 1.116 GB/maand.

### <a name="monitor-costs"></a>Kosten controleren

Als u inzicht wilt krijgen in de kosten van back-upopslag, gaat u naar **Kostenbeheer + Facturering** in de Azure-portal, selecteert u **Kostenbeheer**en selecteert u **Kostenanalyse**. Selecteer het gewenste abonnement als **scope**en filter vervolgens voor de periode en service waarin u geïnteresseerd bent.

Voeg een filter toe voor **servicenaam**en selecteer vervolgens **sql-database** in de vervolgkeuzelijst. Gebruik het filter van de **metersubcategorie** om de factureringsteller voor uw service te kiezen. Selecteer voor één database of een elastische databasegroep **een enkele/elastische afdrukopslag voor putrversterking.** Selecteer voor een beheerde instantie **de back-upopslag van mi pitr**. De subcategorieën **Opslag** en **gegevensberekenen** kunnen u ook interesseren, maar ze zijn niet gekoppeld aan kosten voor back-upopslag.

![Analyse van back-ups van opslagkosten](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

## <a name="backup-retention"></a>Retentie van back-ups

Alle Azure SQL-databases (afzonderlijke, samengevoegde en beheerde instantiedatabases) hebben een standaardbewaarperiode van 7 dagen. U [de bewaartermijn voor back-ups wijzigen](#change-the-pitr-backup-retention-period) in maximaal 35 dagen.

Als u een database verwijdert, houdt SQL Database de back-ups op dezelfde manier als voor een online database. Als u bijvoorbeeld een Basisdatabase verwijdert met een bewaarperiode van zeven dagen, wordt een back-up van vier dagen nog drie dagen opgeslagen.

Als u de back-ups langer dan de maximale bewaartermijn moet bewaren, u de back-upeigenschappen wijzigen om een of meer bewaartermijnen op lange termijn aan uw database toe te voegen. Zie [Langetermijnretentie](sql-database-long-term-retention.md) voor meer informatie.

> [!IMPORTANT]
> Als u de Azure SQL-server verwijdert die SQL-databases host, worden ook alle elastische databasegroepen en databases die tot de server behoren, verwijderd. Ze kunnen niet worden teruggevonden. U een verwijderde server niet herstellen. Maar als u langdurige retentie hebt geconfigureerd, worden de back-ups voor de databases met LTR niet verwijderd en kunnen deze databases worden hersteld.

## <a name="encrypted-backups"></a>Versleutelde back-ups

Als uw database is versleuteld met TDE, worden back-ups automatisch in rust versleuteld, inclusief LTR-back-ups. Wanneer TDE is ingeschakeld voor een Azure SQL-database, worden back-ups ook versleuteld. Alle nieuwe Azure SQL-databases zijn geconfigureerd met Standaard TDE ingeschakeld. Zie [Transparante gegevensversleuteling met Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)voor meer informatie over TDE.

## <a name="backup-integrity"></a>Back-upintegriteit

Het Azure SQL Database-engineeringteam test voortdurend het herstel van geautomatiseerde databaseback-ups van databases die in logische servers en elastische databasepools zijn geplaatst. (Deze test is niet beschikbaar in beheerde instantie.) Bij point-in-time restore ontvangen databases ook DBCC CHECKDB integriteitscontroles.

Beheerde instantie maakt `CHECKSUM` automatische eerste back-up met van databases die zijn hersteld met de native `RESTORE` opdracht of met Azure Data Migration Service nadat de migratie is voltooid.

Eventuele problemen die tijdens de integriteitscontrole worden gevonden, resulteren in een waarschuwing aan het engineeringteam. Zie [Gegevensintegriteit in Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)voor meer informatie.

## <a name="compliance"></a>Naleving

Wanneer u uw database migreert van een op DTU gebaseerde servicelaag naar een vCore-gebaseerde servicelaag, blijft de PITR-retentie behouden om ervoor te zorgen dat het gegevensherstelbeleid van uw toepassing niet in het gedrang komt. Als de standaardretentie niet voldoet aan uw nalevingsvereisten, u de PITR-bewaartermijn wijzigen met PowerShell of de REST API. Zie [De BEWAARperiode voor BACK-up sto.v.](#change-the-pitr-backup-retention-period)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>De bewaartermijn voor PITR-back-ups wijzigen

U de standaardbewaarperiode voor PITR-back-ups wijzigen met behulp van de Azure-portal, PowerShell of de REST API. De volgende voorbeelden illustreren hoe u de PITR-retentie wijzigen in 28 dagen.

> [!WARNING]
> Als u de huidige bewaartermijn verkort, zijn alle bestaande back-ups die ouder zijn dan de nieuwe bewaarperiode niet meer beschikbaar. Als u de huidige bewaartermijn verhoogt, behoudt SQL Database de bestaande back-ups tot het einde van de langere bewaarperiode is bereikt.

> [!NOTE]
> Deze API's hebben alleen invloed op de PITR-bewaartermijn. Als u LTR voor uw database hebt geconfigureerd, wordt dit niet beïnvloed. Zie [Langetermijnretentie](sql-database-long-term-retention.md)voor informatie over het wijzigen van LTR-bewaartermijnen.

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>De BEWAARtermijn voor PITR-back-ups wijzigen met behulp van de Azure-portal

Als u de BEPITR-back-upbewaarperiode wilt wijzigen met behulp van de Azure-portal, gaat u naar het serverobject waarvan u de bewaartermijn wilt wijzigen in de portal. Selecteer vervolgens de juiste optie op basis van het serverobject dat u wijzigt.

#### <a name="single-database-and-elastic-database-pools"></a>[Enkele database en elastische databasepools](#tab/single-database)

Wijzigingen in PITR-back-upretentie voor afzonderlijke Azure SQL-databases worden uitgevoerd op serverniveau. Wijzigingen die op serverniveau zijn aangebracht, zijn van toepassing op databases op de server. Als u pitr-retentie voor een Azure SQL Database-server wilt wijzigen vanuit de Azure-portal, gaat u naar het beheer van het serveroverzicht. Selecteer **Back-ups beheren** in het linkerdeelvenster en selecteer **Bewaar configureren** boven aan het scherm:

![PITR-retentie wijzigen, serverniveau](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[Beheerd exemplaar](#tab/managed-instance)

Wijzigingen in PITR-back-upretentie voor SQL Database-beheerde exemplaren worden uitgevoerd op individueel databaseniveau. Als u pitr-back-upretentie voor een instantiedatabase vanuit de Azure-portal wilt wijzigen, gaat u naar het afzonderlijke databaseoverzichtsblad. Selecteer vervolgens **Back-upretentie configureren** boven aan het scherm:

![PITR-retentie wijzigen, beheerde instantie](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>De BEWAARtermijn voor PITR-back-ups wijzigen met PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De PowerShell AzureRM-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. Zie [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor meer informatie. De argumenten voor de opdrachten in de Az-module zijn vrijwel identiek aan die in de AzureRm-modules.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>De BEWAARtermijn voor PITR-back-ups wijzigen met behulp van de REST API

#### <a name="sample-request"></a>Voorbeeldaanvraag

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Aanvraagbody

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Voorbeeldantwoord

Statuscode: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

Zie [API voor back-upretentie rest voor](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies)meer informatie .

## <a name="next-steps"></a>Volgende stappen

- Databaseback-ups zijn een essentieel onderdeel van elke bedrijfscontinuïteits- en disaster recovery-strategie, omdat ze uw gegevens beschermen tegen onbedoelde beschadiging of verwijdering. Zie Overzicht van bedrijfscontinuïteit [voor](sql-database-business-continuity.md)meer informatie over de andere bedrijfscontinuïteitsoplossingen van Azure SQL SQL Database.
- Meer informatie over het [herstellen van een database naar een tijdstip met behulp van de Azure-portal.](sql-database-recovery-using-backups.md)
- Meer informatie over het [herstellen van een database naar een tijdstip met PowerShell.](scripts/sql-database-restore-database-powershell.md)
- Zie Het bewaren van [back-ups](sql-database-long-term-backup-retention-configure.md)op lange termijn beheren, beheren en herstellen van het behoud van geautomatiseerde back-ups op lange termijn in Azure Blob-opslag met behulp van de Azure-portal.
- Zie Het bewaren van [back-ups](sql-database-long-term-backup-retention-configure.md)op lange termijn beheren, beheren en herstellen van het behoud van geautomatiseerde back-ups op lange termijn in Azure Blob-opslag met PowerShell.
