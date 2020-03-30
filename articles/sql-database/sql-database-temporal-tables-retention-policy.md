---
title: Historische gegevens beheren in tijdelijke tabellen
description: Meer informatie over het gebruik van temporele bewaarbeleid om historische gegevens onder uw controle te houden.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
ms.date: 09/25/2018
ms.openlocfilehash: 3c2460c6f5e0905f45106148ecc3e8a949cf221f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820688"
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Historische gegevens beheren in temporele tabellen met bewaarbeleid

Temporele tabellen kunnen de databasegrootte groter maken dan gewone tabellen, vooral als u historische gegevens voor een langere periode bewaart. Daarom is het bewaarbeleid voor historische gegevens een belangrijk aspect van het plannen en beheren van de levenscyclus van elke tijdelijke tabel. Tijdelijke tabellen in Azure SQL Database worden geleverd met een gebruiksvriendelijk bewaarmechanisme waarmee u deze taak uitvoeren.

Temporele geschiedenisretentie kan worden geconfigureerd op het individuele tafelniveau, waardoor gebruikers flexibele verouderingspolities kunnen maken. Het toepassen van tijdelijke retentie is eenvoudig: er hoeft slechts één parameter te worden ingesteld tijdens het maken van tabelwaarden of schemawijzigingen.

Nadat u het bewaarbeleid hebt gedefinieerd, wordt in Azure SQL Database regelmatig gecontroleerd of er historische rijen zijn die in aanmerking komen voor automatische gegevensopruiming. Identificatie van overeenkomende rijen en de verwijdering ervan uit de geschiedenistabel gebeurt transparant, in de achtergrondtaak die is gepland en uitgevoerd door het systeem. De leeftijdsvoorwaarde voor de rijen van de geschiedenistabel wordt gecontroleerd op basis van de kolom die het einde van SYSTEM_TIME periode vertegenwoordigt. Als de bewaartermijn bijvoorbeeld is ingesteld op zes maanden, voldoen tabelrijen die in aanmerking komen voor opruiming aan de volgende voorwaarde:

```
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
```

In het voorgaande voorbeeld gingen we ervan uit dat de kolom **ValidTo** overeenkomt met het einde van SYSTEM_TIME periode.

## <a name="how-to-configure-retention-policy"></a>Bewaarbeleid configureren

Controleer eerst of tijdelijke historische retentie is ingeschakeld *op databaseniveau*voordat u het bewaarbeleid voor een tijdelijke tabel configureert.

```
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
```

Databasevlag **is_temporal_history_retention_enabled** is standaard ingesteld op AAN, maar gebruikers kunnen deze wijzigen met de instructie ALTER DATABASE. Het is ook automatisch ingesteld op UIT na [punt in de tijd herstellen](sql-database-recovery-using-backups.md) operatie. Voer de volgende instructie uit om het opschonen van tijdelijke geschiedenis voor uw database in te schakelen:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

> [!IMPORTANT]
> U retentie voor tijdelijke tabellen configureren, zelfs als **is_temporal_history_retention_enabled** is uitgeschakeld, maar automatische opschoning voor oude rijen wordt in dat geval niet geactiveerd.

Bewaarbeleid wordt geconfigureerd tijdens het maken van tabelwaarden door waarde op te geven voor de parameter HISTORY_RETENTION_PERIOD:

```sql
CREATE TABLE dbo.WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH
 (
     SYSTEM_VERSIONING = ON
     (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
     )
 );
```

Met Azure SQL Database u de bewaartermijn opgeven met behulp van verschillende tijdeenheden: DAGEN, WEKEN, MAANDEN en JAREN. Als HISTORY_RETENTION_PERIOD wordt weggelaten, wordt infinite retentie aangenomen. U infinite-trefwoord ook expliciet gebruiken.

In sommige scenario's u bewaarna de tabelmaken configureren of eerder geconfigureerde waarde wijzigen. Gebruik in dat geval de verklaring ALTER TABLE:

```sql
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
```

> [!IMPORTANT]
> Als u SYSTEM_VERSIONING instelt op *UIT, blijft de* waarde van de bewaarperiode niet behouden. Als u SYSTEM_VERSIONING op AAN instelt zonder HISTORY_RETENTION_PERIOD expliciet opgegeven, resulteert dit in de ONEINDIGe bewaartermijn.

Als u de huidige status van het bewaarbeleid wilt controleren, gebruikt u de volgende query die tijdelijke bewaarfunctieop basisvan de database markeert met bewaarperioden voor afzonderlijke tabellen:

