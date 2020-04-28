---
title: In-Memory technologieën
description: Azure SQL Database in-Memory technologieën worden de prestaties van transactionele en analytische workloads aanzienlijk verbeterd.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73810264"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>Prestaties optimaliseren met in-Memory technologieën in SQL Database

In de geheugen technologieën in Azure SQL Database kunt u de prestaties van uw toepassing verbeteren en mogelijk de kosten van uw data base verminderen. 

## <a name="when-to-use-in-memory-technologies"></a>Wanneer u in-Memory technologieën wilt gebruiken

Door gebruik te maken van in-Memory technologieën in Azure SQL Database kunt u prestatie verbeteringen aantreffen met verschillende workloads:

- **Transactionele** (online transactionele verwerking (OLTP)) waarbij de meeste aanvragen een kleinere set gegevens lezen of bijwerken (bijvoorbeeld ruwe bewerkingen).
- **Analytisch** (online ANALYTICAL processing (OLAP)) waarbij de meeste query's complexe berekeningen hebben voor de rapportage doeleinden, met een bepaald aantal query's waarmee gegevens worden geladen en toegevoegd aan de bestaande tabellen (dus bulksgewijs laden genoemd) of de gegevens uit de tabellen worden verwijderd. 
- **Gemengde** (hybride trans actie/Analytical Processing (HTAP)) waarbij zowel OLTP-als OLAP-query's worden uitgevoerd op dezelfde set gegevens.

In-Memory technologieën kunnen de prestaties van deze werk belastingen verbeteren door de gegevens die moeten worden verwerkt in het geheugen te bewaren, met behulp van systeem eigen compilatie van de query's of geavanceerde verwerking, zoals batch verwerking en SIMD-instructies die beschikbaar zijn op de onderliggende hardware. 

## <a name="overview"></a>Overzicht

Azure SQL Database heeft de volgende in-Memory technologieën:
- *[In-Memory OLTP neemt het](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)* aantal trans acties per seconde toe en vermindert de latentie voor de verwerking van trans acties. Scenario's die gebruikmaken van in-Memory OLTP zijn: transactie verwerking met hoge door Voer, zoals handel en gaming, gegevens opname van gebeurtenissen of IoT-apparaten, caching, gegevens belasting en tijdelijke tabel-en tabel variabelen scenario's.
- *Geclusterde column Store-indexen* verminderen uw opslag ruimte (Maxi maal 10 keer) en verbetert de prestaties voor rapportage-en analyse query's. U kunt het gebruiken met feiten tabellen in uw data bases voor het aanpassen van meer gegevens in uw gegevens bron en de prestaties te verbeteren. Daarnaast kunt u het gebruiken met historische gegevens in uw operationele data base om te archiveren en om Maxi maal tien keer meer gegevens op te vragen.
- Niet- *geclusterde column Store-indexen* voor HTAP helpen u real-time inzicht te krijgen in uw bedrijf door rechtstreeks query's uit te voeren op de operationele data base, zonder dat u een kostbaar proces voor extra heren, transformeren en laden (ETL) moet uitvoeren en moet wachten tot het Data Warehouse is gevuld. Met niet-geclusterde column Store-indexen kunnen analyse query's snel worden uitgevoerd op de OLTP-data base, en wordt de impact op de operationele werk belasting verminderd.
- Met *geheugen geoptimaliseerde column Store-indexen* voor HTAP kunt u snelle transactie verwerking uitvoeren, en *tegelijkertijd* analyse query's op dezelfde gegevens uitvoeren.

Zowel de column Store-indexen als de in-Memory OLTP zijn onderdeel van het SQL Server product sinds 2012 en 2014. Azure SQL Database en SQL Server dezelfde implementatie van in-Memory technologieën te delen. Nieuwe mogelijkheden voor deze technologieën worden eerst uitgebracht in Azure SQL Database voordat ze in SQL Server worden uitgebracht.

## <a name="benefits-of-in-memory-technology"></a>Voor delen van de technologie in het geheugen

Vanwege de efficiëntere query-en transactie verwerking, kunt u in-Memory technologieën ook de kosten verlagen. Normaal gesp roken hoeft u de prijs categorie van de data base niet bij te werken om de prestaties te verbeteren. In sommige gevallen is het mogelijk dat u de prijs categorie ook kunt verlagen, terwijl de prestatie verbeteringen met in-Memory technologieën nog steeds worden weer gegeven.

Hieronder vindt u twee voor beelden van hoe in-Memory OLTP heeft geholpen om de prestaties aanzienlijk te verbeteren:

