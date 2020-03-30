---
title: "Snelstart: een zoekindex maken in PowerShell met REST API's"
titleSuffix: Azure Cognitive Search
description: Lees in deze REST API snelstart hoe u een index maakt, gegevens laadt en query's uitvoert met de Invoke-RestMethod en de Azure Cognitive Search REST API.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 02/10/2020
ms.openlocfilehash: 612751c2405cd55ad0b3760aa8e093e434a22f57
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77121600"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-powershell-using-rest-apis"></a>Snelstart: een Azure Cognitive Search-index maken in PowerShell met REST API's
> [!div class="op_single_selector"]
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C #](search-create-index-dotnet.md)
> * [Postbode (REST)](search-get-started-postman.md)
> * [Python](search-get-started-python.md)
> * [Portal](search-create-index-portal.md)
> 

In dit artikel wordt u door het proces van het maken, laden en opvragen van een Azure Cognitive Search-index met PowerShell en de [AZURE Cognitive Search REST API's doorlopen.](https://docs.microsoft.com/rest/api/searchservice/) In dit artikel wordt uitgelegd hoe u PowerShell-opdrachten interactief uitvoeren. U ook [een Powershell-script downloaden en uitvoeren](https://github.com/Azure-Samples/azure-search-powershell-samples/tree/master/Quickstart) dat dezelfde bewerkingen uitvoert.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

De volgende services en tools zijn vereist voor deze quickstart. 

+ [PowerShell 5.1 of hoger](https://github.com/PowerShell/PowerShell), met [behulp van Invoke-RestMethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) voor sequentiële en interactieve stappen.

+ [Maak een Azure Cognitive Search-service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U gebruik maken van een gratis service voor deze quickstart. 

## <a name="get-a-key-and-url"></a>Een sleutel en URL ophalen

REST-aanroepen hebben voor elke aanvraag de service-URL en een toegangssleutel nodig. Er wordt een zoekservice gemaakt met beide, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, voert u de volgende stappen uit om de benodigde informatie te krijgen:

1. [Meld u aan bij de Azure-portal](https://portal.azure.com/)en ontvang de URL op de pagina **Overzicht** van uw zoekservice. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

2. Ontvang **in Instellingentoetsen** > **Keys**een beheersleutel voor volledige rechten op de service. Er zijn twee verwisselbare beheerderssleutels, voorzien voor bedrijfscontinuïteit voor het geval u er een moet omdraaien. U de primaire of secundaire sleutel gebruiken voor aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

![Een HTTP-eindpunt en toegangssleutel downloaden](media/search-get-started-postman/get-url-key.png "Een HTTP-eindpunt en toegangssleutel downloaden")

Voor alle aanvragen is een api-sleutel vereist voor elk verzoek dat naar uw service wordt verzonden. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="connect-to-azure-cognitive-search"></a>Verbinding maken met Azure Cognitive Search

1. Maak in PowerShell een **$headers** object om de inhoudstype- en API-sleutel op te slaan. Vervang de admin API-sleutel (YOUR-ADMIN-API-KEY) door een sleutel die geldig is voor uw zoekservice. U hoeft deze koptekst slechts één keer in te stellen voor de duur van de sessie, maar u voegt deze toe aan elk verzoek. 

    ```powershell
    $headers = @{
    'api-key' = '<YOUR-ADMIN-API-KEY>'
    'Content-Type' = 'application/json' 
    'Accept' = 'application/json' }
    ```

2. Maak een **$url** object dat de indexenverzameling van de service opgeeft. Vervang de servicenaam (YOUR-SEARCH-SERVICE-NAME) door een geldige zoekservice.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06&$select=name"
    ```

3. Voer **Invoke-RestMethod** uit om een GET-aanvraag naar de service te verzenden en de verbinding te verifiëren. Voeg **ConvertTo-Json toe,** zodat u de reacties bekijken die vanuit de service zijn teruggestuurd.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

   Als de service leeg is en geen indexen heeft, zijn de resultaten vergelijkbaar met het volgende voorbeeld. Anders ziet u een JSON-weergave van indexdefinities.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
        "value":  [

                ]
    }
    ```

## <a name="1---create-an-index"></a>1 - Een index maken

Tenzij u de portal gebruikt, moet er een index op de service bestaan voordat u gegevens laden. Met deze stap wordt de index gedefinieerd en wordt deze naar de service gepusht. Voor deze stap wordt [de API IndexREST maken](https://docs.microsoft.com/rest/api/searchservice/create-index) gebruikt.

Vereiste elementen van een index zijn een naam en een veldenverzameling. De verzameling velden definieert de structuur van een *document*. Elk veld heeft een naam, type en kenmerken die bepalen hoe het wordt gebruikt (bijvoorbeeld of het full-text doorzoekbaar, filterbaar of opvraagbaar is in zoekresultaten). Binnen een index moet een `Edm.String` van de velden van het type worden aangewezen als de *sleutel* voor de identiteit van het document.

Deze index heeft de naam "hotels-quickstart" en heeft de velddefinities die u hieronder ziet. Het is een subset van een grotere [Hotels index](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) gebruikt in andere walkthroughs. We bijgesneden in deze quickstart voor beknoptheid.

1. Plak dit voorbeeld in PowerShell om een **$body** object te maken dat het indexschema bevat.

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

2. Stel de URI in op de indexenverzameling op uw service en de *hotels-quickstart-index.*

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart?api-version=2019-05-06"
    ```

3. Voer de opdracht uit met **$url,** **$headers**en **$body** om de index op de service te maken. 

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
    ```

    Resultaten moeten er vergelijkbaar uitzien met deze (afgekapt naar de eerste twee velden voor beknoptheid):

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
> Voor verificatie u ook de lijst Met indexen in de portal controleren.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Documenten laden

Als u documenten wilt pushen, gebruikt u een HTTP-POST-verzoek om het URL-eindpunt van uw index te gebruiken. De REST-API voor deze taak is [Documenten toevoegen, bijwerken of verwijderen.](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)

1. Plak dit voorbeeld in PowerShell om een **$body** object te maken met de documenten die u wilt uploaden. 

    Deze aanvraag bevat twee volledige en een gedeeltelijke record. De gedeeltelijke record toont aan dat u onvolledige documenten uploaden. De `@search.action` parameter geeft aan hoe indexering wordt uitgevoerd. Geldige waarden zijn uploaden, samenvoegen, samenvoegenOf Uploaden en verwijderen. Het gedrag mergeOrUpload maakt een nieuw document voor hotelId = 3 of werkt de inhoud bij als deze al bestaat.

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

1. Stel het eindpunt in op de verzameling *hotels-quickstart-documenten* en neem de indexbewerking op (indexen/hotels-quickstart/docs/index).

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2019-05-06"
    ```

1. Voer de opdracht uit met **$url,** **$headers**en **$body** om documenten in de index voor hotels snelstart te laden.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
    ```
    Resultaten moeten er hetzelfde uitzien als het volgende voorbeeld. U ziet een [statuscode van 201.](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes)

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

In deze stap ziet u hoe u een index opvraagt met behulp van de [API voor zoekdocumenten](https://docs.microsoft.com/rest/api/searchservice/search-documents).

Zorg ervoor dat u enkele offertes te gebruiken op zoek $urls. Querytekenreeksen **$** bevatten tekens en u weglaten dat u eraan moet ontsnappen als de hele tekenreeks is ingesloten in afzonderlijke aanhalingstekens..

1. Stel het eindpunt in op de verzameling *hotels-quickstartdocumenten* en voeg een **zoekparameter** toe die u wilt doorgeven in een querytekenreeks. 
  
   Met deze tekenreeks wordt een lege zoekopdracht uitgevoerd (search=*), die een niet-gerangschikte lijst (zoekscore = 1,0) van willekeurige documenten retoureert. Azure Cognitive Search retourneert standaard 50 overeenkomsten tegelijk. Als gestructureerd retourneert deze query een volledige documentstructuur en -waarden. Voeg **$count=true toe** om een aantal documenten in de resultaten te krijgen.

    ```powershell
    $url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2019-05-06&search=*&$count=true'
    ```

1. Voer de opdracht uit om de **$url** naar de service te verzenden.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

    Resultaten moeten er vergelijkbaar uitzien met de volgende uitvoer.

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

Probeer een paar andere queryvoorbeelden om een gevoel voor de syntaxis te krijgen. U een tekenreekszoekopdracht doen, letterlijk $filter query's, de resultatenset beperken, het zoeken beperken tot specifieke velden en meer.

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
## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de resources die u hebt gemaakt, nog nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U resources in de portal vinden en beheren met de koppeling **Alle resources** of **Resourcegroepen** in het linkernavigatiedeelvenster.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u beperkt bent tot drie indexen, indexeerders en gegevensbronnen. U afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u PowerShell gebruikt om de basiswerkstroom voor het maken en openen van inhoud in Azure Cognitive Search te doorlopen. Met de concepten in het achterhoofd raden we aan om over te gaan tot meer geavanceerde scenario's, zoals indexering uit Azure-gegevensbronnen;

> [!div class="nextstepaction"]
> [REST-zelfstudie: semi-gestructureerde gegevens (JSON-blobs) indexeren en zoeken in Azure Cognitive Search](search-semi-structured-data.md)