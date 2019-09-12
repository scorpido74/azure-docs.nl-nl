---
title: "Quickstart: Een zoek index maken in Power shell met REST-Api's-Azure Search"
description: Hierin wordt uitgelegd hoe u een index maakt, gegevens laadt en query's uitvoert met behulp van de invoke-RestMethod van Power shell en de REST API van de Azure Search.
ms.date: 09/10/2019
author: heidisteen
manager: nitinme
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.openlocfilehash: ab82406fa151f5889a563d8154e02da921f1c4e6
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881715"
---
# <a name="quickstart-create-an-azure-search-index-in-powershell-using-rest-apis"></a>Quickstart: Een Azure Search-index maken in Power shell met REST Api's
> [!div class="op_single_selector"]
> * [Power shell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-get-started-postman.md)
> * [Python](search-get-started-python.md)
> * [Portal](search-create-index-portal.md)
> 

In dit artikel wordt stapsgewijs uitgelegd hoe u een Azure Search index maakt, laadt en doorzoekt met behulp van Power shell en de [Azure Search rest api's](https://docs.microsoft.com/rest/api/searchservice/). In dit artikel wordt uitgelegd hoe u Power shell-opdrachten interactief uitvoert. U kunt ook [een Power shell-script downloaden en uitvoeren](https://github.com/Azure-Samples/azure-search-powershell-samples/tree/master/Quickstart) waarmee dezelfde bewerkingen worden uitgevoerd.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

De volgende services en hulpprogram ma's zijn vereist voor deze Quick Start. 

+ [Power shell 5,1 of hoger](https://github.com/PowerShell/PowerShell), met [invoke-RestMethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) voor sequentiële en interactieve stappen.

+ [Een Azure Search-service maken](search-create-service-portal.md) of [een bestaande service vinden](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U kunt een gratis service voor deze Quick Start gebruiken. 

## <a name="get-a-key-and-url"></a>Een sleutel en URL ophalen

REST-aanroepen hebben voor elke aanvraag de service-URL en een toegangssleutel nodig. Een zoekservice wordt gemaakt met beide, dus als u Azure Search hebt toegevoegd aan uw abonnement, volgt u deze stappen om de benodigde gegevens op te halen:

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/)en down load de URL op de pagina **overzicht** van de zoek service. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

2. Haal in **instellingen** > **sleutels**een beheerders sleutel op voor volledige rechten op de service. Er zijn twee uitwissel bare beheer sleutels die voor bedrijfs continuïteit worden verschaft, voor het geval dat u een voor beeld moet doen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

![Een HTTP-eind punt en toegangs sleutel ophalen](media/search-get-started-postman/get-url-key.png "Een HTTP-eind punt en toegangs sleutel ophalen")

Voor alle aanvragen is een API-sleutel vereist voor elke aanvraag die naar uw service wordt verzonden. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="connect-to-azure-search"></a>Verbinding maken met Azure Search

1. Maak in Power shell een **$headers** -object om het inhouds type en de API-sleutel op te slaan. Vervang de API-sleutel van de beheerder (uw-beheer-API-sleutel) door een sleutel die geldig is voor uw zoek service. U hoeft deze header alleen eenmaal in te stellen voor de duur van de sessie, maar u kunt deze ook aan elke aanvraag toevoegen. 

    ```powershell
    $headers = @{
    'api-key' = '<YOUR-ADMIN-API-KEY>'
    'Content-Type' = 'application/json' 
    'Accept' = 'application/json' }
    ```

2. Maak een **$URL** -object dat de verzameling indexen van de service specificeert. Vervang de service naam (uw-zoek SERVICE naam) door een geldige zoek service.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name"
    ```

3. Voer **invoke-RestMethod** uit om een GET-aanvraag naar de service te verzenden en de verbinding te verifiëren. Voeg **ConvertTo-JSON** toe zodat u de antwoorden kunt weer geven die zijn teruggestuurd vanuit de service.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

   Als de service leeg is en geen indexen heeft, zijn de resultaten vergelijkbaar met het volgende voor beeld. Anders ziet u een JSON-weer gave van index definities.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
        "value":  [

                ]
    }
    ```

## <a name="1---create-an-index"></a>1 - Een index maken

Tenzij u de portal gebruikt, moet er een index op de service bestaan voordat u gegevens kunt laden. Met deze stap wordt de index gedefinieerd en naar de service gepusht. De [rest API Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) wordt gebruikt voor deze stap.

Vereiste elementen van een index bevatten een naam en een verzameling velden. De verzameling velden definieert de structuur van een *document*. Elk veld heeft een naam, type en kenmerken die bepalen hoe het wordt gebruikt (bijvoorbeeld of het in volledige tekst kan worden doorzocht, kan worden gefilterd of kunnen worden opgehaald in Zoek resultaten). Binnen een index moet een van de velden van het `Edm.String` type worden aangewezen als de *sleutel* voor document identiteit.

Deze index heet "Hotels-Quick Start" en heeft de veld definities die hieronder worden weer gegeven. Het is een subset van een grotere [Hotels index](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) die wordt gebruikt in andere scenario's. Deze Quick start is in deze Snelstartgids bijgesneden.

1. Plak dit voor beeld in Power shell om een **$Body** -object te maken dat het index schema bevat.

    ```powershell
    $body = @"
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
    "@
    ```

2. Stel de URI in op de verzameling indexen voor uw service en de *Snelstartgids-* index.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06"
    ```

3. Voer de opdracht uit met **$URL**, **$headers**en **$Body** om de index voor de service te maken. 

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
    ```

    De resultaten moeten er ongeveer als volgt uitzien (afgekapt tot de eerste twee velden voor de boog):

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag":  "\"0x8D6EDE28CFEABDA\"",
        "name":  "hotels-quickstart",
        "defaultScoringProfile":  null,
        "fields":  [
                    {
                        "name":  "HotelId",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  true,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  true,
                        "key":  true,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
                    {
                        "name":  "HotelName",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  false,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  false,
                        "key":  false,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
    . . .
    ```

> [!Tip]
> Voor verificatie kunt u ook de lijst indexen in de portal controleren.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2-documenten laden

Als u documenten wilt pushen, gebruikt u een HTTP POST-aanvraag voor het URL-eind punt van uw index. De REST API voor deze taak is het [toevoegen, bijwerken of verwijderen van documenten](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents).

1. Plak dit voor beeld in Power shell om een **$Body** -object te maken met de documenten die u wilt uploaden. 

    Deze aanvraag bevat twee volledige en één gedeeltelijke record. In de gedeeltelijke record ziet u dat u onvolledige documenten kunt uploaden. De `@search.action` para meter geeft aan hoe indexering is voltooid. Geldige waarden zijn upload, Merge, mergeOrUpload en DELETE. Met het gedrag mergeOrUpload maakt u een nieuw document voor hotelId = 3, of werkt u de inhoud bij als deze al bestaat.

    ```powershell
    $body = @"
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
    "@
    ```

1. Stel het eind punt in op de verzameling *Hotels-Quick* start docs en voeg de index bewerking (indices/Hotels-Quick Start/docs/index) toe.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06"
    ```

1. Voer de opdracht uit met **$URL**, **$headers**en **$Body** om documenten te laden in de hotels-index van Snelstartgids.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
    ```
    De resultaten moeten er ongeveer uitzien als in het volgende voor beeld. U ziet de [status code 201](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes).

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2019_05_06.IndexResult)",
        "value":  [
                    {
                        "key":  "1",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "2",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "3",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "4",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    }
                ]
    }
    ```

## <a name="3---search-an-index"></a>3 - Een index doorzoeken

In deze stap ziet u hoe u een query kunt uitvoeren op een index met behulp van de [Search-documenten-API](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Zorg ervoor dat u enkele aanhalings tekens gebruikt in de zoek $urls. Query reeksen bevatten **$** tekens en u kunt weglaten om ze te ontsluiten als de hele teken reeks tussen enkele aanhalings tekens is geplaatst.

1. Stel het eind punt in op de verzameling *Hotels-Quick* start docs en voeg een **Zoek** parameter toe om in een query reeks door te geven. 
  
   Met deze teken reeks wordt een lege zoek opdracht uitgevoerd (Search = *), waarbij een niet-gerangte lijst wordt geretourneerd (zoek score = 1,0) aan wille keurige documenten. Azure Search retourneert standaard 50 overeenkomsten per keer. Als gestructureerd retourneert deze query een volledige document structuur en-waarden. Voeg **$Count = True** toe om een telling van alle documenten in de resultaten op te halen.

    ```powershell
    $url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$count=true'
    ```

1. Voer de opdracht uit om de **$URL** te verzenden naar de service.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

    De resultaten moeten er ongeveer uitzien als in de volgende uitvoer.

    ```
    {
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#docs(*)",
    "@odata.count":  4,
    "value":  [
                  {
                      "@search.score":  0.1547872,
                      "HotelId":  "2",
                      "HotelName":  "Twin Dome Motel",
                      "Description":  "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                      "Category":  "Boutique",
                      "Tags":  "pool free wifi concierge",
                      "ParkingIncluded":  false,
                      "LastRenovationDate":  "1979-02-18T00:00:00Z",
                      "Rating":  3.6,
                      "Address":  "@{StreetAddress=140 University Town Center Dr; City=Sarasota; StateProvince=FL; PostalCode=34243; Country=USA}"
                  },
                  {
                      "@search.score":  0.009068266,
                      "HotelId":  "3",
                      "HotelName":  "Triple Landscape Hotel",
                      "Description":  "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel\u0027s restaurant services.",
                      "Category":  "Resort and Spa",
                      "Tags":  "air conditioning bar continental breakfast",
                      "ParkingIncluded":  true,
                      "LastRenovationDate":  "2015-09-20T00:00:00Z",
                      "Rating":  4.8,
                      "Address":  "@{StreetAddress=3393 Peachtree Rd; City=Atlanta; StateProvince=GA; PostalCode=30326; Country=USA}"
                  },
                . . . 
    ```

Voer een paar andere query voorbeelden uit om een idee te krijgen van de syntaxis. U kunt een teken reeks zoeken, Verbatim $filter query's, de ingestelde resultaten beperken, de zoek opdracht bereiken naar specifieke velden, en meer.

```powershell
# Query example 1
# Search the entire index for the terms 'restaurant' and 'wifi'
# Return only the HotelName, Description, and Tags fields
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=restaurant wifi&$count=true&$select=HotelName,Description,Tags'

# Query example 2 
# Apply a filter to the index to find hotels rated 4 or highter
# Returns the HotelName and Rating. Two documents match.
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$filter=Rating gt 4&$select=HotelName,Rating'

# Query example 3
# Take the top two results, and show only HotelName and Category in the results
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=boutique&$top=2&$select=HotelName,Category'

# Query example 4
# Sort by a specific field (Address/City) in ascending order

$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating'
```
## <a name="clean-up"></a>Opruimen 

Wanneer u in uw eigen abonnement werkt, is het een goed idee aan het einde van een project om te bepalen of u nog steeds de resources nodig hebt die u hebt gemaakt. Resources die actief zijn, kunnen kosten in rekening worden. U kunt resources afzonderlijk verwijderen of de resource groep verwijderen om de volledige set resources te verwijderen.

U kunt resources vinden en beheren in de portal met behulp van de koppeling **alle resources** of **resource groepen** in het navigatie deel venster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, Indexeer functies en gegevens bronnen. U kunt afzonderlijke items in de Portal verwijderen om de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u Power shell gebruikt voor het door lopen van de basis werk stroom voor het maken en openen van inhoud in Azure Search. Met de concepten in het oog wordt u aangeraden over te stappen op geavanceerdere scenario's, zoals indexeren vanuit Azure-gegevens bronnen;

> [!div class="nextstepaction"]
> [REST zelf studie: Semi-gestructureerde gegevens (JSON-blobs) indexeren en doorzoeken in Azure Search](search-semi-structured-data.md)