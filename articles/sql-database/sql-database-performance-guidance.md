---
title: Richtlijnen voor prestatieafstemming voor toepassingen en databases
description: Meer informatie over het afstemmen van databasetoepassingen en databases voor prestaties in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: carlrab; jrasnick
ms.date: 03/10/2020
ms.openlocfilehash: 4f30ebe39d86db7076baa8c29b2a5cf060b07bf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255949"
---
# <a name="tune-applications-and-databases-for-performance-in-azure-sql-database"></a>Toepassingen en databases afstemmen op prestaties in Azure SQL Database

Zodra u een prestatieprobleem hebt geïdentificeerd waarmee u te maken hebt met SQL Database, is dit artikel ontworpen om u te helpen:

- Stem uw toepassing af en pas enkele aanbevolen procedures toe die de prestaties kunnen verbeteren.
- Stem de database af door indexen en query's te wijzigen om efficiënter met gegevens te werken.

In dit artikel wordt ervan uitgegaan dat u de aanbevelingen azure SQL [Database-databaseadvisor](sql-database-advisor.md) en de [aanbevelingen voor automatische afstemming van](sql-database-automatic-tuning.md)Azure SQL Database al hebt doorlopen. Het gaat er ook van uit dat u [een overzicht van monitoring en tuning en](sql-database-monitor-tune-overview.md) de bijbehorende artikelen met betrekking tot het oplossen van prestatieproblemen hebt beoordeeld. Bovendien wordt in dit artikel ervan uitgegaan dat u geen CPU-resources hebt, waardoor het prestatieprobleem wordt uitgevoerd dat kan worden opgelost door de rekengrootte of servicelaag te verhogen om meer resources aan uw database te bieden.

## <a name="tune-your-application"></a>Uw toepassing afstemmen

In traditionele on-premises SQL Server wordt het proces van initiële capaciteitsplanning vaak gescheiden van het proces van het uitvoeren van een toepassing in productie. Hardware- en productlicenties worden eerst aangeschaft en prestatieafstemming wordt daarna uitgevoerd. Wanneer u Azure SQL Database gebruikt, is het een goed idee om het proces van het uitvoeren van een toepassing te verweven en deze af te stemmen. Met het model van het betalen voor capaciteit op aanvraag, u uw toepassing afstemmen om de minimale resources te gebruiken die nu nodig zijn, in plaats van over-provisioning op hardware op basis van gissingen van toekomstige groeiplannen voor een toepassing, die vaak onjuist zijn. Sommige klanten kunnen ervoor kiezen om een toepassing niet af te stemmen en in plaats daarvan ervoor kiezen om hardwarebronnen te overprovisioneren. Deze aanpak is misschien een goed idee als u een belangrijke toepassing niet wilt wijzigen tijdens een drukke periode. Het afstemmen van een toepassing kan echter de resourcevereisten minimaliseren en de maandelijkse facturen verlagen wanneer u de servicelagen in Azure SQL Database gebruikt.

### <a name="application-characteristics"></a>Toepassingskenmerken

Hoewel Azure SQL Database-servicelagen zijn ontworpen om de stabiliteit en voorspelbaarheid van de prestaties voor een toepassing te verbeteren, kunnen sommige aanbevolen procedures u helpen uw toepassing af te stemmen om beter te profiteren van de resources op een rekengrootte. Hoewel veel toepassingen aanzienlijke prestatieverbeteringen hebben door simpelweg over te schakelen naar een hogere rekengrootte of servicelaag, hebben sommige toepassingen extra afstemming nodig om te profiteren van een hoger serviceniveau. Voor betere prestaties u rekening houden met aanvullende toepassingsafstemming voor toepassingen met deze kenmerken:

