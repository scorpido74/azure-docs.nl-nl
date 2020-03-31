---
title: "Snelstart: een zoekindex maken in Python met REST API's"
titleSuffix: Azure Cognitive Search
description: Hier wordt uitgelegd hoe u een index maakt, gegevens laadt en query's uitvoert met Python, Jupyter-notitieblokken en de AZURE Cognitive Search REST API.
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 02/10/2020
ms.openlocfilehash: 93fb9ec735de1abf89eb217d0f4096fcfc0afe94
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78227100"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebooks"></a>Snelstart: een Azure Cognitive Search-index maken in Python met Jupyter-notitieblokken

> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C #](search-create-index-dotnet.md)
> * [Postbode (REST)](search-get-started-postman.md)
> * [Portal](search-create-index-portal.md)
> 

Maak een Jupyter-notitieblok dat een Azure Cognitive Search-index maakt, laadt en opvraagt met python en de [AZURE Cognitive Search REST API's.](https://docs.microsoft.com/rest/api/searchservice/) In dit artikel wordt uitgelegd hoe u stap voor stap een notitieblok maken. U ook [een voltooide Jupyter Python-notebook downloaden en uitvoeren.](https://github.com/Azure-Samples/azure-search-python-samples)

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

De volgende services en tools zijn vereist voor deze quickstart. 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), die Python 3.x en Jupyter Notebooks.

