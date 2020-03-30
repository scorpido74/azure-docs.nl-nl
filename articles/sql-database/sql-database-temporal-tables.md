---
title: Aan de slag met tijdelijke tabellen
description: Meer informatie over het gebruik van Temporele tabellen in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
ms.date: 06/26/2019
ms.openlocfilehash: 98fd2658f3fbcb0e7e29114d29f8dc6ed39eedf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820726"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Aan de slag met tijdelijke tabellen in Azure SQL-database

Temporele tabellen zijn een nieuwe programmeerbaarheidsfunctie van Azure SQL Database waarmee u de volledige geschiedenis van wijzigingen in uw gegevens bijhouden en analyseren, zonder dat u aangepaste codering nodig hebt. Temporele tabellen houden gegevens nauw verwant aan de tijdcontext, zodat opgeslagen feiten alleen binnen de specifieke periode als geldig kunnen worden geïnterpreteerd. Deze eigenschap van Temporele tabellen zorgt voor efficiënte tijdgebaseerde analyse en het verkrijgen van inzichten uit gegevensevolutie.

## <a name="temporal-scenario"></a>Tijdelijk scenario

In dit artikel worden de stappen geïllustreerd om Tijdelijke tabellen in een toepassingsscenario te gebruiken. Stel dat u gebruikersactiviteiten wilt bijhouden op een nieuwe website die vanaf nul wordt ontwikkeld of op een bestaande website die u wilt uitbreiden met analyse van gebruikersactiviteiten. In dit vereenvoudigde voorbeeld gaan we ervan uit dat het aantal bezochte webpagina's gedurende een bepaalde periode een indicator is die moet worden vastgelegd en gecontroleerd in de websitedatabase die wordt gehost op Azure SQL Database. Het doel van de historische analyse van de activiteit van de gebruiker is om ingangen te krijgen om de website te herontwerpen en een betere ervaring voor de bezoekers te bieden.

Het databasemodel voor dit scenario is zeer eenvoudig - de statistiek gebruikersactiviteit wordt weergegeven met één geheel getalveld, **PageVisited,** en wordt vastgelegd samen met basisinformatie op het gebruikersprofiel. Bovendien houdt u voor tijdgebaseerde analyse een reeks rijen voor elke gebruiker, waarbij elke rij het aantal pagina's vertegenwoordigt dat een bepaalde gebruiker binnen een bepaalde periode heeft bezocht.

![Schema](./media/sql-database-temporal-tables/AzureTemporal1.png)

Gelukkig hoeft u geen moeite te doen in uw app om deze activiteitsinformatie bij te houden. Met Temporele tabellen wordt dit proces geautomatiseerd - waardoor u volledige flexibiliteit hebt tijdens het ontwerp van de website en meer tijd hebt om zich te concentreren op de data-analyse zelf. Het enige wat u hoeft te doen is ervoor te zorgen dat **WebSiteInfo** tabel is geconfigureerd als [tijdelijk systeem-versie.](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0) De exacte stappen om temporele tabellen in dit scenario te gebruiken, worden hieronder beschreven.

## <a name="step-1-configure-tables-as-temporal"></a>Stap 1: Tabellen configureren als tijdelijk
Afhankelijk van of u een nieuwe ontwikkeling start of bestaande toepassingen upgradet, maakt u tijdelijke tabellen of wijzigt u bestaande tabellen door tijdelijke kenmerken toe te voegen. In het algemeen kan uw scenario een mix van deze twee opties zijn. Voer deze actie uit met [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), SQL Server Data [Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) of een ander Transact-SQL-ontwikkelingsprogramma.

