---
title: In-Memory-technologieën
description: Azure SQL Database In-Memory-technologieën verbeteren de prestaties van transactionele en analytische workloads aanzienlijk.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/19/2019
ms.openlocfilehash: de60712451d4c2e8a7d931f7a09352f55be05694
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73810264"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>Prestaties optimaliseren met In-Memory-technologieën in SQL Database

Met in-memorytechnologieën in Azure SQL Database u de prestaties van uw toepassing verbeteren en de kosten van uw database mogelijk verlagen. 

## <a name="when-to-use-in-memory-technologies"></a>Wanneer gebruik je In-Memory-technologieën?

Door In-Memory-technologieën in Azure SQL Database te gebruiken, u prestatieverbeteringen realiseren met verschillende workloads:

- **Transactionele** (online transactionele verwerking (OLTP)) waarbij de meeste aanvragen kleinere gegevens (bijvoorbeeld CRUD-bewerkingen) lezen of bijwerken.
- **Analytische** (online analytische verwerking (OLAP)) waarbij de meeste query's complexe berekeningen hebben voor de rapportagedoeleinden, met een bepaald aantal query's die gegevens laden en toevoegen aan de bestaande tabellen (de zogenaamde bulkbelasting) of de gegevens uit de tabellen verwijderen. 
- **Gemengde** (hybride transactie/analytische verwerking (HTAP)) waarbij zowel OLTP- als OLAP-query's op dezelfde set gegevens worden uitgevoerd.

In-memory technologieën kunnen de prestaties van deze workloads verbeteren door de gegevens die moeten worden verwerkt in het geheugen te bewaren, met behulp van native compilatie van de query's, of geavanceerde verwerking, zoals batchverwerking en SIMD-instructies die beschikbaar zijn op de onderliggende hardware. 

## <a name="overview"></a>Overzicht

Azure SQL Database heeft de volgende In-Memory-technologieën:
- *[In-Memory OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)* verhoogt het aantal transacties per seconde en vermindert de latentie voor transactieverwerking. Scenario's die profiteren van In-Memory OLTP zijn: transactionprocessing met een hoge doorvoer, zoals handel en gaming, gegevensopname van gebeurtenissen of IoT-apparaten, caching, gegevensbelasting en tijdelijke tabel- en tabelvariabele scenario's.
- *Geclusterde columnstore-indexen* verkleinen uw opslagvoetafdruk (tot 10 keer) en verbeteren de prestaties voor rapportage- en analysequery's. U het gebruiken met facttabellen in uw gegevensmarts om meer gegevens in uw database te passen en de prestaties te verbeteren. Ook u het gebruiken met historische gegevens in uw operationele database te archiveren en in staat zijn om query tot 10 keer meer gegevens.
- *Niet-geclusterde columnstore-indexen* voor HTAP helpen u om realtime inzicht te krijgen in uw bedrijf door de operationele database rechtstreeks op te vragen, zonder dat u een duur extract-, transformatie- en load-proces (ETL) hoeft uit te voeren en te wachten tot het gegevensmagazijn is ingevuld. Niet-geclusterde columnstore-indexen maken een snelle uitvoering van analysequery's op de OLTP-database mogelijk, terwijl de impact op de operationele werkbelasting wordt verminderd.
- *Met geheugengeoptimaliseerde geclusterde columnstore-indexen* voor HTAP u snelle transactieverwerking uitvoeren en *tegelijkertijd* analysesquery's zeer snel uitvoeren op dezelfde gegevens.

Beide columnstore-indexen en In-Memory OLTP maken sinds respectievelijk 2012 en 2014 deel uit van het SQL Server-product. Azure SQL Database en SQL Server delen dezelfde implementatie van In-Memory-technologieën. In de toekomst worden nieuwe mogelijkheden voor deze technologieën eerst uitgebracht in Azure SQL Database, voordat ze worden uitgebracht in SQL Server.

## <a name="benefits-of-in-memory-technology"></a>Voordelen van in-memory technologie

Vanwege de efficiëntere query- en transactieverwerking helpen In-Memory-technologieën u ook om de kosten te verlagen. U hoeft doorgaans de prijscategorie van de database niet te upgraden om prestatieverbeteringen te behalen. In sommige gevallen u zelfs de prijscategorie verlagen, terwijl u nog steeds prestatieverbeteringen ziet met In-Memory-technologieën.

Hier zijn twee voorbeelden van hoe In-Memory OLTP geholpen om de prestaties aanzienlijk te verbeteren:

