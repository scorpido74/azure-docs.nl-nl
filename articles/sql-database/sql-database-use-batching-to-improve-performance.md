---
title: Batching gebruiken om de prestaties van toepassingen te verbeteren
description: Het onderwerp biedt bewijs dat batching database bewerkingen sterk verbetert de snelheid en schaalbaarheid van uw Azure SQL Database-toepassingen. Hoewel deze batchingtechnieken werken voor elke SQL Server-database, ligt de focus van het artikel op Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: cacc01151edaf31db938cf8abf3d46e75397758f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76545021"
---
# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>Batchverwerking gebruiken om de prestaties van SQL Database-toepassingen te verbeteren

Batching-bewerkingen naar Azure SQL Database verbeteren de prestaties en schaalbaarheid van uw toepassingen aanzienlijk. Om de voordelen te begrijpen, bevat het eerste deel van dit artikel een aantal voorbeeldtestresultaten die opeenvolgende en batched-aanvragen vergelijken met een SQL-database. In de rest van het artikel worden de technieken, scenario's en overwegingen weergegeven waarmee u batching succesvol gebruiken in uw Azure-toepassingen.

## <a name="why-is-batching-important-for-sql-database"></a>Waarom is batching belangrijk voor SQL Database

Batching calls naar een externe service is een bekende strategie voor het verhogen van prestaties en schaalbaarheid. Er zijn vaste verwerkingskosten voor interacties met een externe service, zoals serialisatie, netwerkoverdracht en deserialisatie. Het verpakken van veel afzonderlijke transacties in één batch minimaliseert deze kosten.

In dit artikel willen we verschillende SQL Database batching strategieën en scenario's onderzoeken. Hoewel deze strategieën ook belangrijk zijn voor on-premises toepassingen die SQL Server gebruiken, zijn er verschillende redenen om het gebruik van batching voor SQL Database te benadrukken:

* Er is mogelijk een grotere netwerklatentie bij het openen van SQL Database, vooral als u toegang hebt tot SQL Database van buiten hetzelfde Microsoft Azure-datacenter.
* De multitenant-kenmerken van SQL Database betekenen dat de efficiëntie van de gegevenstoegangslaag correleert met de algehele schaalbaarheid van de database. SQL Database moet voorkomen dat één tenant/gebruiker databasebronnen monopoliseert ten nadele van andere tenants. Als reactie op het gebruik dat hoger is dan vooraf gedefinieerde quota, kan SQL Database de doorvoer verminderen of reageren met uitzonderingen op beperking. Met efficiëntieverbeteringen, zoals batching, u meer werk doen aan SQL Database voordat u deze limieten bereikt. 
* Batching is ook effectief voor architecturen die meerdere databases gebruiken (sharding). De efficiëntie van uw interactie met elke database-eenheid is nog steeds een belangrijke factor in uw algehele schaalbaarheid. 

Een van de voordelen van het gebruik van SQL Database is dat u de servers die de database hosten niet hoeft te beheren. Deze beheerde infrastructuur betekent echter ook dat u anders moet denken over databaseoptimalisaties. U niet langer kijken naar het verbeteren van de database hardware of netwerkinfrastructuur. Microsoft Azure beheert deze omgevingen. Het belangrijkste gebied dat u beheren, is hoe uw toepassing samenwerkt met SQL Database. Batching is een van deze optimalisaties. 

Het eerste deel van het document onderzoekt verschillende batchingtechnieken voor .NET-toepassingen die SQL Database gebruiken. De laatste twee secties hebben betrekking op batchrichtlijnen en scenario's.

## <a name="batching-strategies"></a>Batchstrategieën

### <a name="note-about-timing-results-in-this-article"></a>Opmerking over de timing resultaten in dit artikel

> [!NOTE]
> Resultaten zijn geen benchmarks, maar zijn bedoeld om **relatieve prestaties**weer te geven . De timings zijn gebaseerd op een gemiddelde van ten minste 10 testruns. Bewerkingen worden ingevoegd in een lege tabel. Deze tests werden gemeten vóór V12 en komen niet noodzakelijkerwijs overeen met de doorvoer die u in een V12-database ervaren met behulp van de nieuwe [DTU-servicelagen](sql-database-service-tiers-dtu.md) of [vCore-servicelagen.](sql-database-service-tiers-vcore.md) Het relatieve voordeel van de batchingtechniek moet vergelijkbaar zijn.

### <a name="transactions"></a>Transacties

Het lijkt vreemd om te beginnen met een herziening van batching door het bespreken van transacties. Maar het gebruik van client-side transacties heeft een subtiele server-side batching effect dat de prestaties verbetert. En transacties kunnen worden toegevoegd met slechts een paar regels code, zodat ze een snelle manier bieden om de prestaties van opeenvolgende bewerkingen te verbeteren.

Overweeg de volgende C#-code die een reeks invoeg- en updatebewerkingen bevat in een eenvoudige tabel.

```csharp
List<string> dbOperations = new List<string>();
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
dbOperations.Add("insert MyTable values ('new value',1)");
dbOperations.Add("insert MyTable values ('new value',2)");
dbOperations.Add("insert MyTable values ('new value',3)");
```
De volgende ADO.NET code voert deze bewerkingen achtereenvolgens uit.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();

    foreach(string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn);
        cmd.ExecuteNonQuery();
    }
}
```

De beste manier om deze code te optimaliseren is het implementeren van een vorm van client-side batching van deze gesprekken. Maar er is een eenvoudige manier om de prestaties van deze code te verhogen door simpelweg het verpakken van de volgorde van oproepen in een transactie. Hier is dezelfde code die een transactie gebruikt.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();
    SqlTransaction transaction = conn.BeginTransaction();

    foreach (string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn, transaction);
        cmd.ExecuteNonQuery();
    }

    transaction.Commit();
}
```

