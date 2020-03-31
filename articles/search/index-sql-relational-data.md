---
title: SQL-relationele gegevens modelleren voor importeren en indexeren
titleSuffix: Azure Cognitive Search
description: Meer informatie over het modelleren van relationele gegevens, gedenormaliseerd tot een platte resultaatset, voor indexering en zoeken in volledige tekst in Azure Cognitive Search.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3b973dd05d23d190c77986ca9bf6d39656739cd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72790083"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>Relationele SQL-gegevens modelleren voor importeren en indexeren in Azure Cognitive Search

Azure Cognitive Search accepteert een platte rijset als invoer voor de [indexeringspijplijn](search-what-is-an-index.md). Als uw brongegevens afkomstig zijn van samengevoegde tabellen in een SQL Server-relationele database, wordt in dit artikel uitgelegd hoe u de resultaatset samenstellen en hoe u een bovenliggende-onderliggende relatie modelleren in een Azure Cognitive Search-index.

Ter illustratie verwijzen we naar een hypothetische hotels database, gebaseerd op [demo gegevens.](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels) Stel dat de database bestaat uit een Hotels $ tafel met 50 hotels, en een Kamers $ tafel met kamers van verschillende soorten, tarieven en voorzieningen, voor een totaal van 750 kamers. Er is een één-op-veel relatie tussen de tabellen. In onze benadering biedt een weergave de query die 50 rijen, één rij per hotel, retourneert, met bijbehorende kamerdetails die in elke rij zijn ingesloten.

   ![Tabellen en weergave in de hotels-database](media/index-sql-relational-data/hotels-database-tables-view.png "Tabellen en weergave in de hotels-database")


## <a name="the-problem-of-denormalized-data"></a>Het probleem van gedenormaliseerde gegevens

Een van de uitdagingen bij het werken met een-op-veel relaties is dat standaardquery's die zijn gebouwd op samengevoegde tabellen gedenormaliseerde gegevens retourneren, wat niet goed werkt in een Azure Cognitive Search-scenario. Neem het volgende voorbeeld in overweging dat zich aansluit bij hotels en kamers.

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
De resultaten van deze query retourneren alle velden van het hotel, gevolgd door alle velden van de ruimte, waarbij voorlopige hotelinformatie wordt herhaald voor elke kamerwaarde.

   ![Gedenormaliseerde gegevens, redundante hotelgegevens wanneer kamervelden worden toegevoegd](media/index-sql-relational-data/denormalize-data-query.png "Gedenormaliseerde gegevens, redundante hotelgegevens wanneer kamervelden worden toegevoegd")


Hoewel deze query op het oppervlak slaagt (met alle gegevens in een platte rijset), mislukt deze niet in het leveren van de juiste documentstructuur voor de verwachte zoekervaring. Tijdens het indexeren maakt Azure Cognitive Search één zoekdocument voor elke ingenomen rij. Als uw zoekdocumenten eruit zagen als de bovenstaande resultaten, zou u duplicaten hebben waargenomen - zeven afzonderlijke documenten voor het Twin Dome-hotel alleen. Een vraag over "hotels in Florida" zou zeven resultaten voor alleen de Twin Dome hotel terugkeren, duwen andere relevante hotels diep in de zoekresultaten.

Om de verwachte ervaring van één document per hotel te krijgen, moet u een rijset op de juiste granulariteit, maar met volledige informatie. Gelukkig u dit gemakkelijk doen door de vaststelling van de technieken in dit artikel.

## <a name="define-a-query-that-returns-embedded-json"></a>Een query definiëren die ingesloten JSON retourneert

Als u de verwachte zoekervaring wilt bieden, moet uw gegevensset bestaan uit één rij voor elk zoekdocument in Azure Cognitive Search. In ons voorbeeld willen we één rij voor elk hotel, maar we willen ook dat onze gebruikers kunnen zoeken op andere kamergerelateerde velden waar ze om geven, zoals het nachttarief, de grootte en het aantal bedden, of een uitzicht op het strand, die allemaal deel uitmaken van een kamerdetail.

De oplossing is om de ruimtedetails vast te leggen als geneste JSON en vervolgens de JSON-structuur in een veld in een weergave in te voegen, zoals in de tweede stap wordt weergegeven. 