```sql
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName,  SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2   
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
```


## <a name="how-sql-database-deletes-aged-rows"></a>Hoe SQL Database oude rijen verwijdert

Het opschoningsproces is afhankelijk van de indexindeling van de geschiedenistabel. Het is belangrijk op te merken dat *alleen geschiedenistabellen met een geclusterde index (B-tree of kolomarchief) eindig bewaarbeleid kunnen hebben geconfigureerd.* Er wordt een achtergrondtaak gemaakt om verouderde gegevensopschonen uit te voeren voor alle tijdelijke tabellen met een eindige bewaarperiode.
Opschoningslogica voor de geclusterde rijwaarde (B-tree) verwijdert de oude rij in kleinere segmenten (tot 10K) en minimaliseert de druk op databaselogboek en IO-subsysteem. Hoewel de opruimlogica gebruik maakt van de vereiste B-tree-index, kan de volgorde van de verwijderingen voor de rijen ouder dan de bewaartermijn niet stevig worden gegarandeerd. Neem daarom *geen afhankelijkheid van de opruimvolgorde in uw toepassingen.*

De opschoningstaak voor het geclusterde kolomarchief verwijdert hele [rijgroepen](https://msdn.microsoft.com/library/gg492088.aspx) tegelijk (bevatten doorgaans 1 miljoen rijen per stuk), wat zeer efficiënt is, vooral wanneer historische gegevens in een hoog tempo worden gegenereerd.

![Clusteropslag behouden](./media/sql-database-temporal-tables-retention-policy/cciretention.png)

Uitstekende gegevenscompressie en efficiënte retentie-opschoning maakt geclusterde columnstore-index een perfecte keuze voor scenario's waarin uw werkbelasting snel een grote hoeveelheid historische gegevens genereert. Dat patroon is typisch voor intensieve [transactionele verwerkingsworkloads die tijdelijke tabellen gebruiken](https://msdn.microsoft.com/library/mt631669.aspx) voor het bijhouden en controleren van wijzigingen, trendanalyse of het innemen van IoT-gegevens.

## <a name="index-considerations"></a>Indexoverwegingen

Voor de opschoningstaak voor tabellen met geclusterde index voor rowstore moet de index beginnen met de kolom die overeenkomt met het einde van SYSTEM_TIME periode. Als een dergelijke index niet bestaat, u geen eindige bewaarperiode configureren:

*Msg 13765, Level 16, State 1 <br> </br> Instelling eindige bewaartermijn is mislukt op de systeemversie van de temporele tabel 'temporalstagetestdbdb.dbo.WebsiteUserInfo' omdat de geschiedenistabel 'temporalstagetestdbdb.dbo.WebsiteUserInfoHistory' geen vereiste geclusterde index bevat. Overweeg een geclusterde kolomarchief of B-tree-index te maken die begint met de kolom die overeenkomt met het einde van SYSTEM_TIME periode, in de geschiedenistabel.*

Het is belangrijk op te merken dat de standaardgeschiedenistabel die is gemaakt door Azure SQL Database al een geclusterde index heeft, die compatibel is voor het bewaarbeleid. Als u probeert die index te verwijderen in een tabel met een eindige bewaarperiode, mislukt de bewerking met de volgende fout:

*Msg 13766, Level 16, State 1 <br> </br> Kan de geclusterde index 'WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory' niet laten vallen omdat deze wordt gebruikt voor het automatisch opschonen van verouderde gegevens. Stel HISTORY_RETENTION_PERIOD in op INFINITE op de bijbehorende temporele tabel met systeemversies als u deze index wilt laten vallen.*

Opschonen op de clusterde kolomarchiefindex werkt optimaal als historische rijen worden ingevoegd in de opgaande volgorde (geordend op het einde van de kolom van de periode), wat altijd het geval is wanneer de geschiedenistabel uitsluitend wordt ingevuld door het SYSTEM_VERSIONIOING-mechanisme. Als rijen in de geschiedenistabel niet zijn geordend op het einde van de kolom (wat het geval kan zijn als u bestaande historische gegevens migreert), moet u de clusterindex opnieuw maken bovenop de index van de rijopslag met b-structuur die correct is geordend, om een optimale Prestaties.

Vermijd het opnieuw opbouwen van de clusterstore-index op de geschiedenistabel met de eindige bewaarperiode, omdat deze het bestellen in de rijgroepen kan wijzigen die natuurlijk worden opgelegd door de systeemversiebewerking. Als u de geclusterde kolomarchiefindex opnieuw wilt bouwen op de geschiedenistabel, moet u dat doen door deze opnieuw te maken bovenop de compatibele B-tree-index, waarbij het bestellen in de rijgroepen die nodig zijn voor het regelmatig opschonen van gegevens behouden blijft. Dezelfde benadering moet worden gevolgd als u een tijdelijke tabel maakt met een bestaande geschiedenistabel met een geclusterde kolomindex zonder gegarandeerde gegevensvolgorde:

```sql
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

Wanneer de eindige bewaarperiode is geconfigureerd voor de geschiedenistabel met de geclusterde kolomarchiefindex, u geen extra niet-geclusterde B-tree-indexen op die tabel maken:

```sql
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
```

Een poging om bovenstaande instructie uit te voeren mislukt met de volgende fout:

*Msg 13772, Level 16, State 1 <br> </br> Kan niet-geclusterde index maken op een temporele geschiedenistabel 'WebsiteUserInfoHistory' omdat deze een eindige bewaarperiode en een geclusterde kolomarchiefindex heeft gedefinieerd.*

## <a name="querying-tables-with-retention-policy"></a>Tabellen opvragen met bewaarbeleid

Alle query's op de temporele tabel filteren automatisch historische rijen die overeenkomen met eindigretentiebeleid, om onvoorspelbare en inconsistente resultaten te voorkomen, omdat oude rijen kunnen worden verwijderd door de opruimtaak, *op elk moment in willekeurige volgorde*.

In de volgende afbeelding wordt het queryplan voor een eenvoudige query weergegeven:

```sql
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
```

Het queryplan bevat een extra filter dat is toegepast op het einde van de periodekolom (ValidTo) in de operator Clustered Index Scan in de geschiedenistabel (gemarkeerd). In dit voorbeeld wordt ervan uitgegaan dat de bewaarperiode van één maand is ingesteld in de tabel WebsiteUserInfo.

![Bewaarqueryfilter](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

Als u echter rechtstreeks de geschiedenistabel opvraagt, ziet u mogelijk rijen die ouder zijn dan de opgegeven bewaarperiode, maar zonder enige garantie voor herhaalbare queryresultaten. In de volgende afbeelding wordt het queryuitvoeringsplan voor de query in de geschiedenistabel weergegeven zonder dat er extra filters zijn toegepast:

![Geschiedenis opvragen zonder retentiefilter](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

Vertrouw uw bedrijfslogica niet op de leesgeschiedenistabel na de bewaarperiode, omdat u mogelijk inconsistente of onverwachte resultaten krijgt. We raden u aan tijdelijke query's te gebruiken met FOR SYSTEM_TIME-clausule voor het analyseren van gegevens in tijdelijke tabellen.

## <a name="point-in-time-restore-considerations"></a>Overwegingen voor het herstellen van tijdswijzen

Wanneer u een nieuwe database maakt door [bestaande database te herstellen naar een specifiek tijdstip,](sql-database-recovery-using-backups.md)is de tijdelijke retentie uitgeschakeld op databaseniveau. (**is_temporal_history_retention_enabled** vlag ingesteld op UIT). Met deze functionaliteit u alle historische rijen bij herstel onderzoeken, zonder u zorgen te maken dat oude rijen worden verwijderd voordat u ze opvragen. U deze gebruiken om historische gegevens te *inspecteren na de geconfigureerde bewaarperiode.*

Stel dat een tijdelijke tabel een bewaartermijn van één maand heeft opgegeven. Als uw database is gemaakt in de laag Premium Service, u tot 35 dagen terug in het verleden databasekopie maken met de databasestatus. Dat zou u in staat stellen om historische rijen die tot 65 dagen oud zijn te analyseren door de geschiedenistabel rechtstreeks op te vragen.

Als u tijdelijke retentie-opruiming wilt activeren, voert u de volgende transact-SQL-instructie na punt in de tijd in het herstellen uit:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het gebruik van tijdelijke tabellen in uw toepassingen raadpleegt u [Aan de slag met temporele tabellen in Azure SQL Database](sql-database-temporal-tables.md).

Bezoek Channel 9 om een [echte klant tijdelijke implementatie succesverhaal](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) te horen en bekijk een live [temporele demonstratie](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

Voor gedetailleerde informatie over temporele tabellen, bekijk [MSDN-documentatie](https://msdn.microsoft.com/library/dn935015.aspx).
