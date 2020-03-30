---
title: Gegevensscheeftrekken oplossen - Azure Data Lake-hulpprogramma's voor Visual Studio
description: Problemen met potentiële oplossingen voor problemen met gegevensscheefheid oplossen met Azure Data Lake Tools voor Visual Studio.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/16/2016
ms.openlocfilehash: 9ff7ba5f04a8c1862f8ef136f8f3f6900f00a431
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71802558"
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Problemen met asymmetrische gegevens oplossen met behulp van de Azure Data Lake-tools voor Visual Studio

## <a name="what-is-data-skew"></a>Wat is gegevensscheefheid?

Kort gezegd, gegevens scheeftrekken is een oververtegenwoordigde waarde. Stel je voor dat je 50 belastingonderzoekers hebt toegewezen om belastingaangiften te controleren, één examinator voor elke Amerikaanse staat. De Wyoming examinator, omdat de bevolking daar klein is, heeft weinig te doen. In Californië, echter, wordt de examinator gehouden zeer bezig wegens de grote bevolking van de staat.
    ![Voorbeeld van probleemprobleem met gegevens](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

In ons scenario zijn de gegevens ongelijk verdeeld over alle belastingexaminatoren, wat betekent dat sommige examinatoren meer moeten werken dan andere. In je eigen functie ervaar je hier vaak situaties als het voorbeeld van de fiscalist. In meer technische termen, een hoekpunt krijgt veel meer gegevens dan zijn collega's, een situatie die maakt de hoekpunt werken meer dan de anderen en dat uiteindelijk vertraagt een hele baan. Wat erger is, de taak kan mislukken, omdat vertices kan hebben, bijvoorbeeld, een 5-uur runtime beperking en een 6-GB geheugen beperking.

## <a name="resolving-data-skew-problems"></a>Problemen met gegevensscheeftrekken oplossen

Azure Data Lake-hulpprogramma's voor Visual Studio kunnen helpen detecteren of uw taak een probleem met gegevensscheefheid heeft. Als er een probleem is, u dit oplossen door de oplossingen in deze sectie uit te proberen.

## <a name="solution-1-improve-table-partitioning"></a>Oplossing 1: Tabelpartitionering verbeteren

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>Optie 1: De scheve waarde van de sleutel vooraf filteren

Als dit geen invloed heeft op uw bedrijfslogica, u de waarden met een hogere frequentie vooraf filteren. Als er bijvoorbeeld veel 000-000-000 in kolom-GUID zijn, wilt u die waarde mogelijk niet samenvoegen. Voordat u aggregaat, u WHERE GUID != 000-000-000 schrijven om de hoogfrequente waarde te filteren.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>Optie 2: Een andere partitie- of distributiesleutel kiezen

Als u in het voorgaande voorbeeld alleen de belastingcontrolewerklast in het hele land/regio wilt controleren, u de gegevensdistributie verbeteren door het ID-nummer als sleutel te selecteren. Het kiezen van een andere partitie- of distributiesleutel kan de gegevens soms gelijkmatiger distribueren, maar u moet ervoor zorgen dat deze keuze geen invloed heeft op uw bedrijfslogica. Als u bijvoorbeeld het belastingbedrag voor elke staat wilt berekenen, u _de status_ als partitiesleutel aanwijzen. Als u dit probleem nog steeds ondervindt, probeert u Option 3 te gebruiken.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>Optie 3: Meer partitie- of distributiesleutels toevoegen

In plaats van _alleen status_ als partitiesleutel te gebruiken, u meer dan één sleutel gebruiken voor partitionering. Overweeg bijvoorbeeld _om postcode_ toe te voegen als een extra partitiesleutel om de grootte van gegevenspartities te verkleinen en de gegevens gelijkmatiger te verdelen.

### <a name="option-4-use-round-robin-distribution"></a>Optie 4: Gebruik round-robin distributie

Als u geen geschikte sleutel voor partitie en distributie vinden, u proberen om round-robin distributie te gebruiken. Round-robin distributie behandelt alle rijen gelijk en willekeurig zet ze in overeenkomstige emmers. De gegevens worden gelijkmatig verdeeld, maar het verliest plaatsinformatie, een nadeel dat ook de prestaties van sommige bewerkingen kan verminderen. Bovendien, als u aggregatie voor de scheve sleutel toch doet, zal de gegevens-scheeftrekken probleem blijven bestaan. Zie de sectie U-SQL-tabeldistributies in [CREATE TABLE (U-SQL) voor](/u-sql/ddl/tables/create/managed/create-table-u-sql-creating-a-table-with-schema#dis_sch)meer informatie over round-robin-distributie.

## <a name="solution-2-improve-the-query-plan"></a>Oplossing 2: Het queryplan verbeteren

### <a name="option-1-use-the-create-statistics-statement"></a>Optie 1: De instructie STATISTIEKEN MAKEN gebruiken

U-SQL biedt de instructie CREATE STATISTICS op tabellen. Deze instructie geeft de queryoptimizer meer informatie over de gegevenskenmerken, zoals waardeverdeling, die in een tabel zijn opgeslagen. Voor de meeste query's genereert de queryoptimizer al de benodigde statistieken voor een queryplan van hoge kwaliteit. Af en toe moet u de queryprestaties verbeteren door aanvullende statistieken te maken met STATISTIEKEN MAKEN OF door het queryontwerp te wijzigen. Zie de pagina [U-SQL (CREATE STATISTICS)](/u-sql/ddl/statistics/create-statistics) voor meer informatie.

Codevoorbeeld:

    CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;

>[!NOTE]
>Statistiekeninformatie wordt niet automatisch bijgewerkt. Als u de gegevens in een tabel bijwerkt zonder de statistieken opnieuw te maken, kunnen de queryprestaties afnemen.

### <a name="option-2-use-skewfactor"></a>Optie 2: SkewFACTOR gebruiken

Als u de belasting voor elke staat wilt optellen, moet u groep per staat gebruiken, een benadering die het probleem met gegevensscheeftrekken niet vermijdt. U echter een gegevenshint in uw query geven om gegevensscheefheid in sleutels te identificeren, zodat de optimizer een uitvoeringsplan voor u kan opstellen.

Meestal u de parameter instellen als 0,5 en 1, met 0,5 betekenis niet veel scheeftrekken en 1 betekenis zwaar scheeftrekken. Omdat de hint van invloed is op de optimalisatie van het uitvoeringsplan voor de huidige instructie en alle downstream-instructies, moet u de hint toevoegen voordat de potentiële scheve toets-wise aggregatie.

    SKEWFACTOR (columns) = x

    Provides a hint that the given columns have a skew factor x from 0 (no skew) through 1 (very heavy skew).

Codevoorbeeld:

    //Add a SKEWFACTOR hint.
    @Impressions =
        SELECT * FROM
        searchDM.SML.PageView(@start, @end) AS PageView
        OPTION(SKEWFACTOR(Query)=0.5)
        ;

    //Query 1 for key: Query, ClientId
    @Sessions =
        SELECT
            ClientId,
            Query,
            SUM(PageClicks) AS Clicks
        FROM
            @Impressions
        GROUP BY
            Query, ClientId
        ;

    //Query 2 for Key: Query
    @Display =
        SELECT * FROM @Sessions
            INNER JOIN @Campaigns
                ON @Sessions.Query == @Campaigns.Query
        ;   

### <a name="option-3-use-rowcount"></a>Optie 3: ROWCOUNT gebruiken  
Naast SKEWFACTOR u voor specifieke joincases met scheve toetsen, als u weet dat de andere samengevoegde rijset klein is, de optimizer vertellen door een ROWCOUNT-hint toe te voegen in de U-SQL-instructie voordat JOIN. Op deze manier kan optimizer een broadcast join-strategie kiezen om de prestaties te verbeteren. Houd er rekening mee dat ROWCOUNT het probleem met gegevensscheefheid niet oplost, maar wel wat extra hulp kan bieden.

    OPTION(ROWCOUNT = n)

    Identify a small row set before JOIN by providing an estimated integer row count.

Codevoorbeeld:

    //Unstructured (24-hour daily log impressions)
    @Huge   = EXTRACT ClientId int, ...
                FROM @"wasb://ads@wcentralus/2015/10/30/{*}.nif"
                ;

    //Small subset (that is, ForgetMe opt out)
    @Small  = SELECT * FROM @Huge
                WHERE Bing.ForgetMe(x,y,z)
                OPTION(ROWCOUNT=500)
                ;

    //Result (not enough information to determine simple broadcast JOIN)
    @Remove = SELECT * FROM Bing.Sessions
                INNER JOIN @Small ON Sessions.Client == @Small.Client
                ;

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>Oplossing 3: Verbeter de door de gebruiker gedefinieerde reducer en combiner

U soms een door de gebruiker gedefinieerde operator schrijven om te gaan met ingewikkelde proceslogica, en een goed geschreven reducer en combiner kunnen in sommige gevallen een probleem met gegevensscheefheid beperken.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>Optie 1: Gebruik een recursieve reducer, indien mogelijk

Standaard wordt een door de gebruiker gedefinieerde reducer uitgevoerd in de niet-recursieve modus, wat betekent dat het verminderen van werk voor een sleutel wordt gedistribueerd in één hoekpunt. Maar als uw gegevens scheef zijn, kunnen de enorme gegevenssets worden verwerkt in een enkele hoekpunt en worden uitgevoerd voor een lange tijd.

Om de prestaties te verbeteren, u een kenmerk in uw code toevoegen om reducer te definiëren die in de recursieve modus moet worden uitgevoerd. Vervolgens kunnen de enorme gegevenssets worden gedistribueerd naar meerdere vertices en parallel worden uitgevoerd, wat uw taak versnelt.

Als u een niet-recursieve reducer wilt wijzigen in recursieve, moet u ervoor zorgen dat uw algoritme associatief is. De som is bijvoorbeeld associatief en de mediaan niet. U moet er ook voor zorgen dat de invoer en uitvoer voor reducer hetzelfde schema behouden.

Attribuut van recursieve reducer:

    [SqlUserDefinedReducer(IsRecursive = true)]

Codevoorbeeld:

    [SqlUserDefinedReducer(IsRecursive = true)]
    public class TopNReducer : IReducer
    {
        public override IEnumerable<IRow>
            Reduce(IRowset input, IUpdatableRow output)
        {
            //Your reducer code goes here.
        }
    }

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>Optie 2: Gebruik de combinermodus op rijniveau, indien mogelijk

Net als bij de ROWCOUNT-hint voor specifieke joincases met scheve toetsen, probeert de combinermodus enorme waardensets met scheve sleutels te distribueren naar meerdere vertices, zodat het werk gelijktijdig kan worden uitgevoerd. De combinermodus kan problemen met gegevensscheefheid niet oplossen, maar het kan wel wat extra hulp bieden voor enorme waardesets met scheefwaarden.

Standaard is de combinermodus Volledig, wat betekent dat de set van de linkerrij en de rechterrijset niet kunnen worden gescheiden. Als u de modus als Links/Rechts/Inner instelt, u deelnemen op rijniveau. Het systeem scheidt de bijbehorende rijsets en verdeelt deze in meerdere vertices die parallel lopen. Voordat u de combinermodus configureert, moet u er echter voor zorgen dat de bijbehorende rijsets kunnen worden gescheiden.

In het voorbeeld dat volgt, wordt een gescheiden linkerrijset weergegeven. Elke uitvoerrij is afhankelijk van één invoerrij van links en is mogelijk afhankelijk van alle rijen van rechts met dezelfde sleutelwaarde. Als u de combinermodus als links instelt, scheidt het systeem de enorme linkerrijset in kleine en wijst deze toe aan meerdere vertices.

![Afbeelding van de combinermodus](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Als u de verkeerde combinermodus instelt, is de combinatie minder efficiënt en kunnen de resultaten verkeerd zijn.

Kenmerken van de combinermodus:

- SqlUserDefinedCombiner(Mode=CombinerMode.Full): Elke uitvoerrij is mogelijk afhankelijk van alle invoerrijen van links en rechts met dezelfde sleutelwaarde.

- SqlUserDefinedCombiner(Mode=CombinerMode.Left): Elke uitvoerrij is afhankelijk van één invoerrij van links (en mogelijk alle rijen van rechts met dezelfde sleutelwaarde).

- qlUserDefinedCombiner(Mode=CombinerMode.Right): Elke uitvoerrij is afhankelijk van één invoerrij van rechts (en mogelijk alle rijen van links met dezelfde sleutelwaarde).

- SqlUserDefinedCombiner(Mode=CombinerMode.Inner): Elke uitvoerrij is afhankelijk van één invoerrij van links en rechts met dezelfde waarde.

Codevoorbeeld:

    [SqlUserDefinedCombiner(Mode = CombinerMode.Right)]
    public class WatsonDedupCombiner : ICombiner
    {
        public override IEnumerable<IRow>
            Combine(IRowset left, IRowset right, IUpdatableRow output)
        {
        //Your combiner code goes here.
        }
    }