- **Toepassingen die trage prestaties hebben als gevolg van "spraakzaam" gedrag**

  Chatty-toepassingen maken overmatige gegevenstoegangsbewerkingen die gevoelig zijn voor netwerklatentie. Mogelijk moet u dit soort toepassingen wijzigen om het aantal gegevenstoegangsbewerkingen naar de SQL-database te verminderen. U bijvoorbeeld de prestaties van toepassingen verbeteren door technieken zoals ad-hocquery's batching of het verplaatsen van de query's naar opgeslagen procedures. Zie [Batchquery's voor](#batch-queries)meer informatie.

- **Databases met een intensieve werklast die niet door één machine kunnen worden ondersteund**

   Databases die de resources van de hoogste Premium-rekengrootte overschrijden, kunnen baat hebben bij het uitschalen van de werkbelasting. Zie [Cross-database sharding](#cross-database-sharding) en [Functionele partitionering](#functional-partitioning)voor meer informatie.

- **Toepassingen met suboptimale query's**

  Toepassingen, vooral die in de laag gegevenstoegang, die slecht afgestemde query's hebben, profiteren mogelijk niet van een hogere rekengrootte. Dit geldt ook voor query's die geen WHERE-clausule hebben, ontbrekende indexen hebben of verouderde statistieken hebben. Deze toepassingen profiteren van standaard query performance-tuning technieken. Zie [Ontbrekende indexen](#identifying-and-adding-missing-indexes) en [Queryafstemming en hinten](#query-tuning-and-hinting)voor meer informatie.

- **Toepassingen met een suboptimaal ontwerp voor gegevenstoegang**

   Toepassingen met inherente gegevenstoegang tot gelijktijdigheidsproblemen, bijvoorbeeld impasse, profiteren mogelijk niet van een hogere rekengrootte. Overweeg om retourritten te verminderen ten opzichte van de Azure SQL Database door gegevens aan de clientzijde te plaatsen met de Azure Caching-service of een andere caching-technologie. Zie [Caching van toepassingslagen](#application-tier-caching).

## <a name="tune-your-database"></a>Uw database afstemmen

In deze sectie bekijken we enkele technieken die u gebruiken om Azure SQL Database af te stemmen om de beste prestaties voor uw toepassing te krijgen en deze uit te voeren op de laagst mogelijke rekengrootte. Sommige van deze technieken komen overeen met traditionele SQL Server-aanbevolen procedures voor het afstemmen van de functie, maar andere zijn specifiek voor Azure SQL Database. In sommige gevallen u de verbruikte resources voor een database onderzoeken om gebieden te vinden om traditionele SQL Server-technieken verder af te stemmen en uit te breiden om te werken in Azure SQL Database.

### <a name="identifying-and-adding-missing-indexes"></a>Ontbrekende indexen identificeren en toevoegen

Een veelvoorkomend probleem in de prestaties van de OLTP-database heeft betrekking op het ontwerp van de fysieke database. Vaak worden databaseschema's ontworpen en verzonden zonder op schaal te testen (in belasting of in gegevensvolume). Helaas kunnen de prestaties van een queryplan op kleine schaal aanvaardbaar zijn, maar aanzienlijk verslechteren onder gegevensvolumes op productieniveau. De meest voorkomende bron van dit probleem is het ontbreken van geschikte indexen om te voldoen aan filters of andere beperkingen in een query. Vaak manifesteert ontbrekende indexen zich als een tabelscan wanneer een indexzoeken voldoende zou kunnen zijn.

In dit voorbeeld wordt in het geselecteerde queryplan een scan gebruikt wanneer een zoekopdracht voldoende is:

```sql
DROP TABLE dbo.missingindex;
CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;
```

![Een queryplan met ontbrekende indexen](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Azure SQL Database kan u helpen bij het vinden en oplossen van veelvoorkomende ontbrekende indexvoorwaarden. DMVs die zijn ingebouwd in Azure SQL Database kijken naar querycompilaties waarin een index de geschatte kosten voor het uitvoeren van een query aanzienlijk zou verlagen. Tijdens query-uitvoering houdt SQL Database bij hoe vaak elk queryplan wordt uitgevoerd en houdt de geschatte kloof bij tussen het uitvoerende queryplan en het ingebeelde plan waar die index bestond. U deze DM's gebruiken om snel te raden welke wijzigingen in uw fysieke databaseontwerp de totale werkbelastingkosten voor een database en de werkelijke werkbelasting kunnen verbeteren.

U deze query gebruiken om potentiële ontbrekende indexen te evalueren:

```sql
SELECT
   CONVERT (varchar, getdate(), 126) AS runtime
   , mig.index_group_handle
   , mid.index_handle
   , CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
        (migs.user_seeks + migs.user_scans)) AS improvement_measure
   , 'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
        CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
        (' + ISNULL (mid.equality_columns,'')
        + CASE WHEN mid.equality_columns IS NOT NULL
        AND mid.inequality_columns IS NOT NULL
        THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '') + ')'
        + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement
   , migs.*
   , mid.database_id
   , mid.[object_id]
FROM sys.dm_db_missing_index_groups AS mig
   INNER JOIN sys.dm_db_missing_index_group_stats AS migs
      ON migs.group_handle = mig.index_group_handle
   INNER JOIN sys.dm_db_missing_index_details AS mid
      ON mig.index_handle = mid.index_handle
 ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC
```

In dit voorbeeld heeft de query geresulteerd in deze suggestie:

```sql
CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  
```

Nadat deze is gemaakt, kiest diezelfde SELECT-instructie een ander plan, waarbij een zoeken in plaats van een scan wordt gebruikt en wordt het plan efficiënter uitgevoerd:

![Een queryplan met gecorrigeerde indexen](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

Het belangrijkste inzicht is dat de IO-capaciteit van een gedeeld, commodity-systeem beperkter is dan die van een dedicated servermachine. Er is een premie voor het minimaliseren van onnodige IO om maximaal te profiteren van het systeem in de DTU van elke rekengrootte van de Azure SQL Database-servicelagen. Geschikte fysieke databaseontwerpkeuzes kunnen de latentie voor afzonderlijke query's aanzienlijk verbeteren, de doorvoer van gelijktijdige aanvragen per schaaleenheid verbeteren en de kosten minimaliseren die nodig zijn om aan de query te voldoen. Zie [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx)voor meer informatie over de ontbrekende index-DMVs.

### <a name="query-tuning-and-hinting"></a>Queryafstemming en -hint

De queryoptimizer in Azure SQL Database is vergelijkbaar met de traditionele SQL Server-queryoptimizer. De meeste aanbevolen procedures voor het afstemmen van query's en het begrijpen van de beperkingen van het redeneermodel voor de queryoptimizer zijn ook van toepassing op Azure SQL Database. Als u query's afstemt in Azure SQL Database, u het extra voordeel krijgen van het verminderen van de totale resourcevereisten. Uw toepassing kan mogelijk worden uitgevoerd tegen lagere kosten dan een niet-afgestemd equivalent, omdat deze kan worden uitgevoerd op een lagere rekengrootte.

Een voorbeeld dat gebruikelijk is in SQL Server en dat ook van toepassing is op Azure SQL Database, is hoe de queryoptimizer parameters "snuift". Tijdens het samenstellen evalueert de queryoptimizer de huidige waarde van een parameter om te bepalen of deze een optimaaler queryplan kan genereren. Hoewel deze strategie vaak kan leiden tot een queryplan dat aanzienlijk sneller is dan een plan dat is samengesteld zonder bekende parameterwaarden, werkt het momenteel onvolmaakt, zowel in SQL Server als in Azure SQL Database. Soms wordt de parameter niet gesnoven en soms wordt de parameter gesnoven, maar is het gegenereerde plan suboptimaal voor de volledige set parameterwaarden in een werkbelasting. Microsoft bevat queryhints (richtlijnen) zodat u opzettelijke intentie opgeven en het standaardgedrag van parametersnuiven overschrijven. Vaak u, als u hints gebruikt, aanvragen oplossen waarin het standaard SQL Server- of Azure SQL-database-gedrag onvolmaakt is voor een specifieke klantworkload.

In het volgende voorbeeld wordt uitgelegd hoe de queryprocessor een plan kan genereren dat suboptimaal is, zowel voor prestaties als resourcevereisten. In dit voorbeeld wordt ook weergegeven dat als u een queryhint gebruikt, u de queryruntijd en resourcevereisten voor uw SQL-database verminderen:

```sql
DROP TABLE psptest1;
CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
   WHILE @a < 20000
   BEGIN
     INSERT INTO psptest1(col2) values (1);
     INSERT INTO psptest1(col2) values (@a);
     SET @a += 1;
   END
   COMMIT TRANSACTION
   CREATE INDEX i1 on psptest1(col2);
GO

CREATE PROCEDURE psp1 (@param1 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1
      WHERE col2 = @param1
      ORDER BY col2;
    END
    GO

CREATE PROCEDURE psp2 (@param2 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
      ORDER BY col2
      OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
   END
   GO

CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
GO
```

De installatiecode maakt een tabel met een scheefstaande gegevensdistributie. Het optimale queryplan verschilt op basis van welke parameter is geselecteerd. Helaas compileert het gedrag van de plancache niet altijd de query opnieuw op basis van de meest voorkomende parameterwaarde. Het is dus mogelijk dat een suboptimaal plan in de cache wordt opgeslagen en voor veel waarden wordt gebruikt, zelfs wanneer een ander plan gemiddeld een betere plankeuze kan zijn. Vervolgens maakt het queryplan twee opgeslagen procedures die identiek zijn, behalve dat er een speciale queryhint heeft.

```sql
-- Prime Procedure Cache with scan plan
EXEC psp1 @param1=1;
TRUNCATE TABLE t1;

-- Iterate multiple times to show the performance difference
DECLARE @i int = 0;
WHILE @i < 1000
   BEGIN
      EXEC psp1 @param1=2;
      TRUNCATE TABLE t1;
      SET @i += 1;
    END
```

We raden u aan minstens 10 minuten te wachten voordat u aan deel 2 van het voorbeeld begint, zodat de resultaten duidelijk zijn in de resulterende telemetriegegevens.

```sql
EXEC psp2 @param2=1;
TRUNCATE TABLE t1;

DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END
```

Elk deel van dit voorbeeld probeert 1000 keer een parameterinstructie uit te voeren (om een voldoende belasting te genereren om als testgegevensset te gebruiken). Wanneer de queryprocessor opgeslagen procedures uitvoert, onderzoekt hij de parameterwaarde die tijdens de eerste compilatie aan de procedure wordt doorgegeven (parameter 'sniffing'). De processor caches het resulterende plan en gebruikt het voor latere aanroepen, zelfs als de parameterwaarde anders is. Het optimale plan wordt mogelijk niet in alle gevallen gebruikt. Soms moet u de optimizer begeleiden om een plan te kiezen dat beter is voor de gemiddelde aanvraag in plaats van het specifieke geval van het moment waarop de query voor het eerst werd gecompileerd. In dit voorbeeld genereert het oorspronkelijke plan een 'scanplan' dat alle rijen leest om elke waarde te vinden die overeenkomt met de parameter:

![Queryafstemming met behulp van een scanplan](./media/sql-database-performance-guidance/query_tuning_1.png)

Omdat we de procedure hebben uitgevoerd met behulp van de waarde 1, was het resulterende plan optimaal voor de waarde 1, maar was suboptimaal voor alle andere waarden in de tabel. Het resultaat is waarschijnlijk niet wat je zou willen als je elk plan willekeurig zou kiezen, omdat het plan langzamer presteert en meer resources gebruikt.

Als u de `SET STATISTICS IO` test `ON`uitvoert met ingesteld op , wordt het logische scanwerk in dit voorbeeld achter de schermen uitgevoerd. U zien dat er 1.148 reads gedaan door het plan (dat is inefficiënt, als de gemiddelde zaak is om slechts een rij terug):

![Queryafstemming met behulp van een logische scan](./media/sql-database-performance-guidance/query_tuning_2.png)

In het tweede deel van het voorbeeld wordt een queryhint gebruikt om de optimizer te vertellen een specifieke waarde te gebruiken tijdens het compilatieproces. In dit geval dwingt het de queryprocessor om de waarde die `UNKNOWN`als parameter wordt doorgegeven te negeren en in plaats daarvan aan te nemen . Dit verwijst naar een waarde met de gemiddelde frequentie in de tabel (het negeren van scheeftrekken). Het resulterende plan is een op zoek gebaseerd plan dat sneller is en gemiddeld minder resources gebruikt dan het plan in deel 1 van dit voorbeeld:

![Queryafstemming met behulp van een queryhint](./media/sql-database-performance-guidance/query_tuning_3.png)

U het effect zien in de **tabel sys.resource_stats** (er is een vertraging vanaf het moment dat u de test uitvoert en wanneer de gegevens de tabel vullen). In dit voorbeeld wordt deel 1 uitgevoerd tijdens het tijdvenster 22:25:00 en deel 2 uitgevoerd om 22:35:00. Het eerdere tijdvenster gebruikte meer resources in dat tijdvenster dan het latere venster (vanwege verbeteringen in de planefficiëntie).

```sql
SELECT TOP 1000 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Voorbeeldresultaten van queryafstemming](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> Hoewel het volume in dit voorbeeld opzettelijk klein is, kan het effect van suboptimale parameters aanzienlijk zijn, vooral op grotere databases. Het verschil, in extreme gevallen, kan tussen seconden voor snelle gevallen en uren voor langzame gevallen zijn.

U **sys.resource_stats** onderzoeken om te bepalen of de resource voor een test meer of minder resources gebruikt dan een andere test. Wanneer u gegevens vergelijkt, scheidt u de timing van tests, zodat ze zich niet in hetzelfde venster van 5 minuten bevinden in de **sys.resource_stats-weergave.** Het doel van de oefening is om de totale hoeveelheid gebruikte resources te minimaliseren en niet om de piekresources te minimaliseren. Over het algemeen vermindert het optimaliseren van een stukje code voor latentie ook het verbruik van resources. Zorg ervoor dat de wijzigingen die u in een toepassing aanbrengt noodzakelijk zijn en dat de wijzigingen geen negatieve invloed hebben op de klantervaring voor iemand die mogelijk queryhints in de toepassing gebruikt.

Als een werkbelasting een set herhalende query's heeft, is het vaak zinvol om de optimaliteit van uw plankeuzes vast te leggen en te valideren, omdat hiermee de minimale resourcegrootte-eenheid wordt aangegaan die nodig is om de database te hosten. Nadat u het hebt gevalideerd, moet u af en toe de plannen opnieuw bekijken om ervoor te zorgen dat ze niet zijn afgebroken. Meer informatie over [queryhints (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx)vindt u.

### <a name="very-large-database-architectures"></a>Zeer grote databasearchitecturen

Vóór de release van [Hyperscale-servicelaag](sql-database-service-tier-hyperscale.md) voor afzonderlijke databases in Azure SQL Database, hebben klanten capaciteitslimieten voor afzonderlijke databases bereikt. Deze capaciteitslimieten bestaan nog steeds voor gepoolde databases in elastische pools en instantiedatabase in beheerde exemplaren. In de volgende twee secties worden twee opties besproken voor het oplossen van problemen met zeer grote databases in Azure SQL Database wanneer u de hyperscale-servicelaag niet gebruiken.

### <a name="cross-database-sharding"></a>Cross-database sharding

Omdat Azure SQL Database draait op commodity-hardware, zijn de capaciteitslimieten voor een afzonderlijke database lager dan voor een traditionele on-premises SQL Server-installatie. Sommige klanten gebruiken shardingtechnieken om databasebewerkingen over meerdere databases te verspreiden wanneer de bewerkingen niet binnen de grenzen van een afzonderlijke database in Azure SQL Database passen. De meeste klanten die shardingtechnieken gebruiken in Azure SQL Database splitsen hun gegevens in één dimensie over meerdere databases. Voor deze benadering moet u begrijpen dat OLTP-toepassingen vaak transacties uitvoeren die van toepassing zijn op slechts één rij of op een kleine groep rijen in het schema.

> [!NOTE]
> SQL Database biedt nu een bibliotheek om te helpen bij het hardmaken. Zie [Overzicht van de elastic database-clientbibliotheek](sql-database-elastic-database-client-library.md)voor meer informatie.

Als een database bijvoorbeeld klantnaam-, order- en ordergegevens bevat (zoals het traditionele voorbeeld Northwind-database die wordt verzonden met SQL Server), u deze gegevens splitsen in meerdere databases door een klant te groeperen met de bijbehorende order- en orderdetails Informatie. U garanderen dat de gegevens van de klant in een individuele database blijven. De toepassing zou verschillende klanten over databases splitsen, effectief verspreiden van de belasting over meerdere databases. Met sharding kunnen klanten niet alleen de maximale limiet voor databasegrootte vermijden, maar azure SQL Database kan ook workloads verwerken die aanzienlijk groter zijn dan de limieten van de verschillende rekengroottes, zolang elke afzonderlijke database in zijn DTU past.

Hoewel databasesharding de totale resourcecapaciteit voor een oplossing niet vermindert, is het zeer effectief in het ondersteunen van zeer grote oplossingen die over meerdere databases zijn verspreid. Elke database kan worden uitgevoerd op een andere rekengrootte ter ondersteuning van zeer grote, "effectieve" databases met hoge resourcevereisten.

#### <a name="functional-partitioning"></a>Functionele partitionering

SQL Server-gebruikers combineren vaak veel functies in een afzonderlijke database. Als een toepassing bijvoorbeeld logica heeft om voorraad voor een winkel te beheren, kan die database logica hebben die is gekoppeld aan voorraad, het bijhouden van inkooporders, opgeslagen procedures en geïndexeerde of gematerialiseerde weergaven die rapportage aan het einde van de maand beheren. Deze techniek maakt het eenvoudiger om de database te beheren voor bewerkingen zoals back-up, maar het vereist ook dat u de grootte van de hardware om de piekbelasting over alle functies van een toepassing te behandelen.

Als u een scale-outarchitectuur gebruikt in Azure SQL Database, is het een goed idee om verschillende functies van een toepassing op te splitsen in verschillende databases. Door deze techniek te gebruiken, schaalt elke toepassing onafhankelijk. Naarmate een toepassing drukker wordt (en de belasting van de database toeneemt), kan de beheerder onafhankelijke rekengroottes kiezen voor elke functie in de toepassing. Aan de limiet, met deze architectuur, kan een toepassing groter zijn dan een enkele commodity-machine aankan omdat de belasting over meerdere machines is verdeeld.

### <a name="batch-queries"></a>Batchquery's

Voor toepassingen die toegang hebben tot gegevens met behulp van grote, frequente ad-hocquery's, wordt een aanzienlijke hoeveelheid reactietijd besteed aan netwerkcommunicatie tussen de toepassingslaag en de azure SQL-databaselaag. Zelfs wanneer zowel de toepassing als Azure SQL Database zich in hetzelfde datacenter bevinden, kan de netwerklatentie tussen de twee worden vergroot door een groot aantal gegevenstoegangsbewerkingen. Als u de retourritten van het netwerk voor de gegevenstoegangsbewerkingen wilt verminderen, u de optie gebruiken om de ad-hocquery's te batcheren of deze samen te stellen als opgeslagen procedures. Als u de ad-hocquery's batcheert, u meerdere query's verzenden als één grote batch in één reis naar Azure SQL Database. Als u ad-hocquery's samenstelt in een opgeslagen procedure, u hetzelfde resultaat bereiken als wanneer u ze batcht. Het gebruik van een opgeslagen procedure geeft u ook het voordeel dat u de kans vergroot dat de queryplannen in Azure SQL Database worden caching, zodat u de opgeslagen procedure opnieuw gebruiken.

Sommige toepassingen zijn schrijfintensief. Soms u de totale IO-belasting op een database verminderen door na te denken over het samen batchschrijven. Vaak is dit net zo eenvoudig als het gebruik van expliciete transacties in plaats van automatisch vastleggen transacties in opgeslagen procedures en ad hoc batches. Zie [Batching-technieken voor SQL Database-toepassingen in Azure voor](sql-database-use-batching-to-improve-performance.md)een evaluatie van verschillende technieken die u gebruiken. Experimenteer met uw eigen werkbelasting om het juiste model voor batching te vinden. Zorg ervoor dat u begrijpt dat een model mogelijk iets andere transactionele consistentiegaranties heeft. Het vinden van de juiste werkbelasting die het gebruik van resources minimaliseert, vereist het vinden van de juiste combinatie van consistentie en trade-offs voor prestaties.

### <a name="application-tier-caching"></a>Caching op toepassingsniveau

Sommige databasetoepassingen hebben leeszware workloads. Caching-lagen kunnen de belasting van de database verminderen en kunnen mogelijk de rekengrootte verminderen die nodig is om een database te ondersteunen met Azure SQL Database. Met [Azure Cache voor Redis](https://azure.microsoft.com/services/cache/)u, als u een leeszware werkbelasting hebt, de gegevens één keer lezen (of misschien één keer per machine op toepassingsniveau, afhankelijk van hoe deze is geconfigureerd) en die gegevens vervolgens buiten uw SQL-database opslaan. Dit is een manier om de belasting van de database (CPU en lees IO) te verminderen, maar er is een effect op de consistentie van de transacties, omdat de gegevens die uit de cache worden gelezen mogelijk niet synchroon lopen met de gegevens in de database. Hoewel in veel toepassingen een bepaald niveau van inconsistentie aanvaardbaar is, geldt dat niet voor alle workloads. U moet alle toepassingsvereisten volledig begrijpen voordat u een cachingstrategie op toepassingsniveau implementeert.

## <a name="next-steps"></a>Volgende stappen

- Zie [DTU-gebaseerd inkoopmodel](sql-database-service-tiers-dtu.md)voor meer informatie over op DTU gebaseerde servicelagen.
- Zie [vCore-gebaseerd inkoopmodel voor](sql-database-service-tiers-vcore.md)meer informatie over vCore-gebaseerde servicelagen.
- Zie Wat is een [Azure-elastische pool voor](sql-database-elastic-pool.md) meer informatie over elastische pools?
- Zie [Wanneer u een elastische pool moet overwegen](sql-database-elastic-pool-guidance.md) voor informatie over prestaties en elastische pools
