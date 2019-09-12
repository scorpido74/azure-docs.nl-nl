---
title: "Quickstart: Een zoek index in postman maken met REST-Api's-Azure Search"
description: Meer informatie over het aanroepen van de Azure Search REST-Api's met behulp van Postman en voorbeeld gegevens en definities.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 09/10/2019
ms.author: heidist
ms.openlocfilehash: 138fd3d50a5f462c6a0ad954043f107c3c250917
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881532"
---
# <a name="quickstart-create-an-azure-search-index-in-postman-using-rest-apis"></a>Quickstart: Een Azure Search index maken in postman met REST-Api's
> [!div class="op_single_selector"]
> * [Postman](search-get-started-postman.md)
> * [C#](search-create-index-dotnet.md)
> * [Python](search-get-started-python.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
>*

Een van de eenvoudigste manieren om de [Azure Search rest api's](https://docs.microsoft.com/rest/api/searchservice) te verkennen, is het gebruik van Postman of een ander hulp programma voor webtests om HTTP-aanvragen te formuleren en de antwoorden te controleren. Met de juiste hulpmiddelen en deze instructies kunt u aanvragen verzenden en antwoorden bekijken voordat u code gaat schrijven.

In dit artikel wordt uitgelegd hoe u aanvragen interactief kunt formuleren. U kunt ook [een postman-verzameling downloaden en importeren](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) om vooraf gedefinieerde aanvragen te gebruiken.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

De volgende services en hulpprogram ma's zijn vereist voor deze Quick Start. 

+ [Postman desktop-app](https://www.getpostman.com/) wordt gebruikt voor het verzenden van aanvragen naar Azure Search.

+ [Een Azure Search-service maken](search-create-service-portal.md) of [een bestaande service vinden](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U kunt een gratis service voor deze Quick Start gebruiken. 

## <a name="get-a-key-and-url"></a>Een sleutel en URL ophalen

REST-aanroepen hebben voor elke aanvraag de service-URL en een toegangssleutel nodig. Een zoekservice wordt gemaakt met beide, dus als u Azure Search hebt toegevoegd aan uw abonnement, volgt u deze stappen om de benodigde gegevens op te halen:

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/)en down load de URL op de pagina **overzicht** van de zoek service. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Haal in **instellingen** > **sleutels**een beheerders sleutel op voor volledige rechten op de service. Er zijn twee uitwissel bare beheer sleutels die voor bedrijfs continuïteit worden verschaft, voor het geval dat u een voor beeld moet doen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

![Een HTTP-eind punt en toegangs sleutel ophalen](media/search-get-started-postman/get-url-key.png "Een HTTP-eind punt en toegangs sleutel ophalen")

Voor alle aanvragen is een API-sleutel vereist voor elke aanvraag die naar uw service wordt verzonden. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="connect-to-azure-search"></a>Verbinding maken met Azure Search

In deze sectie gebruikt u het webprogramma van de keuze om verbindingen met Azure Search in te stellen. Elk hulp programma bewaart informatie over de aanvraag header voor de sessie, wat betekent dat u slechts één keer de API-sleutel en het inhouds type hoeft in te voeren.

Voor beide hulp middelen moet u een opdracht kiezen (GET, POST, PUT enzovoort), een URL-eind punt opgeven en voor sommige taken JSON opgeven in de hoofd tekst van de aanvraag. Vervang de naam van de zoek service (uw-zoek service naam) door een geldige waarde. Voeg `$select=name` alleen de naam van elke index toe. 

    https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name

Let op het HTTPS-voor voegsel, de naam van de service, de naam van een object (in dit geval de index verzameling) en de [API-versie](search-api-versions.md). De API-Version is een vereiste, kleine letter reeks die `?api-version=2019-05-06` is opgegeven als voor de huidige versie. API-versies worden regel matig bijgewerkt. Als u de API-versie toevoegt aan elke aanvraag, kunt u precies bepalen welke versie wordt gebruikt.  

De samen stelling van de aanvraag header bevat twee elementen, inhouds type, plus de API-sleutel die wordt gebruikt om te verifiëren bij Azure Search. Vervang de API-sleutel van de beheerder (uw-AZURE-SEARCH-ADMIN-API-sleutel) door een geldige waarde. 

    api-key: <YOUR-AZURE-SEARCH-ADMIN-API-KEY>
    Content-Type: application/json

In postman een aanvraag formuleren die eruitziet als de volgende scherm afbeelding. Kies **ophalen** als werk woord, geef de URL op en klik op **verzenden**. Met deze opdracht wordt verbinding gemaakt met Azure Search, wordt de verzameling indexen gelezen en wordt de HTTP-status code 200 geretourneerd op een geslaagde verbinding. Als uw service al indexen heeft, bevat het antwoord ook index definities.

![URL en header] van Postman-aanvraag (media/search-get-started-postman/postman-url.png "URL en header") van Postman-aanvraag

## <a name="1---create-an-index"></a>1 - Een index maken

In Azure Search maakt u doorgaans de index voordat u deze met gegevens laadt. De [rest API Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) wordt voor deze taak gebruikt. 

De URL is uitgebreid met de naam `hotels` van de index.

U doet dit als volgt in postman:

1. Wijzig de term in **put**.

2. Kopiëren in deze URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06`.

3. Geef in de hoofd tekst van de aanvraag de index definitie (code kopiëren-gereed) op.

4. Klik op **verzenden**.

Het ![JSON-document indexeren in de hoofd tekst van de aanvraag] Het (media/search-get-started-postman/postman-request.png "JSON-document indexeren in de hoofd tekst van de aanvraag")

### <a name="index-definition"></a>Indexdefinitie

De verzameling velden definieert de document structuur. Elk document moet deze velden bevatten en elk veld moet een gegevens type hebben. Tekenreeksvelden worden gebruikt voor zoekopdrachten in de volledige tekst. Voeg daarom numerieke gegevens als tekenreeksen toe als u wilt dat naar die inhoud kan worden gezocht.

Kenmerken voor het veld bepalen de toegestane bewerking. De REST API's staan standaard veel bewerkingen toe. Standaard kunnen alle tekenreeksen bijvoorbeeld worden doorzocht, opgehaald en gefilterd en zijn ze geschikt voor facetten. Vaak hoeft u alleen kenmerken in te stellen wanneer u een gedrag moet uitschakelen.

```json
{
    "name": "hotels-quickstart",  
    "fields": [
        {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
        {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
        {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
        {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
        {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
        {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
        {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
        {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
        {"name": "Address", "type": "Edm.ComplexType", 
        "fields": [
        {"name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true},
        {"name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
        {"name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
        {"name": "PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
        {"name": "Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true}
        ]
     }
  ]
}
```

Wanneer u deze aanvraag indient, krijgt u een HTTP 201-respons om aan te geven dat de index is gemaakt. U kunt deze bewerking controleren in de portal, maar houd er rekening mee dat de portalpagina met vernieuwingsintervallen werkt, zodat het enkele minuten kan duren voordat deze actueel is.

> [!TIP]
> Als u een HTTP 504-respons ontvangt, controleert u of de URL HTTPS bevat. Als de HTTP-fout 400 of 404 wordt weergegeven ziet, controleert u of de aanvraagtekst op fouten die mogelijk zijn opgetreden tijden kopiëren en plakken. Een HTTP 403 duidt doorgaans op een probleem met de API-sleutel (een ongeldige sleutel of een syntaxisfout in de opgegeven API-sleutel).

## <a name="2---load-documents"></a>2-documenten laden

De index maken en de index vullen zijn afzonderlijke stappen. In Azure Search bevat de index alle doorzoekbare gegevens die u kunt aanleveren als JSON-documenten. De [rest API voor het toevoegen, bijwerken of verwijderen van documenten](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) wordt voor deze taak gebruikt. 

De URL is uitgebreid met de verzamelingen `docs` en `index` de bewerking.

U doet dit als volgt in postman:

1. Wijzig de bewerking in **POST**.

2. Kopiëren in deze URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06`.

3. Geef in de hoofd tekst van de aanvraag de JSON-documenten (code kopiëren-gereed hieronder) op.

4. Klik op **verzenden**.

![JSON-documenten in de hoofd tekst van de aanvraag](media/search-get-started-postman/postman-docs.png "JSON-documenten in de hoofd tekst van de aanvraag")

### <a name="json-documents-to-load-into-the-index"></a>JSON-documenten die in de index moeten worden geladen

De aanvraagtekst bevat vier documenten die moeten worden toegevoegd aan de index hotels.

```json
{
    "value": [
    {
    "@search.action": "upload",
    "HotelId": "1",
    "HotelName": "Secret Point Motel",
    "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
    "Category": "Boutique",
    "Tags": [ "pool", "air conditioning", "concierge" ],
    "ParkingIncluded": false,
    "LastRenovationDate": "1970-01-18T00:00:00Z",
    "Rating": 3.60,
    "Address": 
        {
        "StreetAddress": "677 5th Ave",
        "City": "New York",
        "StateProvince": "NY",
        "PostalCode": "10022",
        "Country": "USA"
        } 
    },
    {
    "@search.action": "upload",
    "HotelId": "2",
    "HotelName": "Twin Dome Motel",
    "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
    "Category": "Boutique",
    "Tags": [ "pool", "free wifi", "concierge" ],
    "ParkingIncluded": false,
    "LastRenovationDate": "1979-02-18T00:00:00Z",
    "Rating": 3.60,
    "Address": 
        {
        "StreetAddress": "140 University Town Center Dr",
        "City": "Sarasota",
        "StateProvince": "FL",
        "PostalCode": "34243",
        "Country": "USA"
        } 
    },
    {
    "@search.action": "upload",
    "HotelId": "3",
    "HotelName": "Triple Landscape Hotel",
    "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
    "Category": "Resort and Spa",
    "Tags": [ "air conditioning", "bar", "continental breakfast" ],
    "ParkingIncluded": true,
    "LastRenovationDate": "2015-09-20T00:00:00Z",
    "Rating": 4.80,
    "Address": 
        {
        "StreetAddress": "3393 Peachtree Rd",
        "City": "Atlanta",
        "StateProvince": "GA",
        "PostalCode": "30326",
        "Country": "USA"
        } 
    },
    {
    "@search.action": "upload",
    "HotelId": "4",
    "HotelName": "Sublime Cliff Hotel",
    "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
    "Category": "Boutique",
    "Tags": [ "concierge", "view", "24-hour front desk service" ],
    "ParkingIncluded": true,
    "LastRenovationDate": "1960-02-06T00:00:00Z",
    "Rating": 4.60,
    "Address": 
        {
        "StreetAddress": "7400 San Pedro Ave",
        "City": "San Antonio",
        "StateProvince": "TX",
        "PostalCode": "78216",
        "Country": "USA"
        }
    }
  ]
}
```

In een paar seconden ziet u een HTTP 201-antwoord in de sessie lijst. Dit geeft aan dat de documenten zijn gemaakt. 

Als u een 207-respons ontvang, is minimaal één document niet geüpload. Als u een 404-respons ontvangt, bevat de header of het hoofdgedeelte van de aanvraag een syntaxisfout: controleer of u het eindpunt hebt gewijzigd zodat dit `/docs/index` bevat.

> [!Tip]
> Voor bepaalde gegevensbronnen kunt u de alternatieve methode *indexer* gebruiken die de indexering vereenvoudigt en de hoeveelheid code die is vereist vermindert. Zie [Indexeerbewerkingen](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) voor meer informatie.


## <a name="3---search-an-index"></a>3 - Een index doorzoeken

Nu u een index en documenten hebt geladen, kunt u er query's mee verzenden met behulp van [Zoek documenten rest API](https://docs.microsoft.com/rest/api/searchservice/search-documents).

De URL is uitgebreid met een query-expressie die is opgegeven met behulp van de zoek operator.

U doet dit als volgt in postman:

1. Wijzig de term in **Get**.

2. Kopiëren in deze URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$count=true&api-version=2019-05-06`.

3. Klik op **verzenden**.

Deze query is leeg en retourneert een telling van de documenten in de zoek resultaten. De aanvraag en het antwoord moeten eruitzien als in de volgende schermopname voor Postman nadat u op **Send** (Verzenden) hebt geklikt. De statuscode moet 200 zijn.

 ![Ophalen met zoek reeks in de URL](media/search-get-started-postman/postman-query.png "Ophalen met zoek reeks in de URL")

Voer een paar andere query voorbeelden uit om een idee te krijgen van de syntaxis. U kunt een teken reeks zoeken, Verbatim $filter query's, de ingestelde resultaten beperken, de zoek opdracht bereiken naar specifieke velden, en meer.

Wissel de huidige URL uit met de onderstaande items en klik op **verzenden** elke keer om de resultaten weer te geven.

```
# Query example 1 - Search on restaurant and wifi
# Return only the HotelName, Description, and Tags fields
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=restaurant wifi&$count=true&$select=HotelName,Description,Tags&api-version=2019-05-06

# Query example 2 - Apply a filter to the index to find hotels rated 4 or highter
# Returns the HotelName and Rating. Two documents match
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$filter=Rating gt 4&$select=HotelName,Rating&api-version=2019-05-06

# Query example 3 - Take the top two results, and show only HotelName and Category in the results
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=boutique&$top=2&$select=HotelName,Category&api-version=2019-05-06

# Query example 4 - Sort by a specific field (Address/City) in ascending order
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating&api-version=2019-05-06
```

## <a name="get-index-properties"></a>Index eigenschappen ophalen
U kunt ook [Get Statistics](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) gebruiken om te zoeken naar de document tellingen en de index grootte: 

```
https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/stats?api-version=2019-05-06`
```

Door `/stats` toe te voegen aan uw URL worden index gegevens geretourneerd. In Postman moet uw aanvraag er ongeveer als volgt uitzien en bevat de reactie het aantal documenten en de gebruikte ruimte in bytes.

 ![Index gegevens ophalen](media/search-get-started-postman/postman-system-query.png "Index gegevens ophalen")

Let op dat de syntaxis van api-version verschilt. Voeg voor deze aanvraag `?` toe aan api-version. De `?` scheidt het URL-pad van de query teken reeks, terwijl & elk paar ' name = waarde ' in de query teken reeks scheidt. Voor deze query is api-version het eerste en enige item in de querytekenreeks.

## <a name="clean-up"></a>Opruimen

Wanneer u in uw eigen abonnement werkt, is het een goed idee aan het einde van een project om te bepalen of u nog steeds de resources nodig hebt die u hebt gemaakt. Resources die actief zijn, kunnen kosten in rekening worden. U kunt resources afzonderlijk verwijderen of de resource groep verwijderen om de volledige set resources te verwijderen.

U kunt resources vinden en beheren in de portal met behulp van de koppeling **alle resources** of **resource groepen** in het navigatie deel venster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, Indexeer functies en gegevens bronnen. U kunt afzonderlijke items in de Portal verwijderen om de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u kern taken moet uitvoeren, kunt u door gaan met aanvullende REST API-aanroepen voor meer geavanceerde functies, zoals indexers of [het instellen van een cognitieve Zoek pijplijn](cognitive-search-tutorial-blob.md). Voor de volgende stap wordt u aangeraden de volgende koppeling te volgen:

> [!div class="nextstepaction"]
> [REST zelf studie: Semi-gestructureerde gegevens (JSON-blobs) indexeren en doorzoeken in Azure Search](search-semi-structured-data.md)
