---
title: Zoeken via Azure SQL-gegevens
titleSuffix: Azure Cognitive Search
description: Importeer gegevens uit Azure SQL Database met indexeerders voor zoeken in volledige tekst in Azure Cognitive Search. Dit artikel behandelt verbindingen, indexerconfiguratie en gegevensopname.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c09727e8d92a449b41124eae6ad8381d66cb2619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113302"
---
# <a name="connect-to-and-index-azure-sql-database-content-using-an-azure-cognitive-search-indexer"></a>Verbinding maken met Azure SQL Database-inhoud en indexeren met een Azure Cognitive Search-indexer

Voordat u een [Azure Cognitive Search-index](search-what-is-an-index.md)opvragen, moet u deze vullen met uw gegevens. Als de gegevens in een Azure SQL-database worden weergegeven, kan een **Azure Cognitive Search-indexer voor Azure SQL Database** (of Azure **SQL-indexer** kortweg) het indexeringsproces automatiseren, wat betekent dat er minder code moet worden geschreven en minder infrastructuur om u zorgen over te maken.

Dit artikel behandelt de mechanica van het gebruik van [indexeerders,](search-indexer-overview.md)maar beschrijft ook functies die alleen beschikbaar zijn met Azure SQL-databases (bijvoorbeeld geïntegreerde change tracking). 