- Door gebruik te maken van In-Memory OLTP [kon Quorum Business Solutions hun werklast verdubbelen en tegelijkertijd de DTU's met 70% verbeteren.](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

  - DTU betekent *database transactie-eenheid,* en het omvat een meting van het verbruik van hulpbronnen.
- De volgende video toont een aanzienlijke verbetering van het resourceverbruik met een voorbeeldworkload: [In-Memory OLTP in Azure SQL Database Video](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB).
  - Zie het blogbericht [In-Memory OLTP in Azure SQL Database Blog Post](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/) voor meer informatie

> [!NOTE]  
> In-Memory-technologieën zijn beschikbaar in Azure SQL-databases met Premium- en Bedrijfskritische lagen en Premium-elastische pools.

De volgende video legt mogelijke prestatieverbeteringen uit met In-Memory-technologieën in Azure SQL Database. Houd er rekening mee dat de prestatiewinst die u ziet altijd afhankelijk is van vele factoren, waaronder de aard van de werkbelasting en gegevens, het toegangspatroon van de database, enzovoort.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

In dit artikel worden aspecten beschreven van OLTP- en columnstore-indexen in geheugengeheugen die specifiek zijn voor Azure SQL Database en bevat het ook voorbeelden:

- U ziet de impact van deze technologieën op de limieten voor opslag en gegevensgrootte.
- U ziet hoe u de verplaatsing beheert van databases die deze technologieën tussen de verschillende prijsniveaus gebruiken.
- U ziet twee voorbeelden die het gebruik van In-Memory OLTP illustreren, evenals columnstore-indexen in Azure SQL Database.

Zie voor meer informatie:

- [In-Memory OLTP-overzichts- en gebruiksscenario's](https://msdn.microsoft.com/library/mt774593.aspx) (inclusief verwijzingen naar casestudies van klanten en informatie om aan de slag te gaan)
- [Documentatie voor in-memory OLTP](https://msdn.microsoft.com/library/dn133186.aspx)
- [Columnstore Indexen Gids](https://msdn.microsoft.com/library/gg492088.aspx)
- Hybride transactionele/analytische verwerking (HTAP), ook wel [real-time operationele analyse genoemd](https://msdn.microsoft.com/library/dn817827.aspx)

## <a name="in-memory-oltp"></a>In-memory OLTP

In-memory OLTP-technologie biedt extreem snelle gegevenstoegangsbewerkingen door alle gegevens in het geheugen te bewaren. Het maakt ook gebruik van gespecialiseerde indexen, native compilatie van query's en toegang tot gegevens zonder vergrendeling om de prestaties van de OLTP-workload te verbeteren. Er zijn twee manieren om uw OlTP-gegevens in het geheugen te ordenen:

- **Geheugengeoptimaliseerde rijopslagindeling** waarbij elke rij een afzonderlijk geheugenobject is. Dit is een klassiek In-Memory OLTP-formaat dat is geoptimaliseerd voor krachtige OLTP-workloads. Er zijn twee typen geheugengeoptimaliseerde tabellen die kunnen worden gebruikt in de voor geheugen geoptimaliseerde rowstore-indeling:
  - *Duurzame tabellen* (SCHEMA_AND_DATA) waar de rijen die in het geheugen zijn geplaatst, behouden blijven na het opnieuw opstarten van de server. Dit type tabellen gedraagt zich als een traditionele rowstore-tabel met de extra voordelen van optimalisaties in het geheugen.
  - *Niet-duurzame tabellen* (SCHEMA_ONLY) waar de rijen na het opnieuw opstarten niet behouden blijven. Dit type tabel is ontworpen voor tijdelijke gegevens (bijvoorbeeld vervanging van tijdelijke tabellen) of tabellen waarbij u snel gegevens moet laden voordat u deze verplaatst naar een aantal volgehouden tabel (zogenaamde faseringstabellen).
- **Met geheugen geoptimaliseerde kolomarchiefindeling** waarbij gegevens zijn ingedeeld in een kolomindeling. Deze structuur is ontworpen voor HTAP-scenario's waarbij u analytische query's moet uitvoeren op dezelfde gegevensstructuur waarin uw OLTP-werkbelasting wordt uitgevoerd.

> [!Note]
> In-Memory OLTP-technologie is ontworpen voor de gegevensstructuren die volledig in het geheugen kunnen verblijven. Aangezien de gegevens in het geheugen niet op de schijf kunnen worden verwijderd, moet u ervoor zorgen dat u een database gebruikt die voldoende geheugen heeft. Zie [Gegevensgrootte en opslagdop voor OLTP in het geheugen](#data-size-and-storage-cap-for-in-memory-oltp) voor meer informatie.

Een snelle inleiding op In-Memory OLTP: [Quickstart 1: In-Memory OLTP Technologies for Faster T-SQL Performance](https://msdn.microsoft.com/library/mt694156.aspx) (een ander artikel om u op weg te helpen)

Diepgaande video's over de technologieën:

- [In-Memory OLTP in Azure SQL Database](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (die een demo van prestatievoordelen en stappen bevat om deze resultaten zelf te reproduceren)
- [In-Memory OLTP Video's: Wat het is en wanneer / hoe het te gebruiken](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)

Er is een programmatische manier om te begrijpen of een bepaalde database In-Memory OLTP ondersteunt. U de volgende Transact-SQL-query uitvoeren:
```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```
Als de query **1**retourneert, wordt IN-Memory OLTP ondersteund in deze database. In de volgende query's worden alle objecten geïdentificeerd die moeten worden verwijderd voordat een database kan worden gedegradeerd tot Standaard/Basic:
```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Gegevensgrootte en opslagdop voor in-memory OLTP

In-Memory OLTP bevat geheugengeoptimaliseerde tabellen, die worden gebruikt voor het opslaan van gebruikersgegevens. Deze tabellen zijn vereist om in het geheugen te passen. Omdat u het geheugen rechtstreeks beheert in de SQL Database-service, hebben we het concept van een quotum voor gebruikersgegevens. Dit idee wordt aangeduid als *In-Memory OLTP-opslag*.

Elke ondersteunde prijscategorie voor één database en elke prijscategorie elastische pool omvat een bepaalde hoeveelheid OLTP-opslag in het geheugen. Zie [Op DTU gebaseerde resourcelimieten - enkele database,](sql-database-dtu-resource-limits-single-databases.md) [Op DTU gebaseerde resourcelimieten - elastische pools,](sql-database-dtu-resource-limits-elastic-pools.md)[vCore-gebaseerde resourcelimieten - afzonderlijke databases](sql-database-vcore-resource-limits-single-databases.md) en [vCore-gebaseerde resourcelimieten - elastische pools](sql-database-vcore-resource-limits-elastic-pools.md).

De volgende items tellen mee voor uw OLTP-opslagdop in het geheugen:

- Rijen voor actieve gebruikersgegevens in geheugengeoptimaliseerde tabellen en tabelvariabelen. Houd er rekening mee dat oude rijversies niet meetellen voor de dop.
- Indexen op geheugengeoptimaliseerde tabellen.
- Operationele overhead van ALTER TABLE-bewerkingen.

Als u de limiet bereikt, ontvangt u een fout buiten het quotum en u geen gegevens meer invoegen of bijwerken. Als u deze fout wilt beperken, verwijdert u gegevens of verhoogt u de prijscategorie van de database of groep.

Zie [Monitor opslag in](sql-database-in-memory-oltp-monitoring.md)het geheugen voor meer informatie over het bewaken van het in-memory OLTP-opslaggebruik en het configureren van waarschuwingen wanneer u bijna op de dop staat.

#### <a name="about-elastic-pools"></a>Over elastische zwembaden

Met elastische pools wordt de In-Memory OLTP-opslag gedeeld in alle databases in de groep. Daarom kan het gebruik in één database mogelijk van invloed zijn op andere databases. Twee oplossingen hiervoor zijn:

- Configureer `Max-eDTU` `MaxvCore` een of voor databases die lager zijn dan het aantal eDTU of vCore voor de groep als geheel. Met dit maximum wordt het IN-Memory OLTP-opslaggebruik in elke database in de groep gecaponeerd tot de grootte die overeenkomt met het aantal eDTU's.
- Een `Min-eDTU` of `MinvCore` configureren die groter is dan 0. Dit garandeert minimaal dat elke database in de groep de hoeveelheid beschikbare OLTP-opslag in het geheugen heeft die overeenkomt met de geconfigureerde `Min-eDTU` of `vCore`.

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>Servicelagen wijzigen van databases die gebruikmaken van In-Memory OLTP-technologieën

U uw database of instantie altijd upgraden naar een hogere laag, zoals van Algemeen Doel naar Bedrijfskritiek (of Standaard naar Premium). De beschikbare functionaliteit en resources nemen alleen maar toe.

Maar het verlagen van de laag kan een negatieve invloed hebben op uw database. De impact is vooral zichtbaar wanneer u degradeert van Business Critical naar Algemeen Doel (of Premium naar Standard of Basic) wanneer uw database OLTP-objecten in het geheugen bevat. Geheugengeoptimaliseerde tabellen zijn niet beschikbaar na de downgrade (zelfs als ze zichtbaar blijven). Dezelfde overwegingen zijn van toepassing wanneer u de prijscategorie van een elastische groep verlaagt of een database met In-Memory-technologieën verplaatst naar een elastische groep Standaard of Basis.

> [!Important]
> In-Memory OLTP wordt niet ondersteund in de laag Algemeen Doel, Standaard of Basic. Daarom is het niet mogelijk om een database met OLTP-objecten in het geheugen naar de standaard- of basic-laag te verplaatsen.

Voordat u de database downgraden naar Standard/Basic, verwijdert u alle voor geheugen geoptimaliseerde tabellen en tabeltypen, evenals alle native gecompileerde T-SQL-modules. 

*Schalen naar resources in business critical tier:* Gegevens in geheugengeoptimaliseerde tabellen moeten passen binnen de In-Memory OLTP-opslag die is gekoppeld aan de laag van de database of Beheerde instantie, of deze is beschikbaar in de elastische groep. Als u de laag probeert te verkleinen of de database wilt verplaatsen naar een groep die niet voldoende beschikbare OLTP-opslag in het geheugen heeft, mislukt de bewerking.

## <a name="in-memory-columnstore"></a>Kolomarchief in het geheugen

In-memory columnstore-technologie stelt u in staat om een grote hoeveelheid gegevens in de tabellen op te slaan en op te vragen. Columnstore-technologie maakt gebruik van kolomgebaseerde gegevensopslagindeling en batchqueryverwerking om tot 10 keer de queryprestaties in OLAP-workloads te behalen ten opzichte van traditionele celgeoriënteerde opslag. U ook winsten tot 10 keer de gegevenscompressie bereiken over de niet-gecomprimeerde gegevensgrootte.
Er zijn twee typen kolomarchiefmodellen die u gebruiken om uw gegevens te ordenen:

- **Geclusterde kolomarchief** waar alle gegevens in de tabel zijn ingedeeld in de kolomindeling. In dit model worden alle rijen in de tabel in kolomindeling geplaatst die de gegevens sterk comprimeert en u in staat stelt om snelle analytische query's en rapporten op de tabel uit te voeren. Afhankelijk van de aard van uw gegevens kan de grootte van uw gegevens 10x-100x worden verlaagd. Clustered columnstore model maakt het ook mogelijk om een grote hoeveelheid gegevens (bulk-load) snel in te nemen, omdat grote batches van gegevens groter dan 100K rijen worden gecomprimeerd voordat ze worden opgeslagen op schijf. Dit model is een goede keuze voor de klassieke data warehouse scenario's. 
- **Niet-geclusterde kolomarchief** waar de gegevens worden opgeslagen in de traditionele rowstore-tabel en er een index in de kolomarchiefindeling is die wordt gebruikt voor de analytische query's. Dit model maakt Hybrid Transactional-Analytic Processing (HTAP) mogelijk: de mogelijkheid om performante realtime analyses uit te voeren op een transactionele workload. OLTP-query's worden uitgevoerd op de rowstore-tabel die is geoptimaliseerd voor toegang tot een kleine set rijen, terwijl OLAP-query's worden uitgevoerd op de kolomarchiefindex, wat een betere keuze is voor scans en analyses. Azure SQL Database Query optimizer kiest dynamisch rowstore of columnstore-indeling op basis van de query. Niet-geclusterde kolomarchiefindexen verkleinen de grootte van de gegevens niet, omdat de oorspronkelijke gegevensset zonder enige wijziging in de oorspronkelijke rowstore-tabel wordt bewaard. De grootte van de extra kolomarchiefindex moet echter in orde van grootte kleiner zijn dan de gelijkwaardige B-tree-index.

> [!Note]
> In-memory columnstore-technologie bewaart alleen de gegevens die nodig zijn voor verwerking in het geheugen, terwijl de gegevens die niet in het geheugen passen, op de schijf worden opgeslagen. Daarom kan de hoeveelheid gegevens in in-memory columnstore-structuren de hoeveelheid beschikbaar geheugen overschrijden. 

Diepgaande video over de technologie:

- [Columnstore Index: In-Memory Analytics Video's van Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Gegevensgrootte en opslag voor kolomarchiefindexen

Columnstore-indexen hoeven niet in het geheugen te passen. Daarom is de enige limiet op de grootte van de indexen de maximale totale databasegrootte, die is gedocumenteerd in het [Op DTU gebaseerde inkoopmodel](sql-database-service-tiers-dtu.md) en [vCore-gebaseerde inkoopmodelartikelen.](sql-database-service-tiers-vcore.md)

Wanneer u geclusterde kolomarchiefindexen gebruikt, wordt kolomcompressie gebruikt voor de opslag van de basistabel. Deze compressie kan de opslagvoetafdruk van uw gebruikersgegevens aanzienlijk verminderen, wat betekent dat u meer gegevens in de database plaatsen. En de compressie kan verder worden verhoogd met [columnar archivering compressie](https://msdn.microsoft.com/library/cc280449.aspx#using-columnstore-and-columnstore-archive-compression). De hoeveelheid compressie die u bereiken, is afhankelijk van de aard van de gegevens, maar 10 keer de compressie is niet ongewoon.

Als u bijvoorbeeld een database hebt met een maximale grootte van 1 terabyte (TB) en u bereikt 10 keer de compressie met behulp van kolomarchiefindexen, u in totaal 10 TB aan gebruikersgegevens in de database plaatsen.

Wanneer u niet-geclusterde kolomarchiefindexen gebruikt, wordt de basistabel nog steeds opgeslagen in de traditionele rijstore-indeling. Daarom zijn de opslagbesparingen niet zo groot als bij geclusterde kolomarchiefindexen. Als u echter een aantal traditionele niet-geclusterde indexen vervangt door één kolomarchiefindex, u nog steeds een algemene besparing zien in de opslagvoetafdruk voor de tabel.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Servicelagen wijzigen van databases met Kolomarchief-indexen

*Het downgraden van één database naar Basic of Standard* is mogelijk niet mogelijk als uw doellaag lager is dan S3. Columnstore-indexen worden alleen ondersteund op de prijslaag Business Critical/Premium en op de standaardlaag, S3 en hoger, en niet op de basislaag. Wanneer u uw database downgraden naar een niet-ondersteunde laag of niveau, wordt uw columnstore-index niet meer beschikbaar. Het systeem onderhoudt uw columnstore-index, maar maakt nooit gebruik van de index. Als u later een upgrade uitvoert naar een ondersteunde laag of niveau, is uw kolomarchiefindex onmiddellijk klaar om opnieuw te worden gebruikt.

Als u een **geclusterde** kolomarchiefindex hebt, wordt de hele tabel niet meer beschikbaar na de downgrade. Daarom raden we u aan alle *geclusterde* kolomarchiefindexen te laten vallen voordat u uw database downgraden naar een niet-ondersteunde laag of niveau.

> [!Note]
> Managed Instance ondersteunt ColumnStore-indexen in alle lagen.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>Volgende stappen

- [Quickstart 1: In-Memory OLTP-technologieën voor snellere T-SQL-prestaties](https://msdn.microsoft.com/library/mt694156.aspx)
- [In-Memory OLTP gebruiken in een bestaande Azure SQL-toepassing](sql-database-in-memory-oltp-migration.md)
- [Monitor In-Memory OLTP-opslag](sql-database-in-memory-oltp-monitoring.md) voor in-memory OLTP
- [In-memoryfuncties uitproberen in Azure SQL Database](sql-database-in-memory-sample.md)

## <a name="additional-resources"></a>Aanvullende bronnen

### <a name="deeper-information"></a>Diepere informatie

- [Ontdek hoe Quorum de werkbelasting van de sleuteldatabase verdubbelt en DTU met 70% verlaagt met In-Memory OLTP in SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [In-Memory OLTP in Azure SQL Database Blog Post](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)
- [Meer informatie over in-memory OLTP](https://msdn.microsoft.com/library/dn133186.aspx)
- [Meer informatie over kolomarchiefindexen](https://msdn.microsoft.com/library/gg492088.aspx)
- [Meer informatie over realtime operationele analyses](https://msdn.microsoft.com/library/dn817827.aspx)
- Zie [algemene werkbelastingpatronen en migratieoverwegingen](https://msdn.microsoft.com/library/dn673538.aspx) (waarin werkbelastingpatronen worden beschreven waarin OLTP in het geheugen vaak aanzienlijke prestatieverbeteringen oplevert)

### <a name="application-design"></a>Toepassingsontwerp

- [In-Memory OLTP (In-Memory Optimization)](https://msdn.microsoft.com/library/dn133186.aspx)
- [In-Memory OLTP gebruiken in een bestaande Azure SQL-toepassing](sql-database-in-memory-oltp-migration.md)

### <a name="tools"></a>Hulpprogramma's

- [Azure-portal](https://portal.azure.com/)
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