- Door gebruik te maken van in-Memory OLTP [kunnen quorum bedrijfs oplossingen hun werk belasting verdubbelen terwijl dtu's met 70% wordt verbeterd](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database).

  - DTU betekent dat de *trans actie-eenheid voor de data base*en de meting van het Resource verbruik bevatten.
- In de volgende video wordt gedemonstreerd hoe veel verbeteringen van resources worden verbruikt met een voor beeld van een werk belasting: [in-Memory OLTP in Azure SQL database video](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB).
  - Zie voor meer informatie het blog bericht: [in-Memory OLTP in Azure SQL database blog bericht](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

> [!NOTE]  
> In-Memory technologieën zijn beschikbaar in Premium en Bedrijfskritiek tier Azure SQL-data bases en Premium elastische Pools.

In de volgende video worden mogelijke prestatie verbeteringen met in-Memory technologieën in Azure SQL Database uitgelegd. Houd er rekening mee dat de prestatie verbetering die u altijd ziet, afhankelijk is van een groot aantal factoren, zoals de aard van de werk belasting en de gegevens, het toegangs patroon van de data base, enzovoort.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

In dit artikel worden de aspecten beschreven van OLTP-en column Store-indexen die specifiek zijn voor Azure SQL Database en bevatten ook voor beelden:

- U ziet de impact van deze technologieën op opslag-en gegevens grootte limieten.
- U ziet hoe u de verplaatsing beheert van data bases die gebruikmaken van deze technologieën tussen de verschillende prijs categorieën.
- U ziet twee voor beelden die het gebruik van in-Memory OLTP en column Store-indexen in Azure SQL Database illustreren.

Zie voor meer informatie:

- [Overzicht van in-Memory OLTP en gebruiks scenario's](https://msdn.microsoft.com/library/mt774593.aspx) (bevat verwijzingen naar casestudy's van klanten en informatie om aan de slag te gaan)
- [Documentatie voor in-Memory OLTP](https://msdn.microsoft.com/library/dn133186.aspx)
- [Hand leiding voor column Store-indexen](https://msdn.microsoft.com/library/gg492088.aspx)
- Hybride transactionele/analytische verwerking (HTAP), ook wel bekend als [real-time Operational Analytics](https://msdn.microsoft.com/library/dn817827.aspx)

## <a name="in-memory-oltp"></a>OLTP in het geheugen

De OLTP-technologie in het geheugen biedt extreem snelle bewerkingen voor gegevens toegang door alle gegevens in het geheugen te bewaren. Het maakt ook gebruik van gespecialiseerde indexen, systeem eigen compilatie van query's en vrije gegevens toegang om de prestaties van de OLTP-werk belasting te verbeteren. Er zijn twee manieren om de OLTP-gegevens in het geheugen te ordenen:

- Voor het **geheugen geoptimaliseerde rowstore** -indeling, waarbij elke rij een afzonderlijk geheugen object is. Dit is een klassieke OLTP-indeling in het geheugen die is geoptimaliseerd voor de hoge prestaties van OLTP-workloads. Er zijn twee typen tabellen die zijn geoptimaliseerd voor geheugen die kunnen worden gebruikt in de rowstore-indeling die is geoptimaliseerd voor geheugen:
  - *Duurzame tabellen* (SCHEMA_AND_DATA) waarbij de rijen in het geheugen worden bewaard na het opnieuw opstarten van de server. Dit type tabellen gedraagt zich als een traditionele rowstore-tabel met de extra voor delen van in-Memory optimalisaties.
  - *Niet-duurzame tabellen* (SCHEMA_ONLY) waarin de rijen niet worden bewaard na opnieuw opstarten. Dit type tabel is bedoeld voor tijdelijke gegevens (bijvoorbeeld vervangen van tijdelijke tabellen) of tabellen waarin u snel gegevens moet laden voordat u deze naar een andere persistente tabel verplaatst (dat wil zeggen faserings tabellen genoemd).
- **Met geheugen geoptimaliseerde column Store-** indeling waarbij gegevens in een kolom indeling worden ingedeeld. Deze structuur is ontworpen voor HTAP-scenario's waarin u analytische query's moet uitvoeren op dezelfde gegevens structuur waar de OLTP-werk belasting wordt uitgevoerd.

> [!Note]
> De OLTP-technologie in het geheugen is ontworpen voor de gegevens structuren die volledig in het geheugen kunnen worden opgeslagen. Omdat de in-Memory gegevens niet naar de schijf kunnen worden geoffload, moet u ervoor zorgen dat u de data base gebruikt die voldoende geheugen heeft. Zie [gegevens grootte en opslag limiet voor OLTP in het geheugen](#data-size-and-storage-cap-for-in-memory-oltp) voor meer informatie.

Een snelle primer op in-Memory OLTP: [Snelstartgids 1: OLTP-technologieën in het geheugen voor snellere T-SQL-prestaties](https://msdn.microsoft.com/library/mt694156.aspx) (een ander artikel om u te helpen aan de slag)

Uitgebreide Video's over de technologieën:

- [In-Memory OLTP in Azure SQL database](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (bevat een demo van de prestatie voordelen en stappen om deze resultaten zelf te reproduceren)
- [In-Memory OLTP-Video's: wat is het en wanneer/hoe u deze kunt gebruiken](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)

Er is een programmatische manier om te begrijpen of een bepaalde data base in-Memory OLTP ondersteunt. U kunt de volgende Transact-SQL-query uitvoeren:
```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```
Als de query **1**retourneert, wordt OLTP in het geheugen ondersteund in deze data base. Met de volgende query's worden alle objecten geïdentificeerd die moeten worden verwijderd voordat een Data Base kan worden gedowngraded naar Standard/Basic:
```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Gegevens grootte en opslag limiet voor OLTP in het geheugen

In-Memory OLTP bevat tabellen die zijn geoptimaliseerd voor geheugen, die worden gebruikt voor het opslaan van gebruikers gegevens. Deze tabellen moeten in het geheugen passen. Omdat u het geheugen rechtstreeks beheert in de SQL Database-Service, hebben we het concept van een quotum voor gebruikers gegevens. Dit wordt aangeduid als *OLTP-opslag in het geheugen*.

Elke ondersteunde prijs categorie voor één data base en elke prijs categorie voor elastische Pools bevat een bepaalde hoeveelheid OLTP-opslag in het geheugen. Zie [op DTU gebaseerde resource limieten-één data base](sql-database-dtu-resource-limits-single-databases.md), [op DTU gebaseerde resource limieten: elastische Pools](sql-database-dtu-resource-limits-elastic-pools.md),[vCore resource limieten-individuele data bases](sql-database-vcore-resource-limits-single-databases.md) en [vCore resource limieten: elastische Pools](sql-database-vcore-resource-limits-elastic-pools.md).

De volgende items tellen mee in de OLTP-opslag limiet van in het geheugen:

- Gegevens rijen van actieve gebruikers in tabellen die zijn geoptimaliseerd voor geheugen en tabel variabelen. De oude rijkoppen tellen niet mee voor het kapje.
- Indexen op tabellen die zijn geoptimaliseerd voor geheugen.
- Operationele overhead van ALTER TABLE-bewerkingen.

Als u het kapje bereikt, ontvangt u een fout melding met de melding dat u geen gegevens meer kunt invoegen of bijwerken. Als u deze fout wilt verhelpen, verwijdert u de gegevens of verhoogt u de prijs categorie van de data base of groep.

Zie [in-Memory-opslag controleren](sql-database-in-memory-oltp-monitoring.md)voor meer informatie over het bewaken van het gebruik van in-Memory OLTP-opslag en het configureren van waarschuwingen wanneer u bijna de limiet bereikt.

#### <a name="about-elastic-pools"></a>Over elastische Pools

Met elastische Pools wordt de OLTP-opslag in het geheugen gedeeld met alle data bases in de groep. Het gebruik in de ene data base kan daarom van invloed zijn op andere data bases. Er zijn twee oplossingen voor dit:

- Configureer een `Max-eDTU` of `MaxvCore` voor data bases die kleiner zijn dan het aantal eDTU-of vCore voor de groep als geheel. Dit maximum is het gebruik van OLTP-opslag in het geheugen, in elke data base in de pool, tot de grootte die overeenkomt met het aantal eDTU.
- Configureer een `Min-eDTU` of `MinvCore` dat groter is dan 0. Dit minimum garandeert dat elke data base in de pool de hoeveelheid beschik bare OLTP-opslag in het geheugen heeft die overeenkomt met het geconfigureerde `Min-eDTU` of `vCore`.

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>Service lagen wijzigen van data bases die gebruikmaken van in-Memory OLTP-technologieën

U kunt uw data base of exemplaar altijd upgraden naar een hogere laag, bijvoorbeeld van Algemeen naar Bedrijfskritiek (of Standard to Premium). De beschik bare functionaliteit en bronnen worden alleen verhoogd.

Het downgradeen van de laag kan echter een negatieve invloed hebben op uw data base. De impact is vooral duidelijk wanneer u een downgrade van Bedrijfskritiek naar Algemeen (of Premium to Standard of Basic) uitvoert wanneer uw data base in-Memory OLTP-objecten bevat. Tabellen die zijn geoptimaliseerd voor geheugen, zijn niet beschikbaar na de downgrade (zelfs als ze zichtbaar blijven). Deze overwegingen zijn van toepassing wanneer u de prijs categorie van een elastische pool verlaagt of een Data Base met in-Memory technologieën verplaatst naar een Standard-of Basic elastische groep.

> [!Important]
> OLTP in het geheugen wordt niet ondersteund in de laag Algemeen, Standard of Basic. Daarom is het niet mogelijk om een Data Base met een in-Memory OLTP-objecten te verplaatsen naar de laag Standard of Basic.

Voordat u de data base downgradet naar Standard/Basic, verwijdert u alle tabellen en tabel typen die zijn geoptimaliseerd voor geheugen en alle systeem eigen, gecompileerde T-SQL-modules. 

*Resources in bedrijfskritiek laag schalen*: gegevens in tabellen die zijn geoptimaliseerd voor geheugen moeten passen binnen de OLTP-opslag in het geheugen die is gekoppeld aan de laag van de data base of het beheerde exemplaar of beschikbaar is in de elastische pool. Als u de laag wilt schalen of de Data Base wilt verplaatsen naar een pool die niet voldoende beschik bare OLTP-opslag in het geheugen heeft, mislukt de bewerking.

## <a name="in-memory-columnstore"></a>In-Memory column Store

Met de column Store-technologie in het geheugen kunt u een grote hoeveelheid gegevens in de tabellen opslaan en er query's op uitvoeren. Column Store-technologie maakt gebruik van op kolommen gebaseerde gegevens opslag indeling en batch query verwerking om Maxi maal tien keer de query prestaties in OLAP-workloads te verkrijgen via traditionele rij-georiënteerde opslag. U kunt ook Maxi maal tien keer de gegevens compressie van de niet-gecomprimeerde gegevens grootte behaalt.
Er zijn twee soorten column Store-modellen die u kunt gebruiken om uw gegevens te organiseren:

- **Geclusterde column Store** waarbij alle gegevens in de tabel in de kolom indeling zijn ingedeeld. In dit model worden alle rijen in de tabel in de kolom indeling geplaatst, waardoor de gegevens zeer worden gecomprimeerd en kunt u snelle analytische query's en rapporten op de tabel uitvoeren. Afhankelijk van de aard van uw gegevens, is de omvang van uw gegevens mogelijk onverminderd 10x-100x. Met een geclusterd column Store-model kunt u ook snel een grote hoeveelheid gegevens (bulksgewijs laden) maken, aangezien grote batches gegevens die groter zijn dan 100.000-rijen, worden gecomprimeerd voordat ze op schijf worden opgeslagen. Dit model is een goede keuze voor de klassieke data warehouse-scenario's. 
- **Niet-geclusterde column Store** waarin de gegevens worden opgeslagen in een traditionele rowstore-tabel en er een index is in de column Store-indeling die wordt gebruikt voor de analytische query's. Dit model maakt hybride transactionele analyse verwerking mogelijk (HTAP): de mogelijkheid om de uitvoering van een real-time analyse op een transactionele werk belasting uit te voeren. OLTP-query's worden uitgevoerd op rowstore-tabel die is geoptimaliseerd voor toegang tot een kleine set rijen, terwijl OLAP-query's worden uitgevoerd op column store-index die beter geschikt is voor scans en analyses. Met Azure SQL Database query optimalisatie wordt de rowstore-of column Store-indeling dynamisch gekozen op basis van de query. Niet-geclusterde column Store-indexen verminderen de grootte van de gegevens omdat de oorspronkelijke gegevensset in de oorspronkelijke rowstore-tabel zonder enige wijziging wordt bewaard. De grootte van de aanvullende column store-index moet echter kleiner zijn dan de equivalente index van de B-structuur.

> [!Note]
> In-Memory column Store-technologie bewaart alleen de gegevens die nodig zijn voor verwerking in het geheugen, terwijl de gegevens die niet in het geheugen passen, op schijf worden opgeslagen. Daarom kan de hoeveelheid gegevens in column Store-structuren in het geheugen groter zijn dan de hoeveelheid beschikbaar geheugen. 

Diep gaande video over de technologie:

- [Column store-index: in-Memory Analytics Video's van Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Gegevens grootte en-opslag voor column Store-indexen

Column Store-indexen hoeven niet in het geheugen te passen. Daarom is de enige limiet voor de grootte van de indexen de maximale totale database grootte, die wordt beschreven in het [op DTU gebaseerde aankoop model](sql-database-service-tiers-dtu.md) en [op vCore gebaseerde inkoop model](sql-database-service-tiers-vcore.md) artikelen.

Wanneer u geclusterde column Store-indexen gebruikt, wordt kolom compressie gebruikt voor de basis tabel opslag. Deze compressie kan de opslag capaciteit van uw gebruikers gegevens aanzienlijk verminderen. Dit betekent dat u meer gegevens in de Data Base kunt aanpassen. En de compressie kan verder worden verhoogd met de [kolom archivering compressie](https://msdn.microsoft.com/library/cc280449.aspx#using-columnstore-and-columnstore-archive-compression). De hoeveelheid compressie die u kunt behaalt, is afhankelijk van de aard van de gegevens, maar tien keer de compressie is niet ongebruikelijk.

Als u bijvoorbeeld een Data Base hebt met een maximale grootte van 1 terabyte (TB) en u de compressie 10 keer behaalt met behulp van Column Store-indexen, kunt u in totaal 10 TB aan gebruikers gegevens in de Data Base gebruiken.

Wanneer u niet-geclusterde column Store-indexen gebruikt, wordt de basis tabel nog steeds opgeslagen in de traditionele rowstore-indeling. Daarom zijn de opslag besparingen niet zo groot als bij geclusterde column Store-indexen. Als u echter een aantal traditionele, niet-geclusterde indexen wilt vervangen door een enkele column store-index, kunt u nog steeds een totale besparing zien in de opslag ruimte voor de tabel.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Service lagen van data bases met column Store-indexen wijzigen

Het *downgradeen van één Data Base naar Basic of Standard* is mogelijk niet mogelijk als uw doellaag lager is dan S3. Column Store-indexen worden alleen ondersteund in de prijs categorie Bedrijfskritiek/Premium en op de Standard-laag, S3 en hoger, en niet op de laag Basic. Wanneer u uw data base downgradet naar een niet-ondersteunde laag of niveau, wordt uw column store-index niet meer beschikbaar. Het systeem behoudt uw column store-index, maar maakt geen gebruik van de index. Als u later een upgrade naar een ondersteunde laag of een ondersteund niveau uitvoert, is uw column store-index onmiddellijk gereed om opnieuw te worden gebruikt.

Als u een **geclusterde** column store-index hebt, wordt de hele tabel na de downgrade niet meer beschikbaar. Daarom wordt u aangeraden alle *geclusterde* column Store-indexen weg te halen voordat u de data base downgradet naar een niet-ondersteunde laag of een niet-ondersteund niveau.

> [!Note]
> Een beheerd exemplaar ondersteunt column Store-indexen in alle lagen.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>Volgende stappen

- [Snelstartgids 1: OLTP-technologieën in het geheugen voor snellere T-SQL-prestaties](https://msdn.microsoft.com/library/mt694156.aspx)
- [In-Memory OLTP gebruiken in een bestaande Azure SQL-toepassing](sql-database-in-memory-oltp-migration.md)
- [OLTP-opslag in het geheugen bewaken](sql-database-in-memory-oltp-monitoring.md) voor OLTP in het geheugen
- [Probeer de functies in het geheugen in Azure SQL Database](sql-database-in-memory-sample.md)

## <a name="additional-resources"></a>Extra resources

### <a name="deeper-information"></a>Dieper informatie

- [Meer informatie over hoe quorum de werk belasting van de Key Data Base verdubbelt tijdens het verlagen van DTU met 70% met in-Memory OLTP in SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [In-Memory OLTP in Azure SQL Database blog bericht](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)
- [Meer informatie over in-Memory OLTP](https://msdn.microsoft.com/library/dn133186.aspx)
- [Meer informatie over column Store-indexen](https://msdn.microsoft.com/library/gg492088.aspx)
- [Meer informatie over real-time operationele analyses](https://msdn.microsoft.com/library/dn817827.aspx)
- Bekijk [algemene werk belasting patronen en migratie overwegingen](https://msdn.microsoft.com/library/dn673538.aspx) (waarin de werkbelasting patronen worden beschreven waarbij in-Memory OLTP doorgaans aanzienlijke prestatie verbeteringen oplevert)

### <a name="application-design"></a>Toepassingsontwerp

- [OLTP in het geheugen (in-Memory optimalisatie)](https://msdn.microsoft.com/library/dn133186.aspx)
- [In-Memory OLTP gebruiken in een bestaande Azure SQL-toepassing](sql-database-in-memory-oltp-migration.md)

### <a name="tools"></a>Hulpprogramma's

- [Azure Portal](https://portal.azure.com/)
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
