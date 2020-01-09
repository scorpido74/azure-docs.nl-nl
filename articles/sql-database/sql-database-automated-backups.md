---
title: automatische, geografisch redundante back-ups
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
ms.openlocfilehash: 6b880696b4922c68c73ce4ff59f72a62ce5a5a30
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75348942"
---
# <a name="automated-backups"></a>Automatische back-ups

SQL Database maakt automatisch de database back-ups die gedurende de duur van de geconfigureerde Bewaar periode worden bewaard en maakt gebruik van [geografisch redundante opslag met lees toegang (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) om ervoor te zorgen dat ze worden bewaard, zelfs als het Data Center niet beschikbaar is. Deze back-ups worden automatisch gemaakt. Database back-ups zijn een essentieel onderdeel van een strategie voor bedrijfs continuïteit en herstel na nood gevallen, omdat uw gegevens worden beschermd tegen onbedoelde beschadiging of verwijdering. Als uw back-ups gedurende lange tijd beschikbaar zijn voor uw beveiligings regels, kunt u een [lange termijn retentie](sql-database-long-term-retention.md) configureren op Singleton-data bases en elastische Pools.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Wat is een SQL Database back-up?

SQL Database gebruikt SQL Server technologie om elke week [volledige back-ups](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) te maken, [differentiële back-](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) ups elke 12 uur en [back-ups van transactie logboeken](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) om de 5-10 minuten. De back-ups worden opgeslagen in [Ra-GRS-opslag-blobs](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) die worden gerepliceerd naar een [gekoppeld Data Center](../best-practices-availability-paired-regions.md) voor beveiliging tegen een storing in een Data Center. Wanneer u een Data Base herstelt, moet u de volledige, differentiële en transactie logboek back-ups herstellen.

U kunt deze back-ups gebruiken om:

- **Een bestaande data base in het verleden herstellen naar een bepaald tijdstip** binnen de retentie periode met behulp van de Azure Portal, Azure PowerShell, Azure CLI of rest API. In één data base en elastische Pools wordt met deze bewerking een nieuwe data base gemaakt op dezelfde server als de oorspronkelijke data base. In het beheerde exemplaar kan deze bewerking een kopie maken van de data base of een ander beheerd exemplaar onder hetzelfde abonnement.
- **Een verwijderde data base herstellen op het moment dat deze is verwijderd** of op elk moment binnen de retentie periode. De verwijderde data base kan alleen worden hersteld in de logische server of het beheerde exemplaar waarin de oorspronkelijke Data Base is gemaakt.
- **Een Data Base terugzetten naar een andere geografische regio**. Met geo-Restore kunt u een geografische nood situatie herstellen wanneer u geen toegang hebt tot uw server en data base. Er wordt overal ter wereld een nieuwe data base gemaakt op elke bestaande server.
- **Een Data Base herstellen vanaf een specifieke lange termijn back-up** op Individuele database of elastische pool als de data base is geconfigureerd met een lange termijn retentie beleid (LTR). Met LTR kunt u een oude versie van de data base herstellen met behulp van [de Azure Portal](sql-database-long-term-backup-retention-configure.md#using-azure-portal) of [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) om te voldoen aan een nalevings aanvraag of een oude versie van de toepassing uit te voeren. Zie [Langetermijnretentie](sql-database-long-term-retention.md) voor meer informatie.
- Zie [Data Base terugzetten van back-ups](sql-database-recovery-using-backups.md)om een herstel bewerking uit te voeren.

> [!NOTE]
> In azure Storage verwijst de term *replicatie* naar het kopiëren van bestanden van de ene locatie naar een andere. De *database replicatie* van SQL verwijst naar het behoud van meerdere secundaire data bases gesynchroniseerd met een primaire data base.

U kunt enkele van deze bewerkingen uitproberen met de volgende voor beelden:

| | Azure Portal | Azure PowerShell |
|---|---|---|
| Retentie van back-ups wijzigen | [Individuele database](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) <br/> [Beheerd exemplaar](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) | [Individuele database](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Beheerd exemplaar](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Lange termijn retentie van back-ups wijzigen | [Individuele database](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Beheerd exemplaar-N.v.t.  | [Individuele database](sql-database-long-term-backup-retention-configure.md)<br/>Beheerd exemplaar-N.v.t.  |
| Data base terugzetten vanaf een bepaald tijdstip | [Individuele database](sql-database-recovery-using-backups.md#point-in-time-restore) | [Individuele database](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Beheerd exemplaar](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Verwijderde database herstellen | [Individuele database](sql-database-recovery-using-backups.md) | [Individuele database](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Beheerd exemplaar](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Data base terugzetten vanuit Azure Blob Storage | Eén data base-N.v.t. <br/>Beheerd exemplaar-N.v.t.  | Eén data base-N.v.t. <br/>[Beheerd exemplaar](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>Back-upfrequentie

### <a name="point-in-time-restore"></a>Herstel naar een bepaald tijdstip

SQL Database ondersteunt self-service for Point-in-time Restore (PITR) door automatisch volledige back-ups, differentiële back-ups en back-ups van transactie logboeken te maken. Volledige database back-ups worden wekelijks gemaakt, differentiële back-ups van data bases worden doorgaans elke 12 uur gemaakt en back-ups van transactie logboeken worden over het algemeen elke 5-10 minuten gemaakt, met de frequentie gebaseerd op de reken grootte en de hoeveelheid database activiteit. De eerste volledige back-up wordt onmiddellijk gepland nadat er een Data Base is gemaakt. Het wordt doorgaans binnen 30 minuten voltooid, maar het kan langer duren als de Data Base een aanzienlijke omvang heeft. De eerste back-up kan bijvoorbeeld langer duren op een herstelde data base of een kopie van een Data Base. Na de eerste volledige back-up worden alle verdere back-ups automatisch op de achtergrond gepland en beheerd. De exacte timing van alle databaseback-ups wordt bepaald door de SQL Database-service, omdat deze de algehele werkbelasting van het systeem evenredig verdeelt. U kunt de back-uptaken niet wijzigen of uitschakelen. 

De PITR-back-ups zijn geografisch redundant en worden beschermd door [Azure Storage cross-regionale replicatie](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Zie herstel naar een bepaald [tijdstip](sql-database-recovery-using-backups.md#point-in-time-restore) voor meer informatie.

### <a name="long-term-retention"></a>Langetermijnretentie

Enkele en gegroepeerde Data bases bieden de mogelijkheid om op lange termijn retentie (LTR) van volledige back-ups te configureren voor Maxi maal tien jaar in Azure Blob-opslag. Als LTR-beleid is ingeschakeld, worden de wekelijkse volledige back-ups automatisch gekopieerd naar een andere RA-GRS-opslag container. Om te voldoen aan de verschillende vereisten voor naleving, kunt u verschillende Bewaar perioden selecteren voor wekelijkse, maandelijkse en/of jaarlijkse back-ups. Het opslag verbruik is afhankelijk van de geselecteerde frequentie van back-ups en de retentie periode (n). U kunt de [LTR-prijs calculator](https://azure.microsoft.com/pricing/calculator/?service=sql-database) gebruiken om de kosten van v.l.n.r.-opslag te schatten.

Net als PITR zijn de LTR-back-ups geo-redundant en worden beveiligd door [Azure Storage cross-regionale replicatie](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage).

Zie [lange termijn retentie van back-ups](sql-database-long-term-retention.md)voor meer informatie.

## <a name="backup-storage-consumption"></a>Opslag gebruik back-ups 

Voor afzonderlijke data bases wordt het totale gebruik van back-ups als volgt berekend:   
`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`.

Voor elastische Pools wordt de totale opslag grootte van de back-up geaggregeerd op het groeps niveau en wordt als volgt berekend:   
`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`. 

Back-ups die ouder zijn dan de Bewaar periode, worden automatisch opgeschoond op basis van hun tijds tempel. Omdat voor de differentiële back-ups en logboek back-ups een eerdere volledige back-up is vereist om nuttig te zijn, worden ze samen in wekelijkse segmenten opgeruimd. 

Azure SQL Database berekent uw totale Bewaar back-upopslag als een cumulatieve waarde. Elk uur wordt deze waarde gerapporteerd aan de Azure-facturerings pijplijn die verantwoordelijk is voor het samen voegen van dit uur gebruik om uw verbruik aan het einde van elke maand te berekenen. Nadat de data base is verwijderd, neemt het verbruik af als ouderdom van back-ups. Zodra de back-ups ouder zijn dan de retentie periode, wordt de facturering gestopt. 


### <a name="monitoring-consumption"></a>Bewakings verbruik

Elk type back-up (volledig, differentieel en logboek) wordt op de Blade database bewaking gerapporteerd als afzonderlijke metriek. Het volgende diagram laat zien hoe u het opslag gebruik van back-ups kunt bewaken.  

![Bewaak het gebruik van database back-ups op de Blade database bewaking van de Azure Portal](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-the-backup-storage-consumption"></a>Het gebruik van back-upopslag nauw keurig afstemmen

Het surplus van de back-upopslag is afhankelijk van de werk belasting en de grootte van de afzonderlijke data bases. U kunt overwegen om een aantal van de volgende afstemmings technieken te implementeren om het verbruik van back-upopslag verder te verlagen:

* Verminder de [Bewaar periode voor back-ups](#change-pitr-backup-retention-period-using-azure-portal) naar de minimale vereisten voor uw behoeften.
* Vermijd het vaker uitvoeren van grote schrijf bewerkingen dan nodig, zoals het opnieuw opbouwen van indexen.
* Overweeg voor het gebruik van [geclusterde column Store-indexen](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), het aantal niet-geclusterde indexen te verminderen en de bewerkingen voor bulksgewijs laden te overwegen met aantal rijen rond 1.000.000.
* In Algemeen servicelaag is de ingerichte gegevens opslag minder duur dan de prijs van de overtollige back-upopslag, omdat klanten die voortdurend veel overtollige back-upopslag kosten kunnen overwegen de gegevens opslag te verg Roten om op te slaan op de back-upopslag.
* Gebruik TempDB in uw ETL-logica voor het opslaan van tijdelijke resultaten in plaats van permanente tabellen (alleen van toepassing op Managed instance).
* Overweeg TDE versleuteling uit te scha kelen voor data bases die geen gevoelige gegevens bevatten (ontwikkelings-of test databases, bijvoorbeeld). Back-ups voor niet-versleutelde data bases worden doorgaans gecomprimeerd met een hogere compressie ratio.

> [!IMPORTANT]
> Voor analytische data-werk belastingen van datamart, wordt het ten zeerste aanbevolen om [geclusterde column Store-indexen](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)te gebruiken, het aantal niet-geclusterde indexen te verlagen en ook bulksgewijze belasting bewerkingen te overwegen met een aantal rijen rond 1.000.000 om het verbruik van de back-upopslag te verminderen.


## <a name="storage-costs"></a>Opslagkosten


### <a name="dtu-model"></a>DTU-model

Er worden geen extra kosten in rekening gebracht voor back-upopslag voor data bases en elastische Pools met behulp van het DTU-model. 

### <a name="vcore-model"></a>vCore-model

Voor afzonderlijke data bases geldt een minimum hoeveelheid voor back-upopslag die gelijk is aan 100% van de database grootte, zonder extra kosten. Voor elastische Pools en beheerde instanties is er geen extra kosten in rekening gebracht voor een minimale back-upopslag die gelijk is aan 100% van de toegewezen gegevens opslag voor de grootte van de groep of het exemplaar. Voor aanvullend verbruik van back-upopslag worden GB/maand berekend. Dit extra verbruik is afhankelijk van de werk belasting en de grootte van de afzonderlijke data bases.

Azure SQL DB berekent uw totale in-retentie back-upopslag als een cumulatieve waarde. Elk uur wordt deze waarde gerapporteerd aan de Azure-facturerings pijplijn die verantwoordelijk is voor het samen voegen van dit uur gebruik om uw verbruik aan het einde van elke maand te verkrijgen. Nadat de data base is verwijderd, verlagen we het verbruik van de back-ups. Zodra de retentie periode is verstreken, wordt de facturering gestopt. Omdat alle logboek back-ups en differentiële back-ups worden bewaard voor de volledige Bewaar periode, hebben data bases die sterk zijn gewijzigd, hogere back-upkosten. 

We gaan ervan uit dat de data base 744 GB aan back-upopslag heeft verzameld en dat deze hoeveelheid gedurende een hele maand constant blijft. Om dit cumulatieve opslag verbruik te converteren naar een uur gebruik, delen we het met 744,0 (31 dagen per maand * 24 uur per dag). SQL DB rapporteert dus dat de data base elk uur 1 GB PITR back-up heeft verbruikt. In azure billing wordt dit samengesteld en wordt een gebruik van 744 GB voor de hele maand en de kosten weer gegeven op basis van het tarief $/GB/mo in uw regio. 

Nu is een complexere voor beeld. Stel dat de data base in het midden van de maand de Bewaar periode heeft verhoogd tot 14 dagen en dit (hypothetisch) resulteert in de totale back-upopslag, verdubbeld tot 1488 GB. SQL DB rapporteert 1 GB aan gebruik voor uur 1-372 en meld het gebruik vervolgens als 2 GB voor uur 373-744. Dit wordt geaggregeerd als een definitieve factuur van 1116 GB/mo. 

U kunt kosten analyse van Azure-abonnement gebruiken om uw huidige uitgaven op back-upopslag te bepalen.

![Kosten analyse back-upopslag](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

Als u bijvoorbeeld inzicht wilt krijgen in de opslag kosten voor back-ups voor een beheerd exemplaar, gaat u naar uw abonnement in Azure Portal en opent u de Blade kosten analyse. Selecteer de meter subcategorie **mi pitr back-upopslag** om uw huidige back-upkosten en kosten prognose te bekijken. U kunt ook andere subcategorieën van de meter, zoals een **beheerde instantie voor algemeen** gebruik-opslag of **beheerde instantie, compute GEN5** gebruiken om de opslag kosten voor back-ups te vergelijken met andere kosten categorieën.

## <a name="backup-retention"></a>Retentie van back-ups

Alle Azure SQL-data bases (enkelvoudige, gegroepeerde en beheerde exemplaar databases) hebben een standaard retentie periode van **zeven** dagen. U kunt de [Bewaar periode voor back-ups tot 35 dagen wijzigen](#change-pitr-backup-retention-period).

Als u een Data Base verwijdert SQL Database, worden de back-ups op dezelfde manier bewaard als voor een online-data base. Als u bijvoorbeeld een eenvoudige data base verwijdert met een Bewaar periode van zeven dagen, wordt een back-up die vier dagen oud is, drie dagen lang opgeslagen.

Als u de back-ups langer wilt bewaren dan de maximale Bewaar periode, kunt u de back-upeigenschappen wijzigen om een of meer lange termijn Bewaar perioden toe te voegen aan uw data base. Zie [Langetermijnretentie](sql-database-long-term-retention.md) voor meer informatie.

> [!IMPORTANT]
> Als u de Azure SQL-Server verwijdert die als host fungeert voor SQL-data bases, worden alle elastische Pools en data bases die deel uitmaken van de server, ook verwijderd en kunnen ze niet worden hersteld. U kunt een verwijderde server niet herstellen. Maar als u lange termijn retentie hebt geconfigureerd, worden de back-ups voor de data bases met LTR niet verwijderd en kunnen deze data bases worden hersteld.

## <a name="encrypted-backups"></a>Versleutelde back-ups

Als uw data base is versleuteld met TDE, worden de back-ups automatisch versleuteld op rest, inclusief LTR-back-ups. Wanneer TDE is ingeschakeld voor een Azure-SQL database, worden back-ups ook versleuteld. Alle nieuwe Azure SQL-data bases worden geconfigureerd met TDE standaard ingeschakeld. Zie [transparent Data Encryption met Azure SQL database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)voor meer informatie over TDe.

## <a name="backup-integrity"></a>Back-upintegriteit

Het Azure SQL Database engineering team test voortdurend het herstellen van automatische database back-ups van data bases die zijn geplaatst in logische servers en elastische Pools (dit is niet beschikbaar in een beheerd exemplaar). Bij herstel naar een bepaald tijdstip ontvangen data bases ook integriteits controles met DBCC CHECKDB.

Een beheerd exemplaar maakt automatische initiële back-up met `CHECKSUM` van de data bases die zijn hersteld met behulp van de systeem eigen `RESTORE` opdracht of gegevens migratie service nadat de migratie is voltooid.

Problemen die tijdens de integriteits controle worden gevonden, zullen leiden tot een waarschuwing voor het technische team. Zie voor meer informatie over gegevens integriteit in Azure SQL Database [gegevens integriteit in Azure SQL database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="compliance"></a>Naleving

Wanneer u uw data base migreert van een service tier op basis van DTU naar een vCore service tier, blijft de retentie van de PITR bewaard om ervoor te zorgen dat het gegevens herstel beleid van uw toepassing niet wordt aangetast. Als de standaard retentie niet voldoet aan uw nalevings vereisten, kunt u de retentie periode van PITR wijzigen met behulp van Power shell of REST API. Zie de [Bewaar periode voor back-ups wijzigen](#change-pitr-backup-retention-period)voor meer informatie.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-pitr-backup-retention-period"></a>Retentie periode voor PITR-back-ups wijzigen

U kunt de standaard retentie periode voor PITR-back-ups wijzigen met behulp van de Azure Portal, Power shell of de REST API. In de volgende voor beelden ziet u hoe u de retentie van PITR wijzigt in 28 dagen.

> [!WARNING]
> Als u de huidige Bewaar periode verkort, zijn alle bestaande back-ups die ouder zijn dan de nieuwe Bewaar periode niet meer beschikbaar. Als u de huidige retentie periode verhoogt, worden de bestaande back-ups door SQL Database bewaard totdat de langere Bewaar periode wordt bereikt.

> [!NOTE]
> Deze Api's zijn alleen van invloed op de PITR-retentie periode. Als u LTR voor uw data base hebt geconfigureerd, wordt dit niet beïnvloed. Zie [lange termijn retentie](sql-database-long-term-retention.md)voor meer informatie over het wijzigen van de v.l.n.r.-retentie periode (n).

### <a name="change-pitr-backup-retention-period-using-azure-portal"></a>De retentie periode voor PITR-back-ups wijzigen met Azure Portal

Als u de retentie periode voor PITR-back-ups wilt wijzigen met behulp van de Azure Portal, gaat u naar het Server object waarvan u de Bewaar periode wilt wijzigen in de portal en selecteert u vervolgens de juiste optie op basis van het Server object dat u wilt wijzigen.

#### <a name="single-database--elastic-poolstabsingle-database"></a>[Eén data base & elastische Pools](#tab/single-database)

De wijziging van de PITR-back-upbewaaring voor één Azure SQL-Data Base wordt uitgevoerd op server niveau. Wijzigingen die zijn aangebracht op server niveau, zijn van toepassing op data bases op die server. Als u PITR voor Azure SQL Database Server wilt wijzigen vanuit Azure Portal, gaat u naar de Blade Server overzicht, klikt u op back-ups beheren in het navigatie menu en klikt u vervolgens op retentie configureren op de navigatie balk.

![PITR wijzigen Azure Portal](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instancetabmanaged-instance"></a>[Beheerd exemplaar](#tab/managed-instance)

De wijziging van de PITR-back-upbewaaring voor SQL Database beheerde instantie wordt uitgevoerd op het niveau van een afzonderlijke data base. Als u de retentie van een PITR voor een exemplaar database van Azure Portal wilt wijzigen, gaat u naar de Blade overzicht van de afzonderlijke data base en klikt u vervolgens op back-up configureren op de navigatie balk.

![PITR wijzigen Azure Portal](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-pitr-backup-retention-period-using-powershell"></a>De retentie periode voor PITR-back-ups wijzigen met Power shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>De retentie periode voor PITR wijzigen met behulp van REST API

#### <a name="sample-request"></a>Voorbeeldaanvraag

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Aanvraagtekst

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
- Als u wilt herstellen naar een bepaald tijdstip met behulp van de Azure Portal, raadpleegt u [Data Base herstellen naar een bepaald tijdstip met behulp van de Azure Portal](sql-database-recovery-using-backups.md).
- Zie [Data Base herstellen naar een bepaald tijdstip met behulp van Power shell](scripts/sql-database-restore-database-powershell.md)als u op een bepaald tijdstip wilt herstellen met behulp van Power shell.
- Voor het configureren, beheren en herstellen van de lange termijn retentie van automatische back-ups in Azure Blob-opslag met behulp van de Azure Portal, Zie [lange termijn retentie van back-ups beheren met behulp van de Azure Portal](sql-database-long-term-backup-retention-configure.md).
- Zie [lange termijn retentie beheren met Power shell](sql-database-long-term-backup-retention-configure.md)voor meer informatie over het configureren, beheren en herstellen van de lange termijn retentie van automatische back-ups in Azure Blob-opslag met behulp van Power shell.