Transacties worden in beide voorbeelden daadwerkelijk gebruikt. In het eerste voorbeeld is elke afzonderlijke aanroep een impliciete transactie. In het tweede voorbeeld wordt met een expliciete transactie alle aanmeldingen verwerkt. Volgens de documentatie voor het [write-ahead transactielogboek](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide?view=sql-server-ver15#WAL)worden logboekrecords naar de schijf gespoeld wanneer de transactie wordt gepleegd. Dus door meer oproepen in een transactie op te nemen, kan het schrijven naar het transactielogboek vertragen totdat de transactie is vastgelegd. In feite schakelt u batching voor de schrijft in het transactielogboek van de server in.

In de volgende tabel ziet u enkele ad hoc testresultaten. De tests voerden dezelfde opeenvolgende wisselplaten uit met en zonder transacties. Voor meer perspectief, de eerste set van tests liep op afstand van een laptop naar de database in Microsoft Azure. De tweede reeks tests liep vanuit een cloudservice en -database die beide binnen hetzelfde Microsoft Azure-datacenter (West US) verbleven. In de volgende tabel ziet u de duur in milliseconden van opeenvolgende wisselplaten met en zonder transacties.

**On-premises naar Azure:**

| Bewerkingen | Geen transactie (ms) | Transactie (ms) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Azure naar Azure (zelfde datacenter):**

| Bewerkingen | Geen transactie (ms) | Transactie (ms) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> Resultaten zijn geen benchmarks. Zie de [opmerking over timing resultaten in dit artikel](#note-about-timing-results-in-this-article).

Op basis van de vorige testresultaten vermindert het verpakken van één bewerking in een transactie de prestaties. Maar naarmate u het aantal bewerkingen binnen één transactie verhoogt, wordt de prestatieverbetering duidelijker. Het prestatieverschil is ook meer merkbaar wanneer alle bewerkingen plaatsvinden binnen het Microsoft Azure-datacenter. De verhoogde latentie van het gebruik van SQL Database van buiten het Microsoft Azure-datacenter overschaduwt de prestatiewinst van het gebruik van transacties.

Hoewel het gebruik van transacties de prestaties kan verhogen, blijven [de aanbevolen procedures voor transacties en verbindingen observeren](https://msdn.microsoft.com/library/ms187484.aspx). Houd de transactie zo kort mogelijk en sluit de databaseverbinding nadat het werk is voltooid. De instructie met gebruik in het vorige voorbeeld zorgt ervoor dat de verbinding wordt gesloten wanneer het volgende codeblok is voltooid.

Het vorige voorbeeld toont aan dat u een lokale transactie toevoegen aan elke ADO.NET code met twee regels. Transacties bieden een snelle manier om de prestaties van code te verbeteren die opeenvolgende bewerkingen invoegen, bijwerken en verwijderen maakt. Overweeg echter voor de snelste prestaties de code verder te wijzigen om te profiteren van batching aan de clientzijde, zoals parameters met tabelwaarde.

Zie [Lokale transacties in ADO.NET](https://docs.microsoft.com/dotnet/framework/data/adonet/local-transactions)voor meer informatie over transacties in ADO.NET .

### <a name="table-valued-parameters"></a>Tabelwaardeparameters

Tabelwaardeparameters ondersteunen door de gebruiker gedefinieerde tabeltypen als parameters in Transact-SQL-instructies, opgeslagen procedures en functies. Met deze batchtechniek aan de clientzijde u meerdere rijen gegevens verzenden binnen de parameter met een tabelwaarde. Als u parameters met tabelwaarde wilt gebruiken, definieert u eerst een tabeltype. Met de volgende transact-SQL-instructie wordt een tabeltype met de naam **MyTableType geopperd.**

```sql
    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );
```

In code maakt u een **gegevenstabel** met exact dezelfde namen en typen van het tabeltype. Geef deze **Gegevenstabel** door in een parameter in een tekstquery of opgeslagen procedureaanroep. In het volgende voorbeeld ziet u deze techniek:

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    DataTable table = new DataTable();
    // Add columns and rows. The following is a simple example.
    table.Columns.Add("mytext", typeof(string));
    table.Columns.Add("num", typeof(int));
    for (var i = 0; i < 10; i++)
    {
        table.Rows.Add(DateTime.Now.ToString(), DateTime.Now.Millisecond);
    }

    SqlCommand cmd = new SqlCommand(
        "INSERT INTO MyTable(mytext, num) SELECT mytext, num FROM @TestTvp",
        connection);

    cmd.Parameters.Add(
        new SqlParameter()
        {
            ParameterName = "@TestTvp",
            SqlDbType = SqlDbType.Structured,
            TypeName = "MyTableType",
            Value = table,
        });

    cmd.ExecuteNonQuery();
}
```

In het vorige voorbeeld voegt het **sqlcommand-object** rijen in van de parameter ** \@TestTvp**met tabelwaarde. Het eerder gemaakte **Object DataTable** wordt aan deze parameter toegewezen met de methode **SqlCommand.Parameters.Add.** Het batchn van de wisselplaten in één aanroep verhoogt de prestaties aanzienlijk ten opzichte van opeenvolgende wisselplaten.

Als u het vorige voorbeeld verder wilt verbeteren, gebruikt u een opgeslagen procedure in plaats van een op tekst gebaseerde opdracht. Met de volgende opdracht Transact-SQL wordt een opgeslagen procedure gemaakt die de parameter **SimpleTestTableType-tabelwaarde** gebruikt.

```sql
CREATE PROCEDURE [dbo].[sp_InsertRows] 
@TestTvp as MyTableType READONLY
AS
BEGIN
INSERT INTO MyTable(mytext, num) 
SELECT mytext, num FROM @TestTvp
END
GO
```

Wijzig vervolgens de **sqlcommand-objectdeclaratie** in het vorige codevoorbeeld in het volgende.

```csharp
SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
cmd.CommandType = CommandType.StoredProcedure;
```

In de meeste gevallen hebben parameters met tabelwaarde gelijkwaardige of betere prestaties dan andere batchtechnieken. Tabelwaardeparameters hebben vaak de voorkeur, omdat ze flexibeler zijn dan andere opties. Andere technieken, zoals SQL bulkcopy, maken bijvoorbeeld alleen het invoegen van nieuwe rijen mogelijk. Maar met parameters met tabelwaarde u logica in de opgeslagen procedure gebruiken om te bepalen welke rijen worden bijgewerkt en welke invoegen zijn. Het tabeltype kan ook worden gewijzigd om een kolom 'Bewerking' te bevatten die aangeeft of de opgegeven rij moet worden ingevoegd, bijgewerkt of verwijderd.

In de volgende tabel worden ad-hoctestresultaten weergegeven voor het gebruik van parameters met tabelwaarde in milliseconden.

| Bewerkingen | On-premises naar Azure (ms) | Azure zelfde datacenter (ms) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10.000 |23830 |3586 |

> [!NOTE]
> Resultaten zijn geen benchmarks. Zie de [opmerking over timing resultaten in dit artikel](#note-about-timing-results-in-this-article).
> 
> 

De prestatiewinst van batching is meteen duidelijk. In de vorige sequentiële test duurden 1000 bewerkingen 129 seconden buiten het datacenter en 21 seconden vanuit het datacenter. Maar met tabelwaardeparameters duren 1000 bewerkingen slechts 2,6 seconden buiten het datacenter en 0,4 seconden binnen het datacenter.

Zie Parameters met [tabelwaarde](https://msdn.microsoft.com/library/bb510489.aspx)voor meer informatie over parameters met tabelwaarde .

### <a name="sql-bulk-copy"></a>SQL-bulkkopie

SQL-bulkkopie is een andere manier om grote hoeveelheden gegevens in een doeldatabase in te voegen. .NET-toepassingen kunnen de klasse **SqlBulkCopy** gebruiken om bulkinsertbewerkingen uit te voeren. **SqlBulkCopy** is qua functie vergelijkbaar met het command-line-gereedschap **Bcp.exe**of de transact-SQL-instructie **BULK INSERT**. In het volgende codevoorbeeld ziet u hoe u de rijen in de tabel **Source DataTable**kopiëren naar de doeltabel in SQL Server, MyTable.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
    {
        bulkCopy.DestinationTableName = "MyTable";
        bulkCopy.ColumnMappings.Add("mytext", "mytext");
        bulkCopy.ColumnMappings.Add("num", "num");
        bulkCopy.WriteToServer(table);
    }
}
```

Er zijn enkele gevallen waarin bulkkopie de voorkeur heeft boven parameters met tabelwaarde. Zie de vergelijkingstabel met parameters met tabelwaarde versus BULKINSERT-bewerkingen in het artikel [Tabelwaardeparameters](https://msdn.microsoft.com/library/bb510489.aspx).

De volgende ad hoc testresultaten tonen de prestaties van batching met **SqlBulkCopy** in milliseconden.

| Bewerkingen | On-premises naar Azure (ms) | Azure zelfde datacenter (ms) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10.000 |21605 |2737 |

> [!NOTE]
> Resultaten zijn geen benchmarks. Zie de [opmerking over timing resultaten in dit artikel](#note-about-timing-results-in-this-article).
> 
> 

In kleinere batchgroottes presteerden de parameters met tabelwaarde beter dan de klasse **SqlBulkCopy.** **SqlBulkCopy** presteerde echter 12-31% sneller dan tabelwaardeparameters voor de tests van 1.000 en 10.000 rijen. Net als tabel-gewaardeerde parameters, **SqlBulkCopy** is een goede optie voor batched inserts, vooral in vergelijking met de prestaties van niet-batched operaties.

Zie Bulk Copy Operations in [SQL Server](https://msdn.microsoft.com/library/7ek5da1a.aspx)voor meer informatie over bulkcopy in ADO.NET.

### <a name="multiple-row-parameterized-insert-statements"></a>Parameterized INSERT-instructies met meerdere rijen

Een alternatief voor kleine batches is het maken van een grote parameterinstructie INSERT die meerdere rijen invoegt. Het volgende codevoorbeeld toont deze techniek aan.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    string insertCommand = "INSERT INTO [MyTable] ( mytext, num ) " +
        "VALUES (@p1, @p2), (@p3, @p4), (@p5, @p6), (@p7, @p8), (@p9, @p10)";

    SqlCommand cmd = new SqlCommand(insertCommand, connection);

    for (int i = 1; i <= 10; i += 2)
    {
        cmd.Parameters.Add(new SqlParameter("@p" + i.ToString(), "test"));
        cmd.Parameters.Add(new SqlParameter("@p" + (i+1).ToString(), i));
    }

    cmd.ExecuteNonQuery();
}
```

Dit voorbeeld is bedoeld om het basisconcept te tonen. Een realistischer scenario zou door de vereiste entiteiten lopen om de querytekenreeks en de opdrachtparameters tegelijkertijd te construeren. U bent beperkt tot een totaal van 2100 queryparameters, dus dit beperkt het totale aantal rijen dat op deze manier kan worden verwerkt.

De volgende ad hoc testresultaten tonen de prestaties van dit type invoeginstructie in milliseconden.

| Bewerkingen | Parameters met tabelwaarde (ms) | INSERT met één instructie (ms) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> Resultaten zijn geen benchmarks. Zie de [opmerking over timing resultaten in dit artikel](#note-about-timing-results-in-this-article).
> 
> 

Deze aanpak kan iets sneller zijn voor batches die minder dan 100 rijen zijn. Hoewel de verbetering klein is, is deze techniek een andere optie die goed zou kunnen werken in uw specifieke toepassingsscenario.

### <a name="dataadapter"></a>Gegevensadapter

Met de klasse **DataAdapter** u een **object DataSet** wijzigen en vervolgens de wijzigingen indienen als bewerkingen INSERT, UPDATE en DELETE. Als u de **DataAdapter** op deze manier gebruikt, is het belangrijk op te merken dat er voor elke afzonderlijke bewerking afzonderlijke oproepen worden gedaan. Gebruik de eigenschap **UpdateBatchSize** om de prestaties te verbeteren voor het aantal bewerkingen dat tegelijk moet worden gebatched. Zie [Batchoperations uitvoeren met dataadapters voor](https://msdn.microsoft.com/library/aadf8fk2.aspx)meer informatie.

### <a name="entity-framework"></a>Entiteitskader

Entity Framework ondersteunt momenteel geen batching. Verschillende ontwikkelaars in de community hebben geprobeerd tijdelijke oplossingen aan te tonen, zoals de **methode SaveChanges** overschrijven. Maar de oplossingen zijn meestal complex en aangepast aan het applicatie- en gegevensmodel. Het Entity Framework codeplex-project heeft momenteel een discussiepagina over deze functieaanvraag. Zie [Ontwerpvergaderingnotities - 2 augustus 2012](https://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012)om deze discussie te bekijken.

### <a name="xml"></a>XML

Voor de volledigheid vinden we het belangrijk om over XML te praten als batchstrategie. Het gebruik van XML heeft echter geen voordelen ten opzichte van andere methoden en verschillende nadelen. De benadering is vergelijkbaar met parameters met tabelwaarde, maar een XML-bestand of -tekenreeks wordt doorgegeven aan een opgeslagen procedure in plaats van een door de gebruiker gedefinieerde tabel. De opgeslagen procedure ontneemt de commando's in de opgeslagen procedure.

Er zijn verschillende nadelen aan deze aanpak:

* Werken met XML kan omslachtig en foutgevoelig zijn.
* Het ontschepen van de XML in de database kan CPU-intensief zijn.
* In de meeste gevallen is deze methode trager dan parameters met tabelwaarde.

Om deze redenen wordt het gebruik van XML voor batchquery's niet aanbevolen.

## <a name="batching-considerations"></a>Batching overwegingen

De volgende secties bieden meer richtlijnen voor het gebruik van batching in SQL Database-toepassingen.

### <a name="tradeoffs"></a>Compromissen

Afhankelijk van uw architectuur kan batching een afweging tussen prestaties en veerkracht inhouden. Denk bijvoorbeeld aan het scenario waarin uw rol onverwacht naar beneden gaat. Als u één rij gegevens verliest, is de impact kleiner dan de impact van het verliezen van een grote batch niet-ingediende rijen. Er is een groter risico wanneer u rijen buffert voordat u ze in een bepaald tijdvenster naar de database verzendt.

Evalueer t.a.v. het type bewerkingen dat u batcht. Batch agressiever (grotere batches en langere tijd vensters) met gegevens die minder kritisch is.

### <a name="batch-size"></a>Batchgrootte

In onze tests, was er meestal geen voordeel aan het breken van grote partijen in kleinere brokken. In feite resulteerde deze onderverdeling vaak in tragere prestaties dan het indienen van één grote batch. Overweeg bijvoorbeeld een scenario waarin u 1000 rijen wilt invoegen. In de volgende tabel ziet u hoe lang het duurt om parameters met tabelwaarde te gebruiken om 1000 rijen in te voegen wanneer deze worden onderverdeeld in kleinere batches.

| Batchgrootte | Iteraties | Parameters met tabelwaarde (ms) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> Resultaten zijn geen benchmarks. Zie de [opmerking over timing resultaten in dit artikel](#note-about-timing-results-in-this-article).
> 
> 

U zien dat de beste prestatie voor 1000 rijen is om ze allemaal tegelijk in te dienen. In andere tests (hier niet getoond), was er een kleine prestatiewinst om een 10000 rij batch te breken in twee batches van 5000. Maar het tabelschema voor deze tests is relatief eenvoudig, dus u moet tests uitvoeren op uw specifieke gegevens en batchgroottes om deze bevindingen te verifiëren.

Een andere factor om rekening mee te houden is dat als de totale batch te groot wordt, SQL Database kan gas geven en weigeren om de batch te plegen. Test voor de beste resultaten uw specifieke scenario om te bepalen of er een ideale batchgrootte is. Maak de batchgrootte configureerbaar tijdens runtime om snelle aanpassingen op basis van prestaties of fouten in te schakelen.

Ten slotte moet u de grootte van de partij in evenwicht brengen met de risico's die verbonden zijn aan batching. Als er tijdelijke fouten zijn of als de rol mislukt, moet u rekening houden met de gevolgen van het opnieuw proberen van de bewerking of van het verlies van de gegevens in de batch.

### <a name="parallel-processing"></a>Parallelle verwerking

Wat als u de aanpak van het verminderen van de batch grootte, maar gebruikt meerdere threads om het werk uit te voeren? Nogmaals, onze tests toonden aan dat een aantal kleinere multithreaded batches meestal slechter uitgevoerd dan een enkele grotere partij. Met de volgende test wordt geprobeerd om 1000 rijen in een of meer parallelle batches in te voegen. Deze test laat zien hoe meer gelijktijdige batches daadwerkelijk de prestaties verminderden.

| Batchgrootte [Iteraties] | Twee draden (ms) | Vier draden (ms) | Zes draden (ms) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> Resultaten zijn geen benchmarks. Zie de [opmerking over timing resultaten in dit artikel](#note-about-timing-results-in-this-article).
> 
> 

Er zijn verschillende mogelijke redenen voor de verslechtering van de prestaties als gevolg van parallellisme:

* Er zijn meerdere gelijktijdige netwerkoproepen in plaats van één.
* Meerdere bewerkingen tegen één tabel kunnen leiden tot onenigheid en blokkering.
* Er zijn overheadkosten verbonden aan multithreading.
* De kosten van het openen van meerdere verbindingen wegen op tegen het voordeel van parallelle verwerking.

Als u verschillende tabellen of databases target, is het mogelijk om met deze strategie enige prestatiewinst te zien. Database sharding of federaties zou een scenario voor deze aanpak. Sharding maakt gebruik van meerdere databases en leidt verschillende gegevens naar elke database. Als elke kleine batch naar een andere database gaat, kan het uitvoeren van de bewerkingen parallel efficiënter zijn. De prestatiewinst is echter niet significant genoeg om te gebruiken als basis voor een beslissing om databasesharding in uw oplossing te gebruiken.

In sommige ontwerpen kan parallelle uitvoering van kleinere batches resulteren in een verbeterde doorvoer van aanvragen in een systeem onder belasting. In dit geval, ook al is het sneller om een enkele grotere batch te verwerken, kan het verwerken van meerdere batches parallel efficiënter zijn.

Als u parallelle uitvoering gebruikt, u overwegen het maximum aantal werknemersthreads te beheren. Een kleiner getal kan leiden tot minder twist en een snellere uitvoeringstijd. Houd ook rekening met de extra belasting die dit op de doeldatabase plaatst, zowel in verbindingen als in transacties.

### <a name="related-performance-factors"></a>Gerelateerde prestatiefactoren

Typische richtlijnen voor databaseprestaties hebben ook invloed op batching. De invoegprestaties worden bijvoorbeeld verlaagd voor tabellen met een grote primaire sleutel of veel niet-geclusterde indexen.

Als parameters met een tabelwaarde een opgeslagen procedure gebruiken, u de opdracht **SET NOCOUNT ON** aan het begin van de procedure gebruiken. Deze instructie onderdrukt de terugkeer van de telling van de getroffen rijen in de procedure. In onze tests had het gebruik van **SET NOCOUNT ON** echter geen effect of verminderde prestaties. De testopgeslagen procedure was eenvoudig met één **INSERT-opdracht** uit de parameter met tabelwaarde. Het is mogelijk dat complexere opgeslagen procedures baat zouden hebben bij deze verklaring. Maar ga er niet van uit dat het toevoegen **van SET NOCOUNT ON** aan uw opgeslagen procedure automatisch de prestaties verbetert. Als u het effect wilt begrijpen, test u uw opgeslagen procedure met en zonder de instructie **NOCOUNT ON INSTELLEN.**

## <a name="batching-scenarios"></a>Batching-scenario's

In de volgende secties wordt beschreven hoe parameters met tabelwaarde worden gebruikt in drie toepassingsscenario's. Het eerste scenario laat zien hoe buffering en batching kunnen samenwerken. Het tweede scenario verbetert de prestaties door masterdetailbewerkingen uit te voeren in één opgeslagen proceduregesprek. Het laatste scenario laat zien hoe parameters met tabelwaarde kunnen worden gebruikt in een "UPSERT"-bewerking.

### <a name="buffering"></a>Buffering

Hoewel er een aantal scenario's die voor de hand liggende kandidaat voor batching zijn, zijn er veel scenario's die kunnen profiteren van batching door vertraagde verwerking. Vertraagde verwerking brengt echter ook een groter risico met zich mee dat de gegevens verloren gaan in het geval van een onverwachte storing. Het is belangrijk om dit risico te begrijpen en de gevolgen te overwegen.

Overweeg bijvoorbeeld een webtoepassing die de navigatiegeschiedenis van elke gebruiker bijhoudt. Op elk paginaverzoek kan de toepassing een databaseaanroep doen om de paginaweergave van de gebruiker op te nemen. Maar hogere prestaties en schaalbaarheid kunnen worden bereikt door de navigatieactiviteiten van de gebruikers te bufferen en deze gegevens vervolgens in batches naar de database te verzenden. U de database-update activeren op verstreken tijd en/of buffergrootte. Een regel kan bijvoorbeeld aangeven dat de batch na 20 seconden moet worden verwerkt of wanneer de buffer 1000 artikelen bereikt.

In het volgende codevoorbeeld wordt [Reactieve extensies gebruikt - Rx](https://msdn.microsoft.com/data/gg577609) om gebufferde gebeurtenissen te verwerken die door een bewakingsklasse zijn opgehaald. Wanneer de buffer vullingen of een time-out is bereikt, wordt de batch met gebruikersgegevens naar de database verzonden met een parameter met tabelwaarde.

Met de volgende klasse NavHistoryData worden de navigatiegegevens van de gebruiker aangepast. Het bevat basisinformatie zoals de gebruikers-id, de toegang tot de URL en de toegangstijd.

```csharp
public class NavHistoryData
{
    public NavHistoryData(int userId, string url, DateTime accessTime)
    { UserId = userId; URL = url; AccessTime = accessTime; }
    public int UserId { get; set; }
    public string URL { get; set; }
    public DateTime AccessTime { get; set; }
}
```

De klasse NavHistoryDataMonitor is verantwoordelijk voor het bufferen van de gebruikersnavigatiegegevens naar de database. Het bevat een methode, RecordUserNavigationEntry, die reageert door het verhogen van een **OnAdded** gebeurtenis. De volgende code toont de constructorlogica die Rx gebruikt om een waarneembare verzameling te maken op basis van de gebeurtenis. Vervolgens onderschrijft het deze waarneembare verzameling met de Buffer-methode. De overbelasting geeft aan dat de buffer elke 20 seconden of 1000 vermeldingen moet worden verzonden.

```csharp
public NavHistoryDataMonitor()
{
    var observableData =
        Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

    observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
}
```

De handler zet alle gebufferde items om in een tabelwaardetype en geeft dit type vervolgens door aan een opgeslagen procedure die de batch verwerkt. De volgende code toont de volledige definitie voor zowel de NavHistoryDataEventArgs als de NavHistoryDataMonitor klassen.

```csharp
public class NavHistoryDataEventArgs : System.EventArgs
{
    public NavHistoryDataEventArgs(NavHistoryData data) { Data = data; }
    public NavHistoryData Data { get; set; }
}

public class NavHistoryDataMonitor
{
    public event EventHandler<NavHistoryDataEventArgs> OnAdded;

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
    }
```

De handler zet alle gebufferde items om in een tabelwaardetype en geeft dit type vervolgens door aan een opgeslagen procedure die de batch verwerkt. De volgende code toont de volledige definitie voor zowel de NavHistoryDataEventArgs als de NavHistoryDataMonitor klassen.

```csharp
    public class NavHistoryDataEventArgs : System.EventArgs
    {
        if (OnAdded != null)
            OnAdded(this, new NavHistoryDataEventArgs(data));
    }

    protected void Handler(IList<EventPattern<NavHistoryDataEventArgs>> items)
    {
        DataTable navHistoryBatch = new DataTable("NavigationHistoryBatch");
        navHistoryBatch.Columns.Add("UserId", typeof(int));
        navHistoryBatch.Columns.Add("URL", typeof(string));
        navHistoryBatch.Columns.Add("AccessTime", typeof(DateTime));
        foreach (EventPattern<NavHistoryDataEventArgs> item in items)
        {
            NavHistoryData data = item.EventArgs.Data;
            navHistoryBatch.Rows.Add(data.UserId, data.URL, data.AccessTime);
        }

        using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
        {
            connection.Open();

            SqlCommand cmd = new SqlCommand("sp_RecordUserNavigation", connection);
            cmd.CommandType = CommandType.StoredProcedure;

            cmd.Parameters.Add(
                new SqlParameter()
                {
                    ParameterName = "@NavHistoryBatch",
                    SqlDbType = SqlDbType.Structured,
                    TypeName = "NavigationHistoryTableType",
                    Value = navHistoryBatch,
                });

            cmd.ExecuteNonQuery();
        }
    }
}
```

Als u deze bufferklasse wilt gebruiken, maakt de toepassing een statisch Object NavHistoryDataMonitor. Elke keer dat een gebruiker een pagina opent, roept de toepassing de methode NavHistoryDataMonitor.RecordUserNavigationEntry aan. De bufferlogica gaat over tot het verzenden van deze vermeldingen in batches naar de database.

### <a name="master-detail"></a>Hoofddetails

Tabelwaardeparameters zijn handig voor eenvoudige INSERT-scenario's. Het kan echter moeilijker zijn om batchinserts die meer dan een tabel te betrekken. Het "master/detail"-scenario is een goed voorbeeld. De hoofdtabel identificeert de primaire entiteit. Een of meer detailtabellen slaan meer gegevens over de entiteit op. In dit scenario dwingen buitenlandse sleutelrelaties de relatie van details af met een unieke hoofdentiteit. Overweeg een vereenvoudigde versie van een tabel PurchaseOrder en de bijbehorende orderdetailtabel. Met de volgende Transact-SQL wordt de tabel PurchaseOrder gemaakt met vier kolommen: OrderID, OrderDate, CustomerID en Status.

```sql
CREATE TABLE [dbo].[PurchaseOrder](
[OrderID] [int] IDENTITY(1,1) NOT NULL,
[OrderDate] [datetime] NOT NULL,
[CustomerID] [int] NOT NULL,
[Status] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_PurchaseOrder] 
PRIMARY KEY CLUSTERED ( [OrderID] ASC ))
```

Elke bestelling bevat een of meer productaankopen. Deze informatie wordt vastgelegd in de tabel PurchaseOrderDetail. Met de volgende Transact-SQL wordt de tabel PurchaseOrderDetail gemaakt met vijf kolommen: OrderID, OrderDetailID, ProductID, UnitPrice en OrderQty.

```sql
CREATE TABLE [dbo].[PurchaseOrderDetail](
[OrderID] [int] NOT NULL,
[OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
[ProductID] [int] NOT NULL,
[UnitPrice] [money] NULL,
[OrderQty] [smallint] NULL,
CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED 
( [OrderID] ASC, [OrderDetailID] ASC ))
```

De kolom OrderID in de tabel PurchaseOrderDetail moet verwijzen naar een order uit de tabel Inkooporder. De volgende definitie van een buitenlandse sleutel dwingt deze beperking af.

```sql
ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
REFERENCES [dbo].[PurchaseOrder] ([OrderID])
```

Als u tabelparameters wilt gebruiken, moet u voor elke doeltabel één door de gebruiker gedefinieerd tabeltype hebben.

```sql
CREATE TYPE PurchaseOrderTableType AS TABLE 
( OrderID INT,
    OrderDate DATETIME,
    CustomerID INT,
    Status NVARCHAR(50) );
GO

CREATE TYPE PurchaseOrderDetailTableType AS TABLE 
( OrderID INT,
    ProductID INT,
    UnitPrice MONEY,
    OrderQty SMALLINT );
GO
```

Definieer vervolgens een opgeslagen procedure die tabellen van deze typen accepteert. Met deze procedure kan een toepassing een set orders en ordergegevens in één gesprek lokaal batchen. De volgende Transact-SQL biedt de volledige opgeslagen proceduredeclaratie voor dit voorbeeld van de aankooporder.

```sql
CREATE PROCEDURE sp_InsertOrdersBatch (
@orders as PurchaseOrderTableType READONLY,
@details as PurchaseOrderDetailTableType READONLY )
AS
SET NOCOUNT ON;

-- Table that connects the order identifiers in the @orders
-- table with the actual order identifiers in the PurchaseOrder table
DECLARE @IdentityLink AS TABLE ( 
SubmittedKey int, 
ActualKey int, 
RowNumber int identity(1,1)
);

-- Add new orders to the PurchaseOrder table, storing the actual
-- order identifiers in the @IdentityLink table   
INSERT INTO PurchaseOrder ([OrderDate], [CustomerID], [Status])
OUTPUT inserted.OrderID INTO @IdentityLink (ActualKey)
SELECT [OrderDate], [CustomerID], [Status] FROM @orders ORDER BY OrderID;

-- Match the passed-in order identifiers with the actual identifiers
-- and complete the @IdentityLink table for use with inserting the details
WITH OrderedRows As (
SELECT OrderID, ROW_NUMBER () OVER (ORDER BY OrderID) As RowNumber 
FROM @orders
)
UPDATE @IdentityLink SET SubmittedKey = M.OrderID
FROM @IdentityLink L JOIN OrderedRows M ON L.RowNumber = M.RowNumber;

-- Insert the order details into the PurchaseOrderDetail table, 
-- using the actual order identifiers of the master table, PurchaseOrder
INSERT INTO PurchaseOrderDetail (
[OrderID],
[ProductID],
[UnitPrice],
[OrderQty] )
SELECT L.ActualKey, D.ProductID, D.UnitPrice, D.OrderQty
FROM @details D
JOIN @IdentityLink L ON L.SubmittedKey = D.OrderID;
GO
```

In dit voorbeeld worden in @IdentityLink de lokaal gedefinieerde tabel de werkelijke OrderID-waarden van de nieuw ingevoegde rijen opgeslagen. Deze order-id's verschillen van de @orders tijdelijke @details OrderID-waarden in de parameters met tabelwaarde. Daarom verbindt de @IdentityLink tabel vervolgens de OrderID-waarden van de @orders parameter met de echte OrderID-waarden voor de nieuwe rijen in de tabel Inkooporder. Na deze stap @IdentityLink kan de tabel het invoegen van de ordergegevens vergemakkelijken met de werkelijke OrderID die voldoet aan de externe sleutelbeperking.

Deze opgeslagen procedure kan worden gebruikt vanuit code of vanuit andere Transact-SQL-aanroepen. Zie het gedeelte parameters met tabelwaarde van dit document voor een codevoorbeeld. De volgende Transact-SQL laat zien hoe u de sp_InsertOrdersBatch.

```sql
declare @orders as PurchaseOrderTableType
declare @details as PurchaseOrderDetailTableType

INSERT @orders 
([OrderID], [OrderDate], [CustomerID], [Status])
VALUES(1, '1/1/2013', 1125, 'Complete'),
(2, '1/13/2013', 348, 'Processing'),
(3, '1/12/2013', 2504, 'Shipped')

INSERT @details
([OrderID], [ProductID], [UnitPrice], [OrderQty])
VALUES(1, 10, $11.50, 1),
(1, 12, $1.58, 1),
(2, 23, $2.57, 2),
(3, 4, $10.00, 1)

exec sp_InsertOrdersBatch @orders, @details
```

Met deze oplossing kan elke batch een set OrderID-waarden gebruiken die bij 1 beginnen. Deze tijdelijke OrderID-waarden beschrijven de relaties in de batch, maar de werkelijke OrderID-waarden worden bepaald op het moment van de invoegbewerking. U dezelfde instructies in het vorige voorbeeld herhaaldelijk uitvoeren en unieke orders genereren in de database. Daarom u overwegen meer code- of databaselogica toe te voegen die dubbele orders voorkomt bij het gebruik van deze batchtechniek.

In dit voorbeeld wordt aangetoond dat nog complexere databasebewerkingen, zoals master-detailbewerkingen, kunnen worden gebatched met behulp van parameters met tabelwaarde.

### <a name="upsert"></a>UPSERT UPSERT

Een ander batchscenario omvat het gelijktijdig bijwerken van bestaande rijen en het invoegen van nieuwe rijen. Deze bewerking wordt soms aangeduid als een "UPSERT" (update + insert) bewerking. In plaats van afzonderlijke gesprekken te voeren naar INSERT en UPDATE, is de instructie MERGE het meest geschikt voor deze taak. De instructie MERGE kan zowel invoegen als bijwerken in één gesprek uitvoeren.

Tabelwaardeparameters kunnen met de instructie MERGE worden gebruikt om updates en inserts uit te voeren. Denk bijvoorbeeld aan een vereenvoudigde tabel met werknemers met de volgende kolommen: EmployeeID, FirstName, LastName, SocialSecurityNumber:

```sql
CREATE TABLE [dbo].[Employee](
[EmployeeID] [int] IDENTITY(1,1) NOT NULL,
[FirstName] [nvarchar](50) NOT NULL,
[LastName] [nvarchar](50) NOT NULL,
[SocialSecurityNumber] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
([EmployeeID] ASC ))
```

In dit voorbeeld u het feit gebruiken dat het SocialSecurityNumber uniek is om een samenvoeging van meerdere werknemers uit te voeren. Maak eerst het door de gebruiker gedefinieerde tabeltype:

```sql
CREATE TYPE EmployeeTableType AS TABLE 
( Employee_ID INT,
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    SocialSecurityNumber NVARCHAR(50) );
GO
```

Maak vervolgens een opgeslagen procedure of schrijfcode die de instructie MERGE gebruikt om de update uit te voeren en in te voegen. In het volgende voorbeeld wordt de instructie MERGE @employeesgebruikt op een parameter met tabelwaarde van het type EmployeeTableType. De inhoud @employees van de tabel wordt hier niet weergegeven.

```sql
MERGE Employee AS target
USING (SELECT [FirstName], [LastName], [SocialSecurityNumber] FROM @employees) 
AS source ([FirstName], [LastName], [SocialSecurityNumber])
ON (target.[SocialSecurityNumber] = source.[SocialSecurityNumber])
WHEN MATCHED THEN 
UPDATE SET
target.FirstName = source.FirstName, 
target.LastName = source.LastName
WHEN NOT MATCHED THEN
    INSERT ([FirstName], [LastName], [SocialSecurityNumber])
    VALUES (source.[FirstName], source.[LastName], source.[SocialSecurityNumber]);
```

Zie de documentatie en voorbeelden voor de instructie MERGE voor meer informatie. Hoewel hetzelfde werk kan worden uitgevoerd in een door meerdere stappen opgeslagen procedureaanroep met afzonderlijke insert- en updatebewerkingen, is de instructie MERGE efficiënter. Databasecode kan ook Transact-SQL-aanroepen construeren die de merge-instructie rechtstreeks gebruiken zonder dat er twee databaseoproepen nodig zijn voor INSERT en UPDATE.

## <a name="recommendation-summary"></a>Aanbevelingssamenvatting

De volgende lijst bevat een overzicht van de aanbevelingen voor batching die in dit artikel worden besproken:

* Gebruik buffering en batching om de prestaties en schaalbaarheid van SQL Database-toepassingen te verhogen.
* Begrijp de afwegingen tussen batching/buffering en tolerantie. Tijdens een rolfout kan het risico van het verliezen van een onverwerkte batch bedrijfskritieke gegevens opwegen tegen het prestatievoordeel van batching.
* Probeer alle oproepen naar de database binnen één datacenter te houden om de latentie te verminderen.
* Als u kiest voor één batchingtechniek, bieden tabelwaardeparameters de beste prestaties en flexibiliteit.
* Volg deze algemene richtlijnen voor de snelste invoegprestaties, maar test uw scenario:
  * Gebruik voor < 100 rijen één parametermet de opdracht INVOEGEN.
  * Voor < 1000 rijen gebruikt u parameters met tabelwaarde.
  * Voor >= 1000 rijen gebruikt u SqlBulkCopy.
* Voor het bijwerken en verwijderen van bewerkingen gebruikt u parameters met tabelwaarde met opgeslagen procedurelogica die de juiste bewerking bepaalt voor elke rij in de tabelparameter.
* Richtlijnen voor batchgrootte:
  * Gebruik de grootste batchgroottes die zinvol zijn voor uw toepassing en zakelijke vereisten.
  * Breng de prestatiewinst van grote batches in evenwicht met de risico's van tijdelijke of catastrofale storingen. Wat is het gevolg van nieuwe pogingen of verlies van de gegevens in de batch? 
  * Test de grootste batchgrootte om te controleren of SQL Database deze niet afwijst.
  * Maak configuratie-instellingen die batching beheren, zoals de batchgrootte of het buffertijdvenster. Deze instellingen bieden flexibiliteit. U het batchgedrag in de productie wijzigen zonder de cloudservice opnieuw te implementeren.
* Vermijd parallelle uitvoering van batches die werken op één tabel in één database. Als u ervoor kiest om één batch over meerdere werknemersthreads te verdelen, voert u tests uit om het ideale aantal threads te bepalen. Na een niet-gespecificeerde drempelwaarde zullen meer threads de prestaties verlagen in plaats van deze te verhogen.
* Overweeg buffering op grootte en tijd als een manier om batching te implementeren voor meer scenario's.

## <a name="next-steps"></a>Volgende stappen

Dit artikel richtte zich op hoe databaseontwerp- en coderingstechnieken met betrekking tot batching de prestaties en schaalbaarheid van uw toepassing kunnen verbeteren. Maar dit is slechts een factor in uw algemene strategie. Zie [Azure SQL Database-prestatierichtlijnen voor afzonderlijke databases](sql-database-performance-guidance.md) en [prijs- en prestatieoverwegingen voor een elastische pool voor](sql-database-elastic-pool-guidance.md)meer manieren om de prestaties en schaalbaarheid te verbeteren.