> [!IMPORTANT]
> Het wordt aanbevolen om altijd de nieuwste versie van Management Studio te gebruiken, zodat uw versie gesynchroniseerd blijft met updates voor Microsoft Azure en SQL Database. [SQL Server Management Studio bijwerken](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="create-new-table"></a>Nieuwe tabel maken
Gebruik contextmenu-item 'Nieuwe systeemversietabel' in SSMS Object Explorer om de queryeditor te openen met een sjabloonscript voor tijdelijke tabelen en gebruik vervolgens 'Waarden opgeven voor sjabloonparameters' (Ctrl+Shift+M) om de sjabloon in te vullen:

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

Kies in SSDT de sjabloon 'Temporele tabel (systeemversie)" wanneer u nieuwe items toevoegt aan het databaseproject. Hiermee wordt de tabelontwerper geopend en u eenvoudig de tabelindeling opgeven:

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

U ook een tijdelijke tabel maken door de Transact-SQL-instructies rechtstreeks op te geven, zoals in het onderstaande voorbeeld wordt weergegeven. Houd er rekening mee dat de verplichte elementen van elke tijdelijke tabel de periodedefinitie en de SYSTEM_VERSIONING-clausule zijn met een verwijzing naar een andere gebruikerstabel die historische rijversies opslaat:

```
CREATE TABLE WebsiteUserInfo 
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED 
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL 
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
```

Wanneer u een tijdelijke tabel met systeemversie maakt, wordt de bijbehorende geschiedenistabel met de standaardconfiguratie automatisch gemaakt. De standaardgeschiedenistabel bevat een geclusterde B-tree-index op de periodekolommen (einde, begin) en paginacompressie is ingeschakeld. Deze configuratie is optimaal voor de meeste scenario's waarin tijdelijke tabellen worden gebruikt, met name voor [gegevenscontrole.](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0) 

In dit specifieke geval streven we ernaar om op tijd gebaseerde trendanalyses uit te voeren over een langere gegevensgeschiedenis en met grotere gegevenssets, zodat de opslagkeuze voor de geschiedenistabel een geclusterde kolomarchiefindex is. Een geclusterde kolomarchief biedt zeer goede compressie en prestaties voor analytische query's. Temporele tabellen bieden u de flexibiliteit om indexen op de huidige en tijdelijke tabellen volledig onafhankelijk te configureren. 

> [!NOTE]
> Columnstore-indexen zijn beschikbaar in de premiumlaag en in de standaardlaag, S3 en hoger.
>

In het volgende script ziet u hoe de standaardindex op de geschiedenistabel kan worden gewijzigd in het geclusterde kolomarchief:

```
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
```

Tijdelijke tabellen worden weergegeven in de Object Explorer met het specifieke pictogram voor eenvoudigere identificatie, terwijl de geschiedenistabel wordt weergegeven als een onderliggend knooppunt.

![Wijzigingstabel](./media/sql-database-temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Bestaande tabel wijzigen in tijdelijk
Laten we het alternatieve scenario bespreken waarin de tabel WebsiteUserInfo al bestaat, maar niet is ontworpen om een geschiedenis van wijzigingen bij te houden. In dit geval u de bestaande tabel eenvoudig uitbreiden om tijdelijk te worden, zoals in het volgende voorbeeld wordt weergegeven:

```
ALTER TABLE WebsiteUserInfo 
ADD 
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN   
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo); 

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
```

## <a name="step-2-run-your-workload-regularly"></a>Stap 2: Uw werklast regelmatig uitvoeren
Het belangrijkste voordeel van Temporele tabellen is dat u uw website op geen enkele manier hoeft te wijzigen of aanpassen om het bijhouden van wijzigingen uit te voeren. Na het maken van tijdelijke tabellen blijven eerdere rijversies transparant bestaan telkens wanneer u wijzigingen in uw gegevens uitvoert. 

Om gebruik te maken van automatische wijziging tracking voor dit specifieke scenario, laten we gewoon bijwerken kolom **PagesVisited** elke keer dat een gebruiker eindigt hun sessie op de website:

```
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
```

Het is belangrijk op te merken dat de updatequery niet hoeft te weten wanneer de werkelijke bewerking heeft plaatsgevonden, noch hoe historische gegevens worden bewaard voor toekomstige analyse. Beide aspecten worden automatisch afgehandeld door de Azure SQL Database. Het volgende diagram laat zien hoe geschiedenisgegevens worden gegenereerd bij elke update.

![TemporeleArchitectuur](./media/sql-database-temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>Stap 3: Historische gegevensanalyse uitvoeren
Wanneer temporele systeemversies zijn ingeschakeld, is historische gegevensanalyse slechts één query verwijderd van u. In dit artikel zullen we een paar voorbeelden geven die betrekking hebben op gemeenschappelijke analysescenario's - om alle details te leren, verschillende opties te verkennen die zijn geïntroduceerd met de [FOR-SYSTEM_TIME-clausule.](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3)

Voer de volgende query uit om de top 10 gebruikers te zien die zijn besteld op basis van het aantal bezochte webpagina's van een uur geleden:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
```

U deze query eenvoudig wijzigen om de sitebezoeken te analyseren vanaf een dag geleden, een maand geleden of op elk gewenst moment in het verleden.

Als u een basisstatistische analyse voor de vorige dag wilt uitvoeren, gebruikt u het volgende voorbeeld:

```
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
```

Als u binnen een bepaalde periode naar activiteiten van een specifieke gebruiker wilt zoeken, gebruikt u de clausule CONTAINED IN:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
```

Grafische visualisatie is vooral handig voor tijdelijke query's, omdat u trends en gebruikspatronen op een intuïtieve manier heel gemakkelijk weergeven:

![TemporalGraph (TemporalGraph)](./media/sql-database-temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Evoluerend tabelschema
Normaal gesproken moet u het temporele tabelschema wijzigen terwijl u app-ontwikkeling doet. Voer daarvoor gewoon reguliere ALTER TABLE-instructies uit en Azure SQL Database geeft wijzigingen in de geschiedenistabel op de juiste manier door. In het volgende script ziet u hoe u extra kenmerk toevoegen voor het bijhouden:

```
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
```

U ook de kolomdefinitie wijzigen terwijl uw werkbelasting actief is:

```
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
```

Ten slotte u een kolom verwijderen die u niet meer nodig hebt.

```
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
```

U ook de nieuwste [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) gebruiken om het temporele tabelschema te wijzigen terwijl u bent verbonden met de database (onlinemodus) of als onderdeel van het databaseproject (offlinemodus).

## <a name="controlling-retention-of-historical-data"></a>Het beheren van het bewaren van historische gegevens
Met tijdelijke tabellen in systeemversies kan de geschiedenistabel de databasegrootte meer vergroten dan gewone tabellen. Een grote en steeds groeiende geschiedenistabel kan een probleem worden, zowel als gevolg van pure opslagkosten als door het opleggen van een prestatiebelasting op temporele query's. Daarom is het ontwikkelen van een beleid voor het bewaren van gegevens voor het beheren van gegevens in de geschiedenistabel een belangrijk aspect van het plannen en beheren van de levenscyclus van elke tijdelijke tabel. Met Azure SQL Database hebt u de volgende benaderingen voor het beheren van historische gegevens in de temporele tabel:

* [Tabellen partitioneren](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
* [Aangepast opschonescript](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

## <a name="next-steps"></a>Volgende stappen

- Zie [Tijdelijke tabellen](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables)voor meer informatie over tijdelijke tabellen .
- Bezoek Channel 9 om een [echte klant tijdelijke implementatie succesverhaal](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) te horen en bekijk een live [temporele demonstratie](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