1. Stel dat u twee aangesloten tafels, Hotels $ en Kamers $, die details bevatten voor 50 hotels en 750 kamers, en zijn toegetreden op het HotelID veld. Individueel bevatten deze tafels 50 hotels en 750 gerelateerde kamers.

    ```sql
    CREATE TABLE [dbo].[Hotels$](
      [HotelID] [nchar](10) NOT NULL,
      [HotelName] [nvarchar](255) NULL,
      [Description] [nvarchar](max) NULL,
      [Description_fr] [nvarchar](max) NULL,
      [Category] [nvarchar](255) NULL,
      [Tags] [nvarchar](255) NULL,
      [ParkingIncluded] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [LastRenovationDate] [smalldatetime] NULL,
      [Rating] [float] NULL,
      [StreetAddress] [nvarchar](255) NULL,
      [City] [nvarchar](255) NULL,
      [State] [nvarchar](255) NULL,
      [ZipCode] [nvarchar](255) NULL,
      [GeoCoordinates] [nvarchar](255) NULL
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
    GO

    CREATE TABLE [dbo].[Rooms$](
      [HotelID] [nchar](10) NULL,
      [Description] [nvarchar](255) NULL,
      [Description_fr] [nvarchar](255) NULL,
      [Type] [nvarchar](255) NULL,
      [BaseRate] [float] NULL,
      [BedOptions] [nvarchar](255) NULL,
      [SleepsCount] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [Tags] [nvarchar](255) NULL
    ) ON [PRIMARY]
    GO
    ```

2. Maak een weergave die bestaat uit alle`SELECT * from dbo.Hotels$`velden in de bovenliggende tabel ( ), met de toevoeging van een nieuw gebied *Kamers* dat de uitvoer van een geneste query bevat. Een **VOOR JSON** `SELECT * from dbo.Rooms$` AUTO clausule op structuren van de output als JSON. 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   De volgende schermafbeelding toont de resulterende weergave, met het veld *Kamers* nvarchar onderaan. Het veld *Kamers* bestaat alleen in de weergave Hotelkamers.

   ![HotelRooms bekijken](media/index-sql-relational-data/hotelsrooms-view.png "HoteRooms bekijken")

1. Voer `SELECT * FROM dbo.HotelRooms` uit om de rijset op te halen. Deze query retourneert 50 rijen, één per hotel, met bijbehorende kamerinformatie als JSON-verzameling. 

   ![Rowset van hotelkamers bekijken](media/index-sql-relational-data/hotelrooms-rowset.png "Rowset van hotelkamers bekijken")

Deze rijset is nu klaar voor import in Azure Cognitive Search.

> [!NOTE]
> Deze benadering gaat ervan uit dat ingesloten JSON zich onder de [maximale kolomgroottelimieten van SQL Server bevindt.](https://docs.microsoft.com/sql/sql-server/maximum-capacity-specifications-for-sql-server) Als uw gegevens niet passen, u een programmatische benadering proberen, zoals geïllustreerd in [Voorbeeld: Modelleer de AdventureWorks Inventory-database voor Azure Cognitive Search](search-example-adventureworks-modeling.md).

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>Gebruik een complexe collectie voor de "vele" kant van een één-op-veel relatie

Maak aan de azure cognitive search-kant een indexschema dat de één-op-veelrelatie modelleert met behulp van geneste JSON. De resultaatset die u in de vorige sectie hebt gemaakt, komt over het algemeen overeen met het onderstaande indexschema (we snijden een aantal velden voor beknoptheid).

Het volgende voorbeeld is vergelijkbaar met het voorbeeld in [Hoe complexe gegevenstypen te modelleren](search-howto-complex-data-types.md#creating-complex-fields). De *kamers* structuur, die is de focus van dit artikel, is in de velden collectie van een index genaamd *hotels*. In dit voorbeeld wordt ook een complex type voor *Adres*weergegeven, dat verschilt van *Kamers* omdat het bestaat uit een vaste set items, in tegenstelling tot het meervoudige, willekeurige aantal items dat in een verzameling is toegestaan.

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
        { "name": "BedOptions", "type": "Edm.String", "searchable": true, "filterable": true, "facetable": true },
        { "name": "SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
        { "name": "SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
        { "name": "Tags", "type": "Edm.Collection", "searchable": true }
      ]
    }
  ]
}
```

Gezien de vorige resultaatset en het bovenstaande indexschema beschikt u over alle vereiste componenten voor een succesvolle indexeringsbewerking. De afgevlakte gegevensset voldoet aan indexeringsvereisten, maar behoudt detailgegevens. In de Azure Cognitive Search-index vallen zoekresultaten gemakkelijk in hotelgebaseerde entiteiten, met behoud van de context van afzonderlijke kamers en hun kenmerken.

## <a name="next-steps"></a>Volgende stappen

Met uw eigen gegevensset u de [wizard Gegevens importeren](search-import-data-portal.md) gebruiken om de index te maken en te laden. De wizard detecteert de ingesloten JSON-verzameling, zoals die in *Kamers,* en leidt een indexschema af dat een complexe tekstverzameling bevat. 

  ![Index afgeleid door wizard Gegevens importeren](media/index-sql-relational-data/search-index-rooms-complex-collection.png "Index afgeleid door wizard Gegevens importeren")

Probeer de volgende snelstart om de basisstappen van de wizard Gegevens importeren te leren.

> [!div class="nextstepaction"]
> [Snelstart: een zoekindex maken met Azure-portal](search-get-started-portal.md)