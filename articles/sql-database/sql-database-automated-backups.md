---
title: Automatische, geografisch redundante back-ups
description: SQL Database maakt om de paar minuten automatisch een lokale back-up van de data base en maakt gebruik van geografisch redundante opslag met lees toegang voor geo-redundantie.
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
ms.openlocfilehash: 7cbe0015eeb9b46cd72496a220ce7f7d094cb61d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198559"
---
# <a name="automated-backups"></a>Automatische back-ups

Azure SQL Database maakt automatisch de database back-ups die gedurende de geconfigureerde Bewaar periode bewaard blijven. Er wordt gebruikgemaakt van [geografisch redundante opslag met lees toegang (RA-GRS)](../storage/common/storage-redundancy.md) om ervoor te zorgen dat de back-ups worden bewaard, zelfs als het Data Center niet beschikbaar is.

Database back-ups zijn een essentieel onderdeel van een strategie voor bedrijfs continuïteit en herstel na nood gevallen, omdat uw gegevens worden beschermd tegen onbedoelde beschadiging of verwijdering. Als uw-beveiligings regels vereisen dat uw back-ups langere tijd beschikbaar zijn (tot tien jaar), kunt u [lange termijn retentie](sql-database-long-term-retention.md) configureren voor Singleton-data bases en Pools voor elastische data bases.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Wat is een SQL Database back-up?

SQL Database gebruikt SQL Server technologie om elke week [volledige back-ups](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) te maken, [differentiële back](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) -ups elke 12 uur en [back-ups van transactie logboeken](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) om de vijf tot tien minuten. De back-ups worden opgeslagen in [Ra-GRS-opslag-blobs](../storage/common/storage-redundancy.md) die worden gerepliceerd naar een [gekoppeld Data Center](../best-practices-availability-paired-regions.md) voor beveiliging tegen een storing in het Data Center. Wanneer u een Data Base herstelt, bepaalt de service welke volledige, differentiële en transactie logboek back-ups moeten worden hersteld.

U kunt deze back-ups gebruiken om:

