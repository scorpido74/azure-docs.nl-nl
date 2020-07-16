---
title: "Quickstart: Een zoekindex maken in Postman met behulp van REST API's"
titleSuffix: Azure Cognitive Search
description: In deze quickstart over REST API's leert u hoe u de REST API's van Azure Cognitive Search aanroept met behulp van Postman, inclusief voorbeeldgegevens en -definities.
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 02/10/2020
ms.openlocfilehash: 07c5e73ecd53bad0e5d5ec7959b288e0b6237a87
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171921"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-postman-using-rest-apis"></a>Quickstart: Een Azure Cognitive Search-index maken in Postman met behulp van REST API's
> [!div class="op_single_selector"]
> * [Postman](search-get-started-postman.md)
> * [C#](search-create-index-dotnet.md)
> * [Python](search-get-started-python.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
>*

Een van de eenvoudigste manieren om de [REST API voor Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice) te verkennen is door Postman of een ander webtestprogramma te gebruiken om HTTP-aanvragen te formuleren en de antwoorden te bekijken. Met de juiste hulpmiddelen en deze instructies kunt u aanvragen verzenden en antwoorden bekijken voordat u code gaat schrijven.

In dit artikel wordt uitgelegd hoe u aanvragen interactief kunt formuleren. U kunt ook een [Postman-verzameling downloaden en importeren](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) om vooraf gedefinieerde aanvragen te gebruiken.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

De volgende services en hulpprogramma's zijn vereist voor deze quickstart. 

+ U kunt met de [Postman-desktop-app](https://www.getpostman.com/) aanvragen verzenden naar Azure Cognitive Search.

+ [Maak een Azure Cognitive Search-service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U kunt een gratis service voor deze quickstart gebruiken. 

## <a name="get-a-key-and-url"></a>Een sleutel en URL ophalen

REST-aanroepen hebben voor elke aanvraag de service-URL en een toegangssleutel nodig. Een zoekservice wordt gemaakt met beide, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, volgt u deze stappen om de benodigde informatie op te halen:

1. [Meld u aan bij Azure Portal](https://portal.azure.com/) en haal op de pagina **Overzicht** van uw zoekservice de URL op. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Haal onder **Instellingen** > **Sleutels** een beheersleutel op voor volledige rechten op de service. Er zijn twee uitwisselbare beheersleutels die voor bedrijfscontinuïteit worden verstrekt voor het geval u een moet overschakelen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

![Een HTTP-eindpunt en toegangssleutel ophalen](media/search-get-started-postman/get-url-key.png "Een HTTP-eindpunt en toegangssleutel ophalen")

Voor alle aanvragen is een API-sleutel vereist op elke aanvraag die naar uw service wordt verzonden. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="connect-to-azure-cognitive-search"></a>Verbinding maken met Azure Cognitive Search

In deze sectie gebruikt u het webprogramma van uw keuze om verbindingen met Azure Cognitive Search in te stellen. Elk hulpprogramma bewaart informatie over aanvraagheaders voor de sessie, wat betekent dat u de API-sleutel en het inhoudstype maar één keer hoeft in te voeren.

Voor beide hulpprogramma's moet u een opdracht kiezen (GET, POST, PUT enzovoort), een URL-eindpunt opgeven en voor sommige taken JSON opgeven in de hoofdtekst van de aanvraag. Vervang de naam van de zoekservice (YOUR-SEARCH-SERVICE-NAME) door een geldige waarde. Voeg `$select=name` toe om alleen de naam van elke index te retourneren. 

> `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2020-06-30&$select=name`

Let op het HTTPS-voorvoegsel, de naam van de service, de naam van een object (in dit geval de indexverzameling) en de [API-versie](search-api-versions.md). De API-versie is een vereiste tekenreeks in kleine letters die wordt opgegeven als `?api-version=2020-06-30` voor de huidige versie. API-versies worden regelmatig bijgewerkt. Als u de API-versie toevoegt aan elke aanvraag, kunt u precies bepalen welke versie wordt gebruikt.  

De samenstelling van de aanvraagheader bevat twee elementen, inhoudstype en de API-sleutel waarmee u verificaties kunt uitvoeren bij Azure Cognitive Search. Vervang de API-sleutel van de beheerder (YOUR-AZURE-SEARCH-ADMIN-API-KEY) door een geldige waarde. 

```http
api-key: <YOUR-AZURE-SEARCH-ADMIN-API-KEY>
Content-Type: application/json
```

Formuleer in Postman een aanvraag die vergelijkbaar is met de volgende schermopname. Kies **GET** als bewerking, geef de URL op en klik op **Verzenden**. Met deze opdracht wordt verbinding gemaakt met Azure Cognitive Search, wordt de indexverzameling gelezen en wordt de HTTP-statuscode 200 geretourneerd bij een geslaagde verbinding. Als uw service al indexen heeft, bevat het antwoord ook indexdefinities.

![URL en header voor Postman-aanvraag](media/search-get-started-postman/postman-url.png "URL en header voor Postman-aanvraag")

## <a name="1---create-an-index"></a>1 - Een index maken

In Azure Cognitive Search maakt u doorgaans de index voordat u deze met gegevens laadt. De [REST API index maken](https://docs.microsoft.com/rest/api/searchservice/create-index) wordt gebruikt voor deze taak. 

De URL wordt uitgebreid met de indexnaam `hotels`.

U doet dit als volgt in Postman:

1. Wijzig het werkwoord in **PUT**.

2. Kopieer in deze URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart?api-version=2020-06-30`.

3. Geef in de hoofdtekst van de aanvraag de indexdefinitie (voor kopiëren gereedstaande code wordt hieronder opgegeven) op.

4. Klik op **Verzenden**.

![Het JSON-document indexeren in de hoofdtekst van de aanvraag](media/search-get-started-postman/postman-request.png "Het JSON-document indexeren in de hoofdtekst van de aanvraag")

### <a name="index-definition"></a>Indexdefinitie

Met de verzameling velden wordt de structuur van een document gedefinieerd. Elk document moet deze velden bevatten en elk veld moet een gegevenstype hebben. Tekenreeksvelden worden gebruikt voor zoekopdrachten in de volledige tekst. Voeg daarom numerieke gegevens als tekenreeksen toe als u wilt dat naar die inhoud kan worden gezocht.

Kenmerken voor het veld bepalen de toegestane bewerking. De REST API's staan standaard veel bewerkingen toe. Standaard kunnen alle tekenreeksen bijvoorbeeld worden doorzocht, opgehaald en gefilterd en zijn ze geschikt voor facetten. Vaak hoeft u de kenmerken alleen in te stellen als u een bewerking wilt uitschakelen.

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

## <a name="2---load-documents"></a>2 - Documenten laden

De index maken en de index vullen zijn afzonderlijke stappen. In Azure Cognitive Search bevat de index alle doorzoekbare gegevens die u kunt aanleveren als JSON-documenten. De [REST API voor documenten toevoegen, bijwerken of verwijderen](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) wordt voor deze taak gebruikt. 

De URL is uitgebreid met de verzamelingen `docs` en de bewerking `index`.

U doet dit als volgt in Postman:

1. Wijzig de bewerking in **POST**.

2. Kopieer in deze URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2020-06-30`.

3. Geef in de hoofdtekst van de aanvraag de JSON-documenten (voor kopiëren gereedstaande code vindt u hieronder) op.

4. Klik op **Verzenden**.

![JSON-documenten in de hoofdtekst van de aanvraag](media/search-get-started-postman/postman-docs.png "JSON-documenten in de hoofdtekst van de aanvraag")

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

Over enkele seconden verschijnt er een HTTP 201-respons in de sessielijst. Dit geeft aan dat de documenten zijn gemaakt. 

Als u een 207-respons ontvang, is minimaal één document niet geüpload. Als u een 404-respons ontvangt, bevat de header of het hoofdgedeelte van de aanvraag een syntaxisfout: controleer of u het eindpunt hebt gewijzigd zodat dit `/docs/index` bevat.

> [!Tip]
> Voor bepaalde gegevensbronnen kunt u de alternatieve methode *indexer* gebruiken die de indexering vereenvoudigt en de hoeveelheid code die is vereist vermindert. Zie [Indexeerbewerkingen](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) voor meer informatie.


## <a name="3---search-an-index"></a>3 - Een index doorzoeken

Nu er een index en documenten zijn geladen, kunt u hier query's op uitvoeren met behulp van de instructie [REST API documenten doorzoeken](https://docs.microsoft.com/rest/api/searchservice/search-documents).

De URL is uitgebreid met een query-expressie die is opgegeven met behulp van de zoekoperator.

U doet dit als volgt in Postman:

1. Wijzig de bewerking in **GET**.

2. Kopieer in deze URL `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$count=true&api-version=2020-06-30`.

3. Klik op **Verzenden**.

Deze query is leeg en retourneert het aantal documenten in de zoekresultaten. De aanvraag en het antwoord moeten eruitzien als in de volgende schermopname voor Postman nadat u op **Send** (Verzenden) hebt geklikt. De statuscode moet 200 zijn.

 ![GET met zoekreeks in de URL](media/search-get-started-postman/postman-query.png "GET met zoekreeks in de URL")

Probeer een paar andere queryvoorbeelden om een idee te krijgen van de syntaxis. U kunt een tekenreekszoekopdracht uitvoeren, letterlijke $filter-query's uitvoeren, de ingestelde resultaten beperken, het bereik van de zoekopdracht aanpassen voor specifieke velden, en meer.

Wissel de huidige URL uit met de onderstaande items en klik steeds op **Verzenden** om de resultaten weer te geven.

```
# Query example 1 - Search on restaurant and wifi
# Return only the HotelName, Description, and Tags fields
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=restaurant wifi&$count=true&$select=HotelName,Description,Tags&api-version=2020-06-30

# Query example 2 - Apply a filter to the index to find hotels rated 4 or highter
# Returns the HotelName and Rating. Two documents match
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$filter=Rating gt 4&$select=HotelName,Rating&api-version=2020-06-30

# Query example 3 - Take the top two results, and show only HotelName and Category in the results
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=boutique&$top=2&$select=HotelName,Category&api-version=2020-06-30

# Query example 4 - Sort by a specific field (Address/City) in ascending order
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating&api-version=2020-06-30
```

## <a name="get-index-properties"></a>Indexeigenschappen ophalen
U kunt ook [Statistieken ophalen](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) gebruiken om een query naar aantallen documenten en indexgrootte uit te voeren: 

```
https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/stats?api-version=2020-06-30
```

Als u `/stats` toevoegt aan uw URL, worden indexgegevens geretourneerd. In Postman moet uw aanvraag er ongeveer als volgt uitzien en bevat de reactie het aantal documenten en de gebruikte ruimte in bytes.

 ![Indexgegevens ophalen](media/search-get-started-postman/postman-system-query.png "Indexgegevens ophalen")

Let op dat de syntaxis van api-version verschilt. Voeg voor deze aanvraag `?` toe aan api-version. Met de `?` scheidt u het URL-pad van de querytekenreeks, terwijl u met & elk 'naam=waarde'-paar in de queryreeks scheidt. Voor deze query is api-version het eerste en enige item in de querytekenreeks.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling **Alle resources** of **Resourcegroepen** in het navigatiedeelvenster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, indexeerfuncties en gegevensbronnen. U kunt afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u kerntaken moet uitvoeren, kunt u doorgaan met aanvullende REST API-aanroepen voor meer geavanceerde functies, zoals indexeerfuncties of [een Cognitive Search-pijplijn instellen](cognitive-search-tutorial-blob.md). Voor de volgende stap wordt de volgende koppeling aangeraden:

> [!div class="nextstepaction"]
> [Zelfstudie over REST: Semi-gestructureerde gegevens (JSON-blobs) indexeren en doorzoeken in Azure Cognitive Search](search-semi-structured-data.md)