Naast Azure SQL-databases biedt Azure Cognitive Search indexers voor [Azure Cosmos DB,](search-howto-index-cosmosdb.md) [Azure Blob-opslag](search-howto-indexing-azure-blob-storage.md)en [Azure-tabelopslag](search-howto-indexing-azure-tables.md). Als u ondersteuning wilt aanvragen voor andere gegevensbronnen, geeft u uw feedback op het [feedbackforum Azure Cognitive Search.](https://feedback.azure.com/forums/263029-azure-search/)

## <a name="indexers-and-data-sources"></a>Indexers en gegevensbronnen

Een **gegevensbron** geeft aan welke gegevens moeten worden geïndexeerd, referenties voor gegevenstoegang en beleid dat wijzigingen in de gegevens efficiënt identificeert (nieuwe, gewijzigde of verwijderde rijen). Het wordt gedefinieerd als een onafhankelijke bron, zodat het kan worden gebruikt door meerdere indexeerders.

Een **indexer** is een bron die één gegevensbron verbindt met een gerichte zoekindex. Een indexeerder wordt op de volgende manieren gebruikt:

* Voer een eenmalige kopie van de gegevens uit om een index in te vullen.
* Een index bijwerken met wijzigingen in de gegevensbron in een planning.
* Voer on-demand uit om een index zo nodig bij te werken.

Eén indexeerder kan slechts één tabel of weergave gebruiken, maar u meerdere indexeerders maken als u meerdere zoekindexen wilt invullen. Zie [Indexerbewerkingen: Typische werkstroom voor](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations#typical-workflow)meer informatie over concepten.

U een Azure SQL-indexer instellen en configureren met:

* Wizard Gegevens importeren in de [Azure-portal](https://portal.azure.com)
* Azure Cognitive Search [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)
* Azure Cognitive Search [REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)

In dit artikel gebruiken we de REST API om **indexeerders** en **gegevensbronnen**te maken.

## <a name="when-to-use-azure-sql-indexer"></a>Wanneer Azure SQL Indexer gebruiken
Afhankelijk van verschillende factoren met betrekking tot uw gegevens, kan het gebruik van Azure SQL-indexer al dan niet geschikt zijn. Als uw gegevens aan de volgende vereisten voldoen, u Azure SQL-indexer gebruiken.

| Criteria | Details |
|----------|---------|
| Gegevens zijn afkomstig van één tabel of weergave | Als de gegevens over meerdere tabellen zijn verspreid, u één weergave van de gegevens maken. Als u echter een weergave gebruikt, u sql server-geïntegreerde wijzigingsdetectie niet gebruiken om een index te vernieuwen met incrementele wijzigingen. Zie Gewijzigde [rijen vastleggen en verwijderde rijen](#CaptureChangedRows) hieronder voor meer informatie. |
| Gegevenstypen zijn compatibel | De meeste, maar niet alle SQL-typen worden ondersteund in een Azure Cognitive Search-index. Zie [Gegevenstypen toewijzen](#TypeMapping)voor een lijst voor een lijst. |
| Real-time gegevenssynchronisatie is niet vereist | Een indexeerder kan uw tabel ten hoogste om de vijf minuten opnieuw indexeren. Als uw gegevens regelmatig worden gewijzigd en de wijzigingen binnen enkele seconden of enkele minuten in de index moeten worden weergegeven, raden we u aan de [REST API](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) of [.NET SDK](search-import-data-dotnet.md) te gebruiken om bijgewerkte rijen rechtstreeks te pushen. |
| Incrementele indexering is mogelijk | Als u een grote gegevensset hebt en van plan bent de indexerer volgens een planning uit te voeren, moet Azure Cognitive Search in staat zijn om nieuwe, gewijzigde of verwijderde rijen efficiënt te identificeren. Niet-incrementele indexering is alleen toegestaan als u op aanvraag indexeert (niet volgens schema) of als u minder dan 100.000 rijen indexeert. Zie Gewijzigde [rijen vastleggen en verwijderde rijen](#CaptureChangedRows) hieronder voor meer informatie. |

> [!NOTE] 
> Azure Cognitive Search ondersteunt alleen SQL Server-verificatie. Als u ondersteuning nodig hebt voor Azure Active Directory Password-verificatie, stemt u voor deze [UserVoice-suggestie.](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica)

## <a name="create-an-azure-sql-indexer"></a>Een Azure SQL-indexer maken

1. Maak de gegevensbron:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   U de verbindingstekenreeks ophalen via de [Azure-portal;](https://portal.azure.com) gebruik `ADO.NET connection string` maken van de optie.

2. Maak de doelazure cognitive search-index als u die nog niet hebt. U een index maken met behulp van de [portal](https://portal.azure.com) of de [API Voor indexmaken](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Controleer of het schema van uw doelindex compatibel is met het schema van de brontabel - zie [toewijzing tussen SQL- en Azure Cognitive-zoekgegevenstypen](#TypeMapping).

3. Maak de indexer door deze een naam te geven en verwijst naar de gegevensbron en doelindex:

    ```
    POST https://myservice.search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
    ```

Een indexeerder die op deze manier is gemaakt, heeft geen planning. Het wordt automatisch één keer uitgevoerd wanneer het wordt gemaakt. U het op elk gewenst moment opnieuw uitvoeren met een **run indexer-aanvraag:**

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2019-05-06
    api-key: admin-key

U verschillende aspecten van het indexergedrag aanpassen, zoals batchgrootte en hoeveel documenten kunnen worden overgeslagen voordat de uitvoering van een indexer mislukt. Zie [API voor indexeren maken](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)voor meer informatie .

Mogelijk moet u Azure-services toestaan verbinding te maken met uw database. Zie [Verbinding maken vanuit Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) voor instructies over hoe u dit doen.

Als u de status en uitvoeringsgeschiedenis van de indexer wilt controleren (aantal geïndexeerde items, fouten, enz.), gebruikt u een **statusaanvraag voor indexeren:**

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2019-05-06
    api-key: admin-key

Het antwoord moet op het volgende lijken:

    {
        "\@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }

Uitvoeringsgeschiedenis bevat maximaal 50 van de meest recent voltooide uitvoeringen, die in de omgekeerde chronologische volgorde worden gesorteerd (zodat de laatste uitvoering eerst in het antwoord komt).
Aanvullende informatie over het antwoord vindt u in [de status Indexer ontvangen](https://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Indexers op een planning uitvoeren
U de indexer ook regelmatig volgens een schema laten uitvoeren. Voeg hiervoor de eigenschap **planning** toe bij het maken of bijwerken van de indexeerder. In het onderstaande voorbeeld ziet u een PUT-verzoek om de indexeerder bij te werken:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

De **intervalparameter** is vereist. Het interval verwijst naar de tijd tussen het begin van twee opeenvolgende indexeruitvoeringen. Het kleinste toegestane interval is 5 minuten; de langste is een dag. Het moet worden opgemaakt als een XSD "dayTimeDuration" waarde (een beperkte subset van een [ISO 8601 duurwaarde).](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) Het patroon hiervoor `P(nD)(T(nH)(nM))`is: . Voorbeelden: `PT15M` voor elke 15 `PT2H` minuten, voor elke 2 uur.

Zie [Indexeerders voor Azure Cognitive Search](search-howto-schedule-indexers.md)voor meer informatie over het definiëren van indexerschema's.

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Nieuwe, gewijzigde en verwijderde rijen vastleggen

Azure Cognitive Search maakt gebruik **van incrementele indexering** om te voorkomen dat u de hele tabel opnieuw moet indexeren of elke keer dat een indexer wordt uitgevoerd. Azure Cognitive Search biedt twee wijzigingsdetectiebeleidsregels ter ondersteuning van incrementele indexering. 

### <a name="sql-integrated-change-tracking-policy"></a>SQL Integrated Change Tracking Policy
Als uw SQL-database [het bijhouden van wijzigingen](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server)ondersteunt, raden we u aan sql integrated change tracking policy **te**gebruiken. Dit is het meest efficiënte beleid. Bovendien kan Azure Cognitive Search verwijderde rijen identificeren zonder dat u een expliciete kolom 'soft delete' aan uw tabel hoeft toe te voegen.

#### <a name="requirements"></a>Vereisten 

+ Vereisten voor databaseversies:
  * SQL Server 2012 SP3 en hoger als u SQL Server gebruikt op Azure VM's.
  * Azure SQL Database V12, als u Azure SQL Database gebruikt.
+ Alleen tafels (geen weergaven). 
+ Schakel in de database [wijzigingstracking](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) in voor de tabel. 
+ Er hoeft geen samengestelde primaire sleutel (een primaire sleutel met meer dan één kolom) op de tabel.  

#### <a name="usage"></a>Gebruik

Als u dit beleid wilt gebruiken, maakt of werkt u uw gegevensbron als volgt aan:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

Wanneer u SQL-beleid voor het bijhouden van geïntegreerde wijzigingen gebruikt, geeft u geen afzonderlijk detectiebeleid voor gegevensverwijdering op - dit beleid heeft ingebouwde ondersteuning voor het identificeren van verwijderde rijen. Om de deletes echter "automatisch" te kunnen detecteren, moet de documentsleutel in uw zoekindex dezelfde zijn als de primaire sleutel in de SQL-tabel. 

> [!NOTE]  
> Wanneer u [TRUNCATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/truncate-table-transact-sql) gebruikt om een groot aantal rijen uit een SQL-tabel te verwijderen, moet de indexer worden [gereset](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) om de status van het bijhouden van wijzigingen opnieuw in te stellen om rijverwijderingen op te pikken.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Detectiebeleid voor hoogwatermarkeringswissel

Dit wijzigingsdetectiebeleid is gebaseerd op een kolom 'hoogwatermarkering' die de versie of het tijdstip vastlegt waarop een rij voor het laatst is bijgewerkt. Als u een weergave gebruikt, moet u een hoog watermerkbeleid gebruiken. De hoogwatermarkeringskolom moet aan de volgende eisen voldoen.

#### <a name="requirements"></a>Vereisten 

* Alle invoegsels geven een waarde voor de kolom op.
* Alle updates voor een item wijzigen ook de waarde van de kolom.
* De waarde van deze kolom neemt toe met elke invoeging of update.
* Query's met de volgende WHERE- en ORDER BY-clausules kunnen efficiënt worden uitgevoerd:`WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> We raden ten zeerste aan om het gegevenstype [rowversion](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql) te gebruiken voor de hoogwatermarkeringskolom. Als een ander gegevenstype wordt gebruikt, is het niet gegarandeerd dat wijzigingstracking alle wijzigingen vastlegt in de aanwezigheid van transacties die gelijktijdig met een indexerquery worden uitgevoerd. Wanneer u **rijversie** gebruikt in een configuratie met alleen-lezen replica's, moet u de indexer op de primaire replica aanwijzen. Alleen een primaire replica kan worden gebruikt voor scenario's voor het synchroniseren van gegevens.

#### <a name="usage"></a>Gebruik

Als u een hoogwatermarkeringsbeleid wilt gebruiken, maakt of werkt u uw gegevensbron als volgt bij:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }

> [!WARNING]
> Als de brontabel geen index heeft op de hoogwatermarkeringskolom, kunnen query's die door de SQL-indexer worden gebruikt, een time-out krijgen. In het `ORDER BY [High Water Mark Column]` bijzonder vereist de clausule dat een index efficiënt moet worden uitgevoerd wanneer de tabel veel rijen bevat.
>
>

Als u time-outfouten tegenkomt, `queryTimeout` u de configuratie-instelling indexer gebruiken om de time-out van de query in te stellen op een waarde die hoger is dan de standaardtime-out van 5 minuten. Als u bijvoorbeeld de time-out wilt instellen op 10 minuten, maakt of werkt u de indexer met de volgende configuratie:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

U de `ORDER BY [High Water Mark Column]` clausule ook uitschakelen. Dit wordt echter niet aanbevolen, omdat als de indexeruitvoering wordt onderbroken door een fout, de indexer alle rijen opnieuw moet verwerken als deze later wordt uitgevoerd - zelfs als de indexer al bijna alle rijen heeft verwerkt tegen de tijd dat deze werd onderbroken. Als u `ORDER BY` de clausule `disableOrderByHighWaterMarkColumn` wilt uitschakelen, gebruikt u de instelling in de definitie van indexer:  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Detectiebeleid voor het verwijderen van kolommen voor zachte verwijdering verwijderen
Wanneer rijen uit de brontabel worden verwijderd, wilt u deze rijen waarschijnlijk ook uit de zoekindex verwijderen. Als u het SQL-beleid voor het bijhouden van wijzigingen gebruikt, wordt dit voor u geregeld. Het trackingbeleid voor hoogwaterwijzigingen helpt u echter niet bij verwijderde rijen. Wat u moet doen?

Als de rijen fysiek uit de tabel worden verwijderd, kan Azure Cognitive Search de aanwezigheid van records die niet meer bestaan, niet afleiden.  U echter de techniek 'soft-delete' gebruiken om rijen logisch te verwijderen zonder ze uit de tabel te verwijderen. Voeg een kolom toe aan uw tabel of weergave en markeer rijen als verwijderd met die kolom.

Wanneer u de soft-delete-techniek gebruikt, u het beleid voor zachte verwijdering als volgt opgeven bij het maken of bijwerken van de gegevensbron:

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

De **softDeleteMarkerValue** moet een tekenreeks zijn : gebruik de tekenreeksweergave van uw werkelijke waarde. Als u bijvoorbeeld een kolom integer hebt waarin verwijderde rijen zijn `"1"`gemarkeerd met de waarde 1, gebruikt u . Als u een BIT-kolom hebt waarin verwijderde rijen zijn gemarkeerd met `True` `true`de werkelijke waarde Booleaanse letters, gebruikt u de tekenreeks letterlijk of , de aanvraag maakt niet uit.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-cognitive-search-data-types"></a>Toewijzing tussen SQL- en Azure Cognitive Search-gegevenstypen
| SQL-gegevenstype | Toegestane doelindexveldtypen | Opmerkingen |
| --- | --- | --- |
| bit |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.String | |
| echt, zweven |Edm.Double, Edm.String | |
| smallmoney, geld decimale numerieke |Edm.String |Azure Cognitive Search ondersteunt geen conversie van decimale typen naar Edm.Double omdat dit precisie zou verliezen |
| char, nchar, varchar, nvarchar |Edm.String<br/>Collection(EDM.String) |Een SQL-tekenreeks kan worden gebruikt om een veld Collection(Edm.String) in te vullen als de tekenreeks een JSON-array met tekenreeksen vertegenwoordigt:`["red", "white", "blue"]` |
| smalldatetijd, datumtijd, datumtijd2, datum, datumverschuiving |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifer |Edm.String | |
| Geografie |Edm.GeographyPoint |Alleen geografische exemplaren van type POINT met SRID 4326 (wat de standaardinstelling is) worden ondersteund |
| rijversie |N.v.t. |Kolommen in rijversies kunnen niet worden opgeslagen in de zoekindex, maar ze kunnen wel worden gebruikt voor het bijhouden van wijzigingen |
| tijd, tijdspanne, binair, varbinary, afbeelding, xml, geometrie, CLR-typen |N.v.t. |Niet ondersteund |

## <a name="configuration-settings"></a>Configuratie-instellingen
SQL-indexer legt verschillende configuratie-instellingen bloot:

| Instelling | Gegevenstype | Doel | Standaardwaarde |
| --- | --- | --- | --- |
| queryTime-out |tekenreeks |Hiermee stelt u een time-out in voor SQL-queryuitvoering |5 minuten ("00:05:00") |
| orderbyhogewatermarkkolom uitschakelen |Booleaanse waarde |Hiermee wordt de SQL-query die wordt gebruikt door het hoogwatermarkeringsbeleid de ORDER BY-component weglaten. Zie [Hoogwatermarkeringsbeleid](#HighWaterMarkPolicy) |false |

Deze instellingen worden `parameters.configuration` gebruikt in het object in de indexerdefinitie. Als u bijvoorbeeld de time-out van de query wilt instellen op 10 minuten, maakt of werkt u de indexer met de volgende configuratie:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="faq"></a>Veelgestelde vragen

**V: Kan ik Azure SQL-indexer gebruiken met SQL-databases die draaien op IaaS VM's in Azure?**

Ja. U moet uw zoekservice echter toestaan verbinding te maken met uw database. Zie [Een verbinding configureren van een Azure Cognitive Search-indexer naar SQL Server op een Azure VM](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)voor meer informatie.

**V: Kan ik Azure SQL-indexer gebruiken met SQL-databases die on-premises worden uitgevoerd?**

Niet direct. We raden een directe verbinding niet aan of ondersteunen deze, omdat u uw databases moet openen voor internetverkeer. Klanten zijn erin geslaagd met dit scenario met behulp van bridgetechnologieën zoals Azure Data Factory. Zie [Gegevens pushen naar een Azure Cognitive Search-index met Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-azure-search-connector)voor meer informatie.

**V: Kan ik Azure SQL-indexer gebruiken met andere databases dan SQL Server die in IaaS op Azure worden uitgevoerd?**

Nee. We ondersteunen dit scenario niet, omdat we de indexeerder niet hebben getest met andere databases dan SQL Server.  

**V: Kan ik meerdere indexers maken die volgens een schema werken?**

Ja. Er kan echter slechts één indexer tegelijk op één knooppunt worden uitgevoerd. Als u meerdere indexers tegelijk wilt uitvoeren, u overwegen uw zoekservice op te schalen naar meer dan één zoekeenheid.

**V: Heeft het uitvoeren van een indexer invloed op mijn querywerkbelasting?**

Ja. Indexer wordt uitgevoerd op een van de knooppunten in uw zoekservice en de bronnen van het knooppunt worden gedeeld tussen het indexeren en weergeven van queryverkeer en andere API-aanvragen. Als u intensieve indexerings- en queryworkloads uitvoert en een hoog percentage van 503 fouten of toenemende responstijden tegenkomt, u overwegen [uw zoekservice op te schalen.](search-capacity-planning.md)

**V: Kan ik een secundaire replica in een [failovercluster](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) gebruiken als gegevensbron?**

Dat hangt ervan af. Voor volledige indexering van een tabel of weergave u een secundaire replica gebruiken. 

Azure Cognitive Search ondersteunt twee wijzigingsdetectiebeleidsregels: SQL integrated change tracking en High Water Mark.

Op alleen-lezen replica's ondersteunt SQL-database geen geïntegreerde change tracking. Daarom moet u gebruik maken van High Water Mark beleid. 

Onze standaardaanbeveling is om het gegevenstype rowversion te gebruiken voor de hoogwatermarkeringskolom. Het gebruik van rowversion is echter `MIN_ACTIVE_ROWVERSION` afhankelijk van de functie van SQL Database, die niet wordt ondersteund op alleen-lezen replica's. Daarom moet u de indexer naar een primaire replica wijzen als u rijversie gebruikt.

Als u rowversion probeert te gebruiken op een alleen-lezen replica, ziet u de volgende fout: 

    "Using a rowversion column for change tracking is not supported on secondary (read-only) availability replicas. Please update the datasource and specify a connection to the primary availability replica.Current database 'Updateability' property is 'READ_ONLY'".

**V: Kan ik een alternatieve, niet-rijversiekolom gebruiken voor het bijhouden van hoogwatermarkeringen?**

Het is niet aan te raden. Alleen **rijversie** zorgt voor betrouwbare datasynchronisatie. Afhankelijk van de toepassingslogica kan het echter veilig zijn als:

+ U ervoor zorgen dat wanneer de indexer wordt uitgevoerd, er geen openstaande transacties op de tabel staan die wordt geïndexeerd (bijvoorbeeld alle tabelupdates vinden plaats als een batch op een schema en het Azure Cognitive Search-indexerschema is ingesteld om overlapping met de tabel te voorkomen updateschema).  

+ U doet periodiek een volledige reindex om gemiste rijen op te halen. 