- **Een bestaande data base herstellen naar een bepaald tijdstip in het verleden** binnen de retentie periode met behulp van de Azure Portal, Azure PowerShell, Azure CLI of de rest API. Voor afzonderlijke data bases en Pools voor elastische data bases maakt deze bewerking een nieuwe Data Base op dezelfde server als de oorspronkelijke data base. In het beheerde exemplaar kan deze bewerking een kopie maken van de data base of dezelfde of een ander beheerd exemplaar onder hetzelfde abonnement.
- **Een verwijderde data base herstellen naar het tijdstip van de verwijdering** of op elk moment binnen de Bewaar periode. De verwijderde data base kan alleen worden hersteld op dezelfde logische server of hetzelfde beheerde exemplaar waarin de oorspronkelijke Data Base is gemaakt.
- **Een Data Base herstellen naar een andere geografische regio**. Met geo-Restore kunt u een geografische nood situatie herstellen wanneer u geen toegang hebt tot uw server en data base. Er wordt een nieuwe Data Base op een bestaande server gemaakt, waar dan ook ter wereld.
- **Een Data Base herstellen van een specifieke lange termijn back-up** op een enkele data base of pool voor Elastic data base, als de data base is geconfigureerd met een lange termijn Bewaar beleid (LTR). Met LTR kunt u een oude versie van de data base herstellen met behulp van [de Azure Portal](sql-database-long-term-backup-retention-configure.md#using-azure-portal) of [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) om te voldoen aan een nalevings aanvraag of een oude versie van de toepassing uit te voeren. Zie [Langetermijnretentie](sql-database-long-term-retention.md) voor meer informatie.

Zie [Data Base terugzetten van back-ups](sql-database-recovery-using-backups.md)om een herstel bewerking uit te voeren.

> [!NOTE]
> In Azure Storage verwijst de term *replicatie* naar het kopiëren van bestanden van de ene locatie naar een andere. SQL Server *database replicatie* verwijst naar het behoud van meerdere secundaire data bases gesynchroniseerd met een primaire data base.

U kunt enkele van deze bewerkingen uitproberen met de volgende voor beelden:

| | Azure Portal | Azure PowerShell |
|---|---|---|
| Retentie van back-ups wijzigen | [Eén data base](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [Beheerd exemplaar](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [Eén data base](sql-database-automated-backups.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[Beheerd exemplaar](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Lange termijn retentie van back-ups wijzigen | [Eén data base](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Beheerd exemplaar-N.v.t.  | [Eén data base](sql-database-long-term-backup-retention-configure.md)<br/>Beheerd exemplaar-N.v.t.  |
| Een Data Base vanaf een bepaald moment herstellen | [Eén data base](sql-database-recovery-using-backups.md#point-in-time-restore) | [Eén data base](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Beheerd exemplaar](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Een verwijderde database herstellen | [Eén data base](sql-database-recovery-using-backups.md) | [Eén data base](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Beheerd exemplaar](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Een Data Base herstellen vanuit Azure Blob-opslag | Eén data base-N.v.t. <br/>Beheerd exemplaar-N.v.t.  | Eén data base-N.v.t. <br/>[Beheerd exemplaar](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>Back-upfrequentie

### <a name="point-in-time-restore"></a>Terugzetten naar eerder tijdstip

SQL Database ondersteunt self-service voor PITR (Point-in-time Restore) door automatisch volledige back-ups, differentiële back-ups en back-ups van transactie logboeken te maken. Volledige database back-ups worden wekelijks gemaakt en differentiële back-ups van data bases worden over het algemeen elke 12 uur gemaakt. Back-ups van transactie logboeken worden over het algemeen elke vijf tot tien minuten gemaakt. De frequentie van back-ups van transactie Logboeken is gebaseerd op de berekenings grootte en de hoeveelheid database activiteit. 

De eerste volledige back-up wordt onmiddellijk gepland nadat er een Data Base is gemaakt. Deze back-up wordt gewoonlijk binnen 30 minuten voltooid, maar kan langer duren als de data base groot is. De eerste back-up kan bijvoorbeeld langer duren op een herstelde data base of een kopie van een Data Base. Na de eerste volledige back-up worden alle verdere back-ups automatisch op de achtergrond gepland en beheerd. De exacte timing van alle databaseback-ups wordt bepaald door de SQL Database-service, omdat deze de algehele werkbelasting van het systeem evenredig verdeelt. U kunt de back-uptaken niet wijzigen of uitschakelen.

### <a name="default-backup-retention-period"></a>Standaard retentie periode voor back-ups

PITR-back-ups worden beveiligd met geografisch redundante opslag. Zie [Redundantie in Azure Storage](../storage/common/storage-redundancy.md) voor meer informatie.

Zie herstel naar een bepaald [tijdstip](sql-database-recovery-using-backups.md#point-in-time-restore)voor meer informatie over PITR.

### <a name="long-term-retention"></a>Lange bewaartermijn

Voor enkelvoudige en gepoolde data bases kunt u de lange termijn retentie (LTR) van volledige back-ups configureren voor Maxi maal tien jaar in Azure Blob-opslag. Als u het LTR-beleid inschakelt, worden de wekelijkse volledige back-ups automatisch gekopieerd naar een andere RA-GRS-opslag container. Om aan verschillende nalevings vereisten te voldoen, kunt u verschillende Bewaar perioden selecteren voor wekelijkse, maandelijkse en/of jaarlijkse back-ups. Het opslag verbruik is afhankelijk van de geselecteerde frequentie van back-ups en de retentie periode of peri Oden. U kunt de [LTR-prijs calculator](https://azure.microsoft.com/pricing/calculator/?service=sql-database) gebruiken om de kosten van v.l.n.r.-opslag te schatten.

Net als bij PITR-back-ups worden LTR-back-ups beschermd met geografisch redundante opslag. Zie [Redundantie in Azure Storage](../storage/common/storage-redundancy.md) voor meer informatie.

Voor meer informatie over LTR, Zie [lange termijn retentie van back-ups](sql-database-long-term-retention.md).

## <a name="backup-storage-consumption"></a>Opslag gebruik back-ups

Voor afzonderlijke data bases wordt deze vergelijking gebruikt voor het berekenen van het totale opslag gebruik van back-ups:

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

Voor Pools voor elastische data bases wordt de totale opslag grootte van de back-up geaggregeerd op het groeps niveau en wordt als volgt berekend:

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`

Back-ups die plaatsvinden vóór de Bewaar periode, worden automatisch opgeschoond op basis van hun tijds tempel. Omdat voor differentiële back-ups en logboek back-ups een eerdere volledige back-up is vereist, worden ze samen in wekelijkse segmenten opgeschoond.

Azure SQL Database berekent uw totale in-retentie back-upopslag als een cumulatieve waarde. Elk uur wordt deze waarde gerapporteerd aan de Azure-facturerings pijplijn, die verantwoordelijk is voor het samen voegen van dit uur gebruik om uw verbruik aan het einde van elke maand te berekenen. Nadat de data base is verwijderd, neemt het verbruik af als ouderdom van back-ups. Nadat back-ups ouder zijn geworden dan de retentie periode, wordt de facturering stopgezet.

   > [!IMPORTANT]
   > Back-ups van een Data Base worden bewaard voor de opgegeven Bewaar periode, zelfs als de data base is verwijderd. Tijdens het verwijderen en opnieuw maken van een Data Base kunnen regel matig opslag en reken kosten worden bespaard, waardoor de opslag kosten voor back-ups toenemen, omdat micro soft voor elke verwijderde data base een back-up van de opgegeven Bewaar periode (mini maal 7 dagen per seconde) voor elke gedropte database behoudt.

### <a name="monitor-consumption"></a>Verbruik bewaken

Elk type back-up (volledig, differentieel en logboek) wordt op de Blade database bewaking gerapporteerd als een afzonderlijke metriek. In het volgende diagram ziet u hoe u het verbruik van back-upopslag voor één data base bewaakt. Deze functie is momenteel niet beschikbaar voor beheerde exemplaren.

![Gebruik van database back-ups in de Azure Portal bewaken](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Het gebruik van back-upopslag nauw keuriger instellen

Het verbruik van het back-upopslag is afhankelijk van de werk belasting en de grootte van de afzonderlijke data bases. Bekijk een aantal van de volgende afstemmings technieken om het verbruik van back-upopslag te verminderen:

* Verminder de [Bewaar periode voor back-ups](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) naar de minimale vereisten voor uw behoeften.
* Vermijd grote schrijf bewerkingen, zoals het opnieuw opbouwen van indexen, vaker dan u nodig hebt.
* Overweeg het gebruik van [geclusterde column Store-indexen](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), het aantal niet-geclusterde indexen te verlagen en te overwegen om bewerkingen met bulksgewijs laden te maken met het aantal rijen rond 1.000.000.
* In de servicelaag voor algemeen gebruik is de ingerichte gegevens opslag minder duur dan de prijs van de overtollige back-upopslag. Als u continu hoge back-upopslagkosten hebt, kunt u overwegen om de gegevens opslag te verg Roten om op te slaan in de back-upopslag.
* Gebruik TempDB in plaats van permanente tabellen in uw ETL-logica voor het opslaan van tijdelijke resultaten. (Alleen van toepassing op een beheerd exemplaar.)
* Overweeg TDE versleuteling uit te scha kelen voor data bases die geen gevoelige gegevens bevatten (bijvoorbeeld data bases voor ontwikkeling of testen). Back-ups voor niet-versleutelde data bases worden doorgaans gecomprimeerd met een hogere compressie ratio.

> [!IMPORTANT]
> Voor analytische data-datamart \ Data Warehouse-workloads raden wij u ten zeerste aan om [geclusterde column Store-indexen](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)te gebruiken, het aantal niet-geclusterde indexen te verminderen en te overwegen om bulksgewijs laad bewerkingen met een aantal rijen rond 1.000.000 om het verbruik van de back-upopslag te verminderen.

## <a name="storage-costs"></a>Opslagkosten

De prijs voor opslag is afhankelijk van het feit of u het DTU-model of het vCore-model gebruikt.

### <a name="dtu-model"></a>DTU-model

Er worden geen extra kosten in rekening gebracht voor back-upopslag voor data bases en Pools voor elastische Data Base als u het DTU-model gebruikt.

### <a name="vcore-model"></a>vCore-model

Voor afzonderlijke data bases geldt een minimum waarde voor back-upopslag die gelijk is aan 100 procent van de grootte van de data base, zonder extra kosten. Voor Pools voor elastische data bases en beheerde instanties is een minimum hoeveelheid voor back-upopslag gelijk aan 100 procent van de toegewezen gegevens opslag voor de grootte van de groep of het exemplaar, zonder extra kosten. Voor aanvullend verbruik van back-upopslag worden GB/maand berekend. Dit extra verbruik is afhankelijk van de werk belasting en de grootte van de afzonderlijke data bases.

Azure SQL Database berekent uw totale Bewaar back-upopslag als een cumulatieve waarde. Elk uur wordt deze waarde gerapporteerd aan de Azure-facturerings pijplijn, die verantwoordelijk is voor het samen voegen van dit uur gebruik om uw verbruik aan het einde van elke maand te verkrijgen. Nadat de data base is verwijderd, verkleint micro soft het verbruik als de leeftijd van de back-ups. Nadat back-ups ouder zijn geworden dan de retentie periode, wordt de facturering stopgezet. Omdat alle logboek back-ups en differentiële back-ups worden bewaard voor de volledige Bewaar periode, worden er voor veel gewijzigde data bases hogere back-upkosten in rekening gebracht.

Ga ervan uit dat een Data Base 744 GB aan back-upopslag heeft verzameld en dat deze hoeveelheid constant blijft gedurende een hele maand. Als u dit cumulatieve opslag gebruik wilt converteren naar het uurverbruik, deelt u dit op 744,0 (31 dagen per maand * 24 uur per dag). Zo rapporteert SQL Database dat de data base elk uur 1 GB PITR back-up heeft verbruikt. In azure billing wordt dit verbruik geaggregeerd en wordt een gebruik van 744 GB voor de hele maand weer gegeven. De kosten worden berekend op basis van het $/GB/month-tempo in uw regio.

Nu is een complexere voor beeld. Stel dat de data base in het midden van de maand de Bewaar periode heeft verhoogd tot 14 dagen. Stel dat deze toename (hypothetisch) resulteert in de totale back-upopslag, verdubbeld tot 1.488 GB. SQL Database zou 1 GB aan gebruik melden voor uren van 1 tot en met 372. Hiermee wordt het gebruik gerapporteerd als 2 GB voor uren 373 tot 744. Dit gebruik wordt geaggregeerd naar een voltooide factuur van 1.116 GB/maand.

### <a name="monitor-costs"></a>Kosten bewaken

Als u meer wilt weten over de kosten voor back-upopslag, gaat u naar **Cost Management + facturering** in de Azure Portal, selecteert u **Cost Management**en selecteert u vervolgens **kosten analyse**. Selecteer het gewenste abonnement als **bereik**en filter vervolgens op de periode en service waarop u bent geïnteresseerd.

Voeg een filter toe voor **service naam**en selecteer vervolgens **SQL data base** in de vervolg keuzelijst. Gebruik het filter **meter subcategorie** om de facturerings teller voor uw service te kiezen. Selecteer voor één data base of een pool voor elastische Data Base **één/elastische pool pitr back-upopslag**. Voor een beheerd exemplaar selecteert u **mi pitr Backup Storage**. De subcategorieën voor **opslag** en **berekening** kunnen u ook interesseren, maar ze zijn niet gekoppeld aan back-upopslagkosten.

![Kosten analyse back-upopslag](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

## <a name="backup-retention"></a>Retentie van back-ups

Alle Azure SQL-data bases (enkelvoudige, gegroepeerde en beheerde exemplaar databases) hebben een standaard retentie periode van 7 dagen. U kunt [de Bewaar periode voor back-ups wijzigen naar een](#change-the-pitr-backup-retention-period) waarde van 35 dagen.

Als u een Data Base verwijdert SQL Database, worden de back-ups op dezelfde manier bewaard als voor een online-data base. Als u bijvoorbeeld een eenvoudige data base verwijdert met een Bewaar periode van zeven dagen, wordt een back-up die vier dagen oud is, drie dagen lang opgeslagen.

Als u de back-ups langer wilt bewaren dan de maximale Bewaar periode, kunt u de back-upeigenschappen wijzigen om een of meer lange termijn Bewaar perioden toe te voegen aan uw data base. Zie [Langetermijnretentie](sql-database-long-term-retention.md) voor meer informatie.

> [!IMPORTANT]
> Als u de Azure SQL-Server verwijdert die als host fungeert voor SQL-data bases, worden alle Pools voor elastische data bases en data bases die deel uitmaken van de server ook verwijderd. Ze kunnen niet worden hersteld. U kunt een verwijderde server niet herstellen. Maar als u lange termijn retentie hebt geconfigureerd, worden de back-ups voor de data bases met LTR niet verwijderd en kunnen deze data bases worden hersteld.

## <a name="encrypted-backups"></a>Versleutelde back-ups

Als uw data base is versleuteld met TDE, worden back-ups automatisch versleuteld op rest, waaronder LTR-back-ups. Wanneer TDE is ingeschakeld voor een Azure-SQL database, worden back-ups ook versleuteld. Alle nieuwe Azure SQL-data bases worden geconfigureerd met TDE standaard ingeschakeld. Zie [transparent Data Encryption met Azure SQL database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)voor meer informatie over TDe.

## <a name="backup-integrity"></a>Back-upintegriteit

Het Azure SQL Database engineering team test voortdurend het herstellen van automatische database back-ups van data bases die zijn geplaatst in logische servers en Pools voor elastische data bases. (Deze test is niet beschikbaar in een beheerd exemplaar.) Bij herstel naar een bepaald tijdstip ontvangen data bases ook DBCC CHECKDB-integriteits controles.

Een beheerd exemplaar maakt automatische initiële back-ups `CHECKSUM` van data bases die zijn hersteld met de systeem eigen `RESTORE` opdracht of met Azure Data Migration service nadat de migratie is voltooid.

Problemen die tijdens de integriteits controle worden gevonden, zullen leiden tot een waarschuwing voor het technische team. Zie [gegevens integriteit in Azure SQL database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)voor meer informatie.

## <a name="compliance"></a>Naleving

Wanneer u uw data base migreert van een service tier op basis van DTU naar een vCore service tier, blijft de retentie van de PITR bewaard om ervoor te zorgen dat het gegevens herstel beleid van uw toepassing niet wordt aangetast. Als de standaard retentie niet voldoet aan uw nalevings vereisten, kunt u de Bewaar periode voor PITR wijzigen met behulp van Power shell of de REST API. Zie [de retentie periode voor PITR-back-ups wijzigen](#change-the-pitr-backup-retention-period)voor meer informatie.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>De retentie periode voor PITR-back-ups wijzigen

U kunt de standaard retentie periode voor PITR-back-ups wijzigen met behulp van de Azure Portal, Power shell of de REST API. In de volgende voor beelden ziet u hoe u de retentie van PITR wijzigt in 28 dagen.

> [!WARNING]
> Als u de huidige Bewaar periode verkort, zijn alle bestaande back-ups die ouder zijn dan de nieuwe Bewaar periode niet meer beschikbaar. Als u de huidige retentie periode verhoogt, worden de bestaande back-ups door SQL Database bewaard totdat het einde van de langere Bewaar periode wordt bereikt.

> [!NOTE]
> Deze Api's zijn alleen van invloed op de PITR-retentie periode. Als u LTR hebt geconfigureerd voor uw data base, heeft dit geen invloed op dit probleem. Zie [lange termijn retentie](sql-database-long-term-retention.md)voor informatie over het wijzigen van v.l.n.r.-Bewaar perioden.

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>De retentie periode voor PITR-back-ups wijzigen met behulp van de Azure Portal

Als u de retentie periode voor PITR-back-ups wilt wijzigen met behulp van de Azure Portal, gaat u naar het Server object waarvan u de Bewaar periode wilt wijzigen in de portal. Selecteer vervolgens de gewenste optie op basis van het Server object dat u wilt wijzigen.

#### <a name="single-database-and-elastic-database-pools"></a>[Afzonderlijke data bases en Pools voor elastische data bases](#tab/single-database)

Wijzigingen in de PITR voor het bewaren van back-ups voor één Azure SQL-Data Base worden uitgevoerd op server niveau. Wijzigingen die op server niveau zijn aangebracht, zijn van toepassing op de data bases op de server. Als u de retentie van PITR voor een Azure SQL Database-Server wilt wijzigen vanuit de Azure Portal, gaat u naar de Blade Server overzicht. Selecteer **back-ups beheren** in het linkerdeel venster en selecteer vervolgens **retentie configureren** aan de bovenkant van het scherm:

![PITR-retentie, server niveau wijzigen](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[Beheerd exemplaar](#tab/managed-instance)

Wijzigingen in de PITR voor het bewaren van back-ups voor SQL Database beheerde instanties worden uitgevoerd op het niveau van een afzonderlijke data base. Ga naar de Blade overzicht van de afzonderlijke Data Base om de PITR-back-up te wijzigen voor een exemplaar database van de Azure Portal. Selecteer vervolgens de optie voor het **bewaren van back-ups configureren** boven aan het scherm:

![PITR-retentie, beheerd exemplaar wijzigen](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>De retentie periode voor PITR-back-ups wijzigen met behulp van Power shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De Power shell AzureRM-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor meer informatie. De argumenten voor de opdrachten in de module AZ zijn vrijwel identiek aan die in de AzureRm-modules.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>De retentie periode voor PITR-back-ups wijzigen met behulp van de REST API

#### <a name="sample-request"></a>Voorbeeld aanvraag

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

Status code: 200

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

Zie [retentie van back-ups rest API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Database back-ups zijn een essentieel onderdeel van een strategie voor bedrijfs continuïteit en herstel na nood gevallen, omdat uw gegevens worden beschermd tegen onbedoelde beschadiging of verwijdering. Zie [overzicht van bedrijfs continuïteit](sql-database-business-continuity.md)voor meer informatie over de andere Azure SQL database oplossingen voor bedrijfs continuïteit.
- Meer informatie over [het herstellen van een Data Base naar een bepaald tijdstip met behulp van de Azure Portal](sql-database-recovery-using-backups.md).
- Meer informatie over het herstellen van [een Data Base naar een bepaald tijdstip met behulp van Power shell](scripts/sql-database-restore-database-powershell.md).
- Voor informatie over het configureren, beheren en herstellen van een lange termijn retentie van automatische back-ups in Azure Blob-opslag met behulp van de Azure Portal, Zie [lange termijn retentie van back-ups beheren met behulp van de Azure Portal](sql-database-long-term-backup-retention-configure.md).
- Zie [lange termijn retentie van back-ups beheren met Power shell](sql-database-long-term-backup-retention-configure.md)voor meer informatie over het configureren, beheren en herstellen van een lange termijn retentie van automatische back-ups in Azure Blob-opslag met behulp van Power shell.