+ [Maak een Azure Cognitive Search-service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U de laag Gratis gebruiken voor deze quickstart. 

## <a name="get-a-key-and-url"></a>Een sleutel en URL ophalen

REST-aanroepen hebben voor elke aanvraag de service-URL en een toegangssleutel nodig. Er wordt een zoekservice gemaakt met beide, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, voert u de volgende stappen uit om de benodigde informatie te krijgen:

1. [Meld u aan bij de Azure-portal](https://portal.azure.com/)en ontvang de URL op de pagina **Overzicht** van uw zoekservice. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Ontvang **in Instellingentoetsen** > **Keys**een beheersleutel voor volledige rechten op de service. Er zijn twee verwisselbare beheerderssleutels, voorzien voor bedrijfscontinuïteit voor het geval u er een moet omdraaien. U de primaire of secundaire sleutel gebruiken voor aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

![Een HTTP-eindpunt en toegangssleutel downloaden](media/search-get-started-postman/get-url-key.png "Een HTTP-eindpunt en toegangssleutel downloaden")

Voor alle aanvragen is een api-sleutel vereist voor elk verzoek dat naar uw service wordt verzonden. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="connect-to-azure-cognitive-search"></a>Verbinding maken met Azure Cognitive Search

Start in deze taak een Jupyter-notitieblok en controleer of u verbinding maken met Azure Cognitive Search. U doet dit door een lijst met indexen van uw service aan te vragen. Op Windows met Anaconda3 u Anaconda Navigator gebruiken om een notitieblok te starten.

1. Maak een nieuw Python3-notitieblok.

1. Laad in de eerste cel de bibliotheken die worden gebruikt voor het werken met JSON en het formuleren van HTTP-aanvragen.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. Voer in de tweede cel de aanvraagelementen in die op elke aanvraag constanten zijn. Vervang de naam van de zoekservice (UW-ZOEK-SERVICE-NAAM) en de admin-API-sleutel (YOUR-ADMIN-API-KEY) door geldige waarden. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2019-05-06'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

   Als u ConnectionError `"Failed to establish a new connection"`krijgt, controleert u of de api-toets een primaire of`?` secundaire beheersleutel is en of alle voor- en achterliggende tekens (en `/`) op hun plaats zijn.

1. In de derde cel, formuleer het verzoek. Deze GET-aanvraag is gericht op de indexenverzameling van uw zoekservice en selecteert de naameigenschap van bestaande indexen.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Voer elke stap uit. Als er indexen bestaan, bevat het antwoord een lijst met indexnamen. In de screenshot hieronder heeft de service al een azureblob-index en een realestate-us-sample index.

   ![Python-script in Jupyter-notitieblok met HTTP-aanvragen voor Azure Cognitive Search](media/search-get-started-python/connect-azure-search.png "Python-script in Jupyter-notitieblok met HTTP-aanvragen voor Azure Cognitive Search")

   Een lege indexverzameling retourneert daarentegen dit antwoord:`{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1 - Een index maken

Tenzij u de portal gebruikt, moet er een index op de service bestaan voordat u gegevens laden. Deze stap maakt gebruik van de [API IndexREST maken](https://docs.microsoft.com/rest/api/searchservice/create-index) om een indexschema naar de service te pushen.

Vereiste elementen van een index zijn een naam, een veldenverzameling en een sleutel. De verzameling velden definieert de structuur van een *document*. Elk veld heeft een naam, type en kenmerken die bepalen hoe het veld wordt gebruikt (bijvoorbeeld of het volledig doorzoekbaar, filterbaar of opvraagbaar is in zoekresultaten). Binnen een index moet een `Edm.String` van de velden van het type worden aangewezen als de *sleutel* voor de identiteit van het document.

Deze index heeft de naam "hotels-quickstart" en heeft de velddefinities die u hieronder ziet. Het is een subset van een grotere [Hotels index](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) gebruikt in andere walkthroughs. We bijgesneden in deze quickstart voor beknoptheid.

1. Plak in de volgende cel het volgende voorbeeld in een cel om het schema op te geven. 

    ```python
    index_schema = {
       "name": "hotels-quickstart",  
       "fields": [
         {"name": "HotelId", "type": "Edm.String", "key": "true", "filterable": "true"},
         {"name": "HotelName", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "true", "facetable": "false"},
         {"name": "Description", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "en.lucene"},
         {"name": "Description_fr", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "fr.lucene"},
         {"name": "Category", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Tags", "type": "Collection(Edm.String)", "searchable": "true", "filterable": "true", "sortable": "false", "facetable": "true"},
         {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Rating", "type": "Edm.Double", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Address", "type": "Edm.ComplexType", 
         "fields": [
         {"name": "StreetAddress", "type": "Edm.String", "filterable": "false", "sortable": "false", "facetable": "false", "searchable": "true"},
         {"name": "City", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "StateProvince", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "PostalCode", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Country", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"}
        ]
       }
      ]
    }
    ```

2. In een andere cel, formuleer het verzoek. Met deze POST-aanvraag wordt de indexverzameling van uw zoekservice gericht en wordt een index op basis van het indexschema dat u in de vorige cel hebt opgegeven.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Voer elke stap uit.

   Het antwoord omvat de JSON-weergave van het schema. De volgende screenshot toont slechts een deel van het antwoord.

    ![Verzoek om een index te maken](media/search-get-started-python/create-index.png "Verzoek om een index te maken")

> [!Tip]
> Een andere manier om het maken van indexen te verifiëren, is door de lijst Met indexen in de portal te controleren.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Documenten laden

Als u documenten wilt pushen, gebruikt u een HTTP-POST-verzoek om het URL-eindpunt van uw index te gebruiken. De REST-API is [Documenten toevoegen, bijwerken of verwijderen.](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) Documenten zijn afkomstig van [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) op GitHub.

1. Geef in een nieuwe cel vier documenten op die voldoen aan het indexschema. Geef voor elk document een uploadactie op.

    ```python
    documents = {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
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
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
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

2. In een andere cel, formuleer het verzoek. Deze POST-aanvraag is gericht op het verzamelen van documenten van de hotels-quickstart-index en duwt de documenten die in de vorige stap zijn verstrekt.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Voer elke stap uit om de documenten naar een index in uw zoekservice te pushen. Resultaten moeten er hetzelfde uitzien als het volgende voorbeeld. 

    ![Documenten naar een index verzenden](media/search-get-started-python/load-index.png "Documenten naar een index verzenden")

## <a name="3---search-an-index"></a>3 - Een index doorzoeken

In deze stap ziet u hoe u een index opvraagt met behulp van de [API Voor zoekdocumenten REST](https://docs.microsoft.com/rest/api/searchservice/search-documents).

1. Geef in een cel een queryexpressie op die een lege zoekopdracht uitvoert (search=*), waarbij een niet-gerangschikte lijst (zoekscore = 1,0) van willekeurige documenten wordt retournerd. Azure Cognitive Search retourneert standaard 50 overeenkomsten tegelijk. Als gestructureerd retourneert deze query een volledige documentstructuur en -waarden. Voeg $count=true toe om een aantal documenten in de resultaten te krijgen.

   ```python
   searchstring = '&search=*&$count=true'
   ```

1. Geef in een nieuwe cel het volgende voorbeeld om te zoeken op de termen "hotels" en "wifi". Voeg $select toe om op te geven welke velden in de zoekresultaten moeten worden opgenomen.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'
   ```

1. In een andere cel, formuleer een verzoek. Deze GET-aanvraag is gericht op het verzamelen van documenten van de index voor hotels snel beginnen en wordt de query die u in de vorige stap hebt opgegeven, gekoppeld.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. Voer elke stap uit. Resultaten moeten er vergelijkbaar uitzien met de volgende uitvoer. 

    ![Een index doorzoeken](media/search-get-started-python/search-index.png "Een index doorzoeken")

1. Probeer een paar andere queryvoorbeelden om een gevoel voor de syntaxis te krijgen. U `searchstring` de volgende voorbeelden vervangen door de volgende voorbeelden en vervolgens de zoekaanvraag opnieuw uitvoeren. 

   Een filter toepassen: 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'
   ```

   Neem de top twee resultaten:

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description,Category'
   ```

    Orde op een specifiek veld:

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince, Tags'
   ```

## <a name="clean-up"></a>Opruimen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de resources die u hebt gemaakt, nog nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U resources in de portal vinden en beheren met de koppeling **Alle resources** of **Resourcegroepen** in het linkernavigatiedeelvenster.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u beperkt bent tot drie indexen, indexeerders en gegevensbronnen. U afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

Als vereenvoudiging maakt deze quickstart gebruik van een verkorte versie van de Hotels-index. U de volledige versie maken om interessantere query's uit te proberen. Als u de volledige versie en alle 50 documenten wilt ophalen, voert u de wizard **Gegevens importeren** uit de ingebouwde voorbeeldgegevensbronnen *selecteren.*

> [!div class="nextstepaction"]
> [Snelstart: een index maken in de Azure-portal](search-get-started-portal.md)
